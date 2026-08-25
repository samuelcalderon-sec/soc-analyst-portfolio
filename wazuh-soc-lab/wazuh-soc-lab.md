# Wazuh SOC / Blue Team Lab

Laboratorio práctico de **SOC / Blue Team** orientado a la monitorización, detección e investigación de actividad sospechosa sobre un endpoint Windows utilizando **Wazuh 4.12.0** y **Sysmon**.

El proyecto reproduce, a escala de laboratorio, el flujo de trabajo de un analista SOC de Nivel 1: generación de telemetría, detección, triage, análisis de procesos y líneas de comando, correlación de eventos, reconstrucción temporal, evaluación de impacto, mapeo MITRE ATT&CK y documentación de una conclusión defendible.

## Objetivo

El objetivo principal fue construir y operar un entorno funcional de monitoreo y detección de seguridad, demostrando la capacidad de:

* Generar y analizar telemetría real de Windows.
* Diseñar y validar reglas de detección en Wazuh.
* Realizar triage de alertas.
* Analizar relaciones padre/hijo entre procesos.
* Investigar líneas de comando y parámetros sospechosos.
* Correlacionar eventos mediante `ProcessGuid` y `ParentProcessGuid`.
* Reconstruir una línea de tiempo de actividad.
* Decodificar comandos PowerShell ofuscados.
* Correlacionar telemetría de endpoint con validación de red.
* Mapear comportamiento observado a MITRE ATT&CK.
* Evaluar impacto y determinar una clasificación defendible.
* Documentar evidencia, limitaciones, inferencias y conclusiones.

El objetivo no fue construir un SIEM empresarial ni desarrollar administración avanzada de Wazuh/OpenSearch, sino demostrar capacidades prácticas de análisis y operación SOC.

## Arquitectura del laboratorio

El laboratorio estuvo compuesto por tres elementos principales:

```text
Kali Linux
192.168.1.21
     │
     │ TCP/4444
     ▼
Windows Endpoint
WIN10-SOC-ENDPOINT01
Sysmon + Wazuh Agent
     │
     │ Sysmon telemetry
     ▼
Wazuh SIEM
Manager + Indexer/OpenSearch + Dashboard
Ubuntu Server
```

### Componentes

| Componente          | Tecnología                      |
| ------------------- | ------------------------------- |
| SIEM                | Wazuh 4.12.0                    |
| Sistema del SIEM    | Ubuntu Server 24.04.4           |
| Endpoint            | Windows                         |
| Telemetría          | Sysmon                          |
| Agent               | Wazuh Agent                     |
| Plataforma ofensiva | Kali Linux                      |
| Validación de red   | Windows `netstat` + Kali Netcat |

## Telemetría

La principal fuente de telemetría utilizada durante la investigación fue **Sysmon Event ID 1 — Process Creation**, ingerida exitosamente de extremo a extremo en Wazuh.

Entre los campos analizados estuvieron:

* `Image`
* `ParentImage`
* `CommandLine`
* `ParentCommandLine`
* `User`
* `ParentUser`
* `IntegrityLevel`
* `ProcessId`
* `ParentProcessId`
* `ProcessGuid`
* `ParentProcessGuid`
* `SHA256`
* Timestamp UTC

Un elemento central del análisis fue el uso de `ProcessGuid` y `ParentProcessGuid` para validar relaciones padre/hijo. Esto permitió diferenciar relaciones de procesos realmente confirmadas de relaciones que únicamente parecían formar parte de la misma cadena temporal.

Sysmon Event ID 3 fue habilitado y confirmado en Windows, pero no fue ingerido correctamente por Wazuh en este entorno. La actividad de red fue por ello validada de forma independiente mediante `netstat` en Windows y Netcat en Kali.

## Ingeniería de detección

Se configuraron reglas locales de Wazuh orientadas principalmente a comportamiento de procesos y parámetros de línea de comando.

| Regla    | Detección                                                                |
| -------- | ------------------------------------------------------------------------ |
| `100100` | Creación de `notepad.exe`                                                |
| `100102` | PowerShell ejecutado con `-EncodedCommand`                               |
| `100104` | PowerShell generado por `cmd.exe`                                        |
| `100105` | `cmd.exe` generado por `notepad.exe`                                     |
| `100106` | Ajuste de una excepción para reducir actividad legítima del agente Wazuh |

Las reglas `100102`, `100104` y `100105` fueron validadas mediante evidencia obtenida durante el Incidente 001.

El diseño priorizó relaciones entre procesos y características de la línea de comando en lugar de depender exclusivamente de nombres de archivos.

## Línea base

Antes de ejecutar el escenario controlado se estableció una línea base de actividad legítima.

Una ejecución normal de Notepad presentó la relación:

```text
explorer.exe
    └── notepad.exe
```

La actividad disparó la regla `100100`, pero fue clasificada como **Benigna / Esperada** después de analizar contexto adicional: ruta estándar del sistema, firma de Microsoft, proceso padre esperado, usuario, nivel de integridad y ausencia de comportamiento hijo sospechoso.

Esto permitió validar una capacidad fundamental del análisis SOC:

> Una alerta correctamente disparada no implica automáticamente un incidente de seguridad.

## Incidente 001 — Investigación controlada

El caso principal del laboratorio fue una simulación autorizada de comportamiento sospechoso sobre el endpoint Windows.

El escenario investigado involucró:

```text
Notepad
   ↓
CMD
   ↓
PowerShell
   ↓
PowerShell + -EncodedCommand
   ↓
TCP connection → Kali Linux:4444
```

Las etapas de ejecución fueron detectadas mediante las reglas locales:

```text
100105 → Notepad → CMD
100104 → CMD → PowerShell
100102 → PowerShell → EncodedCommand
```

La aparición de estos indicadores sobre el mismo endpoint y usuario, dentro de una ventana temporal reducida, justificó un análisis detallado durante el triage.

## Análisis del PowerShell codificado

El comando observado utilizó:

```text
-EncodedCommand
```

El contenido fue decodificado desde Base64/UTF-16LE y permitió identificar una conexión TCP hacia:

```text
192.168.1.21:4444
```

El comando utilizó `System.Net.Sockets.TcpClient` para establecer, mantener durante aproximadamente 60 segundos y posteriormente cerrar la conexión.

El análisis determinó que el código no implementaba lectura/escritura mediante `NetworkStream`, ejecución remota de comandos ni una shell interactiva.

Por ello, el comportamiento fue documentado técnicamente como una **simulación controlada de comunicación tipo C2 mediante una conexión TCP**, y no como una reverse shell interactiva ni como evidencia de un C2 real.

## Hallazgo crítico de correlación

Uno de los resultados más importantes de la investigación fue que la telemetría **no permitió confirmar que todos los eventos formaran una única rama continua del árbol de procesos**.

Cada relación individual fue validada mediante `ProcessGuid` / `ParentProcessGuid`:

```text
Notepad → CMD
CMD → PowerShell
PowerShell → PowerShell + EncodedCommand
```

Sin embargo, los GUID de los procesos padre utilizados en cada salto no coincidieron con los procesos intermedios documentados en el salto anterior.

Por tanto, la investigación no afirmó una cadena continua que la evidencia no permitía demostrar.

Este hallazgo demuestra una capacidad fundamental de investigación:

**ProcessId / ParentProcessId por sí solos no son suficientes para reconstruir de forma confiable una cadena de procesos; la relación debe validarse mediante los identificadores proporcionados por Sysmon.**

## Correlación de evidencia

La investigación combinó múltiples fuentes:

1. Alertas Wazuh generadas a partir de Sysmon Event ID 1.
2. Relaciones `ProcessGuid` / `ParentProcessGuid`.
3. Líneas de comando.
4. Hashes SHA256.
5. Timestamps UTC.
6. Validación de la conexión mediante `netstat` en Windows.
7. Observación de la conexión en el listener Netcat de Kali.

Esta correlación permitió construir una interpretación coherente del comportamiento observado pese a las limitaciones de telemetría de red del SIEM.

## MITRE ATT&CK

El comportamiento observado fue mapeado a:

| Técnica     | Nombre                                     | Evidencia                                                                |
| ----------- | ------------------------------------------ | ------------------------------------------------------------------------ |
| `T1059.003` | Windows Command Shell                      | `cmd.exe` generado por Notepad                                           |
| `T1059.001` | PowerShell                                 | Ejecución de PowerShell desde `cmd.exe` y otras instancias de PowerShell |
| `T1027`     | Obfuscated/Compressed Files or Information | Uso de `-EncodedCommand` con contenido Base64/UTF-16LE                   |

La conexión TCP no fue asignada a una técnica específica de Command and Control debido a que la evidencia disponible no demostró un protocolo de aplicación, intercambio de comandos ni otro comportamiento que justificara una clasificación más específica.

## Clasificación del incidente

Desde una perspectiva de producción, la combinación de:

* Aplicación no administrativa generando `cmd.exe`.
* `cmd.exe` generando PowerShell.
* PowerShell ejecutando contenido codificado.
* Conexión TCP saliente.
* Mismo host y usuario.

justificaría una investigación de alta prioridad.

Dentro del laboratorio, la actividad fue deliberadamente generada y autorizada.

**Clasificación final:**

```text
Benigno / Validación de Seguridad Autorizada

Disposición:
Cerrado — Simulación de Incidente Controlada
```

Las detecciones no fueron consideradas falsos positivos. Las reglas detectaron correctamente los comportamientos para los cuales fueron diseñadas; la clasificación benigna se debió al contexto autorizado del laboratorio.

## Capacidades demostradas

Este proyecto permitió validar de forma práctica competencias relacionadas con:

* SOC Level 1 operations
* Security monitoring
* SIEM operations
* Windows security telemetry
* Sysmon analysis
* Process tree analysis
* Parent-child process correlation
* Command-line analysis
* PowerShell analysis
* Behavioral detection
* Alert triage
* Event correlation
* Timeline reconstruction
* IOC / IOA analysis
* MITRE ATT&CK mapping
* Host and network correlation
* Incident classification
* Incident response fundamentals
* Technical security documentation
* Evidence-based analysis

## Limitaciones

Durante el proyecto se presentaron limitaciones asociadas a los recursos disponibles para el despliegue de Wazuh/OpenSearch.

Adicionalmente, Sysmon Event ID 3 fue confirmado en Windows pero no pudo ser ingerido correctamente por Wazuh en este entorno. La investigación compensó esta limitación mediante validación directa y correlacionada utilizando `netstat` y Netcat.

Estas limitaciones fueron documentadas y consideradas durante la evaluación del incidente.

## Conclusiones

El laboratorio demostró la capacidad de operar un ciclo analítico SOC de Nivel 1 sobre un entorno real de laboratorio, desde la generación y recopilación de telemetría hasta la detección, triage, correlación, reconstrucción, mapeo MITRE ATT&CK y documentación de una conclusión defendible.

El principal resultado no fue únicamente la generación exitosa de alertas, sino la capacidad de **distinguir evidencia confirmada de inferencias**, identificar una discontinuidad real en la cadena de procesos y evitar presentar como hecho una relación que la telemetría no permitía confirmar.

Esto permitió aplicar un enfoque de investigación basado en evidencia y comportamiento en lugar de asumir una narrativa de ataque predefinida.

## Documentación completa

El análisis técnico completo, incluyendo metodología, configuración, evidencia, análisis detallado, línea de tiempo, correlación, limitaciones, MITRE ATT&CK, evaluación de impacto y conclusiones se encuentra en:

[**Informe Final del Laboratorio SOC**](documentation/Informe-Final-Laboratorio-SOC.pdf)

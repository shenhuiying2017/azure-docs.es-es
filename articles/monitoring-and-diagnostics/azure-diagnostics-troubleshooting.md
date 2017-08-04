---
title: "Solución de problemas de Diagnósticos de Azure | Microsoft Docs"
description: "Solucione problemas al utilizar Diagnósticos de instancias de Azure Virtual Machine, Service Fabric o Cloud Services."
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 66469bce-d457-4d1e-b550-a08d2be4d28c
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/12/2017
ms.author: robb
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: df53e92b877b4790bb700f176a1988d265ec4678
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="azure-diagnostics-troubleshooting"></a>Solución de problemas de Diagnósticos de Azure
Solución de problemas relacionados con el uso de Diagnósticos de Azure. Para obtener información sobre Diagnósticos de Azure, consulte la [introducción a Diagnósticos de Azure](azure-diagnostics.md).

## <a name="logical-components"></a>Componentes lógicos
**Diagnostics Plugin Launcher (DiagnosticsPluginLauncher.exe)**: inicia la extensión Azure Diagnostics. Funciona como el proceso de punto de entrada.

**Diagnostics Plugin (DiagnosticsPlugin.exe)**: proceso principal que inicia el componente anterior; configura el agente de supervisión, lo inicia y administra su ciclo de vida. 

**Agente de supervisión (procesos de MonAgent\*.exe)**: estos procesos realizan el grueso del trabajo; es decir, supervisan, recopilan y transfieren los datos de diagnóstico.  

## <a name="logartifact-paths"></a>Rutas de acceso de registro y artefacto
Estas son las rutas de acceso a algunos de registros y artefactos más importantes. Se seguirá haciendo referencia a ellos en el resto del documento:
### <a name="cloud-services"></a>Cloud Services
| Artefacto | path |
| --- | --- |
| **Archivo de configuración de Azure Diagnostics** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\Config.txt |
| **Archivos de registro** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\ |
| **Almacén local para los datos de diagnóstico** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Tables |
| **Archivo de configuración del agente de supervisión** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Paquete de extensión de Azure Diagnostics** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version> |
| **Ruta de acceso a la utilidad de recopilación de registros** | %SystemDrive%\Packages\GuestAgent\ |
| **Archivo de registro de MonAgentHost** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

### <a name="virtual-machines"></a>Máquinas virtuales
| Artefacto | path |
| --- | --- |
| **Archivo de configuración de Azure Diagnostics** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\RuntimeSettings |
| **Archivos de registro** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\Logs\ |
| **Almacén local para los datos de diagnóstico** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Tables |
| **Archivo de configuración del agente de supervisión** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MaConfig.xml |
| **Archivo de estado** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\Status |
| **Paquete de extensión de Azure Diagnostics** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>|
| **Ruta de acceso a la utilidad de recopilación de registros** | C:\WindowsAzure\Packages |
| **Archivo de registro de MonAgentHost** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

## <a name="azure-diagnostics-is-not-starting"></a>Diagnósticos de Azure no se inicia
Examine los archivos **DiagnosticsPluginLauncher.log** y **DiagnosticsPlugin.log** en la ubicación de los archivos de registro proporcionada anteriormente para obtener información sobre el motivo de que los diagnósticos no se consigan iniciar. 

Si estos registros indican `Monitoring Agent not reporting success after launch`, significa que hubo un error al iniciar MonAgentHost.exe. Examine los registros en la ubicación indicada para `MonAgentHost log file` en la sección anterior.

La última línea de los archivos de registro contiene el código de salida.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
Si encuentra un código de salida **negativo**, consulte la [tabla de códigos de salida](#azure-diagnostics-plugin-exit-codes) en [Referencias](#references).

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Los datos de diagnóstico no se registran en Almacenamiento de Azure
Determine si no se muestra ningún dato o solo no se muestran algunos datos.

### <a name="diagnostics-infrastructure-logs"></a>Registros de infraestructura de diagnóstico
Los registros de infraestructura de diagnóstico es donde Azure Diagnostics registra los errores encontrados. Asegúrese de que ha habilitado ([¿cómo?](#how-to-check-diagnostics-extension-configuration)) la captura de registros de infraestructura de diagnóstico en la configuración y busque rápidamente los errores de interés que se muestran en la tabla `DiagnosticInfrastructureLogsTable` en la cuenta de almacenamiento configurada.

### <a name="no-data-is-showing-up"></a>No se muestra ningún dato
La causa más común de que falten datos de eventos enteros es una información de cuenta de almacenamiento definida incorrectamente.

Solución: corrija la configuración de Diagnostics y vuelva a instalar esta extensión.

Si la cuenta de almacenamiento está configurada correctamente, conéctese a la máquina mediante Escritorio remoto y asegúrese de que DiagnosticsPlugin.exe y MonAgentCore.exe se están ejecutando. En caso negativo, siga las instrucciones de la sección [Azure Diagnostics no se inicia](#azure-diagnostics-is-not-starting). Si los procesos están en ejecución, vaya a [¿Se capturan los datos localmente?](#is-data-getting-captured-locally) y siga en adelante esta guía desde allí.

### <a name="part-of-the-data-is-missing"></a>No están todos los datos
Si quiere obtener algunos datos pero no otros. Esto significa que la recopilación de datos o canalización de transferencia se ha establecido correctamente. Siga las subsecciones que se muestran aquí para acotar el problema:
#### <a name="is-collection-configured"></a>¿Está configurada la colección? 
La configuración de diagnóstico contiene la parte que indica que se recopile un tipo determinado de datos. [Revise la configuración](#how-to-check-diagnostics-extension-configuration) para asegurarse de que no está buscando datos que no se han configurado para la recopilación.
#### <a name="is-the-host-generating-data"></a>¿Genera datos el host?
- **Contadores de rendimiento**: abra el Monitor de rendimiento y compruebe el contador.
- **Registros de seguimiento**: conéctese a la máquina virtual mediante Escritorio remoto y agregue TextWriterTraceListener al archivo de configuración de la aplicación.  Consulte http://msdn.microsoft.com/en-us/library/sk36c28t.aspx para configurar el agente de escucha de texto.  Asegúrese de que el elemento `<trace>` tenga `<trace autoflush="true">`.<br />
Si no ve que se generen registros de seguimiento, consulte [Más sobre registros de seguimiento que faltan](#more-about-trace-logs-missing).
- **Seguimientos de ETW**: conéctese a la máquina virtual mediante Escritorio remoto e instale PerfView.  En PerfView, ejecute File (Archivo) -> User Command (Comando de usuario) -> Listen (Escuchar) etwprovder1, etwprovider2, etc.  Tenga en cuenta que el comando de escucha distingue mayúsculas de minúsculas y no puede haber espacios entre la lista de proveedores de ETW separados por coma.  Si no se puede ejecutar el comando, puede hacer clic en el botón "Log" (Registrar) en la esquina inferior derecha de la herramienta Perfview para ver lo que se intentó ejecutar y cuál fue el resultado.  Suponiendo que la entrada sea correcta, aparece una nueva ventana y en unos segundos comenzará a ver seguimientos de ETW.
- **Registros de eventos**: conéctese a la máquina virtual mediante Escritorio remoto. Abra `Event Viewer` y asegurarse de que los eventos existan.
#### <a name="is-data-getting-captured-locally"></a>¿Se capturan los datos localmente?
A continuación, asegúrese de que los datos se capturen localmente.
Los datos se almacenan localmente en archivos `*.tsf` en [el almacén local de datos de diagnóstico](#log-artifacts-path). Diferentes tipos de registros se recopilan en diferentes archivos `.tsf`. Los nombres son similares a los nombres de tablas de Azure Storage. Por ejemplo, `Performance Counters` se recopilan en `PerformanceCountersTable.tsf`, los registros de eventos en `WindowsEventLogsTable.tsf`, etc. Siga las instrucciones de la sección [Extracción de registros locales](#local-log-extraction) para abrir los archivos de recopilación locales y asegúrese de que puede ver que se recopilan en el disco.

Si no ve que los registros se recopilan localmente y ya ha comprobado que el host genera datos, es probable que tenga un problema de configuración. Revise la configuración atentamente para la sección correspondiente. Revise también la configuración generada para MonitoringAgent [MaConfig.xml](#log-artifacts-path) y asegúrese de que haya alguna sección en la que se describa el origen de registro pertinente y que no se pierda en la conversión entre la configuración de Azure Diagnostics y la del agente de supervisión.
#### <a name="is-data-getting-transferred"></a>¿Se transfieren los datos?
Si ha comprobado que los datos se capturan localmente pero sigue sin verlos en su cuenta de almacenamiento: 
- En primer lugar y ante todo, asegúrese de que haya proporcionado una cuenta de almacenamiento correcta y que no haya sustituido claves, etc., para la cuenta de almacenamiento determinada. Para los servicios en la nube, en ocasiones vemos que la gente no actualiza su valor de `useDevelopmentStorage=true`.
- Si se proporciona, la cuenta de almacenamiento es correcta. Asegúrese de que no tenga restricciones de red que no permitan que los componentes alcancen los puntos de conexión de almacenamiento público. Una forma de hacerlo consiste en conectarse a la máquina mediante Escritorio remoto e intentar escribir algo por sí mismo en la misma cuenta de almacenamiento.
- Por último, intente ver qué errores notifica el agente de supervisión. El agente de supervisión escribe sus registros en `maeventtable.tsf` que se encuentra en [el almacén local de datos de diagnóstico](#log-artifacts-path). Siga las instrucciones de [Extracción de registros locales](#local-log-extraction) para abrir este archivo y pruebe y determine si hay `errors` que indiquen la imposibilidad de leer archivos locales o de escribir en el almacenamiento.

### <a name="capturing--archiving-logs"></a>Captura y archivo de registros
Ha realizado los pasos anteriores pero no ha podido determinar qué ha funcionado mal y está pensando en ponerse en contacto con el soporte técnico. Lo primero que podrían pedirle es que recopilara registros de su máquina. Para ahorrar tiempo, hágalo usted mismo. Ejecute la utilidad `CollectGuestLogs.exe` en [Ruta de acceso a la utilidad de recopilación de registros](#log-artifacts-path) y se generará un archivo comprimido con todos los registros de Azure pertinentes en la misma carpeta.

## <a name="diagnostics-data-tables-not-found"></a>Tablas de datos de diagnóstico no encontradas
Las tablas de Azure Storage que contienen eventos de ETW se nombran mediante el código siguiente:

```C#
        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;
```

Este es un ejemplo:

```XML
        <EtwEventSourceProviderConfiguration provider=”prov1”>
          <Event id=”1” />
          <Event id=”2” eventDestination=”dest1” />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider=”prov2”>
          <DefaultEvents eventDestination=”dest2” />
        </EtwEventSourceProviderConfiguration>
```
```JSON
"EtwEventSourceProviderConfiguration": [
    {
        "provider": "prov1",
        "Event": [
            {
                "id": 1
            },
            {
                "id": 2,
                "eventDestination": "dest1"
            }
        ],
        "DefaultEvents": {
            "eventDestination": "DefaultEventDestination",
            "sinks": ""
        }
    },
    {
        "provider": "prov2",
        "DefaultEvents": {
            "eventDestination": "dest2"
        }
    }
]
```
Que genera 4 tablas:

| Evento | Nombre de la tabla |
| --- | --- |
| provider=”prov1” &lt;Event id=”1” /&gt; |WADEvent+MD5(“prov1”)+”1” |
| provider=”prov1” &lt;Event id=”2” eventDestination=”dest1” /&gt; |WADdest1 |
| provider=”prov1” &lt;DefaultEvents /&gt; |WADDefault+MD5(“prov1”) |
| provider=”prov2” &lt;DefaultEvents eventDestination=”dest2” /&gt; |WADdest2 |

## <a name="references"></a>Referencias

### <a name="how-to-check-diagnostics-extension-configuration"></a>Comprobación de la configuración de la extensión Diagnostics
La manera más sencilla de comprobar la configuración de su extensión es ir a http://resources.azure.com y desplazarse a la máquina virtual o al servicio en la nube en los que se encuentra la extensión de Azure Diagnostics (IaaSDiagnostics/PaaDiagnostics) en cuestión.

Como alternativa, conéctese a la máquina mediante Escritorio remoto y examine el archivo de configuración de Azure Diagnostics que se describe en la sección correspondiente [aquí](#log-artifacts-path).

En cualquier caso, busque **Microsoft.Azure.Diagnostics** y luego el campo **xmlCfg** o **WadCfg**. 

En el caso de máquinas virtuales, si está presente el campo WadCfg, significa que la configuración está en JSON. Si el campo xmlCfg está presente, significa que la configuración se ha realizado en XML y codificado en Base 64. Debe [descodificarlo](http://www.bing.com/search?q=base64+decoder) para ver el XML que cargó Diagnostics.

En rol de servicio en la nube, si elige la configuración del disco, los datos están codificados en Base64, por lo que deberá [descodificarlos](http://www.bing.com/search?q=base64+decoder) para ver el XML que se cargó mediante Diagnostics.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Códigos de salida del complemento Azure Diagnostics
El complemento devuelve los siguientes códigos de salida:

| Código de salida | Descripción |
| --- | --- |
| 0 |Correcta. |
| -1 |Error genérico. |
| -2 |No se puede cargar el archivo rcf.<p>Este error interno solo debería ocurrir si el iniciador del complemento del agente invitado se invoca manualmente de forma incorrecta en la máquina virtual. |
| -3 |No se puede cargar el archivo de configuración de Diagnósticos.<p><p>Solución: esto se debe a que un archivo de configuración no supera la validación del esquema. La solución es proporcionar un archivo de configuración que cumpla el esquema. |
| -4 |Ya hay otra instancia de Diagnósticos del agente de supervisión que usa el directorio de recursos local.<p><p>Solución: Especifique un valor distinto para **LocalResourceDirectory**. |
| -6 |El iniciador del complemento del agente invitado intentó iniciar Diagnósticos con una línea de comandos no válida.<p><p>Este error interno solo debería ocurrir si el iniciador del complemento del agente invitado se invoca manualmente de forma incorrecta en la máquina virtual. |
| -10 |El complemento Diagnósticos se cerró con una excepción no controlada. |
| -11 |El agente invitado no pudo crear el proceso encargado de iniciar y supervisar al agente de supervisión.<p><p>Solución: compruebe que hay disponibles recursos del sistema suficientes para iniciar nuevos procesos.<p> |
| -101 |Argumentos no válidos al llamar al complemento Diagnósticos.<p><p>Este error interno solo debería ocurrir si el iniciador del complemento del agente invitado se invoca manualmente de forma incorrecta en la máquina virtual. |
| -102 |El proceso del complemento no se puede iniciar por sí mismo.<p><p>Solución: compruebe que hay disponibles recursos del sistema suficientes para iniciar nuevos procesos. |
| -103 |El proceso del complemento no se puede iniciar por sí mismo. Concretamente, no puede crear el objeto del registrador.<p><p>Solución: compruebe que hay disponibles recursos del sistema suficientes para iniciar nuevos procesos. |
| -104 |No se puede cargar el archivo rcf proporcionado por el agente invitado.<p><p>Este error interno solo debería ocurrir si el iniciador del complemento del agente invitado se invoca manualmente de forma incorrecta en la máquina virtual. |
| -105 |El complemento Diagnósticos no puede abrir el archivo de configuración de Diagnósticos.<p><p>Este error interno solo debería ocurrir si el complemento Diagnósticos se invoca manualmente de forma incorrecta en la máquina virtual. |
| -106 |No se puede leer el archivo de configuración de Diagnósticos.<p><p>Solución: esto se debe a que un archivo de configuración no supera la validación del esquema. Por lo tanto, la solución es proporcionar un archivo de configuración que cumpla el esquema. Consulte [Comprobación de la configuración de la extensión Diagnostics](#how-to-check-diagnostics-extension-configuration). |
| -107 |El directorio de recursos pasado al agente de supervisión no es válido.<p><p>Este error interno solo debería ocurrir si el agente se invoca manualmente de forma incorrecta en la máquina virtual.</p> |
| -108 |No se puede convertir el archivo de configuración de Diagnósticos al archivo de configuración del agente de supervisión.<p><p>Este error interno solo debería ocurrir si el complemento Diagnósticos se invoca manualmente con un archivo de configuración no válido. |
| -110 |Error de configuración general de Diagnósticos.<p><p>Este error interno solo debería ocurrir si el complemento Diagnósticos se invoca manualmente con un archivo de configuración no válido. |
| -111 |No se puede iniciar el agente de supervisión.<p><p>Solución: compruebe que hay suficientes recursos del sistema disponibles. |
| -112 |Error general |

### <a name="local-log-extraction"></a>Extracción de registros locales
El agente de supervisión recopila registros y artefactos como archivos `.tsf`. El archivo `.tsf` no se puede leer pero puede convertirlo a `.csv` de la manera siguiente: 

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
Un archivo nuevo llamado `<relevantLogFile>.csv` se crea en la misma ruta de acceso que el archivo `.tsf` correspondiente.

**NOTA**: Solo debe ejecutar esta utilidad con el archivo tsf principal (por ejemplo, PerformanceCountersTable.tsf). Los archivos complementarios (por ejemplo, PerformanceCountersTables_\*\*001.tsf, PerformanceCountersTables_\*\*002.tsf etc.) se procesarán automáticamente.

### <a name="more-about-trace-logs-missing"></a>Más sobre registros de seguimiento que faltan

**Nota:** Se aplica principalmente solo a servicios en la nube, a no ser que haya configurado la utilidad DiagnosticsMonitorTraceListener en una aplicación que ejecuta la máquina virtual de IaaS. 

- Asegúrese de que DiagnosticMonitorTraceListener esté configurado en el archivo web.config o app.config.  De forma predeterminada, se configura en proyectos de servicios en la nube, pero algunos clientes lo convierten en comentario lo que hace que los diagnósticos no recopilen las instrucciones de seguimiento. 
- Si no se escriben registros con el método OnStart o Run, asegúrese de que DiagnosticMonitorTraceListener se encuentre en app.config.  De forma predeterminada está en web.config, pero eso solo se aplica al código que se ejecuta en w3wp.exe; así que debe estar en app.config para que se capturen los seguimientos que se ejecutan en WaIISHost.exe.
- Asegúrese de que usa Diagnostics.Trace.TraceXXX en lugar de Diagnostics.Debug.WriteXXX.  Las instrucciones Debug se quitarán de una compilación de versión.
- Asegúrese de que el código compilado tenga realmente líneas de Diagnostics.Trace (use Reflector, ildasm o ILSpy para comprobarlo).  Los comandos Diagnostics.Trace se quitan del binario compilado a menos que se use el símbolo de compilación condicional TRACE.  Si usa msbuild para compilar el proyecto, este es un problema habitual con el que se encontrará.

## <a name="known-issues-and-mitigations"></a>Problemas conocidos y mitigaciones
Esta es una lista de problemas conocidos con mitigaciones conocidas:

**1. Dependencia de .NET 4.5:**

WAD tiene una dependencia de tiempo de ejecución en la plataforma .NET 4.5 o superior. En el momento de escribir este artículo, todas las máquinas aprovisionadas para servicios en la nube y todas las imágenes de base oficiales de máquinas virtuales de Azure tienen instalado .NET 4.5 o superior. Aún así, es posible encontrarse en una situación en la que intente ejecutar WAD en una máquina virtual que no tiene .NET 4.5 o superior. Esto sucede cuando crea su máquina a partir de una imagen o instantánea anterior, o trae su propio disco personalizado.

Generalmente se manifiesta como un código de salida **255** al ejecutar DiagnosticsPluginLauncher.exe. El error se produce debido a la excepción no controlada: 
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**Mitigación:** instale .NET 4.5 o superior en su máquina.

**2. Los datos de los contadores de rendimiento están disponibles en el almacenamiento pero no se muestran en el portal**

La experiencia del portal de las máquinas virtuales muestra de forma predeterminada determinados contadores de rendimiento. Si no los ve y sabe que los datos se están generando porque están disponibles en el almacenamiento. Comprobar:
- Si los datos del almacenamiento tienen nombres de contadores en inglés. Si los nombres de los contadores no están en inglés, el gráfico de métricas del portal no podrá reconocerlos.
- Si usa caracteres comodín (\*) en los nombres de los contadores de rendimiento, el portal no podrá correlacionar el contador configurado y recopilado.

**Mitigación**: cambiar el idioma de la máquina a inglés en las cuentas del sistema. Panel de control -> Región -> Administrativa -> Configuración de la copia ->, desactive "Pantalla de inicio de sesión y cuentas del sistema" para que el idioma personalizado no se aplique a la cuenta del sistema. Además, asegúrese de que no usa caracteres comodín si quiere que el portal sea su experiencia de consumo principal.

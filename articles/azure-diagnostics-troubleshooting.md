---
title: "Solución de problemas de Diagnósticos de Azure"
description: "Solución de problemas al utilizar Diagnósticos de Azure en Servicios en la nube de Azure, Máquinas virtuales y "
services: multiple
documentationcenter: .net
author: rboucher
manager: jwhit
editor: 
ms.assetid: 66469bce-d457-4d1e-b550-a08d2be4d28c
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/04/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 0aee1c31876060777c32c768e0767a39de86cb2a
ms.openlocfilehash: 99b782a62726989c6fe2804ebf2e0f521f1cbb24


---
# <a name="azure-diagnostics-troubleshooting"></a>Solución de problemas de Diagnósticos de Azure
Solución de problemas relacionados con el uso de Diagnósticos de Azure. Para obtener información sobre Diagnósticos de Azure, consulte la [introducción a Diagnósticos de Azure](azure-diagnostics.md).

## <a name="azure-diagnostics-is-not-starting"></a>Diagnósticos de Azure no se inicia
Diagnósticos está formado por dos componentes: un complemento de agente invitado y el agente de supervisión. Puede comprobar los archivos de registro **DiagnosticsPluginLauncher.log** y **DiagnosticsPlugin.log** para obtener información sobre por qué los diagnósticos no se pueden iniciar.  

En un rol de servicio en la nube, los archivos de registro para el complemento del agente invitado se ubican en:

```
C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.6.3.0\
```

En una máquina virtual de Azure, los archivos de registro para el complemento del agente invitado se ubican en:

```
C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.6.3.0\Logs\
```

La última línea de los archivos de registro contendrá el código de salida.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```

El complemento devuelve los siguientes códigos de salida:

| Código de salida | Descripción |
| --- | --- |
| 0 |Correcta. |
| -1 |Error genérico. |
| -2 |No se puede cargar el archivo rcf.<p>Este es un error interno que solo debería ocurrir si el complemento del agente invitado se invoca manualmente de forma incorrecta en la máquina virtual. |
| -3 |No se puede cargar el archivo de configuración de Diagnósticos.<p><p>Solución: esto se debe a que un archivo de configuración no supera la validación del esquema. La solución es proporcionar un archivo de configuración que cumpla el esquema. |
| -4 |Ya hay otra instancia de Diagnósticos del agente de supervisión que usa el directorio de recursos local.<p><p>Solución: Especifique un valor distinto para **LocalResourceDirectory**. |
| -6 |El iniciador del complemento del agente invitado intentó iniciar Diagnósticos con una línea de comandos no válida.<p><p>Este es un error interno que solo debería ocurrir si el complemento del agente invitado se invoca manualmente de forma incorrecta en la máquina virtual. |
| -10 |El complemento Diagnósticos se cerró con una excepción no controlada. |
| -11 |El agente invitado no pudo crear el proceso encargado de iniciar y supervisar al agente de supervisión.<p><p>Solución: compruebe que hay disponibles recursos del sistema suficientes para iniciar nuevos procesos.<p> |
| -101 |Argumentos no válidos al llamar al complemento Diagnósticos.<p><p>Este es un error interno que solo debería ocurrir si el complemento del agente invitado se invoca manualmente de forma incorrecta en la máquina virtual. |
| -102 |El proceso del complemento no se puede iniciar por sí mismo.<p><p>Solución: compruebe que hay disponibles recursos del sistema suficientes para iniciar nuevos procesos. |
| -103 |El proceso del complemento no se puede iniciar por sí mismo. Concretamente, no puede crear el objeto del registrador.<p><p>Solución: compruebe que hay disponibles recursos del sistema suficientes para iniciar nuevos procesos. |
| -104 |No se puede cargar el archivo rcf proporcionado por el agente invitado.<p><p>Este es un error interno que solo debería ocurrir si el complemento del agente invitado se invoca manualmente de forma incorrecta en la máquina virtual. |
| -105 |El complemento Diagnósticos no puede abrir el archivo de configuración de Diagnósticos.<p><p>Este es un error interno que solo debería ocurrir si el complemento Diagnósticos se invoca manualmente de forma incorrecta en la máquina virtual. |
| -106 |No se puede leer el archivo de configuración de Diagnósticos.<p><p>Solución: esto se debe a que un archivo de configuración no supera la validación del esquema. Por lo tanto, la solución es proporcionar un archivo de configuración que cumpla el esquema. Puede ver que el archivo XML se entrega a la extensión Diagnósticos en la carpeta *%SystemDrive%\WindowsAzure\Config* de la VM. Abra el archivo XML en cuestión y busque **Microsoft.Azure.Diagnostics** y luego el campo **xmlCfg**. Los datos están codificados en base64, por lo que tendrá que [descodificarlos](http://www.bing.com/search?q=base64+decoder) para ver el archivo XML que cargó Diagnósticos.<p> |
| -107 |El directorio de recursos pasado al agente de supervisión no es válido.<p><p>Este es un error interno que solo debería ocurrir si el agente se invoca manualmente de forma incorrecta en la máquina virtual.</p> |
| -108 |No se puede convertir el archivo de configuración de Diagnósticos al archivo de configuración del agente de supervisión.<p><p>Este es un error interno que solo debería ocurrir si el complemento Diagnósticos se invoca manualmente con un archivo de configuración no válido. |
| -110 |Error de configuración general de Diagnósticos.<p><p>Este es un error interno que solo debería ocurrir si el complemento Diagnósticos se invoca manualmente con un archivo de configuración no válido. |
| -111 |No se puede iniciar el agente de supervisión.<p><p>Solución: compruebe que hay suficientes recursos del sistema disponibles. |
| -112 |Error general |

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Los datos de diagnóstico no se registran en Almacenamiento de Azure
Diagnósticos de Azure almacena todos los datos en Almacenamiento de Azure.

La causa más común de que falten datos de eventos es una información de cuenta de almacenamiento definida incorrectamente.

Solución: corrija el archivo de configuración de Diagnósticos y vuelva a instalar Diagnósticos.
Si el problema persiste después de volver a instalar la extensión de diagnósticos, puede que tenga que continuar la depuración y buscar errores en el agente de supervisión. Antes de que se carguen los datos del evento en la cuenta de almacenamiento, se almacenan en LocalResourceDirectory.

Para el rol de servicio en la nube, el directorio LocalResourceDirectory es:

    C:\Resources\Directory\<CloudServiceDeploymentID>.<RoleName>.DiagnosticStore\WAD<DiagnosticsMajorandMinorVersion>\Tables

Para las máquinas virtuales, el directorio LocalResourceDirectory es:

    C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD<DiagnosticsMajorandMinorVersion>\Tables

Si no hay archivos en la carpeta LocalResourceDirectory, el agente de supervisión no se puede iniciar. Esto suele estar causado por un archivo de configuración no válido, un evento del que se informa en CommandExecution.log.

Si el agente de supervisión está recopilando correctamente los datos de eventos, verá los archivos .tsf para cada evento definido en el archivo de configuración. El agente de supervisión registra los errores en el archivo MaEventTable.tsf. Para inspeccionar el contenido de este archivo, puede utilizar la aplicación tabel2csv para convertir el archivo .tsf en un archivo de valores separados por comas (.csv):

En un rol de servicio en la nube:

    %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

*% SystemDrive %* en un rol de servicio en la nube suele ser D:

En una máquina virtual:

    C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

Los comandos anteriores generan el archivo de registro *maeventtable.csv*, que puede abrir e inspeccionar para buscar mensajes de error.    

## <a name="diagnostics-data-tables-not-found"></a>Tablas de datos de diagnóstico no encontradas
Las tablas del almacenamiento de Azure que contienen datos de diagnóstico de Azure se denominan mediante el código siguiente:

        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;

Aquí tiene un ejemplo:

        <EtwEventSourceProviderConfiguration provider=”prov1”>
          <Event id=”1” />
          <Event id=”2” eventDestination=”dest1” />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider=”prov2”>
          <DefaultEvents eventDestination=”dest2” />
        </EtwEventSourceProviderConfiguration>

Esto generará 4 tablas:

| Evento | Nombre de la tabla |
| --- | --- |
| provider=”prov1” &lt;Event id=”1” /&gt; |WADEvent+MD5(“prov1”)+”1” |
| provider=”prov1” &lt;Event id=”2” eventDestination=”dest1” /&gt; |WADdest1 |
| provider=”prov1” &lt;DefaultEvents /&gt; |WADDefault+MD5(“prov1”) |
| provider=”prov2” &lt;DefaultEvents eventDestination=”dest2” /&gt; |WADdest2 |



<!--HONumber=Nov16_HO3-->



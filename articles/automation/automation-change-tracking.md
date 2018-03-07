---
title: Seguimiento de cambios con Azure Automation | Microsoft Docs
description: "La solución Change Tracking ayuda a identificar los cambios en el software y el servicio de Windows que se producen en su entorno."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: 
ms.assetid: f8040d5d-3c89-4f0c-8520-751c00251cb7
ms.service: automation
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: gwallace
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 79c5f354c3e63856474e46e2b6928af829604e15
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="track-software-changes-in-your-environment-with-the-change-tracking-solution"></a>Seguimiento de cambios de software en su entorno con la solución de seguimiento de cambios

Este artículo le ayuda a usar la solución Change Tracking para identificar fácilmente los cambios del entorno. La solución realiza un seguimiento de los cambios efectuados en el software de Windows y Linux, en los archivos de Windows y Linux, en las claves del registro de Windows, en los servicios de Windows y en los demonios de Linux. Identificar los cambios de configuración puede ayudarle a localizar problemas operativos.

Los cambios en los demonios de Linux, en el registro y los archivos de Windows, en los servicios de Windows y en el software instalado en los servidores supervisados se envían al servicio de Log Analytics en la nube para su procesamiento. Se aplica la lógica a los datos recibidos y el servicio de nube registra los datos. Con la información en el panel de seguimiento de cambios, puede ver fácilmente los cambios realizados en la infraestructura de servidores.

## <a name="enable-change-tracking-and-inventory"></a>Habilitación de Change Tracking e Inventario


Para iniciar el seguimiento de cambios, debe habilitar la solución Change Tracking e Inventory para la cuenta de Automation.

1. En Azure Portal, vaya a la cuenta de Automation
1. Seleccione **Change Tracking** en **Configuración**.
2. Seleccione un área de trabajo de Log Analytics existente o **Crear un área de trabajo nueva** y haga clic en **Habilitar**.

Esto habilita la solución para la cuenta de Automation. La habilitación de la solución puede tardar hasta 15 minutos. Un banner azul le avisa cuando la solución está habilitada. Vuelva a la página **Change Tracking** para administrar la solución.

## <a name="configuring-change-tracking-and-inventory"></a>Configuración de Change Tracking e Inventory

Para obtener información sobre cómo incorporar equipos a la solución, consulte: [Soluciones de automatización de la incorporación](automation-onboard-solutions-from-automation-account.md). Cuando se habilita un nuevo archivo o una clave del registro para realizar su seguimiento, está habilitado tanto para Change Tracking como para Inventory.

### <a name="configure-linux-files-to-track"></a>Configuración de los archivos de Linux de los que se realizará un seguimiento

Use los pasos siguientes para configurar el seguimiento de archivos en equipos Linux:

1. En la cuenta de Automation, seleccione **Change Tracking** en **ADMINISTRACIÓN DE CONFIGURACIÓN**. Haga clic en **Editar configuración** (el símbolo de engranaje).
2. En la página **Change Tracking**, seleccione **Archivos Linux** y, a continuación, haga clic en **+ Agregar** para agregar un nuevo archivo para realizar su seguimiento.
3. En **Agregar archivo de Linux a Change Tracking**, escriba la información del archivo o directorio cuyo seguimiento se va a realizar y haga clic en **Guardar**.

|Propiedad  |DESCRIPCIÓN  |
|---------|---------|
|habilitado     | Determina si se aplica la configuración.        |
|Nombre del elemento     | Nombre descriptivo del archivo cuyo seguimiento se va a realizar.        |
|Grupo     | Un nombre de grupo para agrupar lógicamente los archivos.        |
|Escriba la ruta de acceso     | La ruta de acceso para buscar el archivo. Por ejemplo: "/etc/*.conf"       |
|Tipo de ruta de acceso     | Tipo de elemento cuyo seguimiento se va a realizar; los valores posibles son Archivo y Directorio.        |
|Recursividad     | Determina si se usa recursividad al buscar el elemento cuyo seguimiento se va a realizar.        |
|Usar sudo     | Esta configuración determina si se va a utilizar sudo al buscar el elemento.         |
|Vínculos     | Esta configuración determina cómo se tratan los vínculos simbólicos cuando se recorren directorios.<br> **Omitir**: ignora los vínculos simbólicos y no incluye los archivos y directorios de referencia.<br>**Seguir**: sigue los vínculos simbólicos durante la recursión y también incluye los archivos y directorios de referencia.<br>**Administrar**: sigue los vínculos simbólicos y permite modificar el contenido devuelto.     |

> [!NOTE]
> La opción de administración de vínculos no se recomienda. No se admite la recuperación de contenido de los archivos.

### <a name="configure-windows-files-to-track"></a>Configuración de los archivos de Windows de los que se realizará un seguimiento

Use los pasos siguientes para configurar los archivos de los que se realizará un seguimiento en los equipos Windows:

1. En la cuenta de Automation, seleccione **Change Tracking** en **ADMINISTRACIÓN DE CONFIGURACIÓN**. Haga clic en **Editar configuración** (el símbolo de engranaje).
2. En la página **Change Tracking**, seleccione **Archivos de Windows** y, a continuación, haga clic en **+ Agregar** para agregar un nuevo archivo para realizar su seguimiento.
3. En **Agregar archivo de Windows a Change Tracking**, escriba la información del archivo cuyo seguimiento se va a realizar y haga clic en **Guardar**.

|Propiedad  |DESCRIPCIÓN  |
|---------|---------|
|habilitado     | Determina si se aplica la configuración.        |
|Nombre del elemento     | Nombre descriptivo del archivo cuyo seguimiento se va a realizar.        |
|Grupo     | Un nombre de grupo para agrupar lógicamente los archivos.        |
|Escriba la ruta de acceso     | La ruta de acceso para buscar el archivo, por ejemplo: "c:\temp\myfile.txt"       |

### <a name="configure-windows-registry-keys-to-track"></a>Configuración de las claves del Registro de Windows para realizar un seguimiento

Use los pasos siguientes para configurar las claves del registro para realizar un seguimiento en los equipos Windows:

1. En la cuenta de Automation, seleccione **Change Tracking** en **ADMINISTRACIÓN DE CONFIGURACIÓN**. Haga clic en **Editar configuración** (el símbolo de engranaje).
2. En la página **Change Tracking**, seleccione **Registro de Windows** y, a continuación, haga clic en **+ Agregar** para agregar una nueva clave del registro para realizar su seguimiento.
3. En **Agregar registro de Windows a Change Tracking**, escriba la información de la clave cuyo seguimiento se va a realizar y haga clic en **Guardar**.

|Propiedad  |DESCRIPCIÓN  |
|---------|---------|
|habilitado     | Determina si se aplica la configuración.        |
|Nombre del elemento     | Nombre descriptivo del archivo cuyo seguimiento se va a realizar.        |
|Grupo     | Un nombre de grupo para agrupar lógicamente los archivos.        |
|Clave del registro de Windows   | La ruta de acceso para buscar el archivo. Por ejemplo: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

## <a name="limitations"></a>Limitaciones

Actualmente, la solución Change Tracking no admite los elementos siguientes:

* Carpetas (directorios) para el seguimiento de archivos de Windows
* Recursión para el seguimiento de archivos de Windows
* Caracteres comodín para el seguimiento de archivos de Windows
* Variables de ruta de acceso
* Sistemas de archivos de red
* Contenido del archivo

Otras limitaciones:

* Los valores y la columna **Tamaño máximo de archivos** no se usan en la implementación actual.
* Si recopila más de 2500 archivos en el ciclo de recopilación de 30 minutos, el rendimiento podría degradarse.
* Cuando el tráfico de red es elevado, los registros de cambios pueden tardar hasta seis horas en aparecer.
* Si modifica la configuración mientras un equipo está apagado, el equipo podría publicar cambios que pertenecían a la configuración anterior.

## <a name="known-issues"></a>Problemas conocidos

Actualmente, la solución Change Tracking tiene los siguientes problemas:
* Actualizaciones de revisiones que no se recopilan en las máquinas con Windows 10 Creators Update y Windows Server 2016 Core RS3.

## <a name="change-tracking-data-collection-details"></a>Detalles de la recopilación de datos de seguimiento de cambios

En la tabla siguiente se muestra la frecuencia de recopilación de datos para los tipos de cambios. Para cada tipo, la instantánea del estado actual de los datos también se actualiza al menos cada 24 horas:

| **Tipo de cambio** | **Frecuencia** |
| --- | --- |
| Registro de Windows | 50 minutos | 
| Archivo de Windows | 30 minutos | 
| Archivo de Linux | 15 minutos | 
| Servicios de Windows | 30 minutos | 
| Demonios de Linux | 5 minutos |
| Software de Windows | 30 minutos | 
| Software Linux | 5 minutos | 

### <a name="registry-key-change-tracking"></a>Seguimiento de cambios en las claves del Registro

El objetivo de supervisar los cambios en las claves del Registro identificar los puntos de extensibilidad en los que se puede activar código de terceros y malware. En la lista siguiente se muestra la lista de claves del registro configuradas previamente. Estas claves están configuradas pero no habilitadas. Para realizar el seguimiento de estas claves del registro, debe habilitar cada una de ellas.

> [!div class="mx-tdBreakAll"]
> |  |
> |---------|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Supervisa las entradas de inicio automático comunes que se enlazan directamente con el explorador de Windows y se suelen ejecutar en el proceso Explorer.exe.    |
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Supervisa los scripts que se ejecutan en el inicio.     |
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown**    |
|&nbsp;&nbsp;&nbsp;&nbsp;Supervisa los scripts que se ejecutan al apagar el equipo.     |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Supervisa las claves que se cargan antes de que el usuario inicie sesión en su cuenta de Windows. La clave se usa para programas de 32 bits que se ejecutan en equipos de 64 bits.    |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Supervisa los cambios en la configuración de la aplicación.     |
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Supervisa las entradas de inicio automático comunes que se enlazan directamente con el explorador de Windows y se suelen ejecutar en el proceso Explorer.exe.|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Supervisa las entradas de inicio automático comunes que se enlazan directamente con el explorador de Windows y se suelen ejecutar en el proceso Explorer.exe.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Supervisa el registro del controlador de superposición de iconos.|
|**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Supervisa el registro del controlador de superposición de iconos para programas de 32 bits que se ejecutan en equipos de 64 bits.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects**|
|&nbsp;&nbsp;&nbsp;&nbsp;Supervisa si hay nuevos complementos de objeto auxiliar de explorador para Internet Explorer. Se utiliza para acceder a Document Object Model (DOM) de la página actual y controlar la navegación.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects**|
|&nbsp;&nbsp;&nbsp;&nbsp;Supervisa si hay nuevos complementos de objeto auxiliar de explorador para Internet Explorer. Sirve para acceder a Document Object Model de la página actual y controlar la navegación de programas de 32 bits que se ejecutan en equipos de 64 bits.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Supervisa si hay nuevas extensiones de Internet Explorer, como menús de la herramienta personalizada y botones de la barra de herramientas personalizada.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Supervisa si hay nuevas extensiones de Internet Explorer, como menús de la herramienta personalizada y botones de la barra de herramientas personalizada para programas de 32 bits que se ejecutan en equipos de 64 bits.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Supervisa los controladores de 32 bits asociados a wavemapper, wave1 y wave2, msacm.imaadpcm, .msadpcm, .msgsm610 y vidc. Es similar a la sección [drivers] del archivo SYSTEM.INI.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Supervisa los controladores de 32 bits asociados a wavemapper, wave1 y wave2, msacm.imaadpcm, .msadpcm, .msgsm610 y vidc para programas de 32 bits que se ejecutan en equipos de 64 bits. Es similar a la sección [drivers] del archivo SYSTEM.INI.|
> |**HKEY\_LOCAL\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls**|
|&nbsp;&nbsp;&nbsp;&nbsp;Supervisa la lista de DLL del sistema conocidas o utilizadas; este sistema impide que las personas se aprovechen de permisos de directorio de aplicación débiles colocando versiones de troyanos en DLL del sistema.|
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify**|
|&nbsp;&nbsp;&nbsp;&nbsp;Supervisa la lista de paquetes que pueden recibir notificaciones de eventos de Winlogon, el modelo de compatibilidad de inicio de sesión interactivo para el sistema operativo Windows.|

## <a name="use-change-tracking"></a>Uso de seguimiento de cambios

Después de habilitar la solución, puede ver el resumen de los cambios en los equipos supervisados seleccionando **Change Tracking** en **ADMINISTRACIÓN DE CONFIGURACIÓN** en la cuenta de Automation.

Puede ver los cambios en los equipos y, a continuación, profundizar en los detalles de cada evento. Hay disponibles listas desplegables en la parte superior del gráfico para limitar el gráfico y la información detallada en función del tipo de cambio y los intervalos de tiempo. También puede hacer clic y arrastrar en el gráfico para seleccionar un intervalo de tiempo personalizado.

![imagen del panel de seguimiento de cambios](./media/automation-change-tracking/change-tracking-dash01.png)

Al hacer clic en un cambio o un evento, se abrirá la información detallada sobre dicho cambio. Como puede ver en el ejemplo, el tipo de inicio del servicio cambió de Manual a Automático.

![imagen de los detalles del seguimiento de cambios](./media/automation-change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>Búsqueda de registros

Además de los detalles que se proporcionan en el portal, se puede buscar en los registros. Con la página **Change Tracking** abierta, haga clic en **Log Analytics** y se abrirá la página **Búsqueda de registros**.

### <a name="sample-queries"></a>Consultas de ejemplo

En la tabla siguiente se proporcionan ejemplos de búsquedas de registros para los registros de cambios recopilados por esta solución:

|Consultar  |DESCRIPCIÓN  |
|---------|---------|
|ConfigurationData<br>&#124; where   ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"<br>&#124; where SvcState == "Stopped"<br>&#124; summarize arg_max(TimeGenerated, *) by SoftwareName, Computer         | Muestra los registros de inventario más recientes para los servicios de Windows que se establecieron en Automático, pero se han notificado como Detenidos<br>Los resultados se limitan a la entrada más reciente para esos valores de SoftwareName y Computer      |
|ConfigurationChange<br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Removed"<br>&#124; order by TimeGenerated desc|Muestra los registros de cambios para el software eliminado|

## <a name="next-steps"></a>pasos siguientes

Consulte el tutorial de Change Tracking para más información acerca del uso de la solución:

> [!div class="nextstepaction"]
> [Solución de problemas de cambios en el entorno](automation-tutorial-troubleshoot-changes.md)

* Use [Búsquedas de registros en Log Analytics](../log-analytics/log-analytics-log-searches.md) para ver datos detallados sobre el seguimiento de cambios.

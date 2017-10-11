---
title: Seguimiento de cambios con Azure Log Analytics | Microsoft Docs
description: "La solución Seguimiento de cambios de Log Analytics ayuda a identificar los cambios en el software y el servicio de Windows que se producen en su entorno."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f8040d5d-3c89-4f0c-8520-751c00251cb7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 57af000e47188786a77cdb84ebb6ffb5c50eafaa
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="track-software-changes-in-your-environment-with-the-change-tracking-solution"></a>Seguimiento de cambios de software en su entorno con la solución de seguimiento de cambios

![Símbolo de Change Tacking](./media/log-analytics-change-tracking/change-tracking-symbol.png)

Este artículo le ayudará a usar la solución de seguimiento de cambios de Log Analytics para identificar fácilmente los cambios del entorno. La solución realiza un seguimiento de los cambios efectuados en el software de Windows y Linux, en los archivos y las claves del Registro de Windows, en los servicios de Windows y en los demonios de Linux. Identificar los cambios de configuración puede ayudarle a localizar problemas operativos.

La solución se instala para actualizar el tipo de agente que ha instalado. Se leen los cambios en el software instalado, los servicios de Windows y los demonios de Linux en los servidores de supervisión. A continuación, los datos se envían al servicio Log Analytics en la nube donde se procesan. Se aplica la lógica a los datos recibidos y el servicio de nube registra los datos. Con la información en el panel de seguimiento de cambios, puede ver fácilmente los cambios realizados en la infraestructura de servidores.

## <a name="installing-and-configuring-the-solution"></a>Instalación y configuración de la solución
Utilice la siguiente información para instalar y configurar la solución.

* Debe tener un agente [Windows](log-analytics-windows-agents.md), [Operations Manager](log-analytics-om-agents.md) o [Linux](log-analytics-linux-agents.md) en cada equipo en el que desee supervisar los cambios.
* Agregue la solución Change Tracking al área de trabajo de OMS desde [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ChangeTrackingOMS?tab=Overview). Como alternativa, agregue la solución con la información del artículo sobre la [incorporación de soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md). No es necesario realizar ninguna otra configuración.

### <a name="configure-linux-files-to-track"></a>Configuración de los archivos de Linux de los que se realizará un seguimiento
Use los pasos siguientes para configurar los archivos de los que se realizará un seguimiento en los equipos Linux.

1. En el portal de OMS, haga clic en **Configuración** (el símbolo de engranaje).
2. En la página **Configuración**, haga clic en **Datos** y, luego, en **Seguimiento de archivos de Linux**.
3. En Change Tracking de archivos de Linux, escriba toda la ruta de acceso, incluido el nombre del archivo del que desea realizar un seguimiento y, a continuación, haga clic en el símbolo **Agregar**. Por ejemplo: "/etc/*.conf"
4. Haga clic en **Guardar**.  

> [!NOTE]
> El seguimiento de archivos de Linux tiene funcionalidades adicionales, incluido el seguimiento de directorios, recursión de los directorios y seguimiento de comodines.

### <a name="configure-windows-files-to-track"></a>Configuración de los archivos de Windows de los que se realizará un seguimiento
Use los pasos siguientes para configurar los archivos de los que se realizará un seguimiento en los equipos Windows.

1. En el portal de OMS, haga clic en **Configuración** (el símbolo de engranaje).
2. En la página **Configuración**, haga clic en **Datos** y, a continuación, en **Seguimiento de archivos de Windows**.
3. En Seguimiento de cambios de archivos de Windows, escriba toda la ruta de acceso, incluido el nombre del archivo del que desea realizar un seguimiento y, a continuación, haga clic en el símbolo **Agregar**. Por ejemplo: C:\Archivos de programa (x86)\Internet Explorer\iexplore.exe o C:\Windows\System32\drivers\etc\hosts.
4. Haga clic en **Guardar**.  
   ![Seguimiento de cambios de archivos de Windows](./media/log-analytics-change-tracking/windows-file-change-tracking.png)

### <a name="configure-windows-registry-keys-to-track"></a>Configuración de las claves del Registro de Windows para realizar un seguimiento
Use los pasos siguientes para configurar las claves del Registro para realizar un seguimiento en los equipos Windows.

1. En el portal de OMS, haga clic en **Configuración** (el símbolo de engranaje).
2. En la página **Configuración**, haga clic en **Datos** y en **Seguimiento del Registro de Windows**.
3. En Seguimiento de cambios del Registro de Windows, escriba toda la clave de la que desea realizar un seguimiento y haga clic en el símbolo **Agregar**.
4. Haga clic en **Guardar**.  
   ![Seguimiento de cambios del Registro de Windows](./media/log-analytics-change-tracking/windows-registry-change-tracking.png)

### <a name="explanation-of-linux-file-collection-properties"></a>Explicación de las propiedades de la colección de archivos de Linux
1. **Tipo**
   * **File** (metadatos del archivo de informe: tamaño, fecha de modificación, hash, etc.)
   * **Directory** (metadatos del directorio de informe: tamaño, fecha de modificación, etc.)
2. **Links** (control de referencias de vínculo simbólico de Linux a otros archivos o directorios)
   * **Ignore** (omitir los vínculos simbólicos durante las recursiones para no incluir los archivos/directorios a los que se hace referencia)
   * **Follow** (seguir los vínculos simbólicos durante las recursiones para incluir también los archivos/directorios a los que se hace referencia)
   * **Manage** (seguir los vínculos simbólicos y modificar el tratamiento del contenido devuelto)

   > [!NOTE]   
   > La opción de administración de vínculos no se recomienda. No se admite la recuperación de contenido de los archivos.

3. **Recurse** (recorrer los niveles de carpeta y hacer seguimiento de todos los archivos que cumplen con la instrucción path)
4. **Sudo** (habilitar el acceso a los archivos o directorios que requieren el privilegio sudo)

### <a name="limitations"></a>Limitaciones
Actualmente, la solución Change Tracking no admite los elementos siguientes:

* Carpetas (directorios) para seguimiento de archivos de Windows
* Recursión para seguimiento de archivos de Windows
* Comodines para seguimiento de archivos de Windows
* Variables de ruta de acceso
* Sistemas de archivos de red
* Contenido del archivo

Otras limitaciones:

* Los valores y la columna **Tamaño máximo de archivos** no se usan en la implementación actual.
* Si recopila más de 2500 archivos en el ciclo de recopilación de 30 minutos, el rendimiento podría degradarse.
* Cuando el tráfico de red es elevado, los registros de cambios pueden tardar hasta seis horas en aparecer.
* Si modifica la configuración mientras un equipo está apagado, el equipo podría publicar cambios de archivos que pertenecían a la configuración anterior.

## <a name="change-tracking-data-collection-details"></a>Detalles de la recopilación de datos de seguimiento de cambios
El seguimiento de cambios recopila inventario de software y metadatos de servicio de Windows con los agentes habilitados.

La siguiente tabla muestra los métodos de recopilación de datos y otros detalles sobre cómo se recopilan los datos para el seguimiento de cambios.

| plataforma | Agente directo | Agente de Operations Manager | Agente Linux | Azure Storage | ¿Se requiere Operations Manager? | Se envían los datos del agente de Operations Manager a través del grupo de administración | Frecuencia de recopilación |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Windows y Linux | &#8226; | &#8226; | &#8226; |  |  | &#8226; | Entre 5 y 50 minutos, según el tipo de cambio. Para obtener más información, vea la tabla siguiente. |


En la tabla siguiente se muestra la frecuencia de recopilación de datos para los tipos de cambios.

| **Tipo de cambio** | **frequency** | **¿Envía el** **agente** **las diferencias cuando las encuentra?** |
| --- | --- | --- |
| Registro de Windows | 50 minutos | No |
| Archivo de Windows | 30 minutos | Sí. Si no hay ningún cambio en 24 horas, se envía una instantánea. |
| Archivo de Linux | 15 minutos | Sí. Si no hay ningún cambio en 24 horas, se envía una instantánea. |
| Servicios de Windows | 30 minutos | Sí, cada 30 minutos cuando se detectan cambios. Cada 24 horas se envía una instantánea con independencia de que haya cambio o no. Por lo tanto, se envía la instantánea incluso cuando no hay ningún cambio. |
| Demonios de Linux | 5 minutos | Sí. Si no hay ningún cambio en 24 horas, se envía una instantánea. |
| Software de Windows | 30 minutos | Sí, cada 30 minutos cuando se detectan cambios. Cada 24 horas se envía una instantánea con independencia de que haya cambio o no. Por lo tanto, se envía la instantánea incluso cuando no hay ningún cambio. |
| Software Linux | 5 minutos | Sí. Si no hay ningún cambio en 24 horas, se envía una instantánea. |

### <a name="registry-key-change-tracking"></a>Seguimiento de cambios en las claves del Registro

Log Analytics realiza la supervisión y el seguimiento del Registro de Windows con la solución Seguimiento de cambios. El objetivo de supervisar los cambios en las claves del Registro identificar los puntos de extensibilidad en los que se puede activar código de terceros y malware. En la lista siguiente se muestran las claves del Registro predeterminada de las que la solución realiza un seguimiento y por qué se hace.

- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup
    - Supervisa los scripts que se ejecutan al arrancar.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown
    - Supervisa los scripts que se ejecutan al apagar el equipo.
- HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run
    - Supervisa las claves que se cargan antes de que el usuario inicie sesión en su cuenta de Windows. La clave se usa para programas de 32 bits que se ejecutan en equipos de 64 bits.
- HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components
    - Supervisa los cambios en la configuración de la aplicación.
- HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers
    - Supervisa las entradas de inicio automático comunes que se enlazan directamente en el Explorador de Windows y suele ejecutar In-Process con Explorer.exe.
- HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers
    - Supervisa las entradas de inicio automático comunes que se enlazan directamente en el Explorador de Windows y suele ejecutar In-Process con Explorer.exe.
- HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Background\ShellEx\ContextMenuHandlers
    - Supervisa las entradas de inicio automático comunes que se enlazan directamente en el Explorador de Windows y suele ejecutar In-Process con Explorer.exe.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers
    - Supervisa el registro del controlador de superposición de iconos.
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers
    - Supervisa el registro del controlador de superposición de iconos para programas de 32 bits que se ejecutan en equipos de 64 bits.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects
    - Supervisa si hay nuevos complementos de objeto auxiliar de explorador para Internet Explorer. Se utiliza para acceder a Document Object Model (DOM) de la página actual y controlar la navegación.
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects
    - Supervisa si hay nuevos complementos de objeto auxiliar de explorador para Internet Explorer. Sirve para acceder a Document Object Model de la página actual y controlar la navegación de programas de 32 bits que se ejecutan en equipos de 64 bits.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Internet Explorer\Extensions
    - Supervisa si hay nuevas extensiones de Internet Explorer, como menús de la herramienta personalizada y botones de la barra de herramientas personalizada.
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions
    - Supervisa si hay nuevas extensiones de Internet Explorer, como menús de la herramienta personalizada y botones de la barra de herramientas personalizada para programas de 32 bits que se ejecutan en equipos de 64 bits.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32
    - Supervisa los controladores de 32 bits asociados a wavemapper, wave1 y wave2, msacm.imaadpcm, .msadpcm, .msgsm610 y vidc. Es similar a la sección [drivers] del archivo SYSTEM.INI.
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32
    - Supervisa los controladores de 32 bits asociados a wavemapper, wave1 y wave2, msacm.imaadpcm, .msadpcm, .msgsm610 y vidc para programas de 32 bits que se ejecutan en equipos de 64 bits. Es similar a la sección [drivers] del archivo SYSTEM.INI.
- HKEY\_LOCAL\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls
    - Supervisa la lista de DLL del sistema conocidas o utilizadas; este sistema impide que las personas se aprovechen de permisos de directorio de aplicación débiles colocando versiones de troyanos en DLL del sistema.
- HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify
    - Supervisa la lista de paquetes que pueden recibir notificaciones de eventos de Winlogon, el modelo de compatibilidad de inicio de sesión interactivo para el sistema operativo Windows.


## <a name="use-change-tracking"></a>Uso de seguimiento de cambios
Una vez instalada la solución, puede ver el resumen de los cambios para los servidores supervisados mediante el icono **Seguimiento de cambios** en la página **Información general** de OMS.

![imagen del icono de seguimiento de cambios](./media/log-analytics-change-tracking/change-tracking-tile.png)

Puede ver los cambios en la infraestructura y, a continuación, profundizar en los detalles de las siguientes categorías:

* Cambios realizados por el tipo de configuración para los servicios de Windows y el software
* Cambios de software en aplicaciones y actualizaciones para los servidores individuales
* Número total de cambios de software para cada aplicación
* Paquetes Linux
* Cambios de servicio de Windows para servidores individuales
* Cambios de demonios de Linux

![imagen del panel de seguimiento de cambios](./media/log-analytics-change-tracking/change-tracking-dash01.png)

![imagen del panel de seguimiento de cambios](./media/log-analytics-change-tracking/change-tracking-dash02.png)

### <a name="to-view-changes-for-any-change-type"></a>Para ver los cambios de algún tipo de cambio
1. En la página **Información general**, haga clic en el icono **Seguimiento de cambios**.
2. En el panel **Seguimiento de cambios**, revise la información de resumen en una de las hojas de tipo de cambio y, a continuación, haga clic en una para obtener información detallada acerca de la misma en la página de **búsqueda de registros**.
3. En cualquiera de las páginas de búsqueda de registros, puede ver los resultados por tiempo, resultados detallados y el historial de búsqueda de registros. También puede filtrar por las facetas para restringir los resultados.

## <a name="next-steps"></a>Pasos siguientes
* Use [Búsquedas de registros en Log Analytics](log-analytics-log-searches.md) para ver datos detallados sobre el seguimiento de cambios.

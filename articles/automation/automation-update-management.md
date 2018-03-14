---
title: "Solución Update Management en Azure | Microsoft Docs"
description: "Este artículo está pensado para ayudarle a entender cómo utilizar esta solución para administrar las actualizaciones de los equipos Windows y Linux."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: 
ms.assetid: e33ce6f9-d9b0-4a03-b94e-8ddedcc595d2
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: gwallace
ms.openlocfilehash: 9280925cdd5cccf8d1d2f2b33a7de8523a07cd14
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2018
---
# <a name="update-management-solution-in-azure"></a>Solución Update Management de Azure

La solución Update Management de Azure Automation permite administrar las actualizaciones de seguridad del sistema operativo de los equipos Windows y Linux implementados en Azure, en entornos locales o en otros proveedores de servicios en la nube. Puede evaluar rápidamente el estado de las actualizaciones disponibles en todos los equipos agente y administrar el proceso de instalación de las actualizaciones necesarias para los servidores.

La administración de actualizaciones se puede habilitar en las máquinas virtuales directamente desde una cuenta de [Azure Automation](automation-offering-get-started.md).
Para aprender a habilitar la administración de actualizaciones en máquinas virtuales desde una cuenta de Automation, consulte [Administración de actualizaciones para varias máquinas virtuales de Azure](manage-update-multi.md).

## <a name="solution-overview"></a>Información general de la solución

Los equipos administrados por Update Management usan las siguientes configuraciones para evaluar e implementar actualizaciones:

* Microsoft Monitoring Agent para Windows o Linux
* Extensión DSC (configuración de estado deseado) de PowerShell para Linux
* Hybrid Runbook Worker de Automation
* Microsoft Update o Windows Server Update Services para equipos Windows

El siguiente diagrama muestra una vista conceptual del comportamiento y un flujo de datos con el modo en que la solución evalúa y aplica las actualizaciones de seguridad a todos los equipos Windows Server y Linux conectados en un área de trabajo.    

![Flujo del proceso de administración de actualizaciones](media/automation-update-management/update-mgmt-updateworkflow.png)

Después de que un equipo realiza un examen de cumplimiento de las actualizaciones, el agente reenvía la información de forma masiva a Log Analytics. En un equipo Windows, el examen de cumplimiento se realiza cada 12 horas de forma predeterminada. Además del examen programado, el examen de cumplimiento de las actualizaciones se inicia a los 15 minutos si se reinicia Microsoft Monitoring Agent (MMA), antes y después de la instalación de actualizaciones. En un equipo Linux, el examen de cumplimiento se realiza cada 3 horas de forma predeterminada y se inicia un examen de cumplimiento en 15 minutos si se reinicia el agente MMA.

La solución informa del grado de actualización del equipo en función del origen configurado para la sincronización. Si el equipo Windows está configurado para informar a WSUS, dependiendo de cuándo WSUS sincronizó por última vez con Microsoft Update, los resultados pueden diferir de lo que se muestra en Microsoft Updates. Lo mismo ocurre en los equipos Linux que se configuran para informar a un repositorio local en lugar de a uno público.

Puede implementar e instalar las actualizaciones de software en equipos que requieren las actualizaciones mediante la creación de una implementación programada. Las actualizaciones clasificadas como *Opcional* no se incluyen en el ámbito de implementación para equipos Windows, solo las actualizaciones necesarias. La implementación programada define qué equipos de destino reciben las actualizaciones aplicables, ya sea explícitamente especificando equipos o seleccionando un [grupo de equipos](../log-analytics/log-analytics-computer-groups.md) que se basa en las búsquedas en registros de un conjunto determinado de equipos. También se especifica una programación para aprobar y designar un período de tiempo dentro del cual se autoriza la instalación de las actualizaciones. Los Runbooks instalan las actualizaciones en Azure Automation. No puede ver estos runbooks, que no requieren ninguna configuración. Cuando se crea una implementación de actualizaciones, esta crea una programación que inicia un runbook de actualización maestro a la hora especificada para los equipos incluidos. Este runbook maestro inicia un runbook secundario en cada agente que realiza la instalación de las actualizaciones necesarias.

En la fecha y hora especificadas en la implementación de actualizaciones, los equipos de destino ejecutan la implementación en paralelo. En primer lugar, se realiza un examen para comprobar si las actualizaciones siguen siendo necesarias y las instala. Para los equipos cliente WSUS, si no se aprueban las actualizaciones en WSUS, se produce un error en la implementación de actualizaciones.

## <a name="clients"></a>Clientes

### <a name="supported-client-types"></a>Tipos de cliente admitidos

En la tabla siguiente se muestra una lista de sistemas operativos compatibles: 

|Sistema operativo  |Notas  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Solo admite las evaluaciones de actualización         |
|Windows Server 2008 R2 SP1 y versiones posteriores     |Se necesitan .NET Framework 4.5 y WMF 5.0 o posterior para Windows Server 2008 R2 SP1        |
|CentOS 6 (x86/x64) y 7 (x64)      | Los agentes de Linux deben tener acceso a un repositorio de actualización.        |
|Red Hat Enterprise (x86/x64) 6 y 7 (x64)     | Los agentes de Linux deben tener acceso a un repositorio de actualización.        |
|SUSE Linux Enterprise Server 11 (x86/x64) y 12 (x64)     | Los agentes de Linux deben tener acceso a un repositorio de actualización.        |
|Ubuntu 12.04 LTS y versiones más recientes (x86/x64)       |Los agentes de Linux deben tener acceso a un repositorio de actualización.         |

### <a name="unsupported-client-types"></a>Tipos de cliente no admitidos

En la tabla siguiente se enumeran los sistemas operativos no admitidos:

|Sistema operativo  |Notas  |
|---------|---------|
|Cliente Windows     | No se admiten los sistemas operativos cliente (Windows 7, Windows 10, etc.).        |
|Windows Server 2016 Nano Server     | No compatible       |

### <a name="client-requirements"></a>Requisitos del cliente

#### <a name="windows"></a>Windows

Los agentes de Windows deben estar configurados para comunicarse con un servidor de Windows Server Update Services (WSUS) o tener acceso a Microsoft Update. Además, System Center Configuration Manager no puede administrar simultáneamente el agente de Windows. El [agente de Windows](../log-analytics/log-analytics-agent-windows.md) es necesario. Este agente se instala automáticamente si está incorporando una máquina virtual de Azure.

#### <a name="linux"></a>Linux

Para Linux, el equipo debe tener acceso a un repositorio de actualizaciones, que puede ser público o privado. La solución no admite el Agente de Operations Management Suite para Linux configurado para informar a varias áreas de trabajo de Log Analytics.

Para más información acerca de cómo instalar el agente de OMS para Linux y descargar la versión más reciente, consulte [Agente de Operations Management Suite para Linux](https://github.com/microsoft/oms-agent-for-linux). Para más información sobre cómo instalar el agente de OMS para Windows, consulte [Agente de Operations Management Suite para Windows](../log-analytics/log-analytics-windows-agent.md).  

## <a name="permissions"></a>Permisos
Para crear y administrar implementaciones de actualizaciones, necesita permisos concretos. Para más información acerca de estos permisos, consulte [Acceso basado en rol: administración de actualizaciones](automation-role-based-access-control.md#update-management) 

## <a name="solution-components"></a>Componentes de soluciones
Esta solución consta de los siguientes recursos que se agregan a la cuenta de Automation y a los agentes directamente conectados o al grupo de administración conectado a Operations Manager.

### <a name="hybrid-worker-groups"></a>Grupos de Hybrid Worker
Si habilita esta solución, los equipos Windows conectados directamente al área de trabajo de Log Analytics se configurarán automáticamente como Hybrid Runbook Worker para admitir los runbooks que se incluyen en esta solución. Cada equipo Windows administrado por la solución se enumera en la página de grupos de trabajo híbridos como un grupo de trabajo híbrido del sistema de la cuenta de Automation, siguiendo la convención de nomenclatura *Nombre de host FQDN_GUID*. Estos grupos no pueden ser grupos de destino de runbooks de su cuenta, ya que se produce un error. Estos grupos están diseñados únicamente para admitir la solución de administración.

Sin embargo, puede agregar equipos Windows a un grupo de Hybrid Runbook Worker en la cuenta de Automation para admitir runbooks de Automation siempre que la cuenta para la solución y la pertenencia a grupos de Hybrid Runbook Worker sean las mismas. Esta funcionalidad se agregó a la versión 7.2.12024.0 de Hybrid Runbook Worker.

### <a name="management-packs"></a>Módulos de administración

Si el grupo de administración de System Center Operations Manager está conectado a un área de trabajo de Log Analytics, se instalarán los siguientes módulos de administración en Operations Manager. Estos módulos de administración también se instalan en equipos Windows directamente conectados después de agregar esta solución. No hay nada para configurar o administrar en estos módulos de administración.

* Intelligence Pack Update Assessment de Microsoft System Center Advisor (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Módulo de administración de Update Deployment

Para obtener más información sobre cómo se actualizan los módulos de administración de soluciones, consulte [Conexión de Operations Manager con Log Analytics](../log-analytics/log-analytics-om-agents.md).

### <a name="confirm-non-azure-machines-are-onboarded"></a>Confirmación de que las máquinas que no son de Azure están incorporadas

Para confirmar que las máquinas conectadas directamente se comunican con Log Analytics, después de unos minutos, puede ejecutar la siguiente búsqueda de registros:

#### <a name="linux"></a>Linux

```
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table`
```

En un equipo Windows, revise lo siguiente para comprobar la conectividad del agente con Log Analytics:

1.  Abra Microsoft Monitoring Agent en el panel de control y, en la pestaña **Azure Log Analytics (OMS)**, el agente muestra un mensaje que indica: **Microsoft Monitoring Agent se conectó correctamente al servicio Microsoft Operations Management Suite**.   
2.  Abra el registro de eventos de Windows, vaya a **Registro de aplicaciones y servicios\Operations Manager** y busque los eventos con el identificador 3000 y 5002 del conector de servicio de origen. Estos eventos indican que el equipo se ha registrado en el área de trabajo de Log Analytics y está recibiendo la configuración.  

Si el agente no es capaz de comunicarse con Log Analytics y está configurado para comunicarse con Internet a través de un servidor proxy o firewall, confirme que el firewall o el servidor proxy están configurados correctamente. Para ello, consulte [Configuración de red para el agente de Windows](../log-analytics/log-analytics-agent-windows.md) o [Configuración de red para el agente de Linux](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Si los sistemas Linux están configurados para comunicarse con un servidor proxy o puerta de enlace de OMS y está incorporando esta solución, actualice los permisos de *proxy.conf* para conceder al grupo omiuser permiso de lectura sobre este archivo realizando los siguientes comandos:  
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`  
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Los agentes de Linux recién agregados mostrarán el estado **Actualizado** después de haber realizado una evaluación. Este proceso puede tardar hasta 6 horas.

Para confirmar que un grupo de administración de Operations Manager se comunica con Log Analytics, consulte [Validación de la integración de Operations Manager con OMS](../log-analytics/log-analytics-om-agents.md#validate-operations-manager-integration-with-oms).

## <a name="data-collection"></a>Colección de datos

### <a name="supported-agents"></a>Agentes admitidos
En la tabla siguiente se describen los orígenes conectados que son compatibles con esta solución.

| Origen conectado | Compatible | DESCRIPCIÓN |
| --- | --- | --- |
| Agentes de Windows |Sí |La solución recopila información acerca de las actualizaciones del sistema de los agentes de Windows e inicia la instalación de las actualizaciones necesarias. |
| Agentes de Linux |Sí |La solución recopila información acerca de las actualizaciones del sistema de los agentes de Linux e inicia la instalación de las actualizaciones necesarias en las distribuciones admitidas. |
| Grupo de administración de Operations Manager |Sí |La solución recopila información acerca de las actualizaciones del sistema de agentes en un grupo de administración conectado.<br>No se requiere ninguna conexión directa entre el agente de Operations Manager y Log Analytics. Los datos se reenvían desde el grupo de administración al área de trabajo de Log Analytics. |

### <a name="collection-frequency"></a>Frecuencia de recopilación
Para cada equipo Windows administrado, se realiza un examen dos veces al día. Cada 15 minutos, se llama a la API de Windows para consultar la hora de la última actualización y determinar si ha cambiado el estado y, de ser así, se inicia un examen de cumplimiento. Para cada equipo Linux administrado, se realiza un examen cada tres horas.

Puede tardar entre 30 minutos y 6 horas mostrar en el panel los datos actualizados de los equipos administrados.   

## <a name="viewing-update-assessments"></a>Visualización de evaluaciones de la actualización
Haga clic en **Update Management** en su cuenta de Automation para ver el estado de las máquinas.

Esta vista proporciona información de los equipos, las actualizaciones que faltan, las implementaciones de actualizaciones y las implementaciones de actualizaciones programadas.

Puede ejecutar una búsqueda de registros que devuelve información de la máquina, actualización o implementación seleccionando el elemento en la lista. Se abre la página **Búsqueda de registros** con una consulta para el elemento seleccionado.

## <a name="installing-updates"></a>Instalación de actualizaciones

Una vez se han evaluado las actualizaciones para todos los equipos Linux y Windows de su área de trabajo, puede instalar las actualizaciones necesarias mediante la creación de una *implementación de actualizaciones*. Una implementación de actualizaciones es una instalación programada de las actualizaciones necesarias en uno o más equipos. Especifique la fecha y hora para la implementación, además de un equipo o grupo de equipos que deben incluirse en el ámbito de una implementación. Para más información sobre grupos de equipos, consulte [Grupos de equipos de Log Analytics](../log-analytics/log-analytics-computer-groups.md). Al incluir grupos de equipos en la implementación de actualizaciones, la pertenencia al grupo se evalúa solo en el momento de la creación de la programación. No se reflejan los cambios posteriores en un grupo. Para resolver este problema, elimine la implementación de actualización programada y vuelva a crearla.

> [!NOTE]
> Las máquinas virtuales Windows implementadas desde Azure Marketplace se establecen de forma predeterminada para recibir actualizaciones automáticas del servicio de Windows Update. Este comportamiento no cambia después de agregar esta solución o máquinas virtuales Windows al área de trabajo. Si no administra activamente las actualizaciones con la solución, se aplica el comportamiento predeterminado (aplicar automáticamente las actualizaciones).

Para evitar que las actualizaciones se apliquen fuera de una ventana de mantenimiento en Ubuntu, vuelva a configurar el paquete de actualizaciones desatendidas para deshabilitar las actualizaciones automáticas. Para más información sobre cómo configurarlo, consulte el [tema sobre actualizaciones automáticas en la Guía de Ubuntu Server](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

En el caso de las máquinas virtuales creadas a partir de las imágenes a petición de Red Hat Enterprise Linux (RHEL) en Azure Marketplace se registran para acceder a la instancia de [Red Hat Update Infrastructure (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) implementada en Azure. Cualquier otra distribución de Linux se debe actualizar desde el repositorio de archivos en línea de distribuciones según los métodos admitidos de cada una de ellas.  

## <a name="viewing-missing-updates"></a>Visualización de las actualizaciones que faltan

Haga clic en **Actualizaciones que faltan** para ver la lista de actualizaciones que faltan en las máquinas. Cada actualización aparece y muestra información relacionada con el número de equipos que requieren la actualización, el sistema operativo y el vínculo para más información. Se puede seleccionar cada actualización y la página **Búsqueda de registros** aparece y muestra más detalles acerca de las actualizaciones.

## <a name="viewing-update-deployments"></a>Visualización de implementaciones de actualizaciones

Haga clic en **Implementaciones de actualizaciones** para ver la lista de implementaciones de actualizaciones existentes. Al hacer clic en cualquiera de las implementaciones de actualizaciones de la lista, se abre la página **Ejecución de implementación de actualizaciones** de esa implementación de actualizaciones.

![Información general de los resultados de la implementación de actualizaciones](./media/automation-update-management/update-deployment-run.png)

## <a name="creating-an-update-deployment"></a>Creación de una implementación de actualizaciones

Para crear una implementación de actualizaciones, haga clic en el botón **Implementación de actualizaciones programada**, situado en la parte superior de la pantalla para abrir la página **Nueva implementación de actualizaciones**. Debe proporcionar valores para las propiedades de la tabla siguiente:

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| NOMBRE |Nombre único para identificar la implementación de actualizaciones. |
|Sistema operativo| Linux o Windows|
| Máquinas para actualizar |Seleccione una búsqueda guardada o elija la máquina en la lista desplegable y seleccione equipos individuales |
|Clasificación de actualizaciones|Seleccione todas las clasificaciones de actualizaciones que necesite|
|Actualizaciones para excluir|Escriba todos los KB para excluir sin el prefijo "KB"|
|Configuración de programación|Seleccione la hora de inicio y seleccione Una vez o de manera periódica para la periodicidad|
| Ventana de mantenimiento |Número de minutos establecido para las actualizaciones. El valor no puede ser inferior a 30 minutos ni más de 6 horas |

## <a name="search-logs"></a>Búsqueda de registros

Además de los detalles que se proporcionan en el portal, se puede buscar en los registros. Con la página **Change Tracking** abierta, haga clic en **Log Analytics** y se abrirá la página **Búsqueda de registros**

### <a name="sample-queries"></a>Consultas de ejemplo

En la tabla siguiente se proporcionan ejemplos de búsquedas de registros para los registros de actualizaciones recopilados por esta solución:

| Consultar | DESCRIPCIÓN |
| --- | --- |
|Actualizar<br>&#124; where UpdateState == "Needed" and Optional == false<br>&#124; project Computer, Title, KBID, Classification, PublishedDate |Todos los equipos con actualizaciones pendientes<br>Agregue uno de los siguientes para limitar el sistema operativo:<br>OSType = "Windows"<br>OSType == "Linux" |
| Actualizar<br>&#124; where UpdateState == "Needed" and Optional == false<br>&#124; where Computer == "ContosoVM1.contoso.com"<br>&#124; project Computer, Title, KBID, Product, PublishedDate |Actualizaciones pendientes para un equipo específico (reemplace el valor por el nombre del equipo)|
| Evento<br>&#124; where EventLevelName == "error" and Computer in ((Update &#124; where (Classification == "Security Updates" or Classification == "Critical Updates")<br>&#124; where UpdateState == "Needed" and Optional == false <br>&#124; distinct Computer)) |Eventos de error para las máquinas que tienen pendientes actualizaciones de seguridad o críticas necesarias |
| Actualizar<br>&#124; where UpdateState == "Needed" and Optional == false<br>&#124; distinct Title |Actualizaciones distintivas pendientes en todos los equipos | 
| UpdateRunProgress<br>&#124; where InstallationStatus == "failed" <br>&#124; summarize AggregatedValue = count() by Computer, Title, UpdateRunName |Equipos con actualizaciones con error en la ejecución de la actualización<br>Agregue uno de los siguientes para limitar el sistema operativo:<br>OSType = "Windows"<br>OSType == "Linux" | 
| Actualizar<br>&#124; where OSType == "Linux"<br>&#124; where UpdateState != "Not needed" and (Classification == "Critical Updates" or Classification == "Security Updates")<br>&#124; summarize AggregatedValue = count() by Computer |Lista de todas las máquinas Linux que tienen un paquete de actualización disponible que soluciona puntos vulnerables de seguridad o críticos | 
| UpdateRunProgress<br>&#124; where UpdateRunName == "DeploymentName"<br>&#124; summarize AggregatedValue = count() by Computer|Equipos que se actualizaron en esta ejecución de la actualización (reemplace el valor por el nombre de la implementación de actualizaciones) | 

## <a name="integrate-with-system-center-configuration-manager"></a>Integración con System Center Configuration Manager

Los clientes que han invertido en System Center Configuration Manager para administrar equipos, servidores y dispositivos móviles también dependen de su solidez y nivel de madurez para administrar actualizaciones de software como parte de su ciclo de Administración de actualizaciones de software (SUM).

Para aprender a integrar la solución Update Management de OMS con System Center Configuration Manager, consulte [Integrar System Center Configuration Manager con Update Management de OMS [versión preliminar]](oms-solution-updatemgmt-sccmintegration.md).

## <a name="patching-linux-machines"></a>Aplicación de revisiones de máquinas Linux

Las siguientes secciones explican posibles problemas con la aplicación de revisiones de Linux.

### <a name="package-exclusion"></a>Exclusión de paquetes

En algunas variantes de Linux, como Red Hat Enterprise Linux, las actualizaciones de nivel de sistema operativo pueden producirse a través de paquetes. Esto puede llevar a ejecuciones de Update Management en las que cambia el número de versión del sistema operativo. Puesto que Update Management utiliza los mismos métodos para actualizar paquetes que un administrador utilizaría localmente en el equipo Linux, este comportamiento es deliberado.

Para evitar la actualización de la versión del sistema operativo con las ejecuciones de Update Management, use la característica **Exclusión**.

En Red Hat Enterprise Linux, el nombre del paquete para excluir sería: redhat-release-server.x86_64

![Paquetes que se van a excluir para Linux](./media/automation-update-management/linuxpatches.png)

### <a name="security-patches-not-being-applied"></a>No se aplican las revisiones de seguridad

Al implementar actualizaciones en un equipo Linux, puede seleccionar las clasificaciones de actualizaciones. Esto filtra las actualizaciones que se aplican a aquellas que cumplen los criterios especificados. Este filtro se aplica localmente en el equipo cuando se implementa la actualización. Dado que Update Management realiza enriquecimiento de actualizaciones en la nube, algunas actualizaciones pueden marcarse en Update Management como de impacto para la seguridad, aunque el equipo local no tenga esa información. Como resultado, si aplica actualizaciones críticas para un equipo Linux, puede haber actualizaciones que no están marcadas como de impacto para la seguridad en ese equipo y no se aplican. Sin embargo, es posible que Update Management todavía notifique que esa máquina no es compatible porque tiene información adicional acerca de la actualización pertinente.

La implementación de actualizaciones por clasificación de actualizaciones podría no funcionar en openSUSE Linux debido a que utiliza un modelo de aplicación de revisiones diferente.

## <a name="troubleshooting"></a>solución de problemas

En esta sección se proporciona información para resolver problemas con la solución de administración de actualizaciones.

Si se producen problemas al intentar incorporar la solución o una máquina virtual, compruebe en los registros de eventos **Registros de aplicaciones y servicios y Operations Manager** los eventos con el identificador 4502 y el mensaje de evento que contenga **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**. La tabla siguiente destaca los mensajes de error específicos y una posible solución para cada uno.  

| Message | Motivo | Solución |   
|----------|----------|----------|  
| No se pudo registrar la máquina para la administración de revisiones,<br>error en el registro con la excepción<br>System.InvalidOperationException: {"Message":"La máquina ya<br>está registrada en una cuenta diferente. "} | La máquina ya está incorporada a otra área de trabajo para Update Management | Realice una limpieza de los artefactos antiguos [eliminando el grupo de hybrid runbook](automation-hybrid-runbook-worker.md#remove-hybrid-worker-groups)|  
| No se pudo registrar la máquina para la administración de revisiones,<br>error en el registro con la excepción<br>System.Net.Http.HttpRequestException: error al enviar la solicitud. ---><br>System.Net.WebException: la conexión subyacente<br>se cerró: error inesperado<br>en una operación de recepción. ---> System.ComponentModel.Win32Exception:<br>el cliente y el servidor no pueden comunicarse,<br>dado que no poseen un algoritmo común | El proxy, la puerta de enlace o el firewall están bloqueando la comunicación | [Revise los requisitos de red](automation-offering-get-started.md#network-planning)|  
| No se pudo registrar la máquina para la administración de revisiones,<br>error en el registro con la excepción<br>Newtonsoft.Json.JsonReaderException: error al analizar el valor de infinito positivo. | El proxy, la puerta de enlace o el firewall están bloqueando la comunicación | [Revise los requisitos de red](automation-offering-get-started.md#network-planning)| 
| El certificado presentado por el servicio <wsid>.oms.opinsights.azure.com<br>no fue emitido por una entidad de certificación<br>utilizada para los servicios de Microsoft. Contacto<br>el administrador de red para comprobar si están ejecutando un proxy que intercepte<br>la comunicación TLS/SSL. |El proxy, la puerta de enlace o el firewall están bloqueando la comunicación | [Revise los requisitos de red](automation-offering-get-started.md#network-planning)|  
| No se pudo registrar la máquina para la administración de revisiones,<br>error en el registro con la excepción<br>AgentService.HybridRegistration.<br>PowerShell.Certificates.CertificateCreationException:<br>error al crear un certificado autofirmado. ---><br>System.UnauthorizedAccessException: se denegó el acceso. | Error al generar un certificado autofirmado | Compruebe que la cuenta del sistema tiene<br>acceso de lectura a la carpeta:<br>**C:\ProgramData\Microsoft\**<br>**Crypto\RSA**|  

## <a name="next-steps"></a>Pasos siguientes

Continúe con el tutorial para aprender a administrar actualizaciones de máquinas virtuales Windows.

> [!div class="nextstepaction"]
> [Administración de actualizaciones y revisiones para las máquinas virtuales Windows de Azure](automation-tutorial-troubleshoot-changes.md)

* Usar Búsquedas de registros en [Log Analytics](../log-analytics/log-analytics-log-searches.md) para ver datos detallados sobre la actualización.
* [Crear alertas](../log-analytics/log-analytics-alerts.md) cuando se detectan actualizaciones críticas pendientes en equipos, o bien cuando un equipo tiene las actualizaciones automáticas deshabilitadas.

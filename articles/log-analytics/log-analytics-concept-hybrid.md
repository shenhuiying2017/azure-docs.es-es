---
title: "Recopilación de datos del entorno con Azure Log Analytics | Microsoft Docs"
description: "En este tema se le ayudará a comprender cómo recopilar datos y supervisar equipos hospedados localmente o en entornos de nube con Log Analytics."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: magoedte
ms.openlocfilehash: 513855084c8b89d97b049f1df2ec24d0f9789afe
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/13/2017
---
# <a name="collect-data-from-computers-in-your-environment-with-log-analytics"></a>Recopilar datos de equipos en su entorno con Log Analytics

Log Analytics permite recopilar y actuar sobre datos de equipos Windows o Linux que residen en:

* [Máquinas virtuales de Azure](log-analytics-quick-collect-azurevm.md) con la extensión de VM de Log Analytics 
* Centros de datos, como servidores físicos o máquinas virtuales
* Máquinas virtuales en un servicio hospedado en la nube, como Amazon Web Services (AWS)

Los equipos hospedados en su entorno pueden estar conectados directamente a Log Analytics, o si ya está supervisando estos equipos con System Center Operations Manager 2012 R2 o 2016, puede integrar el grupo de administración de Operations Manager con Log Analytics para mantener sus estrategias y procesos de las operaciones de servicio.  

## <a name="overview"></a>Información general

![log-analytics-agent-direct-connect-diagram](media/log-analytics-concept-hybrid/log-analytics-on-prem-comms.png)

Antes de analizar datos recopilados y actuar sobre ellos, deberá instalar y conectar agentes en todos los equipos que quiera que puedan enviar datos al servicio Log Analytics. Puede instalar los agentes en los equipos locales a través del programa de instalación, a través de la línea de comandos o utilizando Configuración de estado deseado (DSC) en Azure Automation. 

El agente para Linux y Windows se comunica con el servicio Log Analytics en el puerto TCP 443 y, si el equipo se conecta a un servidor proxy o firewall para comunicarse a través de Internet, consulte [Configuración del agente para su uso con un servidor proxy o con OMS Gateway](#configuring-the-agent-for-use-with-a-proxy-server-or-oms-gateway) para comprender qué cambios de configuración deben aplicarse. Si va a supervisar el equipo con System Center 2016 - Operations Manager u Operations Manager 2012 R2, puede tener hosts múltiples con el servicio Log Analytics para recopilar datos y reenviarlos al servicio, y ser supervisados por [Operations Manager](log-analytics-om-agents.md). Los equipos de Linux supervisados por un grupo de administración de Operations Manager que se integra con Log Analytics no reciben la configuración de los orígenes de datos y reenvían los datos recopilados a través del grupo de administración. El agente de Windows puede informar acerca de hasta cuatro áreas de trabajo, mientras que el agente de Linux solo puede informar acerca de una sola área de trabajo.  

El agente para Windows y Linux no es solo para conectarse con Log Analytics, sino que también admite la conexión con Azure Automation para hospedar la función Hybrid Runbook Worker y soluciones de administración como Change Tracking y Update Management.  Para obtener más información acerca de la función Hybrid Runbook Worker, consulte [Hybrid Runbook Worker de Azure Automation](../automation/automation-offering-get-started.md#automation-architecture-overview).  

Si las directivas de seguridad de TI no permiten que los equipos de la red se conecten a Internet, el agente se puede configurar para que se conecte a OMS Gateway a fin de recibir información de configuración y enviar los datos recopilados según la solución que haya habilitado. Para obtener más información y pasos acerca de cómo configurar el agente de Linux o Windows para que se comuniquen por una puerta de enlace de OMS con el servicio Log Analytics, consulte [Conexión de equipos sin acceso a OMS mediante la puerta de enlace de OMS](log-analytics-oms-gateway.md). 

## <a name="prerequisites"></a>Requisitos previos
Antes de comenzar, revise los detalles siguientes para comprobar que cumple los requisitos mínimos del sistema.

### <a name="windows-operating-system"></a>Sistema operativo Windows
Las siguientes versiones del sistema operativo Windows son compatibles oficialmente con el agente de Windows:

* Windows Server 2008 Service Pack 1 (SP1) o versiones posteriores
* Windows 7 SP1 y versiones posteriores.

#### <a name="network-configuration"></a>Network configuration (Configuración de red)
A continuación se muestra la información de configuración de proxy y firewall requerida para que el agente de Windows se comunique con Log Analytics. El tráfico es saliente desde la red al servicio Log Analytics. 

| Recurso del agente | Puertos | Omitir inspección de HTTPS|
|----------------|-------|------------------------|
|*.ods.opinsights.azure.com |443 | Sí |
|*.oms.opinsights.azure.com | 443 | Sí | 
|*.blob.core.windows.net | 443 | Sí | 
|*.azure-automation.net | 443 | Sí | 

### <a name="linux-operating-systems"></a>Sistemas operativos Linux
Las distribuciones Linux siguientes son compatibles oficialmente.  Aunque también se puede ejecutar el agente de Linux en otras distribuciones que no se enumeran.

* Amazon Linux 2012.09 a 2015.09 (x86/x64)
* CentOS Linux 5, 6 y 7 (x86/x64)
* Oracle Linux 5, 6 y 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 y 7 (x86/x64)
* Debian GNU/Linux 6, 7 y 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 y 12 (x86/x64)

#### <a name="network-configuration"></a>Network configuration (Configuración de red)
A continuación se muestra la información de configuración de proxy y firewall requerida para que el agente de Linux se comunique con Log Analytics. El tráfico es saliente desde la red al servicio Log Analytics. 

|Recurso del agente| Puertos |  
|------|---------|  
|*.ods.opinsights.azure.com | Puerto 443|   
|*.oms.opinsights.azure.com | Puerto 443|   
|*.blob.core.windows.net | Puerto 443|   
|*.azure-automation.net | Puerto 443|  

El agente de Linux admite la comunicación a través de un servidor proxy o la puerta de enlace de OMS para el servicio Log Analytics mediante el protocolo HTTPS.  Se admite la autenticación anónima y básica (nombre de usuario/contraseña).  El servidor proxy se puede especificar durante la instalación o al modificar el archivo de configuración proxy.conf después de la instalación.  

El valor de configuración de proxy tiene la siguiente sintaxis:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Aunque el servidor proxy no requiera que se autentique, el agente de Linux sí que requerirá que proporcione un pseudousuario y contraseña. Puede ser cualquier nombre de usuario o contraseña.

|Propiedad| Descripción |
|--------|-------------|
|Protocol | https |
|user | Nombre de usuario opcional para la autenticación de proxy |
|contraseña | Contraseña opcional para la autenticación de proxy |
|proxyhost | Dirección o el FQDN de la puerta de enlace de OMS/servidor proxy |
|puerto | Número de puerto opcional para el servidor proxy/puerta de enlace de OMS |

Por ejemplo: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Si usa caracteres especiales como "@" en la contraseña, recibirá un error de conexión de proxy porque el valor no se analiza correctamente.  Para solucionar este problema, codifique la contraseña en la dirección URL con una herramienta como [URLDecode](https://www.urldecoder.org/).  

## <a name="install-and-configure-agent"></a>Instalación y configuración del agente 
Conectar los equipos locales directamente con Log Analytics puede realizarse mediante métodos diferentes según sus requisitos. En la tabla siguiente se resalta cada método para determinar cuál funciona mejor en su organización.

|Origen | Método | Descripción|
|-------|-------------|-------------|
| Equipo de Windows|- [Instalación manual](log-analytics-agent-windows.md)<br>- [DSC de Azure Automation](log-analytics-agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Plantilla de Resource Manager con Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Instale el agente de Microsoft Monitoring desde la línea de comandos o mediante un método automatizado, como DSC de Azure Automation, [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications), o con una plantilla de Azure Resource Manager si implementó Microsoft Azure Stack en su centro de datos.| 
|Equipo con Linux| [Instalación manual](log-analytics-quick-collect-linux-computer.md)|Instale al agente para Linux llamando a un script contenedor hospedado en GitHub. | 
| System Center Operations Manager|[Integrar Operations Manager con Log Analytics](log-analytics-om-agents.md) | Configurar la integración entre Operations Manager y Log Analytics para reenviar datos procedentes de equipos Linux y Windows que informan a un grupo de administración.|  

## <a name="next-steps"></a>Pasos siguientes

* Revise los [orígenes de datos](log-analytics-data-sources.md) para saber qué orígenes de datos hay disponibles para recopilar datos de su sistema Windows o Linux. 

* Obtenga información acerca de las [búsquedas de registros](log-analytics-log-searches.md) para analizar los datos recopilados de orígenes de datos y soluciones. 

* Obtenga información sobre las [soluciones](log-analytics-add-solutions.md) que agregan funcionalidad a Log Analytics y que también recopilan datos en el repositorio de OMS.
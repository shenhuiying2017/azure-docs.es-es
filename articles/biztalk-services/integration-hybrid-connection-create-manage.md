---
title: "Creación y administración de conexiones híbridas| Microsoft Docs"
description: "Obtenga información acerca de cómo crear una conexión híbrida, administrar la conexión e instalar el administrador de conexiones híbridas. MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: erikre
editor: 
ms.assetid: aac0546b-3bae-41e0-b874-583491a395ea
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: ccompy
ms.openlocfilehash: 1751d33b5f6f6a506654daedd15bbd75ae271483
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="create-and-manage-hybrid-connections"></a>Creación y administración de conexiones híbridas

> [!IMPORTANT]
> Las Conexiones híbridas de BizTalk se han retirado y se han reemplazado por las Conexiones híbridas de App Service. Para obtener más información, incluida la forma de administrar las Conexiones híbridas de BizTalk existentes, consulte [Conexiones híbridas de Azure App Service](../app-service/app-service-hybrid-connections.md).

>[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="overview-of-the-steps"></a>Información general de los pasos
1. Para crear una conexión híbrida, especifique el **host name** o **FQDN** of the on-premises resource in your private netwok.
2. Vincule las Aplicaciones web de Azure o las Aplicaciones móviles de Azure a la conexión híbrida.
3. Instale el Administrador de conexiones híbridas en el recurso local y conéctese a la conexión híbrida específica. El portal de Azure ofrece una experiencia con un solo clic para la instalación y la conexión.
4. Administre las conexiones híbridas y sus claves de conexión.

En este tema se muestran estos pasos. 

> [!IMPORTANT]
> Se puede establecer un punto de conexión híbrido en una dirección IP. Si usa una dirección IP, puede o no encontrar el recurso local en función de su cliente. La conexión híbrida depende del cliente que realiza la búsqueda de DNS. En la mayoría de los casos, el **cliente** es el código de aplicación. Si el cliente no realiza una búsqueda de DNS (no intenta resolver la dirección IP como si fuese un nombre de dominio [x.x.x.x]), no se envía tráfico a través de la conexión híbrida.
> 
> Por ejemplo (en pseudocódigo), defina **10.4.5.6** como host local:
> 
> **El siguiente escenario funciona:**  
> `Application code -> GetHostByName("10.4.5.6") -> Resolves to 127.0.0.3 -> Connect("127.0.0.3") -> Hybrid Connection -> on-prem host`
> 
> **El siguiente escenario no funciona:**  
> `Application code -> Connect("10.4.5.6") -> ?? -> No route to host`
> 
> 

## <a name="CreateHybridConnection"></a>Creación de una conexión híbrida
Puede crear una conexión híbrida en [Conexiones híbridas de Azure App Service](../app-service/app-service-hybrid-connections.md) **o** con las [API de REST de BizTalk Services](https://msdn.microsoft.com/library/azure/dn232347.aspx). 

<!-- **To create Hybrid Connections using Web Apps**, see [Connect Azure Web Apps to an On-Premises Resource](../app-service-web/web-sites-hybrid-connection-get-started.md). You can also install the Hybrid Connection Manager (HCM) from your web app, which is the preferred method.  -->

#### <a name="additional"></a>Información adicional
* Se pueden crear varias conexiones híbridas. Consulte [BizTalk Services: Gráfico de ediciones](biztalk-editions-feature-chart.md) para ver el número de conexiones permitidas. 
* Todas las conexiones híbridas se crean con un par de cadenas de conexión: las claves de aplicación que ENVÍAN y las claves locales que ESCUCHAN. Cada par tiene una clave principal y una secundaria. 

## <a name="LinkWebSite"></a>Vincular su aplicación web o móvil de Azure App Service
Para vincular una aplicación web o móvil de Azure App Service a una conexión híbrida existente, seleccione **Usar una conexión híbrida existente** en la hoja Conexiones híbridas. 
<!-- See [Access on-premises resources using hybrid connections in Azure App Service](../app-service-web/web-sites-hybrid-connection-get-started.md). -->

## <a name="InstallHCM"></a>Instalación del Administrador de conexiones híbridas en un entorno local
Después de que se crea una conexión híbrida, instale el Administrador de conexiones híbridas en el recurso local. Este se puede descargar desde Aplicaciones web de Azure o desde el Servicio de BizTalk. 

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]
 
[Conexiones híbridas de Azure App Service](../app-service/app-service-hybrid-connections.md) también es un buen recurso.

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
--> 

#### <a name="additional"></a>Información adicional
* El administrador de conexiones híbridas puede instalarse en los siguientes sistemas operativos:
  
  * Windows Server 2008 R2 (se requiere .NET Framework 4.5+ y Windows Management Framework 4.0+)
  * Windows Server 2012 (se requiere Windows Management Framework 4.0+)
  * Windows Server 2012 R2
* Después de instalar el Administrador de conexiones híbridas, tiene lugar lo siguiente: 
  
  * La conexión híbrida hospedada en Azure se configura automáticamente para usar la cadena de conexión de la aplicación principal. 
  * El recurso local se configura automáticamente para usar la cadena de conexión local principal.
* El Administrador de conexiones híbridas debe usar una cadena de conexión local válida para la autorización. Las aplicaciones web o aplicaciones móviles de Azure deben usar una cadena de conexión de aplicación válida para la autorización.
* Puede escalar las conexiones híbridas mediante la instalación de otra instancia del Administrador de conexiones híbridas en otro servidor. Configure el agente de escucha local para usar la misma dirección como el primer agente de escucha local. En esta situación, el tráfico es distribuido aleatoriamente (round robin) entre los agentes de escucha locales activos. 

## <a name="ManageHybridConnection"></a>Administración de conexiones híbridas

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)] 

[Conexiones híbridas de Azure App Service](../app-service/app-service-hybrid-connections.md) también es un buen recurso.

#### <a name="copyregenerate-the-hybrid-connection-strings"></a>Copia y regeneración de las cadenas de conexión híbridas

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)] 

[Conexiones híbridas de Azure App Service](../app-service/app-service-hybrid-connections.md) también es un buen recurso.

#### <a name="use-group-policy-to-control-the-on-premises-resources-used-by-a-hybrid-connection"></a>Uso de la directiva de grupo para controlar los recursos locales utilizados por una conexión híbrida
1. Descargue las [plantillas administrativas del Administrador de conexiones híbridas](http://www.microsoft.com/download/details.aspx?id=42963).
2. Extraiga los archivos.
3. En el equipo que modifica la directiva de grupo, haga lo siguiente:  
   
   * Copie los archivos .ADMX en la carpeta *%WINROOT%\PolicyDefinitions*.
   * Copie los archivos .ADML en la carpeta *%WINROOT%\PolicyDefinitions\es-es*.

Una vez copiados, puede usar el Editor de directivas de grupo para cambiar la directiva.

## <a name="next"></a>Pasos siguientes
[Introducción a las conexiones híbridas](integration-hybrid-connection-overview.md)

## <a name="see-also"></a>Otras referencias
[API REST para administrar BizTalk Services en Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)  
[BizTalk Services: Gráfico de ediciones](biztalk-editions-feature-chart.md)  
[Creación de una instancia de BizTalk Services](biztalk-provision-services.md)  
[BizTalk Services: pestañas Panel, Monitor y Escala](biztalk-dashboard-monitor-scale-tabs.md)

[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png 

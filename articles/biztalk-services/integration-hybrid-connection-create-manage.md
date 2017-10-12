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
ms.openlocfilehash: 7b8b9072d0e2fd054ca07873c0a9ce772dc2941e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-hybrid-connections"></a>Creación y administración de conexiones híbridas

> [!IMPORTANT]
> Las Conexiones híbridas de BizTalk se han retirado y se han reemplazado por las Conexiones híbridas de App Service. Para obtener más información, incluida la forma de administrar las Conexiones híbridas de BizTalk existentes, consulte [Conexiones híbridas de Azure App Service](../app-service/app-service-hybrid-connections.md).


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
Una conexión híbrida se puede crear en el Portal de Azure mediante Aplicaciones web **o** servicios de BizTalk. 

<!-- **To create Hybrid Connections using Web Apps**, see [Connect Azure Web Apps to an On-Premises Resource](../app-service-web/web-sites-hybrid-connection-get-started.md). You can also install the Hybrid Connection Manager (HCM) from your web app, which is the preferred method.  -->

**Para crear conexiones híbridas en servicios de BizTalk**:

1. Inicie sesión en el [Portal de Azure clásico](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. En el panel de navegación izquierdo, seleccione **Servicios de BizTalk** y luego seleccione el servicio de BizTalk. 
   
    Si no tiene un Servicio de BizTalk existente, puede [Crear un servicio de BizTalk](biztalk-provision-services.md).
3. Seleccione la pestaña **Conexiones híbridas**:  
   ![Pestaña Conexiones híbridas][HybridConnectionTab]
4. Seleccione **Crear una conexión híbrida** o seleccione el botón **AGREGAR** de la barra de tareas. Escriba lo siguiente:
   
   | Propiedad | Descripción |
   | --- | --- |
   | Nombre |El nombre de la conexión híbrida debe ser único y no puede ser el mismo que el servicio de BizTalk. Puede escribir cualquier nombre pero sea concreto con su finalidad. Algunos ejemplos son: <br/><br/>Payroll*SQLServer*<br/>SupplyList*SharepointServer*<br/>Customers*OracleServer* |
   | Nombre de host |Escriba el nombre de host completo, solo el nombre de host o la dirección IPv4 del recurso local. Algunos ejemplos son: <br/><br/>mySQLServer<br/>*mySQLServer*.*Domain*.corp.*yourCompany*.com<br/>*myHTTPSharePointServer*<br/>*myHTTPSharePointServer*.*yourCompany*.com<br/>10.100.10.10<br/><br/>Si usa la dirección IPv4, tenga en cuenta que su cliente o el código de aplicación podrían no resolver la dirección IP. Consulte la nota importante situada al principio de este tema. |
   | Port |Escriba el número de puerto del recurso local. Por ejemplo, si utiliza Aplicaciones web, escriba los puertos 80 o 443. Si utiliza SQL Server, escriba el puerto 1433. |
5. Seleccione la marca de verificación para completar la configuración. 

#### <a name="additional"></a>Información adicional
* Se pueden crear varias conexiones híbridas. Consulte [Servicios de BizTalk: Gráfico de ediciones](biztalk-editions-feature-chart.md) para ver el número de conexiones permitidas. 
* Todas las conexiones híbridas se crean con un par de cadenas de conexión: las claves de aplicación que ENVÍAN y las claves locales que ESCUCHAN. Cada par tiene una clave principal y una secundaria. 

## <a name="LinkWebSite"></a>Vincular su aplicación web o móvil de Azure App Service
Para vincular una aplicación web o móvil de Azure App Service a una conexión híbrida existente, seleccione **Usar una conexión híbrida existente** en la hoja Conexiones híbridas. 
<!-- See [Access on-premises resources using hybrid connections in Azure App Service](../app-service-web/web-sites-hybrid-connection-get-started.md). -->

## <a name="InstallHCM"></a>Instalación del Administrador de conexiones híbridas en un entorno local
Después de que se crea una conexión híbrida, instale el Administrador de conexiones híbridas en el recurso local. Este se puede descargar desde Aplicaciones web de Azure o desde el Servicio de BizTalk. Pasos para los servicios de BizTalk: 

1. Inicie sesión en el [Portal de Azure clásico](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. En el panel de navegación izquierdo, seleccione **Servicios de BizTalk** y luego seleccione el servicio de BizTalk. 
3. Seleccione la pestaña **Conexiones híbridas**:  
   ![Pestaña Conexiones híbridas][HybridConnectionTab]
4. En la barra de tareas, seleccione **Instalación local**:  
   ![Instalación local][HCOnPremSetup]
5. Seleccione **Instalar y configurar** para ejecutar o descargar el Administrador de conexiones híbridas en el sistema local. 
6. Seleccione la marca de verificación para iniciar la instalación. 

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
* El Administrador de conexiones híbridas debe usar una cadena de conexión local válida para la autorización. Las Aplicaciones web de Azure o las Aplicaciones móviles de Azure deben usar una cadena de conexión de aplicación válida para la autorización.
* Puede escalar las conexiones híbridas mediante la instalación de otra instancia del Administrador de conexiones híbridas en otro servidor. Configure el agente de escucha local para usar la misma dirección como el primer agente de escucha local. En esta situación, el tráfico es distribuido aleatoriamente (round robin) entre los agentes de escucha locales activos. 

## <a name="ManageHybridConnection"></a>Administración de conexiones híbridas
Para administrar las conexiones híbridas, puede:

* Usar el Portal de Azure para ir al servicio de BizTalk. 
* Usar [API de REST](http://msdn.microsoft.com/library/azure/dn232347.aspx).

#### <a name="copyregenerate-the-hybrid-connection-strings"></a>Copia y regeneración de las cadenas de conexión híbridas
1. Inicie sesión en el [Portal de Azure clásico](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. En el panel de navegación izquierdo, seleccione **Servicios de BizTalk** y luego seleccione el servicio de BizTalk. 
3. Seleccione la pestaña **Conexiones híbridas**:  
   ![Pestaña Conexiones híbridas][HybridConnectionTab]
4. Seleccione la conexión híbrida. En la barra de tareas, seleccione **Administrar conexión**:  
   ![Administrar opciones][HCManageConnection]
   
    **Administrar conexión** muestra las cadenas de conexión de aplicación y local. Puede copiar las cadenas de conexión o regenerar la clave de acceso usada en la cadena de conexión. 
   
    **Si selecciona Regenerar**, se cambia la clave de acceso compartido que se usa en la cadena de conexión. Haga lo siguiente:
   
   * En el Portal de Azure clásico, seleccione **Sincronizar claves** en la aplicación de Azure.
   * Vuelva a ejecutar la **Instalación local**. Al volver a ejecutar la configuración local, el recurso local se configura automáticamente para usar la cadena de conexión principal actualizada.

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
[Servicios de BizTalk: Gráfico de ediciones](biztalk-editions-feature-chart.md)  
[Crear un Servicio de BizTalk mediante el portal de Azure clásico](biztalk-provision-services.md)  
[Servicios de BizTalk: pestañas Panel, Monitor y Escala](biztalk-dashboard-monitor-scale-tabs.md)

[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png 

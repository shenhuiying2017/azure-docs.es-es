<properties 
	pageTitle="Administración de una aplicación de API" 
	description="Aprenda a administrar aplicaciones de API mediante el portal de vista previa de Azure y el Explorador de servidores de Visual Studio." 
	services="app-service\api" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na"
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="tdykstra"/>

# Administración de una aplicación de API

Este artículo muestra cómo usar el portal de vista previa de Azure para realizar las tareas de administración de aplicaciones de API como, por ejemplo, la configuración de la autenticación y el establecimiento del escalado automático. El artículo también muestra cómo realizar algunas tareas de administración en la ventana Explorador de servidores en Visual Studio.

## Descripción de la arquitectura de aplicación de API

En el Servicio de aplicaciones de Azure, una aplicación de API es una [aplicación web](../app-service-web/app-service-web-overview.md) que tiene características adicionales para hospedar servicios web. En el portal de vista previa de Azure, hay una hoja de **aplicación de API** para administrar las características específicas de la API y una hoja de **host de aplicación de API** para administrar la aplicación web subyacente.

Cada grupo de recursos que contiene al menos una aplicación de API incluye también una *puerta de enlace*. La puerta de enlace actúa como un proxy, controlando la autenticación y otras funciones administrativas para todas las aplicaciones de API en un grupo de recursos. Al igual que una aplicación de API, una puerta de enlace es una aplicación web con funcionalidad adicional, por lo que también hay dos hojas en el portal para administrar la puerta de enlace: una hoja de **puerta de enlace** para funciones específicas de la puerta de enlace y una hoja de **host de puerta de enlace** para administrar la aplicación web subyacente.

### Tareas de hojas de aplicación de API 

La hoja de **aplicación de API** se utiliza para las siguientes tareas:

- Configurar el nivel de acceso: haga clic en **Configuración > Configuración de la aplicación**. El valor predeterminado es interno, lo que significa que solo las aplicaciones de API del mismo grupo de recursos pueden llamar a la aplicación de API. Para obtener más información, consulte [Protección de una aplicación de API](app-service-api-dotnet-add-authentication.md).   
- Configurar la directiva de actualización: haga clic en **Configuración > Configuración de la aplicación**. El valor predeterminado es **Activado**. Esto significa que cuando se publica una nueva versión de la aplicación de API en Marketplace, la aplicación de API se actualiza automáticamente a la nueva versión si se trata de un cambio no radical.  
- Configurar la autenticación para llamadas salientes desde la aplicación de API: haga clic en **Configuración > Autenticación**. Si la aplicación de API realiza llamadas a un servicio externo que requiere autenticación, los valores de configuración requeridos se introducen aquí. Por ejemplo, un conector de Dropbox requiere un identificador de cliente y un secreto de cliente para tener acceso al servicio de Dropbox.
- Configurar [RBAC](../role-based-access-control-configure.md): haga clic en **Configuración > Usuarios**. El acceso de usuario que configure aquí determina solo quién puede tener acceso a las características específicas de la aplicación de API. Para configurar RBAC para las características de la aplicación web, utilice la hoja de **host de aplicación de API**. Normalmente deseará mantener sincronizados los valores de RBAC para la aplicación de API y el host de la aplicación de API. Si asigna a alguien acceso a la aplicación de API pero no al host de la aplicación de API, no podrá utilizar las características de la hoja de **aplicación de API** que realmente guardan relación con el host de la aplicación de API. La relación entre la hoja de **aplicación de API** y la hoja de **host de aplicación de API** se explica más adelante.
- Ver definición de API: haga clic en **Definición de API** en la sección **Resumen** para ver una lista de los métodos expuestos por la **aplicación de API**.

### Características comunes de las hojas de aplicación de API y de host de aplicación de API 

La hoja de **aplicación de API** le permite realizar muchas tareas que pertenecen a la aplicación web subyacente. Por ejemplo, proporciona botones para detener, iniciar y reiniciar la aplicación web que hospeda la aplicación de API. Sin embargo, estas tareas también se pueden realizar mediante la hoja de **host de aplicación de API**. Este es el motivo por el cual las dos hojas comparten gran parte de la interfaz de usuario. Los botones **Detener**, **Iniciar** y **Reiniciar** de la hoja de **aplicación de API** tienen exactamente el mismo efecto que los botones **Detener**, **Iniciar** y **Reiniciar** de la hoja de **host de aplicación de API**. Del mismo modo, la información de supervisión proporcionada en la hoja de **aplicación de API** coincide con la que muestra la hoja de **host de aplicación de API**.

Las únicas funciones proporcionadas en la hoja de **aplicación de API** que no están duplicadas en la hoja de **host de aplicación de API** se enumeran en la sección anterior.

### Tareas de la hoja de host de aplicación de API

La hoja de **host de aplicación de API** se utiliza para todas las tareas que haría para cualquier aplicación web. Para obtener más información, consulte [Administración de aplicaciones web en el portal](../app-service-web/web-sites-manage.md).

### Tareas de la hoja de puerta de enlace

La hoja de **puerta de enlace** se utiliza para las siguientes tareas:

- Configurar el proveedor de autenticación para las llamadas entrantes a aplicaciones de API: haga clic en **Configuración > Identidad**. Si la puerta de enlace debe autenticar a los usuarios antes de permitirles llamar a las aplicaciones de API en el grupo de recursos, los valores de configuración requeridos se introducen aquí. Para obtener más información, consulte [Configuración y prueba de un conector SaaS en el Servicio de aplicaciones de Azure](app-service-api-connnect-your-app-to-saas-connector.md). 
- Configurar [RBAC](../role-based-access-control-configure.md): haga clic en **Configuración > Usuarios**. Los mismos comentarios anteriores que explican la relación entre la configuración de RBAC especificada en las hojas de aplicación de API y de host de aplicación de API se aplica a las hojas de puerta de enlace y de host de puerta de enlace.

### Tareas de la hoja de host de puerta de enlace

La hoja de **host de puerta de enlace** se utiliza para todas las tareas que haría para cualquier aplicación web. Para obtener más información, consulte [Administración de aplicaciones web en el portal](../app-service-web/web-sites-manage.md).

## Navegación hasta la hoja de aplicación de API 

Una manera de obtener acceso a la hoja de **aplicación de API** es a través de la característica de exploración del portal de vista previa de Azure. En la página principal del portal, haga clic en **Examinar > Aplicaciones de API** para ver todas las aplicaciones de API que se pueden administrar.

![](./media/app-service-api-manage-in-portal/browse.png)

![](./media/app-service-api-manage-in-portal/apiappslist.png)

Al hacer clic en una fila en la hoja de la lista de **aplicaciones de API**, el portal muestra la hoja de **aplicación de API**.

![](./media/app-service-api-manage-in-portal/apiappblade.png)

## Navegación hasta la hoja de host de aplicación de API

Para obtener acceso a la hoja de **host de aplicación de API**, haga clic en **Host de aplicación de API** en la hoja de **aplicación de API**.

![](./media/app-service-api-manage-in-portal/apiappbladetohost.png)

![](./media/app-service-api-manage-in-portal/apiapphostbladenocallouts.png)

## Navegación hasta la hoja de puerta de enlace

Para obtener acceso a la hoja de **puerta de enlace**, haga clic en el vínculo **Puerta de enlace** en la hoja de **aplicación de API**.
   
![](./media/app-service-api-manage-in-portal/apiappbladegotogateway.png)

![](./media/app-service-api-manage-in-portal/gatewaybladenocallout.png)

## Navegación hasta la hoja de host de puerta de enlace

Para obtener acceso a la hoja de **host de puerta de enlace**, haga clic en el vínculo **Host de puerta de enlace** en la hoja de **puerta de enlace**.
   
![](./media/app-service-api-manage-in-portal/gatewaybladetohost.png)

![](./media/app-service-api-manage-in-portal/gatewayhost.png)

## Acceso a aplicaciones de API en el Explorador de servidores de Visual Studio

En el Explorador de servidores de Visual Studio puede iniciar una sesión de depuración remota, ver registros de streaming y hacer clic en una entrada de menú que abre la hoja de aplicación de API en el portal.

Para obtener una aplicación de API en el Explorador de servidores, haga clic en **Azure > Servicio de aplicaciones > [nombre del grupo de recursos] > [nombre de la aplicación de API]**, como se muestra en la ilustración.

![](./media/app-service-api-manage-in-portal/se.png)

## Resumen

En este artículo se muestra cómo usar el portal de vista previa de Azure para realizar tareas de administración de aplicaciones de API. Para obtener más información, consulte [¿Qué son Aplicaciones de API?](app-service-api-apps-why-best-platform.md)


<!--HONumber=52-->
 
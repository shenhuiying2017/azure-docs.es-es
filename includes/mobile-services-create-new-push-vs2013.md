Primero, use el Asistente para agregar notificaciones de inserción en Visual Studio 2013 a fin de registrar su aplicación en la Tienda Windows, configure su servicio móvil para habilitar las notificaciones de inserción y agregue código a su aplicación para registrar un canal de dispositivo.

1.  Si todavía no lo ha hecho, siga los pasos que aparecen en [Importación del archivo publishsettings en Visual Studio 2013](/en-us/documentation/articles/mobile-services-windows-how-to-import-publishsettings/) para importar el archivo publisher.settings en Visual Studio.
    
    No es necesario que haga esto si ya usó Visual Studio para crear o administrar servicios móviles en su suscripción de Azure.

2.  En Visual Studio 2013, abra el Explorador de soluciones, haga clic con el botón secundario en el proyecto, haga clic en **Agregar** y, a continuación, haga clic en **Notificación de inserción...**.
    
    ![mobile-add-push-notifications-vs2013](../includes/media/mobile-services-create-new-push-vs2013/mobile-add push-notifications-vs2013.png)
    
    De este modo, se inicia el Asistente para agregar notificaciones de inserción.

3.  Haga clic en **Siguiente**, inicie sesión en su cuenta de la Tienda Windows, proporcione un nombre en **Reservar un nombre nuevo** y haga clic en **Reservar**.
    
    ![mobile-add-push-notifications-vs2013-2](../includes/media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013-2.png)
    
    De este modo, se crea un nuevo registro de aplicaciones.

4.  Haga clic en el nuevo registro en la lista **Nombre de aplicación** y, a continuación, en **Siguiente**.
    
    ![mobile-add-push-notifications-vs2013-3](../includes/media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013-3.png)

5.  En el cuadro de diálogo **Seleccionar un servicio**, haga clic en el nombre del servicio móvil que creó cuando completó la [Introducción a los servicios móviles](/en-us/develop/mobile/tutorials/get-started/) o la [Introducción a los datos](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/) y, a continuación, haga clic en **Siguiente** y en **Finalizar**.
    
    El servicio móvil se actualiza para registrar su SID del paquete de aplicaciones y el secreto de cliente, y se crea una tabla de **canales** nueva. Servicios móviles está ahora configurado para trabajar con los Servicios de notificaciones de inserción de Windows (WNS) para poder enviar notificaciones a su aplicación.
	<div class="dev-callout">

**Nota:**

Cuando su aplicación no está configurada para conectarse con el servicio móvil, el asistente completa también las mismas tareas de configuración que se demostraron en **Introducción a los datos**.
	</div>


<!-- URLs. -->
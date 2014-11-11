<properties linkid="develop-mobile-how-to-guides-import-publish-settings" urlDisplayName="Import your subscription publish settings file in Visual Studio 2013" pageTitle="Import your publish settings file in Visual Studio 2013 | Mobile Services" metaKeywords="Azure import publishsettings, mobile services" description="Learn how to import a subscription publish settings file for your Azure Mobile Services application in Visual Studio 2013." title="Import your subscription publish settings file in Visual Studio 2013" documentationCenter="Mobile" services="" solutions="" manager="" editor="" videoId="" scriptId="" authors="" />

Importación del archivo de configuración de publicación de la suscripción en Visual Studio 2013
===============================================================================================

Antes de crear el servicio móvil, debe importar el archivo de configuración de publicación de la suscripción de Azure en Visual Studio. De esta forma, Visual Studio podrá conectarse a Azure en su nombre.

1.  En Visual Studio 2013, abra el Explorador de soluciones, haga clic con el botón secundario en el proyecto, haga clic en **Agregar** y, a continuación, en **Servicio conectado...**.

    ![agregar servicio conectado](./media/mobile-services-create-new-service-vs2013/mobile-add-connected-service.png)

2.  En el cuadro de diálogo Administrador de servicios, haga clic en **Crear servicio...** y, en **Suscripción**, seleccione **&lt;Importar...&gt;** en el cuadro de diálogo Create Mobile Service.

    ![crear un nuevo servicio móvil desde VS 2013](./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png)

3.  En Import Azure Subscriptions, haga clic en **Descargar archivo de suscripción**, inicie sesión en su cuenta de Azure (si es necesario) y, cuando el explorador le solicite que guarde el archivo, haga clic en **Guardar**.

    ![descargar archivo de suscripción en VS](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription.png)

    **Nota:**

    la ventana de inicio de sesión se muestra en el explorador, que puede encontrarse detrás de la ventana de Visual Studio. No olvide anotar dónde guarda el archivo .publishsettings descargado. Si el proyecto ya está conectado a la suscripción de Azure, puede omitir este paso.

4.  Haga clic en **Examinar**, vaya a la ubicación en la que ha guardado el archivo .publishsettings, seleccione el archivo y, a continuación, haga clic en **Abrir** y en **Importar**.

    ![importar suscripción en VS](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription-2.png)

    Visual Studio importa los datos necesarios para conectar con su suscripción de Azure. Cuando su suscripción ya tiene uno o varios servicios móviles existentes, se muestran los nombres de los servicios.

    **Nota de seguridad**

    Después de importar la configuración de publicación, podría ser conveniente eliminar el archivo .publishsettings descargado dado que contiene información que otros usuarios podrían emplear para tener acceso a su cuenta. Si tiene pensado guardar el archivo para utilizarlo en otros proyectos de aplicaciones relacionados, aplique alguna medida de seguridad.



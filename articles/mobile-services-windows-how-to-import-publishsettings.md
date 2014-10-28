<properties linkid="develop-mobile-how-to-guides-import-publish-settings" urlDisplayName="Import your subscription publish settings file in Visual Studio 2013" pageTitle="Import your publish settings file in Visual Studio 2013 | Mobile Services" metaKeywords="Azure import publishsettings, mobile services" description="Learn how to import a subscription publish settings file for your Azure Mobile Services application in Visual Studio 2013." title="Import your subscription publish settings file in Visual Studio 2013" documentationCenter="Mobile" services="" solutions="" manager="" editor="" videoId="" scriptId="" authors="glenga" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Importación del archivo de configuración de publicación de la suscripción en Visual Studio 2013

Antes de crear el servicio móvil, debe importar el archivo de configuración de publicación de la suscripción de Azure en Visual Studio. De esta forma, Visual Studio podrá conectarse a Azure en su nombre.

1.  En Visual Studio 2013, abra el Explorador de soluciones, haga clic con el botón secundario en el proyecto, haga clic en **Agregar** y, a continuación, en **Servicio conectado...**.

    ![agregar servicio conectado][]

2.  En el cuadro de diálogo Administrador de servicios, haga clic en **Crear servicio...** y, en **Suscripción**, seleccione **Importar...** en el cuadro de diálogo Create Mobile Service.

    ![crear un nuevo servicio móvil desde VS 2013][]

3.  En Import Azure Subscriptions, haga clic en **Descargar archivo de suscripción**, inicie sesión en su cuenta de Azure (si es necesario) y, cuando el explorador le solicite que guarde el archivo, haga clic en **Guardar**.

    ![descargar archivo de suscripción en VS][]

    <div class="dev-callout"><strong>Nota:</strong> <p>la ventana de inicio de sesi&oacute;n se muestra en el explorador, que puede encontrarse detr&aacute;s de la ventana de Visual Studio. No olvide anotar d&oacute;nde guarda el archivo .publishsettings descargado. Si el proyecto ya est&aacute; conectado a la suscripci&oacute;n de Azure, puede omitir este paso.</p></div>

4.  Haga clic en **Examinar**, vaya a la ubicación en la que ha guardado el archivo .publishsettings, seleccione el archivo y, a continuación, haga clic en **Abrir** y en **Importar**.

    ![importar suscripción en VS][]

    Visual Studio importa los datos necesarios para conectar con su suscripción de Azure. Cuando su suscripción ya tiene uno o varios servicios móviles existentes, se muestran los nombres de los servicios.

    <div class="dev-callout"><strong>Nota de seguridad</strong> <p>Despu&eacute;s de importar la configuraci&oacute;n de publicaci&oacute;n, podr&iacute;a ser conveniente eliminar el archivo .publishsettings descargado dado que contiene informaci&oacute;n que otros usuarios podr&iacute;an emplear para tener acceso a su cuenta. Si tiene pensado guardar el archivo para utilizarlo en otros proyectos de aplicaciones relacionados, aplique alguna medida de seguridad.</p></div>

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [agregar servicio conectado]: ./media/mobile-services-create-new-service-vs2013/mobile-add-connected-service.png
  [crear un nuevo servicio móvil desde VS 2013]: ./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png
  [descargar archivo de suscripción en VS]: ./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription.png
  [importar suscripción en VS]: ./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription-2.png

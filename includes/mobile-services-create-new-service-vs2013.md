Los siguientes pasos permiten crear un servicio móvil en Azure y agregar al proyecto código que posibilite el acceso a este nuevo servicio. Antes de crear el servicio móvil, debe importar el archivo publishsettings de la suscripción de Azure en Visual Studio. De esta forma, Visual Studio podrá conectarse a Azure en su nombre. Cuando se crea un servicio móvil, es necesario especificar una Base de datos SQL de Azure. Esta base de datos la utilizará el servicio móvil para almacenar los datos de aplicaciones.

1.  En Visual Studio 2013, abra el Explorador de soluciones, haga clic con el botón secundario en el proyecto, haga clic en **Agregar** y, a continuación, en **Servicio conectado...**.

    ![agregar servicio conectado][agregar servicio conectado]

2.  En el cuadro de diálogo Administrador de servicios, haga clic en **Crear servicio...** y, en **Suscripción**, seleccione **Importar...** en el cuadro de diálogo Create Mobile Service.

    ![crear un nuevo servicio móvil desde VS 2013][crear un nuevo servicio móvil desde VS 2013]

3.  En Import Azure Subscriptions, haga clic en **Descargar archivo de suscripción**, inicie sesión en su cuenta de Azure (si es necesario) y, cuando el explorador le solicite que guarde el archivo, haga clic en **Guardar**.

    ![descargar archivo de suscripción en VS][descargar archivo de suscripción en VS]

    <div class="dev-callout"><strong>Nota:</strong> <p>la ventana de inicio de sesi&oacute;n se muestra en el explorador, que puede encontrarse detr&aacute;s de la ventana de Visual Studio. No olvide anotar d&oacute;nde guarda el archivo .publishsettings descargado. Si el proyecto ya est&aacute; conectado a la suscripci&oacute;n de Azure, puede omitir este paso.</p></div>

4.  Haga clic en **Examinar**, vaya a la ubicación en la que ha guardado el archivo .publishsettings, seleccione el archivo y, a continuación, haga clic en **Abrir** y en **Importar**.

    ![importar suscripción en VS][importar suscripción en VS]

    Visual Studio importa los datos necesarios para conectar con su suscripción de Azure. Cuando su suscripción ya tiene uno o varios servicios móviles existentes, se muestran los nombres de los servicios.

    <div class="dev-callout"><strong>Nota de seguridad</strong> <p>Despu&eacute;s de importar la configuraci&oacute;n de publicaci&oacute;n, podr&iacute;a ser conveniente eliminar el archivo .publishsettings descargado dado que contiene informaci&oacute;n que otros usuarios podr&iacute;an emplear para tener acceso a su cuenta. Si tiene pensado guardar el archivo para utilizarlo en otros proyectos de aplicaciones relacionados, aplique alguna medida de seguridad.</p></div>

5.  De nuevo en el cuadro de diálogo **Create Mobile Service**, seleccione su **Suscripción** y la **Región** deseada para su servicio móvil y, a continuación, escriba un nombre para el mismo en el cuadro **Name**.

    <div class="dev-callout"><strong>Nota:</strong> <p>los nombres de los servicios m&oacute;viles deben ser &uacute;nicos. Cuando el nombre suministrado no se encuentra disponible, una X roja se muestra junto a <strong>Name</strong>. </p></div>

6.  En **Database**, seleccione **\<Create a free SQL Database\>**, proporcione un nombre de usuario en **Server user name**, la contraseña en **Server password** y la confirmación de dicha contraseña en **Server password confirmation** y, a continuación, haga clic en **Create**.

    ![crear servicio desde VS 2013 parte 2][crear servicio desde VS 2013 parte 2]

    > [WACOM.NOTE]
    > Como parte de este tutorial, va a crear una nueva instancia y un nuevo servidor de Base de datos SQL gratuitos. Puede reutilizar esta nueva base de datos y administrarla como lo haría con cualquier otra instancia de Base de datos SQL. Solo puede tener una instancia de base de datos libre. Si ya tiene una base de datos en la misma región que el nuevo servicio móvil, puede elegir en su lugar la base de datos existente. Cuando elija una base de datos existente, asegúrese de suministrar las credenciales de inicio de sesión correctas. En caso contrario, el servicio móvil se creará con un estado incorrecto.

    Cuando se crea el servicio móvil, se agrega al proyecto una referencia a la biblioteca de clientes de Servicios móviles y se actualiza el código fuente del proyecto.

  [agregar servicio conectado]: ./media/mobile-services-create-new-service-vs2013/mobile-add-connected-service.png
  [crear un nuevo servicio móvil desde VS 2013]: ./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png
  [descargar archivo de suscripción en VS]: ./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription.png
  [importar suscripción en VS]: ./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription-2.png
  [crear servicio desde VS 2013 parte 2]: ./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013-2.png

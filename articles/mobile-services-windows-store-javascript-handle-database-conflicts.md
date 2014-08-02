<properties linkid="develop-mobile-tutorials-optimistic-concurrent-data-javascript" urlDisplayName="Optimistic concurrency" pageTitle="Handle database write conflicts with optimistic concurrency (Windows Store) | Mobile Dev Center" metaKeywords="" writer="wesmc" description="Learn how to handle database write conflicts on both the server and in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling database write conflicts" />

Control de conflictos de escritura de bases de datos
====================================================

[C\# para Tienda Windows](/en-us/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/ "C# para Tienda Windows") [JavaScript para Tienda Windows](/en-us/develop/mobile/tutorials/handle-database-write-conflicts-javascript/ "JavaScript para Tienda Windows") [Windows Phone](/en-us/develop/mobile/tutorials/handle-database-write-conflicts-wp8/ "Windows Phone")

Este tutorial se ha elaborado para ayudarle a comprender mejor cómo controlar los conflictos que se producen cuando dos o más clientes escriben en el mismo registro de la base de datos en una aplicación de la Tienda Windows. En algunos casos, dos o más clientes pueden escribir cambios en el mismo elemento y al mismo tiempo. Si no se produjera la detección de conflictos, la última escritura sobrescribiría cualquier actualización anterior incluso si no fuese el resultado deseado. Los Servicios móviles de Azure ofrecen soporte para detectar y resolver estos conflictos. Este tema le guiará a través de los pasos que le permitirán controlar los conflictos de escritura de bases de datos tanto en el servidor como en la aplicación.

En este tutorial agregará funcionalidad a la aplicación de inicio rápido para controlar los conflictos que se produzcan al actualizar la base de datos TodoItem. Este tutorial le guiará a través de estos pasos básicos:

1.  [Actualización de la aplicación para permitir actualizaciones](#uiupdate)
2.  [Habilitación de la detección de conflictos en la aplicación](#enableOC)
3.  [Prueba de conflictos de escritura de bases de datos en la aplicación](#test-app)
4.  [Control automático de la resolución de conflictos en los scripts del servidor](#scriptsexample)

Este tutorial requiere lo siguiente:

-   Microsoft Visual Studio 2013 Express para Windows o posterior.
-   Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar el tutorial [Introducción a los Servicios móviles](/en-us/develop/mobile/tutorials/get-started) descargando la versión del lenguaje de JavaScript del proyecto inicial.
-   [Cuenta de Azure](http://www.windowsazure.com/en-us/pricing/free-trial/)
-   Paquete de NuGet de Servicios móviles de Azure 1.1.5 o posterior. Para obtener la última versión, siga los pasos a continuación:
    1.  En Visual Studio, abra el proyecto y haga clic con el botón secundario en el proyecto en el Explorador de soluciones y, a continuación, haga clic en **Administrar paquetes de NuGet**.

    2.  Expanda **En línea** y haga clic en **Microsoft and .NET**. En el cuadro de texto de búsqueda, escriba **WindowsAzure.MobileServices.WinJS**. Haga clic en **Instalar** en el paquete de NuGet de **Servicios móviles de Azure para WinJS**.

        ![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png)

Actualización de la interfaz de usuarioActualización de la aplicación para permitir actualizaciones
---------------------------------------------------------------------------------------------------

En esta sección, actualizará la interfaz de usuario para permitir la actualización del texto de cada elemento. La plantilla de enlace contendrá un control de casilla y clase de texto para cada elemento de la tabla de base de datos. Podrá actualizar el campo de texto de TodoItem. Esta aplicación controlará el evento `keydown` para que el elemento se actualice al presionar la tecla **Entrar**.

1.  En Visual Studio, abra la versión de lenguaje de JavaScript del proyecto TodoList que descargó en el tutorial [Introducción a los Servicios móviles](/en-us/develop/mobile/tutorials/get-started).
2.  En el Explorador de soluciones de Visual Studio, abra default.html y sustituya la definición de etiqueta div `TemplateItem` por la etiqueta div que se muestra a continuación y guarde el cambio. Esto agrega el control de cuadro de texto para permitirle editar el texto de un objeto TodoItem.

         <div id="TemplateItem" data-win-control="WinJS.Binding.Template">
           <div style="display: -ms-grid; -ms-grid-columns: auto 1fr">
             <input class="itemCheckbox" type="checkbox" data-win-bind="checked: complete; dataContext: this" />
               <div style="-ms-grid-column: 2; -ms-grid-row-align: center; margin-left: 5px">
                 <input id="modifytextbox" class="text win-interactive" data-win-bind="value: text; dataContext: this" />
               </div>
           </div>
         </div>

3.  En el Explorador de soluciones de Visual Studio, expanda la carpeta **js**. Abra el archivo default.js y sustituya la función `updateTodoItem` por la siguiente definición, que no eliminará los elementos actualizados de la interfaz de usuario.

         var updateTodoItem = function (todoItem) {
           // Este código toma un elemento TodoItem recientemente completado y actualiza la base de datos. 
           todoTable.update(todoItem);
           };

4.  En el archivo default.js, agregue el siguiente controlador de eventos para el evento `keydown`, para que el elemento se actualice al presionar la tecla **Entrar**.

         listItems.onkeydown = function (eventArgs) {
           if (eventArgs.key == "Enter") {
             var todoItem = eventArgs.target.dataContext.backingData;
             todoItem.text = eventArgs.target.value;
             updateTodoItem(todoItem);
             }
           };

Ahora la aplicación escribe los cambios de texto en cada elemento de vuelta a la base de datos al presionar la tecla **Entrar**.

Activación de la simultaneidad optimistaActivación de la detección de conflictos en la aplicación
-------------------------------------------------------------------------------------------------

Servicios móviles de Azure es compatible con el control de simultaneidad optimista mediante el seguimiento de cambios en cada elemento mediante la columna de propiedades del sistema `__version` que se agrega a cada tabla. En esta sección, activaremos la aplicación para detectar estos conflictos de escritura a través de la propiedad del sistema `__version`. Una vez que se habilite la propiedad del sistema en el elemento todoTable, la aplicación recibirá una notificación mediante una excepción `MobileServicePreconditionFailedException` durante un intento de actualización si el registro ha cambiado desde la última consulta. La aplicación podrá elegir si confirmar el cambio realizado en la base de datos o dejar intacto el último cambio realizado en la base de datos. Para obtener más información acerca de las propiedades del sistema para Servicios móviles, consulte las [propiedades del sistema](http://go.microsoft.com/fwlink/?LinkId=331143).

1.  En el archivo default.js, debajo de la declaración de la variable `todoTable`, agregue el código para incluir la propiedad del sistema **\_\_version** que permite la compatibilidad con la detección de conflictos de escritura.

         var todoTable = client.getTable('TodoItem');
         todoTable.systemProperties |= WindowsAzure.MobileServiceTable.SystemProperties.Version;

2.  Al agregar la propiedad del sistema `Version` a las propiedades del sistema de la tabla, la aplicación recibirá una notificación con una excepción `MobileServicePreconditionFailedException` durante una actualización si el registro ha cambiado desde la última consulta. Esta excepción será detectada en JavaScript como una función de error. El error incluye la última versión del elemento del servidor que se utiliza para resolver los conflictos. En default.js, actualice la función `updateTodoItem` para detectar el error y llame a una función `resolveDatabaseConflict`.

         var updateTodoItem = function (todoItem) {
           // Este código toma un elemento TodoItem recientemente completado y actualiza la base de datos. 
           // Si la versión de servidor del registro se ha actualizado, obtenemos el registro actualizado
           // del error Precondition Failed para resolver el conflicto.
           var serverItem = null;
           todoTable.update(todoItem).then(null, function (error) {
             if (error.message == "Precondition Failed") {
               serverItem = error.serverInstance;
             }
             else {
               var msgDialog =
                 new Windows.UI.Popups.MessageDialog(error.request.responseText,"Update Failed");
                 msgDialog.showAsync();
             }
           }).done(function () {
             if (serverItem != null)
               resolveDatabaseConflict(todoItem, serverItem);
           });
         };

3.  En default.js, agregue la definición para la función `resolveDatabaseConflict()` a la que se hace referencia en la función `updateTodoItem`. Tenga en cuenta que, para resolver el conflicto, debe configurar la versión local del elemento a la versión actualizada desde el servidor antes de actualizar el elemento en la base de datos. De lo contrario, experimentará continuamente un conflicto.

         var resolveDatabaseConflict = function (localItem, serverItem) {
           var content = "This record has been changed as follows on the server already..\n\n" +
               "id : " + serverItem.id + "\n" +
               "text : " + serverItem.text + "\n" +
               "complete : " + serverItem.complete + "\n\n" +
               "Do you want to overwrite the server instance with your data
         ";
           var msgDialog = new Windows.UI.Popups.MessageDialog(content, "Resolve Database Conflict");
           msgDialog.commands.append(new Windows.UI.Popups.UICommand("Yes"));
           msgDialog.commands.append(new Windows.UI.Popups.UICommand("No"));
           msgDialog.showAsync().done(function (command) {
               if (command.label == "Yes") {
                   localItem.__version = serverItem.__version;
                   updateTodoItem(localItem);
               }
           });
         }

Prueba de la aplicaciónPrueba de conflictos de escritura de bases de datos en la aplicación
-------------------------------------------------------------------------------------------

En esta sección, compilará un paquete de aplicación de la Tienda Windows para instalar la aplicación en un segundo equipo o en una máquina virtual. A continuación ejecutará la aplicación en los dos equipos con el objetivo de generar un conflicto de escritura para probar el código. Las dos instancias de la aplicación intentarán actualizar la misma propiedad `text` del elemento, lo cual exigirá que el usuario resuelva el conflicto.

1.  Cree un paquete de la aplicación de la Tienda Windows para instalarlo en un segundo equipo o en una máquina virtual. Para ello, haga clic en **Proyecto**-\>**Tienda**-\>**Crear paquetes de aplicaciones** en Visual Studio.

    ![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package1.png)

2.  En la pantalla Crear paquetes, haga clic en **No** para que este paquete no se cargue en la Tienda Windows. A continuación, haga clic en **Siguiente**.

    ![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package2.png)

3.  En la pantalla Seleccionar y configurar paquetes, acepte los valores predeterminados y haga clic en **Crear**.

    ![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package3.png)

4.  En la pantalla Creación de paquete completada, haga clic en el vínculo **Ubicación de salida** para abrir la ubicación del paquete.

	![][11]

5.  Copie la carpeta del paquete, "todolist\_1.0.0.0\_AnyCPU\_Debug\_Test", en el segundo equipo. En ese equipo, abra la carpeta del paquete y haga clic con el botón secundario en el script de PowerShell **Add-AppDevPackage.ps1** y haga clic en **Run with PowerShell** como se muestra a continuación. Siga las indicaciones para instalar la aplicación.

    ![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-install-app-package.png)

6.  Ejecute la instancia 1 de la aplicación en Visual Studio; para ello, haga clic en **Depurar**-\>**Iniciar depuración**. En la pantalla Inicio del segundo equipo, haga clic en la flecha abajo para ver "Aplicaciones por nombre". A continuación, haga clic en la aplicación **todolist** para ejecutar la instancia 2 de la aplicación.

    Instancia 1 de la aplicación 	
	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1.png)

    Instancia 2 de la aplicación 	
	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1.png)

7.  En la instancia 1 de la aplicación, actualice el texto del último elemento a **Test Write 1** y, a continuación, presione la tecla **Entrar** para actualizar la base de datos. La siguiente captura de pantalla muestra un ejemplo.

    Instancia 1 de la aplicación 	
	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write1.png)

    Instancia 2 de la aplicación 	
	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1.png)

8.  En este punto, el último elemento en la instancia 2 de la aplicación tiene una versión anterior del elemento. En esa instancia de la aplicación, escriba **Test Write 2** en la propiedad `text` del último elemento y presione **Entrar** para actualizar la base de datos con una propiedad `_version` antigua.

    Instancia 1 de la aplicación 	
	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write2.png)

    Instancia 2 de la aplicación 	
	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write2.png)

9.  Puesto que el valor de `__version` utilizado con el intento de actualización no coincidió con el valor de `__version` del servidor, el SDK de Servicios móviles lanza una excepción `MobileServicePreconditionFailedException` como error en la función `updateTodoItem` para permitir que la aplicación resuelva este conflicto. Para resolver el conflicto, puede hacer clic en **Yes** para confirmar los valores de la instancia 2. Alternativamente, haga clic en **No** para descartar los valores de la instancia 2, dejando así confirmados los valores de la instancia 1 de la aplicación.

    Instancia 1 de la aplicación 	
	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write2.png)

    Instancia 2 de la aplicación 	
	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write2-conflict.png)

Control de conflictos con scriptsControl automático de la resolución de conflictos en los scripts del servidor
--------------------------------------------------------------------------------------------------------------

Puede detectar y resolver conflictos de escritura en los scripts del servidor. Es una buena idea si puede utilizar la lógica de los scripts en lugar de la interacción del usuario para resolver el conflicto. En esta sección, agregará un script del servidor a la tabla TodoItem para la aplicación. La lógica que este script utilizará para resolver conflictos es la siguiente:

-   Si el campo `complete` de TodoItem está establecido en true, se considerará completado y el `texto` no se podrá modificar.
-   Si el  campo `complete` de TodoItem aún es false, tratará de actualizarse y el `texto` se confirmará.

Los siguientes pasos describen la incorporación del script de actualización del servidor y su prueba.

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, en su aplicación.

	![][7]

2.  Haga clic en la pestaña **Data** y, a continuación, haga clic en la tabla **TodoItem**.

	![][8]

3.  Haga clic en **Script** y, a continuación, seleccione la operación **Update**.

	![][9]

4.  Sustituya el script existente por la siguiente función y, a continuación, haga clic en **Save**.

         function update(item, user, request) { 
             request.execute({ 
                 conflict: function (serverRecord) {
                     // Confirmar cambios solo si el elemento no se ha completado.
                     if (serverRecord.complete === false) {
                         // Escribir el elemento actualizado en la tabla.
                         request.execute();
                     }
                     else
                     {
                         request.respond(statusCodes.FORBIDDEN, 'The item is already completed.');
                     }
                 }
             }); 
         }   

5.  Ejecute la aplicación **todolist** en los dos equipos. Cambie el valor de `text` de TodoItem del último elemento de la instancia 2 y presione **Entrar** para que la aplicación actualice la base de datos.

    Instancia 1 de la aplicación 	
	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write2.png)

    Instancia 2 de la aplicación 	
	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write2.png)

6.  En la instancia 1 de la aplicación, escriba un valor diferente para la última propiedad de texto y, a continuación, presione **Entrar**. La aplicación intenta actualizar la base de datos con una propiedad `__version` incorrecta.

    Instancia 1 de la aplicación 	
	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write3.png)

    Instancia 2 de la aplicación 	
	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write3.png)

7.  Observe que no se ha producido ninguna excepción en la aplicación desde que el script del servidor resolvió el conflicto, permitiendo así la actualización dado que el elemento no está marcado como completado. Para comprobar que la actualización fue realmente satisfactoria, haga clic en **Refresh** en la instancia 2 para volver a consultar la base de datos.

    Instancia 1 de la aplicación 	
	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-write3.png)

    Instancia 2 de la aplicación 	
	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-write3.png)

8.  En la instancia 1, haga clic en la casilla para completar el último elemento TodoItem.

    Instancia 1 de la aplicación 	
	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-checkbox.png)

    Instancia 2 de la aplicación 	
	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-write3.png)

9.  En la instancia 2, intente actualizar el último texto de TodoItem y presione **Entrar**. Esto ocasiona un conflicto porque se ha actualizado configurando el campo completo como true. En respuesta al conflicto, el script lo resolvió rechazando la actualización porque el elemento ya se había completado. El script proporcionó un mensaje en la respuesta.

    Instancia 1 de la aplicación 	
	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-2-items.png)

    Instancia 2 de la aplicación 	
	![](./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-already-complete.png)

Pasos siguientes
----------------

Este tutorial le ha mostrado cómo habilitar la aplicación de Tienda Windows para controlar los conflictos de escritura al trabajar con datos en Servicios móviles. A continuación, plantéese completar uno de los siguientes tutoriales en nuestra serie de datos:

-   [Validación y modificación de datos con scripts](/en-us/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/)
    Obtenga más información acerca del uso de scripts de servidor en Servicios móviles para validar y cambiar datos enviados desde su aplicación.

-   [Limitación de consultas con paginación](/en-us/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/)
    Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos que se manejan en una única solicitud.

Una vez que haya completado la serie de datos, también puede probar uno de los siguientes tutoriales de la Tienda Windows:

-   [Introducción a la autenticación](/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-users/)
    Aprenda a autenticar a los usuarios de su aplicación.

-   [Introducción a las notificaciones de inserción](/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-push/)
    Aprenda a enviar una notificación de inserción muy básica a la aplicación con Servicios móviles.

<!-- Anchors. -->
[Update the application to allow updates]: #uiupdate
[Enable Conflict Detection in your application]: #enableOC
[Test database write conflicts in the application]: #test-app
[Automatically handling conflict resolution in server scripts]: #scriptsexample
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package1.png
[1]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package2.png
[2]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1.png 
[3]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write1.png
[4]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write2.png
[5]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write2.png
[6]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write2-conflict.png
[7]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-services-selection.png
[8]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-portal-data-tables.png
[9]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-insert-script-users.png
[10]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package3.png
[11]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-create-app-package4.png
[12]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-install-app-package.png
[13]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app1-write3.png
[14]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-app2-write3.png
[15]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-write3.png
[16]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-checkbox.png
[17]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-2-items.png
[18]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/Mobile-oc-store-already-complete.png
[19]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-manage-nuget-packages-VS.png
[20]: ./media/mobile-services-windows-store-javascript-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png

<!-- URLs. -->
[Optimistic Concurrency Control]: http://go.microsoft.com/fwlink/?LinkId=330935
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started/#create-new-service
[Windows Azure Account]: http://www.windowsazure.com/en-us/pricing/free-trial/
[Validate and modify data with scripts]: /en-us/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/
[Refine queries with paging]: /en-us/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started
[Get started with authentication]: /en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-push/

[Windows Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Developer Code Samples site]:  http://go.microsoft.com/fwlink/p/?LinkId=271146
[System Properties]: http://go.microsoft.com/fwlink/?LinkId=331143


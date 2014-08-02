<properties linkid="develop-mobile-tutorials-optimistic-concurrent-data-wp8" urlDisplayName="Optimistic concurrency" pageTitle="Handle database write conflicts with optimistic concurrency (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to handle database write conflicts on both the server and in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling database write conlicts" authors="" />

Control de conflictos de escritura de bases de datos
====================================================

[C\# para Tienda Windows](/en-us/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/ "C# para Tienda Windows") [JavaScript para Tienda Windows](/en-us/develop/mobile/tutorials/handle-database-write-conflicts-javascript/ "JavaScript para Tienda Windows") [Windows Phone](/en-us/develop/mobile/tutorials/handle-database-write-conflicts-wp8/ "Windows Phone")

Este tutorial tiene como finalidad ayudarle a comprender mejor cómo controlar los conflictos que se producen cuando dos o más clientes escriben en el mismo registro de la base de datos en una aplicación de Windows Phone 8. En algunos casos, dos o más clientes pueden escribir cambios en el mismo elemento y al mismo tiempo. Si no se produjera la detección de conflictos, la última escritura sobrescribiría cualquier actualización anterior incluso si no fuese el resultado deseado. Servicios móviles proporciona la compatibilidad para detectar y solucionar estos conflictos. Este tema le guiará a través de los pasos que le permitirán controlar los conflictos de escritura de bases de datos tanto en el servidor como en la aplicación.

En este tutorial agregará funcionalidad a la aplicación de inicio rápido para controlar los conflictos que se produzcan al actualizar la base de datos TodoItem. Este tutorial le guiará a través de estos pasos básicos:

1.  [Actualización de la aplicación para permitir actualizaciones](#uiupdate)
2.  [Habilitación de la detección de conflictos en la aplicación](#enableOC)
3.  [Prueba de conflictos de escritura de bases de datos en la aplicación](#test-app)
4.  [Control automático de la resolución de conflictos en los scripts del servidor](#scriptsexample)

Este tutorial requiere lo siguiente:

-   Microsoft Visual Studio 2012 Express para Windows Phone 8 o posterior.
-   [SDK de Windows Phone 8](http://go.microsoft.com/fwlink/p/?LinkID=268374) que se ejecute en Windows 8.
-   [Cuenta de Azure](http://www.windowsazure.com/en-us/pricing/free-trial/)
-   Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar [Introducción a los Servicios móviles](/en-us/develop/mobile/tutorials/get-started-wp8).
-   Paquete de NuGet de Servicios móviles de Azure 1.1.0 o posterior. Para obtener la última versión, siga los pasos a continuación:
    1.  En Visual Studio, abra el proyecto y haga clic con el botón secundario en el proyecto en el Explorador de soluciones y, a continuación, haga clic en **Administrar paquetes de NuGet**.

		![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-manage-nuget-packages-VS.png)


    2.  Expanda **En línea** y haga clic en **Microsoft and .NET**. En el cuadro de texto de búsqueda, escriba **Servicios móviles de Azure**. Haga clic en **Instalar** en el paquete de NuGet de **Servicios móviles de Azure**.

		![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png)

Actualización de la interfaz de usuarioActualización de la aplicación para permitir actualizaciones
---------------------------------------------------------------------------------------------------

En esta sección, actualizará la interfaz de usuario TodoList para permitir la actualización del texto de cada elemento en un control ListBox. ListBox contendrá un control de CheckBox y TextBox para cada elemento en la tabla de la base de datos. Podrá actualizar el campo de texto de TodoItem. La aplicación controlará el evento `LostFocus` desde TextBox para actualizar el elemento en la base de datos.

1.  En Visual Studio, abra el proyecto TodoList que descargó en el tutorial [Introducción a los Servicios móviles](/en-us/develop/mobile/tutorials/get-started-wp8).
2.  En el Explorador de soluciones de Visual Studio, abra MainPage.xaml y reemplace la definición `phone:LongListSelector` por el ListBox que aparece a continuación y guarde los cambios.

         <ListBox Grid.Row="4" Grid.ColumnSpan="2" Name="ListItems">
             <ListBox.ItemTemplate>
                 <DataTemplate>
                     <StackPanel Orientation="Horizontal">
                         <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" Checked="CheckBoxComplete_Checked" Margin="10,5" VerticalAlignment="Center"/>
                         <TextBox x:Name="ToDoText" Width="330" Text="{Binding Text, Mode=TwoWay}" AcceptsReturn="False" LostFocus="ToDoText_LostFocus"/>
                     </StackPanel>
                 </DataTemplate>
             </ListBox.ItemTemplate>
         </ListBox>

3.  En el Explorador de soluciones de Visual Studio, abra MainPage.xaml.cs y agregue la siguiente directiva `using`.

         using System.Threading.Tasks;

4.  En el Explorador de soluciones de Visual Studio, abra MainPage.xaml.cs. Agregue el controlador de eventos a la MainPage para el evento `LostFocus` de TextBox como se muestra a continuación.

         private async void ToDoText_LostFocus(object sender, RoutedEventArgs e)
         {
             TextBox tb = (TextBox)sender;
             TodoItem item = tb.DataContext as TodoItem;
             //veamos si cambió el texto
             if (item.Text != tb.Text)
             {
                 item.Text = tb.Text;
                 await UpdateToDoItem(item);
             }
         }

5.  En MainPage.xaml.cs, agregue la definición para el método `UpdateToDoItem()` de MainPage con referencia en el controlador de eventos, como se muestra a continuación.

         private async Task UpdateToDoItem(TodoItem item)
         {
             try
             {
                 //actualizar en la tabla remota
                 await todoTable.UpdateAsync(item);
             }
             catch (Exception ex)
             {
                 MessageBox.Show(ex.Message, "Update Failed", MessageBoxButton.OK);
             }
         }

Ahora la aplicación escribe los cambios del texto en cada elemento devuelto a la base de datos cuando TextBox pierde el enfoque.

Activación de la simultaneidad optimistaActivación de la detección de conflictos en la aplicación
-------------------------------------------------------------------------------------------------

En algunos casos, dos o más clientes pueden escribir cambios en el mismo elemento y al mismo tiempo. Si no se produjera la detección de conflictos, la última escritura sobrescribiría cualquier actualización anterior incluso si no fuese el resultado deseado. El [control de simultaneidad optimista](http://go.microsoft.com/fwlink/?LinkId=330935) asume que cada transacción puede confirmarse y, por lo tanto, no usa ningún bloqueo de recursos. Antes de confirmar una transacción, el control de simultaneidad optimista comprueba que ninguna otra transacción haya modificado los datos. Si los datos se han modificado, la transacción de confirmación se desecha. Servicios móviles de Azure es compatible con el control de simultaneidad optimista mediante el seguimiento de cambios en cada elemento con la columna de propiedades del sistema `__version` que se agrega en cada tabla. En esta sección, activaremos la aplicación para detectar estos conflictos de escritura a través de la propiedad del sistema `__version`. La aplicación recibirá una notificación mediante una excepción `MobileServicePreconditionFailedException` durante un intento de actualización si el registro ha cambiado desde la última consulta. Entonces podrá elegir si confirmar el cambio realizado en la base de datos o dejar intacto el último cambio realizado en la base de datos. Para obtener más información acerca de las propiedades del sistema para Servicios móviles, consulte las [propiedades del sistema](http://go.microsoft.com/fwlink/?LinkId=331143).

1.  En MainPage.xaml.cs, actualice la definición de clase de **TodoItem** con el siguiente código para incluir la propiedad del sistema **\_\_version** que permite la compatibilidad para la detección de conflictos de escritura:

         public class TodoItem
         {
             public string Id { get; set; }            
             [JsonProperty(PropertyName = "text")]
             public string Text { get; set; }            
             [JsonProperty(PropertyName = "complete")]
             public bool Complete { get; set; }            
             [JsonProperty(PropertyName = "__version")]
             public string Version { set; get; }
         }

    **Nota:**

    Cuando use tablas sin tipo, habilite la simultaneidad optimista agregando la marca de versión en las SystemProperties de la tabla.

    ``` {}
    //Habilitar la simultaneidad optimista mediante retrieving __version
    todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
    ```

2.  Al agregar la propiedad `Version` a la clase `TodoItem`, la aplicación recibirá una notificación con una excepción `MobileServicePreconditionFailedException` durante una actualización si el registro ha cambiado desde la última consulta. Esta excepción incluye la última versión del elemento desde el servidor. En MainPage.xaml.cs, agregue el siguiente código para controlar la excepción en el método `UpdateToDoItem()`.

         private async Task UpdateToDoItem(TodoItem item)
         {
             Exception exception = null;
             try
             {
                 //actualizar en la tabla remota
                 await todoTable.UpdateAsync(item);
             }
             catch (MobileServicePreconditionFailedException<TodoItem> writeException)
             {
                 exception = writeException;
             }
             catch (Exception ex)
             {
                 exception = ex;
             }
             if (exception != null)
             {
                 if (exception is MobileServicePreconditionFailedException<TodoItem>)
                 {
                     //Conflicto detectado, el elemento ha cambiado desde la última consulta
                     await ResolveConflict(item, ((MobileServicePreconditionFailedException<TodoItem>)exception).Item);
                 }
                 else
                     MessageBox.Show(exception.Message, "Update Failed", MessageBoxButton.OK);
             }
         }

3.  En MainPage.xaml.cs, agregue la definición para el método `ResolveConflict()` con referencia en `UpdateToDoItem()`. Tenga en cuenta que, para resolver el conflicto, debe configurar la versión local del elemento a la versión actualizada desde el servidor antes de confirmar la decisión del usuario. De lo contrario, experimentará continuamente el conflicto.

         private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)     
         {
             // Pedir al usuario que elija entre el valor de texto local o dejar el 
             // valor de texto actualizado del servidor
             MessageBoxResult mbRes = MessageBox.Show(String.Format("The item has already been updated on the server.\n\n" +
                                                                    "Server value: {0} \n" +
                                                                    "Local value: {1}\n\n" +
                                                                    "Press OK to update the server with the local value.\n\n" +
                                                                    "Press CANCEL to keep the server value.", serverItem.Text, localItem.Text), 
                                                                    "CONFLICT DETECTED ", MessageBoxButton.OKCancel);
             // Aceptar : Después de examinar el texto actualizado desde el servidor, sobrescribirlo
             //      con los cambios hechos en este cliente.
             if (mbRes == MessageBoxResult.OK)
             {
                 // Actualizar la versión del elemento a la versión más reciente
                 // para solucionar el conflicto. De lo contrario, la excepción
                 // aparecerá nuevamente para la actualización intentada.
                 localItem.Version = serverItem.Version;
                 // Actualización recursiva solo en caso de que otro conflicto 
                 // se produzca mientras el usuario toma una decisión.
                 await this.UpdateToDoItem(localItem);
             }
             // Cancelar : Después de examinar el texto actualizado desde el servidor, dejar 
             // intacto el elemento de servidor y actualizar la consulta de este cliente descartando 
             // los cambios propuestos.
             if (mbRes == MessageBoxResult.Cancel)
             {
                 RefreshTodoItems();
             }
         }

Prueba de la aplicaciónPrueba de conflictos de escritura de bases de datos en la aplicación
-------------------------------------------------------------------------------------------

En esta sección, probará el código que controla los conflictos de escritura mediante la ejecución de la aplicación en dos emuladores de Windows Phone 8 distintos (WVGA y WVGA 512M). Ambas aplicaciones cliente intentarán actualizar la misma propiedad `text` del elemento, lo cual exigirá que el usuario resuelva el conflicto.

1.  En Visual Studio, asegúrese de que la opción **Emulator WVGA 512MB (ES)** esté seleccionada en el cuadro desplegable como el destino de implementación, tal como aparece en la captura de pantalla a continuación.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-EmulatorWVGA512MB.png)

2.  En el menú de Visual Studio, haga clic en **GENERAR** y, a continuación, en **Implementar solución**. Si el emulador no estaba en ejecución, tardará unos minutos en cargar el sistema operativo de Windows Phone 8. Compruebe en la ventana de salida que aparece en la parte inferior que la compilación y la implementación en el emulador de Windows Phone 8 se realizaron correctamente.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-build-deploy-wp8.png)

3.  En Visual Studio, cambie el cuadro desplegable de destino de implementación a **Emulator WVGA (ES)**.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-EmulatorWVGA.png)

4.  En el menú de Visual Studio, haga clic en **GENERAR** y, a continuación, en **Implementar solución**. Compruebe en la ventana de salida que aparece en la parte inferior que la compilación y la implementación en el emulador de Windows Phone 8 se realizaron correctamente.

	![][2]

5.  Coloque ambos emuladores en ejecución en paralelo. Podemos simular conflictos de escritura simultánea entre las aplicaciones cliente que se ejecutan en estos emuladores. Deslícese de derecha a izquierda en ambos emuladores para ver la lista de aplicaciones instaladas. Desplácese a la parte inferior de cada lista y haga clic en la aplicación **todolist**.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-start-apps-oc-wp8.png)

6.  En el emulador de la izquierda, actualice el valor `text` del último TodoItem a **Test Write 1** y, a continuación, haga clic en otro cuadro de texto para que el controlador de eventos `LostFocus` actualice la base de datos. La siguiente captura de pantalla muestra un ejemplo.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-write1-wp8.png)

7.  En este punto, el elemento correspondiente del emulador a la derecha tiene una versión anterior y un valor de texto anterior. En el emulador de la derecha, escriba **Test Write 2** para la propiedad de texto. A continuación, haga clic en otro cuadro de texto para que el controlador de eventos `LostFocus` del emulador de la derecha intente actualizar la base de datos con la versión antigua.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-write2-wp8.png)

8.  Como la versión usada con el intento de actualización no coincidió con la versión del servidor, el SDK de los Servicios móviles arroja la excepción `MobileServicePreconditionFailedException`, lo que permite que la aplicación solucione este conflicto. Para solucionar el conflicto, puede hacer clic en **Aceptar** para confirmar los valores de la aplicación de la derecha. También puede hacer clic en **Cancelar** para descargar los valores de la aplicación de la derecha, confirmando así los valores de la aplicación de la izquierda.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-exception-wp8.png)

Control de conflictos con scriptsControl automático de la resolución de conflictos en los scripts del servidor
--------------------------------------------------------------------------------------------------------------

Puede detectar y resolver conflictos de escritura en los scripts del servidor. Es una buena idea si puede utilizar la lógica de los scripts en lugar de la interacción del usuario para resolver el conflicto. En este sección, agregará un script del servidor a la tabla TodoItem para la aplicación. La lógica que este script utilizará para resolver conflictos es la siguiente:

-   Si el `campo complete de TodoItem está establecido en true, se considerará completado y el `texto no se podrá modificar.
-   Si el ` campo complete de TodoItem aún es false, tratará de actualizarse y el `texto se confirmará.

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
                         //escribir el elemento actualizado en la tabla
                         request.execute();
                     }
                     else
                     {
                         request.respond(statusCodes.FORBIDDEN, 'The item is already completed.');
                     }
                 }
             }); 
         }   

5.  Cambie el texto TodoItem por el último elemento en la aplicación del emulador de la izquierda. Luego haga clic en otro cuadro de texto para que el controlador de eventos `LostFocus` actualice la base de datos.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-write1-wp8.png)

6.  En el emulador de la derecha, escriba un valor distinto para la propiedad de texto del último TodoItem. A continuación, haga clic en otro cuadro de texto para que el controlador de eventos `LostFocus` del emulador de la derecha intente actualizar la base de datos con la versión antigua.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-write2-wp8.png)

7.  Observe que no se ha producido ninguna excepción en la aplicación desde que el script del servidor resolvió el conflicto, permitiendo así la actualización dado que el elemento no está marcado como completo. Para comprobar que la actualización se realizó correctamente, haga clic en **Refresh** en la aplicación del emulador de la izquierda para volver a consultar la base de datos.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-insync-wp8.png)

8.  En la aplicación del emulador de la izquierda, haga clic en la casilla para completar el último TodoItem.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-complete-checkbox-wp8.png)

9.  En la aplicación del emulador de la derecha, intente actualizar el mismo texto de TodoItem y active el evento `LostFocus`. En respuesta al conflicto, el script lo resolvió rechazando la actualización porque el elemento ya se había completado.

    ![](./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-already-completed-wp8.png)

Pasos siguientes
----------------

Este tutorial le ha mostrado cómo habilitar la aplicación de Windows Phone 8 para controlar los conflictos de escritura al trabajar con datos en Servicios móviles. A continuación, considere completar uno de los siguientes tutoriales en nuestra serie sobre datos:

-   [Validación y modificación de datos con scripts](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-wp8)
    Obtenga más información acerca del uso de scripts de servidor en Servicios móviles para validar y cambiar datos enviados desde su aplicación.

-   [Limitación de consultas con paginación](/en-us/develop/mobile/tutorials/add-paging-to-data-wp8)
    Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos que se manejan en una única solicitud.

Una vez que haya completado la serie de datos, también puede probar uno de los siguientes tutoriales de Windows Phone 8:

-   [Introducción a la autenticación](/en-us/develop/mobile/tutorials/get-started-with-users-wp8)
    Aprenda a autenticar a los usuarios de su aplicación.

-   [Introducción a las notificaciones de inserción](/en-us/develop/mobile/tutorials/get-started-with-push-wp8)
    Aprenda a enviar una notificación de inserción muy básica a la aplicación con Servicios móviles.


<!-- Anchors. -->
[Update the application to allow updates]: #uiupdate
[Enable Conflict Detection in your application]: #enableOC
[Test database write conflicts in the application]: #test-app
[Automatically handling conflict resolution in server scripts]: #scriptsexample
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-EmulatorWVGA512MB.png
[1]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-EmulatorWVGA.png
[2]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-build-deploy-wp8.png
[3]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-start-apps-oc-wp8.png
[4]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-write1-wp8.png
[5]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-write2-wp8.png
[6]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-exception-wp8.png
[7]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-services-selection.png
[8]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-portal-data-tables.png
[9]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-insert-script-users.png
[10]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-insync-wp8.png
[11]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-complete-checkbox-wp8.png
[12]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-oc-apps-already-completed-wp8.png
[13]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-manage-nuget-packages-VS.png
[14]: ./media/mobile-services-windows-phone-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png


<!-- URLs. -->
[Optimistic Concurrency Control]: http://go.microsoft.com/fwlink/?LinkId=330935
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started/#create-new-service
[Azure Account]: http://www.windowsazure.com/en-us/pricing/free-trial/
[Validate and modify data with scripts]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
[Refine queries with paging]: /en-us/develop/mobile/tutorials/add-paging-to-data-wp8
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-wp8
[Get started with data]: ./mobile-services-get-started-with-data-wp8.md
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-wp8
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-wp8

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Developer Code Samples site]:  http://go.microsoft.com/fwlink/p/?LinkId=271146
[System Properties]: http://go.microsoft.com/fwlink/?LinkId=331143
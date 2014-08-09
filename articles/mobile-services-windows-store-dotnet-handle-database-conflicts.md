<properties linkid="develop-mobile-tutorials-optimistic-concurrent-data-dotnet" urlDisplayName="Optimistic concurrency" pageTitle="Handle database write conflicts with optimistic concurrency (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to handle database write conflicts on both the server and in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling database write conflicts" authors="" />

Control de conflictos de escritura de bases de datos
====================================================

[C\# para Tienda Windows](/es-es/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/ "C# para Tienda Windows") [JavaScript para Tienda Windows](/es-es/develop/mobile/tutorials/handle-database-write-conflicts-javascript/ "JavaScript para Tienda Windows") [Windows Phone](/es-es/develop/mobile/tutorials/handle-database-write-conflicts-wp8/ "Windows Phone")

Este tutorial se ha elaborado para ayudarle a comprender mejor cómo controlar los conflictos que se producen cuando dos o más clientes escriben en el mismo registro de la base de datos en una aplicación de la Tienda Windows. En algunos casos, dos o más clientes pueden escribir cambios en el mismo elemento y al mismo tiempo. Si no se produjera la detección de conflictos, la última escritura sobrescribiría cualquier actualización anterior incluso si no fuese el resultado deseado. Los Servicios móviles de Azure ofrecen soporte para detectar y resolver estos conflictos. Este tema le guiará a través de los pasos que le permitirán controlar los conflictos de escritura de bases de datos tanto en el servidor como en la aplicación.

En este tutorial agregará funcionalidad a la aplicación de inicio rápido para controlar los conflictos que se produzcan al actualizar la base de datos TodoItem. Este tutorial le guiará a través de estos pasos básicos:

1.  [Actualización de la aplicación para permitir actualizaciones](#uiupdate)
2.  [Habilitación de la detección de conflictos en la aplicación](#enableOC)
3.  [Prueba de conflictos de escritura de bases de datos en la aplicación](#test-app)
4.  [Control automático de la resolución de conflictos en los scripts del servidor](#scriptsexample)

Este tutorial requiere lo siguiente:

-   Microsoft Visual Studio 2012 Express para Windows o posterior.
-   Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar [Introducción a los Servicios móviles](/es-es/develop/mobile/tutorials/get-started).
-   [Cuenta de Azure](http://www.windowsazure.com/es-es/pricing/free-trial/)
-   Paquete de NuGet de Servicios móviles de Azure 1.1.0 o posterior. Para obtener la última versión, siga los pasos a continuación:
    1.  En Visual Studio, abra el proyecto y haga clic con el botón secundario en el proyecto en el Explorador de soluciones y, a continuación, haga clic en **Administrar paquetes de NuGet**.

        ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-manage-nuget-packages-VS.png)

    2.  Expanda **En línea** y haga clic en **Microsoft and .NET**. En el cuadro de texto de búsqueda, escriba **Servicios móviles de Azure**. Haga clic en **Instalar** en el paquete de NuGet de **Servicios móviles de Azure**.

        ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png)

Actualización de la interfaz de usuarioActualización de la aplicación para permitir actualizaciones
---------------------------------------------------------------------------------------------------

En esta sección, actualizará la interfaz de usuario TodoList para permitir la actualización del texto de cada elemento en un control ListBox. ListBox contendrá un control TextBox y CheckBox para cada elemento de la tabla de base de datos. Podrá actualizar el campo de texto de TodoItem. La aplicación controlará el evento `LostFocus` desde TextBox para actualizar el elemento en la base de datos.

1.  En Visual Studio, abra el proyecto TodoList que descargó en el tutorial [Introducción a los Servicios móviles](/es-es/develop/mobile/tutorials/get-started).
2.  En el Explorador de soluciones de Visual Studio, abra MainPage.xaml y sustituya la definición `ListView` por la `ListView` que se muestra a continuación y guarde el cambio.

         <ListView Name="ListItems" Margin="62,10,0,0" Grid.Row="1">
             <ListView.ItemTemplate>
                 <DataTemplate>
                     <StackPanel Orientation="Horizontal">
                         <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" Checked="CheckBoxComplete_Checked" Margin="10,5" VerticalAlignment="Center"/>
                         <TextBox x:Name="ToDoText" Height="25" Width="300" Margin="10" Text="{Binding Text, Mode=TwoWay}" AcceptsReturn="False" LostFocus="ToDoText_LostFocus"/>
                     </StackPanel>
                 </DataTemplate>
             </ListView.ItemTemplate>
         </ListView>

3.  En MainPage.xaml.cs, agregue la siguiente directiva `using` en la parte superior de la página.

         using System.Threading.Tasks;

4.  En el Explorador de soluciones de Visual Studio, abra MainPage.xaml.cs. Agregue el controlador de eventos a la MainPage para el evento `LostFocus` de TextBox como se muestra a continuación.

         private async void ToDoText_LostFocus(object sender, RoutedEventArgs e)
         {
             TextBox tb = (TextBox)sender;
             TodoItem item = tb.DataContext as TodoItem;
             // Veamos si el texto se cambia.
             if (item.Text != tb.Text)
             {
                 item.Text = tb.Text;
                 await UpdateToDoItem(item);
             }
         }

5.  En MainPage.xaml.cs, agregue la definición para el método `UpdateToDoItem()` de MainPage con referencia en el controlador de eventos, como se muestra a continuación.

         private async Task UpdateToDoItem(TodoItem item)
         {
             Exception exception = null;            
             try
             {
                 //actualizar en la tabla remota
                 await todoTable.UpdateAsync(item);
             }
             catch (Exception ex)
             {
                 exception = ex;
             }          
             if (exception != null)
             {
                 await new MessageDialog(exception.Message, "Update Failed").ShowAsync();
             }
         }

Ahora la aplicación escribe los cambios del texto en cada elemento devuelto a la base de datos cuando TextBox pierde el enfoque.

Activación de la simultaneidad optimistaActivación de la detección de conflictos en la aplicación
-------------------------------------------------------------------------------------------------

En algunos casos, dos o más clientes pueden escribir cambios en el mismo elemento y al mismo tiempo. Si no se produjera la detección de conflictos, la última escritura sobrescribiría cualquier actualización anterior incluso si no fuese el resultado deseado. [El control de simultaneidad optimista](http://go.microsoft.com/fwlink/?LinkId=330935) asume que cada transacción puede confirmarse y, por lo tanto, no usa ningún bloqueo de recursos. Antes de confirmar una transacción, el control de simultaneidad optimista comprueba que ninguna otra transacción haya modificado los datos. Si los datos se han modificado, la transacción de confirmación se desecha. Servicios móviles de Azure es compatible con el control de simultaneidad optimista mediante el seguimiento de cambios en cada elemento con la columna de propiedades del sistema `__version`, que se agregó en cada tabla. En esta sección, activaremos la aplicación para detectar estos conflictos de escritura a través de la propiedad del sistema `__version`. La aplicación recibirá una notificación mediante una excepción `MobileServicePreconditionFailedException` durante un intento de actualización si el registro ha cambiado desde la última consulta. Entonces podrá elegir si confirmar el cambio realizado en la base de datos o dejar intacto el último cambio realizado en la base de datos. Para obtener más información acerca de las propiedades del sistema para Servicios móviles, consulte las [propiedades del sistema](http://go.microsoft.com/fwlink/?LinkId=331143).

1.  En MainPage.xaml.cs, actualice la definición de clase de **TodoItem** con el siguiente código para incluir la propiedad del sistema **\_\_version** y activar el soporte para la detección de conflictos de escritura.

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

    Al utilizar tablas no escritas, active la simultaneidad optimista; para ello, agregue la marca Version a las propiedades del sistema SystemProperties de la tabla.

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
                 if (exception is MobileServicePreconditionFailedException)
                 {
                     // Conflicto detectado, el elemento ha cambiado desde la última consulta
                     // Resolver el conflicto entre el elemento local y del servidor
                     await ResolveConflict(item, ((MobileServicePreconditionFailedException<TodoItem>) exception).Item);
                 }
                 else
                 {
                     await new MessageDialog(exception.Message, "Update Failed").ShowAsync();
                 }
             }
         }

3.  En MainPage.xaml.cs, agregue la definición para el método `ResolveConflict()` con referencia en `UpdateToDoItem()`. Tenga en cuenta que, para resolver el conflicto, debe configurar la versión local del elemento a la versión actualizada desde el servidor antes de confirmar la decisión del usuario. De lo contrario, experimentará continuamente el conflicto.

		private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
		{
			//Solicitar al usuario que seleccione la resolución entre versiones
			MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n", 
														serverItem.Text, localItem.Text), 
														"CONFLICT DETECTED - Select a resolution:");
			UICommand localBtn = new UICommand("Commit Local Text");
			UICommand ServerBtn = new UICommand("Leave Server Text");
			msgDialog.Commands.Add(localBtn);
			msgDialog.Commands.Add(ServerBtn);			
			localBtn.Invoked = async (IUICommand command) =>
			{
				// Para resolver el conflicto, actualice la versión del 
				// elemento que se va a confirmar. De lo contrario, seguirá
				// almacenando en caché MobileServicePreConditionFailedException.
				localItem.Version = serverItem.Version;				
				// Actualización recursiva aquí en caso de que 
				// se produzca otro cambio cuando el usuario está tomando una decisión.
				await UpdateToDoItem(localItem);
			};			
			ServerBtn.Invoked = async (IUICommand command) =>
			{
				RefreshTodoItems();
			};			
			await msgDialog.ShowAsync();
		}

Prueba de la aplicaciónPueba de conflictos de escritura de bases de datos en la aplicación
------------------------------------------------------------------------------------------

En esta sección, compilará un paquete de aplicación de la Tienda Windows para instalar la aplicación en un segundo equipo o en una máquina virtual. A continuación ejecutará la aplicación en los dos equipos con el objetivo de generar un conflicto de escritura para probar el código. Las dos instancias de la aplicación intentarán actualizar la misma propiedad `text` del elemento, lo cual exigirá que el usuario resuelva el conflicto.

1.  Cree un paquete de la aplicación de la Tienda Windows para instalarlo en un segundo equipo o en una máquina virtual. Para ello, haga clic en **Proyecto**-\>**Tienda**-\>**Crear paquetes de aplicaciones** en Visual Studio.

    ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package1.png)

2.  En la pantalla Crear paquetes, haga clic en **No** para que este paquete no se cargue en la Tienda Windows. A continuación, haga clic en **Siguiente**.

    ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package2.png)

3.  En la pantalla Seleccionar y configurar paquetes, acepte los valores predeterminados y haga clic en **Crear**.

    ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package3.png)

4.  En la pantalla Creación de paquete completada, haga clic en el vínculo **Ubicación de salida** para abrir la ubicación del paquete.

    ![][11]

5.  Copie la carpeta del paquete, "todolist\_1.0.0.0\_AnyCPU\_Debug\_Test", en el segundo equipo. En ese equipo, abra la carpeta del paquete y haga clic con el botón secundario en el script de PowerShell **Add-AppDevPackage.ps1** y haga clic en **Run with PowerShell** como se muestra a continuación. Siga las indicaciones para instalar la aplicación.

    ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-install-app-package.png)

6.  Ejecute la instancia 1 de la aplicación en Visual Studio; para ello, haga clic en **Depurar**-\>**Iniciar depuración**. En la pantalla Inicio del segundo equipo, haga clic en la flecha abajo para ver "Aplicaciones por nombre". A continuación, haga clic en la aplicación **todolist** para ejecutar la instancia 2 de la aplicación.

    Instancia 1 de la aplicación 

	![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1.png)

    Instancia 2 de la aplicación

	![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1.png)

7.  En la instancia 1 de la aplicación, actualice el texto del último elemento a **Test Write 1** y, a continuación, haga clic en otro cuadro de texto para que el controlador del evento `LostFocus` actualice la base de datos. La siguiente captura de pantalla muestra un ejemplo.

    Instancia 1 de la aplicación 

	![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write1.png)

    Instancia 2 de la aplicación 

	![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1.png)

8.  En este punto, el elemento correspondiente en la instancia 2 de la aplicación tiene una versión anterior del elemento. En la instancia de la aplicación, escriba **Test Write 2** para la propiedad `text`. A continuación, haga clic en otro cuadro de texto para que el controlador del evento `LostFocus` intente actualizar la base de datos con la propiedad `_version` anterior.

    Instancia 1 de la aplicación 

	![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write2.png)

    Instancia 2 de la aplicación 

	![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write2.png)

9.  Puesto que el valor `__version` utilizado con el intento de actualización no coincidió con el valor del servidor `__version`, el SDK de Servicios móviles lanza una excepción `MobileServicePreconditionFailedException` para permitir que la aplicación resuelva este conflicto. Para resolver el conflicto, puede hacer clic en **Commit Local Text** para confirmar los valores desde la instancia 2. Alternativamente, haga clic en **Leave Server Text** para descartar los valores en la instancia 2, dejando así confirmados los valores de la instancia 1 de la aplicación.

    Instancia 1 de la aplicación 

	![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write2.png)

    Instancia 2 de la aplicación 

	![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write2-conflict.png)

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

5.  Ejecute la aplicación **todolist** en los dos equipos. Cambie la propiedad `text` de TodoItem del último elemento en la instancia 2. A continuación, haga clic en otro cuadro de texto para que el controlador del evento `LostFocus` actualice la base de datos.

    Instancia 1 de la aplicación 

  ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write2.png)

    Instancia 2 de la aplicación 

  ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write2.png)

6.  En la instancia 1 de la aplicación, escriba un valor diferente para la última propiedad del texto. A continuación, haga clic en otro cuadro de texto para que el controlador del evento `LostFocus` intente actualizar la base de datos con una propiedad `_version` incorrecta.

    Instancia 1 de la aplicación 

  ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write3.png)

    Instancia 2 de la aplicación 

  ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write3.png)

7.  Observe que no se ha producido ninguna excepción en la aplicación desde que el script del servidor resolvió el conflicto, permitiendo así la actualización dado que el elemento no está marcado como completo. Para comprobar que la actualización fue realmente satisfactoria, haga clic en **Refresh** en la instancia 2 para volver a consultar la base de datos.

    Instancia 1 de la aplicación 

  ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-write3.png)

    Instancia 2 de la aplicación 

  ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-write3.png)

8.  En la instancia 1, haga clic en la casilla para completar el último elemento TodoItem.

    Instancia 1 de la aplicación 

  ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-checkbox.png)

    Instancia 2 de la aplicación 

  ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-write3.png)

9.  En la instancia 2, intente actualizar el último texto de TodoItem para activar el evento `LostFocus`. En respuesta al conflicto, el script lo resolvió rechazando la actualización porque el elemento ya se había completado.

    Instancia 1 de la aplicación 

  ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-2-items.png)

    Instancia 2 de la aplicación 

  ![](./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-already-complete.png)

Pasos siguientes
----------------

Este tutorial le ha mostrado cómo habilitar la aplicación de Tienda Windows para controlar los conflictos de escritura al trabajar con datos en Servicios móviles. A continuación, plantéese completar uno de los siguientes tutoriales en nuestra serie de datos:

-   [Validación y modificación de datos con scripts](/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet)
  <br/>Obtenga más información acerca del uso de scripts de servidor en Servicios móviles para validar y cambiar datos enviados desde su aplicación.

-   [Limitación de consultas con paginación](/es-es/develop/mobile/tutorials/add-paging-to-data-dotnet)
  <br/>Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos que se manejan en una única solicitud.

Una vez que haya completado la serie de datos, también puede probar uno de los siguientes tutoriales de la Tienda Windows:

-   [Introducción a la autenticación](/es-es/develop/mobile/tutorials/get-started-with-users-dotnet)
  <br/>Aprenda a autenticar a los usuarios de su aplicación.

-   [Introducción a las notificaciones de inserción](/es-es/develop/mobile/tutorials/get-started-with-push-dotnet)
  <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación con Servicios móviles.


<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package1.png
[1]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package2.png
[2]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1.png 
[3]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write1.png
[4]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write2.png
[5]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write2.png
[6]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write2-conflict.png
[7]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-services-selection.png
[8]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-portal-data-tables.png
[9]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-insert-script-users.png
[10]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package3.png
[11]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-create-app-package4.png
[12]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-install-app-package.png
[13]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app1-write3.png
[14]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-app2-write3.png
[15]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-write3.png
[16]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-checkbox.png
[17]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-2-items.png
[18]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/Mobile-oc-store-already-complete.png
[19]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-manage-nuget-packages-VS.png
[20]: ./media/mobile-services-windows-store-dotnet-handle-database-conflicts/mobile-manage-nuget-packages-dialog.png

<properties pageTitle="Control de conflictos de escritura de base de datos con la simultaneidad optimista (Tienda Windows) | Centro de desarrollo móvil" description="Obtenga información acerca de cómo controlar conflictos de escritura de base de datos tanto en el servidor como en su aplicación de la Tienda Windows." documentationCenter="windows" authors="wesmc7777" manager="dwrede" editor="" services=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc"/>

# Control de conflictos de escritura de bases de datos

<div class="dev-center-tutorial-selector sublanding">
<a href="/es-es/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/" title="Windows Store C#">C# para Tienda Windows</a>
<a href="/es-es/documentation/articles/mobile-services-windows-store-javascript-handle-database-conflicts/" title="Windows Store JavaScript">JavaScript para Tienda Windows</a>
<a href="/es-es/develop/mobile/tutorials/handle-database-write-conflicts-wp8/" title="Windows Phone" class="current">Windows Phone</a>
</div>

Este tutorial tiene como finalidad ayudarle a comprender mejor cómo controlar los conflictos que se producen cuando uno o más clientes escriben en el mismo registro de la base de datos en una aplicación de Windows Phone 8. En algunos casos, dos o más clientes pueden escribir cambios en el mismo elemento y al mismo tiempo. Si no se produjera la detección de conflictos, la última escritura sobrescribiría cualquier actualización anterior incluso si no fuese el resultado deseado. Servicios móviles proporciona la compatibilidad para detectar y solucionar estos conflictos. Este tema le guiará a través de los pasos que le permitirán controlar los conflictos de escritura de bases de datos tanto en el servidor como en la aplicación.

En este tutorial agregará funcionalidad a la aplicación de inicio rápido para controlar los conflictos que se produzcan al actualizar la base de datos TodoItem. Este tutorial le guiará a través de estos pasos básicos:

1. [Actualización de la aplicación para permitir actualizaciones]
2. [Habilitación de la detección de conflictos en la aplicación]
3. [Prueba de conflictos de escritura de bases de datos en la aplicación]
4. [Control automático de la resolución de conflictos en los scripts del servidor]


Este tutorial requiere lo siguiente:

+ Microsoft Visual Studio 2012 Express para Windows Phone 8 o posterior.
+ [SDK de Windows Phone 8] que se ejecute en Windows 8. 
+ [Cuenta de Azure]
+ Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar [Introducción a los Servicios móviles].
+ Paquete de NuGet de Servicios móviles de Azure 1.1.0 o posterior. Para obtener la última versión, siga los pasos a continuación:
	1. En Visual Studio, abra el proyecto y haga clic con el botón derecho en el proyecto en el Explorador de soluciones y, a continuación, haga clic en **Administrar paquetes de NuGet**. 

		![][13]

	2. Expanda **En línea** y haga clic en **Microsoft and .NET**. En el cuadro de texto de búsqueda, escriba **Servicios móviles de Azure**. Haga clic en **Instalar** en el paquete de NuGet de **Servicios móviles de Azure**.

		![][14]


 

<h2><a name="uiupdate"></a>Actualización de la aplicación para permitir actualizaciones</h2>

En esta sección, actualizará la interfaz de usuario TodoList para permitir la actualización del texto de cada elemento en un control ListBox. ListBox contendrá un control de CheckBox y TextBox para cada elemento en la tabla de la base de datos. Podrá actualizar el campo de texto de TodoItem. La aplicación controlará el evento  `LostFocus` desde TextBox para actualizar el elemento en la base de datos.


1. En Visual Studio, abra el proyecto TodoList que descargó en el tutorial [Introducción a los Servicios móviles].
2. En el Explorador de soluciones de Visual Studio, abra MainPage.xaml y reemplace la definición  `phone:LongListSelector` por el ListBox que aparece a continuación y guarde los cambios.

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


2. En el Explorador de soluciones de Visual Studio, abra MainPage.xaml.cs y agregue la siguiente directiva  `using`.

		using System.Threading.Tasks;


3. En el Explorador de soluciones de Visual Studio, abra MainPage.xaml.cs. Agregue el controlador de eventos a la MainPage para el evento  `LostFocus` de TextBox como se muestra a continuación.


        private async void ToDoText_LostFocus(object sender, RoutedEventArgs e)
        {
            TextBox tb = (TextBox)sender;
            TodoItem item = tb.DataContext as TodoItem;
            //let's see if the text changed
            if (item.Text != tb.Text)
            {
                item.Text = tb.Text;
                await UpdateToDoItem(item);
            }
        }

4. En MainPage.xaml.cs, agregue la definición para el método  `UpdateToDoItem()` de MainPage con referencia en el controlador de eventos, como se muestra a continuación.

        private async Task UpdateToDoItem(TodoItem item)
        {
            try
            {
                //update at the remote table
                await todoTable.UpdateAsync(item);
            }
            catch (Exception ex)
            {
                MessageBox.Show(ex.Message, "Update Failed", MessageBoxButton.OK);
            }
        }

Ahora la aplicación escribe los cambios del texto en cada elemento devuelto a la base de datos cuando TextBox pierde el enfoque.

<h2><a name="enableOC"></a>Habilitación de la detección de conflictos en la aplicación</h2>

En algunos casos, dos o más clientes pueden escribir cambios en el mismo elemento y al mismo tiempo. Si no se produjera la detección de conflictos, la última escritura sobrescribiría cualquier actualización anterior incluso si no fuese el resultado deseado. [El control de simultaneidad optimista] asume que cada transacción puede confirmarse y, por lo tanto, no usa ningún bloqueo de recursos. Antes de confirmar una transacción, el control de simultaneidad optimista comprueba que ninguna otra transacción haya modificado los datos. Si los datos se han modificado, la transacción de confirmación se desecha. Servicios móviles de Azure es compatible con el control de simultaneidad optimista mediante el seguimiento de cambios en cada elemento con la columna de propiedades del sistema `__version` que se agrega en cada tabla. En esta sección, habilitaremos la aplicación para detectar estos conflictos de escritura a través de la propiedad del sistema `__version`. La aplicación recibirá una notificación mediante una excepción  `MobileServicePreconditionFailedException` durante un intento de actualización si el registro ha cambiado desde la última consulta. Entonces podrá elegir si confirmar el cambio realizado en la base de datos o dejar intacto el último cambio realizado en la base de datos. Para obtener más información acerca de las propiedades del sistema para Servicios móviles, consulte las [propiedades del sistema].

1. En MainPage.xaml.cs, actualice la definición de clase de **TodoItem** con el siguiente código para incluir la propiedad del sistema **__version** que permite la compatibilidad para la detección de conflictos de escritura:

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

	> [AZURE.NOTE[ Cuando use tablas sin tipo, habilite la simultaneidad optimista agregando la marca de versión en las SystemProperties de la tabla.  
	>
	>````` 
	//Enable optimistic concurrency by retrieving __version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
`````


2. Al agregar la propiedad  `Version` a la clase  `TodoItem`, la aplicación recibirá una notificación con una excepción  `MobileServicePreconditionFailedException` durante una actualización si el registro ha cambiado desde la última consulta. Esta excepción incluye la última versión del elemento desde el servidor. En MainPage.xaml.cs, agregue el siguiente código para controlar la excepción en el método  `UpdateToDoItem()`.

        private async Task UpdateToDoItem(TodoItem item)
        {
            Exception exception = null;
            try
            {
                //update at the remote table
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
                    //conflict detected, the item has changed since the last query
                    await ResolveConflict(item, ((MobileServicePreconditionFailedException<TodoItem>)exception).Item);
                }
                else
                    MessageBox.Show(exception.Message, "Update Failed", MessageBoxButton.OK);
            }
        }


3. En MainPage.xaml.cs, agregue la definición para el método  `ResolveConflict()` al que se hace referencia en  `UpdateToDoItem()`. Observe que para resolver el conflicto, se establece la versión del elemento local en la versión actualizada desde el servidor antes de confirmar la decisión del usuario. De lo contrario, experimentará continuamente el conflicto.


        private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)		
        {
            // Ask user to choose between the local text value or leaving the 
			// server's updated text value
            MessageBoxResult mbRes = MessageBox.Show(String.Format("The item has already been updated on the server.\n\n" +
                                                                   "Server value: {0} \n" +
                                                                   "Local value: {1}\n\n" +
                                                                   "Press OK to update the server with the local value.\n\n" +
                                                                   "Press CANCEL to keep the server value.", serverItem.Text, localItem.Text), 
                                                                   "CONFLICT DETECTED ", MessageBoxButton.OKCancel);
            // OK : After examining the updated text from the server, overwrite it
            //      with the changes made in this client.
            if (mbRes == MessageBoxResult.OK)
            {
                // Update the version of the item to the latest version
                // to resolve the conflict. Otherwise the exception
                // will be thrown again for the attempted update.
                localItem.Version = serverItem.Version;
                // Recursively updating just in case another conflict 
				// occurs while the user is deciding.
                await this.UpdateToDoItem(localItem);
            }
            // CANCEL : After examining the updated text from the server, leave 
			// the server item intact and refresh this client's query discarding 
			// the proposed changes.
            if (mbRes == MessageBoxResult.Cancel)
            {
                RefreshTodoItems();
            }
        }



<h2><a name="test-app"></a>Prueba de conflictos de escritura de bases de datos en la aplicación</h2>

En esta sección, probará el código que controla los conflictos de escritura mediante la ejecución de la aplicación en dos emuladores de Windows Phone 8 distintos (WVGA y WVGA 512M). Ambas aplicaciones cliente intentarán actualizar la misma propiedad  `text` del elemento, lo cual exigirá que el usuario resuelva el conflicto.


1. En Visual Studio, asegúrese de que la opción **Emulator WVGA 512MB (ES)** esté seleccionada en el cuadro desplegable como el destino de implementación, tal como aparece en la captura de pantalla a continuación.

	![][0]

2. En el menú de Visual Studio, haga clic en **GENERAR** y, a continuación, en **Implementar solución**. Si el emulador no estaba en ejecución, tardará unos minutos en cargar el sistema operativo de Windows Phone 8. Compruebe en la ventana de salida que aparece en la parte inferior que la compilación y la implementación en el emulador de Windows Phone 8 se realizaron correctamente.

	![][2]

3. En Visual Studio, cambie el cuadro desplegable de destino de implementación a **Emulator WVGA (ES)**.

	![][1]

4. En el menú de Visual Studio, haga clic en **GENERAR** y, a continuación, en **Implementar solución**. Compruebe en la ventana de salida que aparece en la parte inferior que la compilación y la implementación en el emulador de Windows Phone 8 se realizaron correctamente.

   	![][2]
  
5. Coloque ambos emuladores en ejecución en paralelo. Podemos simular conflictos de escritura simultánea entre las aplicaciones cliente que se ejecutan en estos emuladores. Deslícese de derecha a izquierda en ambos emuladores para ver la lista de aplicaciones instaladas. Desplácese a la parte inferior de cada lista y haga clic en la aplicación **todolist**.

	![][3]

6. En el emulador de la izquierda, actualice el valor  `text` del último TodoItem a **Test Write 1** y, a continuación, haga clic en otro cuadro de texto para que el controlador de eventos  `LostFocus` actualice la base de datos. La siguiente captura de pantalla muestra un ejemplo. 

	![][4]

7. En este punto, el elemento correspondiente del emulador a la derecha tiene una versión anterior y un valor de texto anterior. En el emulador de la derecha, escriba **Test Write 2** para la propiedad de texto. A continuación, haga clic en otro cuadro de texto para que el controlador de eventos  `LostFocus` del emulador de la derecha intente actualizar la base de datos con la versión antigua.

	![][5]

8. Como la versión usada con el intento de actualización no coincidió con la versión del servidor, el SDK de los Servicios móviles arroja la excepción  `MobileServicePreconditionFailedException`, lo que permite que la aplicación solucione este conflicto. Para solucionar el conflicto, puede hacer clic en **Aceptar** para confirmar los valores de la aplicación de la derecha. También puede hacer clic en **Cancelar** para descargar los valores de la aplicación de la derecha, confirmando así los valores de la aplicación de la izquierda. 

	![][6]



<h2><a name="scriptsexample"></a>Control automático de la resolución de conflictos en los scripts del servidor</h2>

Puede detectar y resolver conflictos de escritura en los scripts del servidor. Es una buena idea si puede utilizar la lógica de los scripts en lugar de la interacción del usuario para resolver el conflicto. En esta sección, agregará un script del servidor a la tabla TodoItem para la aplicación. La lógica que este script utilizará para resolver conflictos es la siguiente:

+  Si el campo ` complete` de TodoItem está establecido en true, se considerará completado y  `text` no se podrá modificar.
+  Si el campo `complete` de TodoItem aún es false, tratará de actualizarse y  `text` se confirmará.

Los siguientes pasos describen la incorporación del script de actualización del servidor y su prueba.

1. Inicie sesión en el [Portal de administración de Azure], haga clic en **Servicios móviles** y, a continuación, en su aplicación. 

   	![][7]

2. Haga clic en la pestaña **Datos** y, a continuación, haga clic en la tabla **TodoItem**.

   	![][8]

3. Haga clic en **Script** y, a continuación, seleccione la operación **Update**.

   	![][9]

4. Sustituya el script existente por la siguiente función y, a continuación, haga clic en **Guardar**.

		function update(item, user, request) { 
			request.execute({ 
				conflict: function (serverRecord) {
					// Only committing changes if the item is not completed.
					if (serverRecord.complete === false) {
						//write the updated item to the table
						request.execute();
					}
					else
					{
						request.respond(statusCodes.FORBIDDEN, 'The item is already completed.');
					}
				}
			}); 
		}   
5. Cambie el texto TodoItem por el último elemento en la aplicación del emulador de la izquierda. Luego haga clic en otro cuadro de texto para que el controlador de eventos  `LostFocus` actualice la base de datos.

	![][4]

6. En el emulador de la derecha, escriba un valor distinto para la propiedad de texto del último TodoItem. A continuación, haga clic en otro cuadro de texto para que el controlador de eventos  `LostFocus` del emulador de la derecha intente actualizar la base de datos con la versión antigua.

	![][5]

7. Observe que no se ha producido ninguna excepción en la aplicación desde que el script del servidor resolvió el conflicto, permitiendo así la actualización dado que el elemento no está marcado como completo. Para comprobar que la actualización se realizó correctamente, haga clic en **Refresh** en la aplicación del emulador de la izquierda para volver a consultar la base de datos.

	![][10]

8. En la aplicación del emulador de la izquierda, haga clic en la casilla para completar el último TodoItem.

	![][11]

9. En la aplicación del emulador de la derecha, intente actualizar el mismo texto de TodoItem y active el evento  `LostFocus`. En respuesta al conflicto, el script lo resolvió rechazando la actualización porque el elemento ya se había completado. 

	![][12]


## <a name="next-steps"> </a>Pasos siguientes

Este tutorial le ha mostrado cómo habilitar la aplicación de Windows Phone 8 para controlar los conflictos de escritura al trabajar con datos en Servicios móviles. A continuación, plantéese completar uno de los siguientes tutoriales en nuestra serie de datos:

* [Validar y modificar datos con scripts]
  <br/>Obtenga más información sobre el uso de scripts de servidor en Servicios móviles para validar y cambiar los datos enviados desde su aplicación.

* [Limitación de consultas con paginación]
  <br/>Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos gestionados en una única solicitud.

Una vez que haya completado la serie de datos, también puede probar uno de los siguientes tutoriales de Windows Phone 8:

* [Introducción a la autenticación] 
  <br/>Aprenda la manera de autenticar a los usuarios de la aplicación.

* [Introducción a las notificaciones de inserción]
  <br/>Aprenda la manera de enviar una notificación de inserción muy básica a la aplicación con Servicios móviles.
 
<!-- Anchors. -->
[Actualización de la aplicación para permitir actualizaciones]: #uiupdate
[Habilitación de la detección de conflictos en la aplicación]: #enableOC
[Prueba de conflictos de escritura de bases de datos en la aplicación]: #test-app
[Control automático de la resolución de conflictos en los scripts del servidor]: #scriptsexample
[Pasos siguientes]:#next-steps

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
[Control de concurrencia optimista]: http://go.microsoft.com/fwlink/?LinkId=330935
[Introducción a los servicios móviles]: /es-es/develop/mobile/tutorials/get-started/#create-new-service
[Cuenta de Azure]: http://www.windowsazure.com/es-es/pricing/free-trial/
[Validar y modificar datos con scripts]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
[Limitación de consultas con paginación]: /es-es/develop/mobile/tutorials/add-paging-to-data-wp8
[Introducción a los servicios móviles]: /es-es/develop/mobile/tutorials/get-started-wp8
[Introducción a los datos]: ./mobile-services-get-started-with-data-wp8.md
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-wp8
[Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-wp8

[Portal de administración de Azure]: https://manage.windowsazure.com/
[Portal de administración]: https://manage.windowsazure.com/
[SDK de Windows Phone 8]: http://go.microsoft.com/fwlink/p/?LinkID=268374
[SDK de servicios móviles]: http://go.microsoft.com/fwlink/p/?LinkID=268375
[Sitio de ejemplos de código para desarrolladores]:  http://go.microsoft.com/fwlink/p/?LinkId=271146
[Propiedades del sistema]: http://go.microsoft.com/fwlink/?LinkId=331143


<!--HONumber=42-->

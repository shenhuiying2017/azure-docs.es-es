

Actualización de la aplicación para llamar a la API personalizada
-----------------------------------------------------------------

1.  En Visual Studio, abra el archivo MainPage.xaml en el proyecto de inicio rápido, busque el elemento **Button** llamado `ButtonRefresh` y reemplácelo por el siguiente código XAML:

         <StackPanel Orientation="Horizontal">
             <Button Margin="72,0,0,0" Name="ButtonRefresh" 
                     Click="ButtonRefresh_Click">Refresh</Button>
             <Button Margin="12,0,0,0" Name="ButtonCompleteAll" 
                     Click="ButtonCompleteAll_Click">Complete All</Button>
         </StackPanel>

    Esta acción agrega un nuevo botón a la página.

2.  Abra el archivo de código MainPage.xaml.cs y agregue el siguiente código de definición de clase:

         public class MarkAllResult
         {
             public int Count { get; set; }
         }

    Esta clase se usa para mantener el valor de recuento de filas que devuelve la API personalizada.

3.  Busque el método **RefreshTodoItems** en la clase **MainPage** y asegúrese de que `query` se defina usando el siguiente método **Where**:

         .Where(todoItem => todoItem.Complete == false)

    Esto filtra los elementos para que la consulta no devuelva los elementos completados.

4.  En la clase **MainPage**, agregue el siguiente método:

         private async void ButtonCompleteAll_Click(object sender, RoutedEventArgs e)
         {
             string message;
             try
             {
                 // De manera asincrónica, llamar a la API personalizada mediante el método POST. 
                 var result = await App.MobileService
                     .InvokeApiAsync<MarkAllResult>("completeAll", 
                     System.Net.Http.HttpMethod.Post, null);
                 message =  result.Count + " item(s) marked as complete.";
                 RefreshTodoItems();
             }
             catch (MobileServiceInvalidOperationException ex)
             {
                 message = ex.Message;                
             }
            
             var dialog = new MessageDialog(message);
             dialog.Commands.Add(new UICommand("OK"));
             await dialog.ShowAsync();
         }

    Este método controla el evento **Click** del nuevo botón. El método [InvokeApiAsync](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.invokeapiasync.aspx) se llama en el cliente, el cual envía una solicitud POST a la nueva API personalizada. El resultado devuelto por la API personalizada se muestra en un cuadro de diálogo de mensaje, al igual que todos los errores.

Prueba de la aplicación
-----------------------

1.  En Visual Studio, presione la tecla **F5** para recompilar el proyecto e iniciar la aplicación.

2.  En la aplicación, escriba algún texto en **Insert a TodoItem** y, a continuación, haga clic en **Save**.

3.  Repita el paso anterior hasta que haya agregado varios elementos todo a la lista.

4.  Haga clic en el botón **Complete All**.

	![](./media/mobile-services-windows-store-dotnet-call-custom-api/mobile-custom-api-windows-store-completed.png)

    Aparece un cuadro de diálogo de mensaje que indica el número de elementos marcados como completados y la consulta filtrada se vuelve a ejecutar, acción que borra todos los elementos de la lista.

<h2><a name="update-app"></a>Actualizaci&oacute;n de la aplicaci&oacute;n para llamar a la API personalizada</h2>

1.  En Visual Studio 2012 Express para Windows Phone, abra el archivo MainPage.xaml en el proyecto de inicio rápido, busque el elemento **Button** con el nombre `ButtonRefresh` y reemplácelo por el siguiente código XAML:

         <StackPanel Grid.Row="3" Grid.ColumnSpan="2" Orientation="Horizontal">
             <Button Width="225" Name="ButtonRefresh" 
                 Click="ButtonRefresh_Click">Refresh</Button>
             <Button Width="225"  Name="ButtonCompleteAll" 
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
            
             MessageBox.Show(message);  
         }

    Este método controla el evento **Click** del nuevo botón. Se llama al método **InvokeApiAsync** en el cliente, que envía una solicitud a la nueva API personalizada. El resultado devuelto por la API personalizada se muestra en un cuadro de diálogo de mensaje.

Prueba de la aplicación
-----------------------

1.  En Visual Studio, presione la tecla **F5** para recompilar el proyecto e iniciar la aplicación.

2.  En la aplicación, escriba algún texto en **Insert a TodoItem** y, a continuación, seleccione **Save**.

3.  Repita el paso anterior hasta que haya agregado varios elementos todo a la lista.

4.  Haga clic en el botón **Complete All**.

	![](./media/mobile-services-windows-phone-call-custom-api/mobile-custom-api-windows-phone-completed.png)

    Se mostrará un cuadro de mensaje que indica el número de elementos marcados como completados y la consulta filtrada se vuelve a ejecutar, por lo que se borran todos los elementos de la lista.

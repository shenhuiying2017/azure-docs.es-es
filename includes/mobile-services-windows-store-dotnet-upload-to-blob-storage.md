Actualización de la aplicación cliente de inicio rápido para capturar y cargar imágenes
---------------------------------------------------------------------------------------

1.  En Visual Studio 2012, abra el archivo Package.appxmanifest y, en la pestaña **Capacidades**, habilite las capacidades **Webcam** y **Microphone**.

	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-app-manifest-camera.png)
         
	Esto asegura que la aplicación puede utilizar una cámara conectada al equipo. A los usuarios se les solicitará el acceso a la cámara la primera vez que se ejecuta la aplicación.

2.  Abra el archivo MainPage.xaml y reemplace el elemento **StackPanel** directamente después del primer elemento **Task** por el siguiente código:

         <StackPanel Orientation="Horizontal" Margin="72,0,0,0">
             <TextBox Name="TextInput" Margin="5" MaxHeight="40" MinWidth="300"></TextBox>
             <Button Name="btnTakePhoto" Style="{StaticResource PhotoAppBarButtonStyle}"
                     Click="OnTakePhotoClick" />
             <Button Name="ButtonSave" Style="{StaticResource UploadAppBarButtonStyle}" 
                     Click="ButtonSave_Click"/>
         </StackPanel>

3.  Reemplace el elemento **StackPanel** de **DataTemplate** por el siguiente código:

         <StackPanel Orientation="Vertical">
             <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" 
                         Checked="CheckBoxComplete_Checked" Content="{Binding Text}" 
                         Margin="10,5" VerticalAlignment="Center"/>
             <Image Name="ImageUpload" Source="{Binding ImageUri, Mode=OneWay}"
                     MaxHeight="250"/>
         </StackPanel> 

        Esto agrega una imagen a **ItemTemplate** y establece su origen de enlace como el URI de la imagen cargada en el servicio de almacenamiento de blobs.

4.  Abra el archivo de proyecto MainPage.xaml.cs y agregue la siguiente instrucción **using**:

         using Windows.Media.Capture;
         using Windows.Storage;
         using Windows.UI.Popups;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Blob;

5.  En la clase TodoItem, agregue las siguientes propiedades:

         [JsonProperty(PropertyName = "containerName")]
         public string ContainerName { get; set; }
            
         [JsonProperty(PropertyName = "resourceName")]
         public string ResourceName { get; set; }
            
         [JsonProperty(PropertyName = "sasQueryString")]
         public string SasQueryString { get; set; }
            
         [JsonProperty(PropertyName = "imageUri")]
         public string ImageUri { get; set; } 

	>[WACOM.NOTE]Para agregar propiedades nuevas al objeto TodoItem, debe tener habilitado el esquema dinámico en el servicio móvil. Cuando el esquema dinámico está habilitado, automáticamente se agregan columnas nuevas a la tabla TodoItem que se asignan a estas nuevas propiedades.

6.  En la clase MainPage, agregue el siguiente código:

         // Usar StorageFile para mantener la imagen capturada para la carga.
         StorageFile media = null;
            
         private async void OnTakePhotoClick(object sender, RoutedEventArgs e)
         {
             // Capturar una foto o video nuevos desde el dispositivo.
             CameraCaptureUI cameraCapture = new CameraCaptureUI();
             media = await cameraCapture
                 .CaptureFileAsync(CameraCaptureUIMode.PhotoOrVideo);
         }

	Este código muestra la interfaz de usuario de la cámara para capturar una imagen y guarda la imagen en un archivo de almacenamiento.

1.  Reemplace el método actual `InsertTodoItem` por el siguiente código:

         private async void InsertTodoItem(TodoItem todoItem)
         {
             string errorString = string.Empty;
                
             if (media != null)
             {
                 // Establecer las propiedades de blob de TodoItem.
                 todoItem.ContainerName = "todoitemimages";
                 todoItem.ResourceName = media.Name;
             }
                
             // Enviar el elemento que se va a insertar. Cuando se establecen las propiedades de blob,
             // se genera una SAS en la respuesta.
             await todoTable.InsertAsync(todoItem);
                
             // Si se tiene una SAS devuelta, entonces se carga el blob.
             if (!string.IsNullOrEmpty(todoItem.SasQueryString))
             {
                 // Obtener el URI generado que contiene la SAS 
                 // y extraer las credenciales de almacenamiento.
                 StorageCredentials cred = new StorageCredentials(todoItem.SasQueryString);
                 var imageUri = new Uri(todoItem.ImageUri);
                    
                 // Crear una instancia de un contenedor de almacén de blobs basada en la información del elemento devuelto.
                 CloudBlobContainer container = new CloudBlobContainer(
                     new Uri(string.Format("https://{0}/{1}",
                         imageUri.Host, todoItem.ContainerName)), cred);

                 // Obtener la imagen nueva como una secuencia.
                 using (var fileStream = await media.OpenStreamForReadAsync())
                 {                                      
                     // Cargar la nueva imagen como un blob desde la secuencia.
                     CloudBlockBlob blobFromSASCredential =
                         container.GetBlockBlobReference(todoItem.ResourceName);
                     await blobFromSASCredential.UploadFromStreamAsync(fileStream.AsInputStream());
                 }
                    
                 // Cuando solicita una SAS en el nivel de contenedor en lugar de en el nivel de blob,
                 // puede cargar varias secuencias utilizando las mismas credenciales de contenedor.
             }
                
             // Agregar el elemento nuevo a la colección.
             items.Add(todoItem);
         }

    Este código envía una solicitud al servicio móvil para insertar un elemento TodoItem nuevo, incluido el nombre del archivo de imagen. La respuesta contiene la SAS, que luego se utiliza para insertar la imagen en el almacén de blobs, y el URI de la imagen para el enlace de datos.

El paso final es probar la aplicación y validar que se carga correctamente.

Prueba de carga de imágenes en la aplicación
--------------------------------------------

1.  En Visual Studio, presione la tecla F5 para ejecutar la aplicación.

2.  Escriba texto en el cuadro de texto en **Insert a TodoItem** y, a continuación, haga clic en **Photo**.

	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-appbar.png)

	Se muestra la interfaz de usuario de captura de la cámara.

1.  Haga clic en la imagen para tomar una foto y, a continuación, haga clic en **OK**.

	![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-camera.png)

2.  Haga clic en **Upload** para insertar el elemento nuevo y cargar la imagen.

    ![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-appbar2.png)

3.  El elemento nuevo, junto con la imagen cargada, se muestra en la vista de lista.

    ![](./media/mobile-services-windows-store-dotnet-upload-to-blob-storage/mobile-quickstart-blob-ie.png)

	>[WACOM.NOTE]La imagen se descarga automáticamente del servicio de almacenamiento de blobs cuando la propiedad <code>imageUri</code> del elemento nuevo se enlaza al control de <strong>Image</strong>.



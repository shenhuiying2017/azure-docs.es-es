Este tutorial se basa en la [aplicación GetStartedWithMobileServices][aplicación GetStartedWithMobileServices], que es un proyecto de aplicación universal de Windows en Visual Studio 2013. La interfaz de usuario de esta aplicación es idéntica a la aplicación generada por el inicio rápido de Servicios móviles, excepto en que los elementos agregados se almacenan localmente en la memoria.

1.  Descargue la versión C# de la aplicación de muestra GetStartedWithMobileServices desde el [sitio de ejemplos de código para desarrolladores].

2.  En Visual Studio 2013, abra el proyecto descargado y examine el archivo MainPage.xaml.cs encontrado en la carpeta de proyecto GetStartedWithData.Shared.

    Observe que los objetos **TodoItem** agregados se almacenan en **ObservableCollection\<TodoItem\>** en memoria.

3.  Presione la tecla **F5** para recompilar el proyecto e iniciar la aplicación.

4.  En la aplicación, escriba algún texto en **Insert a TodoItem** y, a continuación, haga clic en **Save**.

    ![][0]

    Se muestra el texto guardado.

5.  Haga clic con el botón secundario en el proyecto de Windows Phone 8.1, haga clic en **Establecer como proyecto de inicio** y presione **F5** para iniciar la aplicación de la Tienda Windows Phone.

    ![][1]

6.  Repita los pasos 3 y 4 para comprobar que la muestra se comporta del mismo modo.

  [aplicación GetStartedWithMobileServices]: http://go.microsoft.com/fwlink/p/?LinkID=510826
  [0]: ./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup.png
  [1]: ./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup-wp8.png

<properties
	pageTitle="Incorporación de Servicios móviles a una aplicación existente (Windows Phone) | Microsoft Azure"
	description="Obtenga información acerca de cómo empezar a usar Servicios móviles para aprovechar datos en su aplicación de Windows Phone."
	services="mobile-services"
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/18/2015" 
	ms.author="wesmc"/>

# Incorporación de Servicios móviles a una aplicación existente

##Información general

[AZURE.INCLUDE [mobile-services-selector-get-started-data-legacy](../../includes/mobile-services-selector-get-started-data-legacy.md)]

En este tema se muestra cómo agregar Servicios móviles de Azure como origen de datos back-end para una aplicación Silverlight de Windows Phone 8.1. En este tutorial descargará un proyecto de Visual Studio para una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil a la aplicación y verá los cambios que se hicieron en los datos durante la ejecución de la aplicación. Para agregar servicios móviles a una aplicación de tienda de Windows Phone 8.1, consulte [esta versión del tutorial](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md).

El servicio móvil que cree en este tutorial admitirá el tiempo de ejecución .NET. Esto le permitirá utilizar lenguajes .NET y Visual Studio para la lógica de negocios de servidor en el servicio móvil. Si desea crear un servicio móvil que le permita escribir su lógica de negocios de servidor en JavaScript, consulte la [versión back-end de JavaScript](mobile-services-windows-phone-get-started-data.md) de este tema.


##Requisitos previos

Este tutorial requiere lo siguiente:

+ Visual Studio Professional 2013 Update 2 o una versión posterior.
+ Una cuenta de Microsoft Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-es%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F" target="_blank">Evaluación gratuita de Azure￼</a>.

##Descarga del proyecto GetStartedWithData

Este tutorial se basa en la [aplicación GetStartedWithMobileServices](https://code.msdn.microsoft.com/Add-Azure-Mobile-to-a-8b906f72), un proyecto de aplicación Silverlight de Windows Phone 8.1 para Visual Studio 2013.

1. Descargue la versión de C# de la aplicación de ejemplo GetStartedWithMobileServices desde el [sitio de Muestras de código para desarrollador](https://code.msdn.microsoft.com/Add-Azure-Mobile-to-a-8b906f72) (en inglés).

	>[AZURE.NOTE]Para crear una aplicación Silverlight de Windows Phone 8.1, en Windows Phone 8.1 cambie el SO de destino en el proyecto de aplicación Silverlight de Windows Phone 8 descargado. Para crear una aplicación de la Tienda de Windows Phone, descargue la [versión de la aplicación de Windows Phone](http://go.microsoft.com/fwlink/p/?LinkId=397372) del proyecto de aplicación de ejemplo GetStartedWithData.

2. Ejecute Visual Studio con privilegios administrativos haciendo clic con el botón derecho en Visual Studio y, a continuación, haciendo clic en **Ejecutar como administrador**.

3. En Visual Studio, abra el proyecto descargado y examine el archivo MainPage.xaml.cs.

   	Observe que los objetos **TodoItem** agregados se almacenan en **ObservableCollection&lt;TodoItem&gt** en memoria.

4. En Visual Studio, elija un tipo de implementación para la aplicación. Puede implementarla en un dispositivo con Windows Phone o en uno de los emuladores incluidos en el SDK de Windows Phone. En este tutorial demostraremos cómo implementarla en un emulador.

5. Presione la tecla **F5**. De este modo, se creará, implementará e iniciará la aplicación para la depuración.

6. En la aplicación, escriba cualquier texto en el cuadro de texto y, a continuación, haga clic en **Guardar** para guardar unos cuantos elementos en la memoria de la aplicación.

   	![Aplicación con almacenamiento en memoria](./media/mobile-services-dotnet-backend-windows-phone-get-started-data/app-view.png)

   	Observe que el texto correspondiente a cada `TodoItem` aparece debajo del botón de actualización, junto con una casilla para marcar el elemento como completado.

##Creación de un servicio móvil

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]


##Descarga del proyecto de servicio móvil e incorporación a la solución

1. Si todavía no lo ha hecho, descargue e instale [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) o una versión posterior.

2. En el [Portal de administración de Azure](https://manage.windowsazure.com/), haga clic en el nuevo servicio móvil y en la página de inicio rápido, haga clic en la plataforma **Windows**, a continuación en **Introducción** y expanda **Conectar una aplicación de Windows o de Windows Phone existente**.

    ![Descarga el proyecto del servicio móvil](./media/mobile-services-dotnet-backend-windows-phone-get-started-data/download-service-project.png)

4. En **Descargar y publicar el servicio en la nube**, haga clic en **Descargar**.

	De este modo, se descarga el proyecto de Visual Studio que implementa el servicio móvil.

4. Descomprima la solución de introducción de servicio personalizado que descargó y copie las carpetas incluidas en el archivo zip en el mismo directorio **C#** donde se encuentra el archivo de la solución Introducción a los datos (.sln). Esto permitirá al Administrador de paquetes NuGet mantener todos los paquetes sincronizados más fácilmente.

5. En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en la solución correspondiente a la aplicación de la Tienda Windows Getting Started with Data. Haga clic en **Agregar** y, a continuación, en **Proyecto existente**.

6. En el cuadro de diálogo **Agregar proyecto existente**, vaya a la carpeta de proyecto de servicio móvil que movió al directorio **C#**, seleccione el archivo de proyecto de C# (.csproj) en el subdirectorio del servicio y haga clic en **Abrir** para agregar el proyecto a la solución.

7. En el Explorador de soluciones de Visual Studio, haga clic con el botón secundario en el proyecto de servicio que acaba de agregar y luego haga clic en **Compilar** para comprobar que se compile sin errores. Durante la compilación, el Administrador de paquetes NuGet puede necesitar restaurar algunos paquetes NuGet a los que se hace referencia en el proyecto.

8. Haga clic con el botón derecho de nuevo en el proyecto de servicio. En esta ocasión, haga clic en **Iniciar nueva instancia** en el menú **Depurar**.

    Visual Studio abre la página web predeterminada del servicio. Puede hacer clic en **Probar ahora** para probar los métodos del servicio móvil desde la página web predeterminada.

    ![Página "pruébelo ahora" del servicio móvil](./media/mobile-services-dotnet-backend-windows-phone-get-started-data/service-welcome-page.png)

    De forma predeterminada, Visual Studio hospedó el servicio móvil de manera local en IIS Express. Puede verlo haciendo clic con el botón derecho en el icono de bandeja correspondiente a IIS Express de la barra de tareas.


##Actualización de la aplicación Windows Phone para que utilice el servicio móvil

En esta sección, actualizará la aplicación de Windows Phone para utilizar el servicio móvil como servicio back-end de la aplicación.


1. En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en el proyecto de aplicación de Windows Phone y, a continuación, haga clic en **Administrar paquetes de NuGet**.

2. En el cuadro de diálogo Administrar paquetes de NuGet, busque **WindowsAzure.MobileServices** en la recopilación de paquetes en línea y haga clic para instalar el paquete de NuGet de Servicios móviles de Azure. A continuación, cierre el cuadro de diálogo.

    ![Instalación del paquete NuGet](./media/mobile-services-dotnet-backend-windows-phone-get-started-data/manage-nuget-packages.png)

3. De vuelta en el Portal de administración de Azure, busque el paso con la etiqueta **Conectar la aplicación y almacenar datos en el servicio**. Copie el fragmento de código que crea la conexión de `MobileServiceClient`.

    ![Conexión del fragmento de código de aplicación](./media/mobile-services-dotnet-backend-windows-phone-get-started-data/copy-mobileserviceclient-snippet.png)

4. En Visual Studio, abra App.xaml.cs. Pegue el fragmento de código al principio de la definición de clase `App`. Además, agregue la siguiente instrucción `using` en la parte superior de ese archivo y guarde el archivo.

		using Microsoft.WindowsAzure.MobileServices;

5. En Visual Studio, abra MainPage.xaml.cs y agregue la instrucción using en la parte superior del archivo:

		using Microsoft.WindowsAzure.MobileServices;

6. En Visual Studio, en MainPage.xaml.cs, reemplace la definición de clase `MainPage` por la definición siguiente y guarde el archivo.

    Este código utiliza el SDK de Servicios móviles a fin de habilitar la aplicación para almacenar los datos en una tabla proporcionada por el servicio en lugar de en la memoria local. Los tres métodos principales son `InsertTodoItem`, `RefreshTodoItems` y `UpdateCheckedTodoItem`. Estos tres métodos permiten, de forma asincrónica, insertar la recopilación de datos en una tabla de Azure, consultarla y actualizarla.

        public sealed partial class MainPage : PhoneApplicationPage
        {
            private MobileServiceCollection<TodoItem, TodoItem> items;
            private IMobileServiceTable<TodoItem> todoTable =
                App.MobileService.GetTable<TodoItem>();
            public MainPage()
            {
                this.InitializeComponent();
            }
            private async void InsertTodoItem(TodoItem todoItem)
            {
                await todoTable.InsertAsync(todoItem);
                items.Add(todoItem);
            }
            private async void RefreshTodoItems()
            {
                items = await todoTable
                    .ToCollectionAsync();
                ListItems.ItemsSource = items;
            }
            private async void UpdateCheckedTodoItem(TodoItem item)
            {
                await todoTable.UpdateAsync(item);
            }
            private void ButtonRefresh_Click(object sender, RoutedEventArgs e)
            {
                RefreshTodoItems();
            }
            private void ButtonSave_Click(object sender, RoutedEventArgs e)
            {
                var todoItem = new TodoItem { Text = InputText.Text };
                InsertTodoItem(todoItem);
            }
            private void CheckBoxComplete_Checked(object sender, RoutedEventArgs e)
            {
                CheckBox cb = (CheckBox)sender;
                TodoItem item = cb.DataContext as TodoItem;
                item.Complete = (bool)cb.IsChecked;
                UpdateCheckedTodoItem(item);
            }
            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
                RefreshTodoItems();
            }
        }


##Prueba de la aplicación de Windows Phone con el servicio hospedado de manera local</h2>

En esta sección, utilizará Visual Studio para probar la aplicación y el servicio móvil localmente en la estación de trabajo de desarrollo. Para probar el servicio móvil hospedado de manera local en IIS Express desde un dispositivo con Windows Phone o uno de los emuladores de Windows Phone, debe configurar IIS Express y la estación de trabajo para permitir las conexiones a la dirección IP y el puerto de la estación de trabajo. Los dispositivos con Windows Phone y los emuladores de Windows Phone se conectan como clientes de red no locales.

#### Configuración de IIS Express para permitir las conexiones remotas

[AZURE.INCLUDE [mobile-services-how-to-configure-iis-express](../../includes/mobile-services-how-to-configure-iis-express.md)]

#### Prueba de la aplicación con el servicio móvil en IIS Express

6. En Visual Studio, abra el archivo App.xaml.cs y convierta en comentario la definición `MobileService` que recientemente pegó en el archivo. Agregue una nueva definición para realizar la conexión en función de la dirección IP y el puerto que configuró en la estación de trabajo. A continuación, guarde el archivo. El código debe ser similar al siguiente...

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "http://192.168.111.11:54321");

        //public static MobileServiceClient MobileService = new MobileServiceClient(
        //    "https://todolist.azure-mobile.net/",
        //    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
        //);


7. En Visual Studio, presione la tecla F7 o haga clic en **Compilar solución** en el menú Compilar para compilar tanto la aplicación de Windows Phone como el servicio móvil. Compruebe que ambos proyectos se compilen sin errores en la ventana de resultados de Visual Studio.

8. En Visual Studio, presione la tecla F5 o haga clic en **Iniciar depuración** en el menú Depurar para ejecutar la aplicación y hospedar el servicio móvil de manera local en IIS Express.

    >[AZURE.NOTE]Asegúrese de ejecutar Visual Studio con la opción **Ejecutar como administrador**. De lo contrario, puede que IIS Express no cargue los cambios de applicationhost.config.

9. Escriba el texto de un nuevo TodoItem. A continuación, haga clic en **Guardar**. De este modo, se inserta un nuevo TodoItem en la base de datos creada por el servicio móvil hospedado de manera local en IIS Express. Haga clic en la casilla correspondiente a uno de los elementos para marcarlo como completado.

10. En Visual Studio, detenga la depuración de la aplicación. Puede ver los cambios en la base de datos creada para el servicio back-end abriendo el Explorador de servidores y expandiendo las conexiones de datos. Haga clic con el botón derecho en la tabla de TodoItems situada debajo de **MS\_TableConnectionString** y, a continuación, haga clic en **Mostrar datos de tabla**

    ![Show data in the table]([14]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-show-local-table-data.png)

11. Cuando acabe de probar el servicio móvil hospedado de manera local, elimine la regla del Firewall de Windows que creó para abrir el puerto de la estación de trabajo.


##Publicación del servicio móvil en Azure

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]


##<a name="test-azure-hosted"></a>Prueba del servicio móvil publicado en Azure

1. En Visual Studio, abra App.xaml.cs. Convierta en comentario el código que crea el `MobileServiceClient` que se conecta al servicio móvil hospedado de manera local. Quite la marca de comentario del código que crea el `MobileServiceClient` que se conecta al servicio de Azure. Guarde los cambios del archivo.

        sealed partial class App : Application
        {
            //public static MobileServiceClient MobileService = new MobileServiceClient(
            //          "http://192.168.111.11:54321");

            // Use this constructor instead after publishing to the cloud
            public static MobileServiceClient MobileService = new MobileServiceClient(
                 "https://todolist.azure-mobile.net/",
                 "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
            );
            ....

2. En Visual Studio, presione la tecla F5 o haga clic en **Iniciar depuración** en el menú Depurar. De este modo, la aplicación se volverá a compilar con el cambio anterior antes de ejecutarla para establecer conexión con el servicio móvil hospedado de manera remota en Azure.

3. Escriba algunos TodoItems nuevos y haga clic en **Guardar** para cada uno de ellos. Haga clic en la casilla para completar algunos de los nuevos elementos. Cada nuevo TodoItem se almacenará y actualizará en la base de datos SQL que configuró previamente para el servicio móvil en el Portal de administración de Azure.

    Puede reiniciar la aplicación para ver si los cambios se aplicaron en la base de datos de Azure. También puede examinar la base de datos utilizando el Portal de administración de Azure o el Explorador de objetos de SQL Server de Visual Studio. En los dos pasos siguientes se utilizará el Portal de administración de Azure para ver los cambios en la base de datos.


4. En el Portal de administración de Azure, haga clic en la opción Administrador correspondiente a la base de datos asociada con el servicio móvil.

    ![Administración de la Base de datos SQL.](./media/mobile-services-dotnet-backend-windows-phone-get-started-data/manage-sql-azure-database.png)

5. Asimismo, en dicho portal, ejecute una consulta para ver los cambios realizados por la aplicación. La consulta será similar a la siguiente, pero con el nombre de la base de datos en lugar de `todolist`.

        SELECT * FROM [todolist].[todoitems]

    ![Consulta la Base de datos SQL.](./media/mobile-services-dotnet-backend-windows-phone-get-started-data/sql-azure-query.png)

Con esto concluye el tutorial **Introducción a los datos**.

##Pasos siguientes

En este tutorial se demostraron los aspectos básicos de la habilitación de una aplicación Windows Phone 8 para trabajar con datos en servicios móviles creados utilizando el tiempo de ejecución .NET. A continuación, pruebe uno de estos otros tutoriales:

* [Introducción a la autenticación] <br/>Aprenda a autenticar a los usuarios de su aplicación.

* [Incorporación de notificaciones de inserción a la aplicación]() <br/>Aprenda a enviar una notificación push muy básica a la aplicación.

* [Referencia conceptual de Servicios móviles con .NET] <br/>Obtenga más información sobre cómo utilizar Servicios móviles con .NET.



<!-- Images. -->

<!-- URLs. -->
[Get started with Mobile Services]: ../mobile-services-dotnet-backend-windows-phone-get-started.md
[Get started with data]: mobile-services-dotnet-backend-windows-phone-get-started-data.md
[Introducción a la autenticación]: mobile-services-dotnet-backend-windows-phone-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-windows-phone-get-started-push.md

[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?linkid=268374
[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Developer Code Samples site]: https://code.msdn.microsoft.com/Add-Azure-Mobile-to-a-8b906f72
[Referencia conceptual de Servicios móviles con .NET]: mobile-services-windows-dotnet-how-to-use-client-library.md
[MobileServiceClient class]: http://go.microsoft.com/fwlink/p/?LinkId=302030
[How to add a new Windows Firewall port rule]: http://go.microsoft.com/fwlink/?LinkId=392240

<!---HONumber=August15_HO8-->
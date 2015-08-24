<properties 
	pageTitle="Creación de un servicio mediante un almacén de datos no relacionales | Microsoft Azure" 
	description="Obtener información acerca de cómo usar un almacén de datos no relacionales como MongoDB o almacenamiento de tablas de Azure con su servicio móvil basado en .NET" 
	services="mobile-services" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="08/08/2015" 
	ms.author="mahender"/>

# Creación de servicio móvil de back-end de .NET que usa MongoDB en lugar de una base de datos SQL para el almacenamiento

Este tema muestra cómo usar un almacén de datos no relacional para su servicio móvil de back-end de .NET. En este tutorial, modificará el proyecto de inicio rápido de Servicios móviles para que use MongoDB en lugar de la Base de datos SQL de Azure como almacén de datos.

Este tutorial requiere que se complete el tutorial [Introducción a Servicios móviles] o [Incorporación de Servicios móviles a una aplicación existente]. También necesitará agregar el servicio de MongoLab a la suscripción.

## <a name="create-store"></a>Creación del almacén no relacional de MongoLab

1. En el [Portal de administración de Azure], haga clic en **Nuevo** y luego en **Marketplace**.

2. Haga clic en el complemento **MongoLab** y siga el asistente para suscribirse y obtener una cuenta de MongoLab.

	Para obtener más información sobre MongoLab, vea la [Página del complemento MongoLab].

2. Una vez configurada la cuenta, haga clic en **Información de conexión** y copie la cadena de conexión.

3. En el servicio móvil, haga clic en la pestaña **Configurar**, vaya a **Cadenas de conexión** y escriba una nueva cadena de conexión con el **Nombre** `MongoConnectionString` y un **Valor** que sea su conexión MongoDB y haga clic en **Guardar**.

	![Adición de la cadena de conexión de MongoDB](./media/mobile-services-dotnet-backend-use-non-relational-data-store/mongo-connection-string.png)

	La cadena de conexión de la cuenta de almacenamiento se almacena cifrada en la configuración de la aplicación. Puede acceder a ella en cualquier controlador de tabla en tiempo de ejecución.

8. En el Explorador de soluciones de Visual Studio, abra el archivo Web.config del proyecto de servicio móvil y agregue la nueva cadena de conexión:

		<add name="MongoConnectionString" connectionString="<MONGODB_CONNECTION_STRING>" />

9. Reemplace el marcador de posición `<MONGODB_CONNECTION_STRING>` por la cadena de conexión de MongoDB.

	El servicio móvil usa esta cadena de conexión cuando se ejecuta en el equipo local, lo que le permite probar el código antes de publicarlo. Cuando se ejecuta en Azure, el servicio móvil usa en su lugar el valor de cadena de conexión establecido en el portal y omite la cadena de conexión del proyecto.

## <a name="modify-service"></a>Modificación de tipos de datos y controladores de tabla

1. Instale el paquete **WindowsAzure.MobileServices.Backend.Mongo** NuGet.

2. Modifique **TodoItem** para que se derive de **DocumentData** en lugar de **EntityData**.

        public class TodoItem : DocumentData
        {
            public string Text { get; set; }

            public bool Complete { get; set; }
        }

3. En **TodoItemController**, reemplace el método **Initialize** por lo siguiente:

        protected override async void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            string connectionStringName = "MongoConnectionString";
            string databaseName = "<YOUR-DATABASE-NAME>";
            string collectionName = "todoItems";
            DomainManager = new MongoDomainManager<TodoItem>(connectionStringName, databaseName, 
				collectionName, Request, Services);
        }

4. En el código del método **Initialize** anterior, reemplace `<YOUR-DATABASE-NAME>` por el nombre que eligió cuando aprovisionó el complemento MongoLab.

Ahora ya está preparado para probar la aplicación.

## <a name="test-application"></a>Prueba de la aplicación

1. (Opcional) Vuelva a publicar el proyecto de back-end de .NET de Servicios móviles.

	También puede probar su servicio móvil localmente antes de publicar el proyecto de back-end de .NET en Azure. Tanto si prueba localmente como en Azure, el servicio móvil seguirá usando el almacenamiento de MongoDB.

4. Mediante el botón **Probar ahora** situado en la página de inicio como antes o con una aplicación cliente conectada a su aplicación móvil, consulte elementos en la base de datos.
 
	Tenga en cuenta que no verá ninguno de los elementos que antes se almacenaban en la base de datos SQL del tutorial de inicio rápido.

	>[AZURE.NOTE]Cuando use el botón **Probar ahora** para iniciar las páginas de la API de ayuda, recuerde que debe proporcionar la clave de aplicación como contraseña (con un nombre de usuario en blanco).

3. Cree un elemento.

	La aplicación y el servicio móvil deberán comportarse como antes, excepto que ahora los datos se almacenan en el almacén no relacional en lugar de en la base de datos de SQL.

##Pasos siguientes

Ahora que ya hemos visto lo fácil que es usar el almacenamiento de tabla con el back-end de .NET, considere la posibilidad de explorar otras opciones de almacenamiento de información de back-end:

+ [Creación de servicio móvil de back-end de .NET que usa almacenamiento de tabla en lugar de una base de datos SQL](mobile-services-dotnet-backend-store-data-table-storage.md)</br> Al igual que el tutorial que acaba de completar, este tema muestra cómo usar un almacén de datos no relacional para el servicio móvil. En este tutorial, modificará el proyecto de inicio rápido de Servicios móviles para que use Almacenamiento de Azure en lugar de una base de datos de SQL como almacén de datos.
 
+ [Conexión a un servidor SQL Server local mediante conexiones híbridas](mobile-services-dotnet-backend-hybrid-connections-get-started.md)</br>Las conexiones híbridas permiten que su servicio móvil se conecte de forma segura a los recursos locales. De este modo, puede hacer que los datos locales sean accesibles para los clientes móviles mediante el uso de Azure. Entre los activos admitidos se incluye cualquier recurso que se ejecute en un puerto TCP estático, como Microsoft SQL Server, MySQL, API Web HTTP y la mayoría de los servicios web personalizados.

+ [Carga de imágenes en el almacenamiento de Azure mediante Servicios móviles](mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage.md)</br>Muestra cómo ampliar el proyecto de ejemplo TodoList para poder cargar imágenes desde su aplicación al almacenamiento de blobs de Azure.


<!-- Anchors. -->
[Create a non-relational store]: #create-store
[Modify data and controllers]: #modify-service
[Test the application]: #test-application


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/create-mongo-lab.png
[1]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/mongo-connection-string.png


<!-- URLs. -->
[Introducción a Servicios móviles]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Incorporación de Servicios móviles a una aplicación existente]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[Portal de administración de Azure]: https://manage.windowsazure.com/
[What is the Table Service]: ../storage-dotnet-how-to-use-tables.md#what-is
[Página del complemento MongoLab]: /gallery/store/mongolab/mongolab
 

<!---HONumber=August15_HO7-->
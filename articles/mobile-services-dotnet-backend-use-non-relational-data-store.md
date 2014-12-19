<properties urlDisplayName="Build a Service Using a Non-Relational Data Store" pageTitle="Creación de un servicio mediante un almacén de datos no relacionales - Servicios móviles de Azure" metaKeywords="" description="Learn how to use a non-relational data store such as MongoDB or Azure Table Storage with your .NET based mobile service" metaCanonical="" services="" documentationCenter="Mobile" title="Build a Service Using a Non-Relational Data Store" authors="yavorg, mahender" solutions="" manager="dwrede" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="yavorg, mahender" />

# Creación de un servicio usando MongoDB como almacén de datos con el backend .NET

Este tema muestra el uso de un almacén de datos no relacional para su servicio móvil. En este tutorial, va a modificar el proyecto de inicio rápido de Servicios móviles para que use MongoDB en lugar de SQL como almacén de datos.

Este tutorial le guiará en la realización de los pasos siguientes para configurar un almacén no relacional:

1. [Creación de un almacén no relacional]
2. [Modificación de datos y controladores]
3. [Prueba de la aplicación]

El tutorial requiere la realización del tutorial [Introducción a Servicios móviles] o [Introducción a los datos].

## <a name="create-store"></a>Creación de un almacén no relacional

1. En el [Portal de administración de Azure], haga clic en **Nuevo** y seleccione **Almacén**.

2. Seleccione el complemento **MongoLab** y navegue por el asistente para suscribirse y obtener una cuenta. Para obtener más información sobre MongoLab, consulte la [Página del complemento MongoLab].

    ![][0]

2. Una vez configurada la cuenta, seleccione **Información de conexión** y copie la cadena de conexión.

3. Navegue a la sección Servicios móviles del portal y elija la pestaña **Configurar**.

4. En **Configuración de aplicación**, pegue la cadena de conexión con la clave "MongoConnectionString" y haga clic en **Guardar**.

    ![][1]

2. Agregue lo siguiente a `TodoItemController`:

        static bool connectionStringInitialized = false;

        private void InitializeConnectionString(string connectionStringName)
        {
            if (!connectionStringInitialized)
            {
                connectionStringInitialized = true;
                if (!this.Services.Settings.Connections.ContainsKey(connectionStringName))
                {
                    var connectionString = this.Services.Settings[connectionStringName];
                    var connectionSetting = new ConnectionSettings(connectionStringName, connectionString);
                    this.Services.Settings.Connections.Add(connectionStringName, connectionSetting);
                }
            }
        }
    
    Este código cargará la configuración de la aplicación y le indicará al servicio móvil que la trate como una conexión que un elemento `TableController` puede usar. Más adelante, llamará a este método cuando se invoque el elemento `TodoItemController`.



## <a name="modify-service"></a>Modificación de datos y controladores

1. Instale el paquete **WindowsAzure.MobileServices.Backend.Mongo** NuGet.

2. Modifique `TodoItem` para que se derive de `DocumentData` en lugar de `EntityData`.

        public class TodoItem : DocumentData
        {
            public string Text { get; set; }

            public bool Complete { get; set; }
        }

3. En `TodoItemController`, reemplace el método `Initialize` por lo siguiente:

        protected override async void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            string connectionStringName = "MongoConnectionString";
            string databaseName = "<YOUR-DATABASE-NAME>";
            string collectionName = "todoItems";
            InitializeConnectionString(connectionStringName);
            DomainManager = new MongoDomainManager<TodoItem>(connectionStringName, databaseName, collectionName, Request, Services);
        }

4. En el código del método `Initialize` anterior, reemplace **YOUR-DATABASE-NAME** por el nombre que eligió cuando aprovisionó el complemento MongoLab.


## <a name="test-application"></a>Prueba de la aplicación

1. Vuelva a publicar el proyecto de backend de servicio móvil.

2. Ejecute la aplicación cliente. Tenga en cuenta que no verá ninguno de los elementos que antes se almacenaban en la base de datos SQL del tutorial de inicio rápido.

3. Cree un elemento. La aplicación debe comportarse como antes, excepto que ahora los datos irán al almacén no relacional.


<!-- Anchors. -->
[Creación de un almacén no relacional]: #create-store
[Modificación de datos y controladores]: #modify-service
[Prueba de la aplicación]: #test-application


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/create-mongo-lab.png
[1]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/mongo-connection-string.png


<!-- URLs. -->
[Introducción a Servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
[Portal de administración de Azure]: https://manage.windowsazure.com/
[Qué es el servicio Tabla]: /es-es/documentation/articles/storage-dotnet-how-to-use-tables/#what-is
[Página del complemento de MongoLab]: /es-es/gallery/store/mongolab/mongolab

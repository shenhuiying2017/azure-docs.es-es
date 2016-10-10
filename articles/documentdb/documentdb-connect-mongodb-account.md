<properties 
	pageTitle="Conexión de una cuenta de DocumentDB con compatibilidad de protocolo para MongoDB | Microsoft Azure" 
	description="Obtenga información acerca de cómo conectarse a una cuenta de DocumentDB con compatibilidad de protocolo para MongoDB, ahora disponible en una versión preliminar. Conéctese mediante la cadena de conexión de MongoDB." 
	keywords="cadena de conexión de mongodb"
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/23/2016" 
	ms.author="mimig"/>

# Conexión de una cuenta de DocumentDB con compatibilidad de protocolo para MongoDB

Obtenga información acerca de cómo conectar una cuenta de Azure DocumentDB con soporte de protocolo para MongoDB con el formato de identificador URI de cadena de conexión de MongoDB estándar.

## Obtención de información de la cadena de conexión de la cuenta

1. En una nueva ventana, inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En la barra de **navegación izquierda** de la hoja Cuenta, haga clic en **Cadena de conexión**. Para ir a la hoja **Cuenta**, en la barra de accesos directos, haga clic en **More Services** (Más servicios) y en **DocumentDB (NoSQL)**; por último, seleccione la cuenta con compatibilidad de protocolo para MongoDB.

	![Captura de pantalla de la hoja Toda la configuración](./media/documentdb-connect-mongodb-account/SettingsBlade.png)

3. Se abre la hoja **Información de cadena de conexión** con toda la información necesaria para conectarse a la cuenta con un controlador para MongoDB, incluida una cadena de conexión precreada.

	![Captura de pantalla de la hoja Cadena de conexión](./media/documentdb-connect-mongodb-account/ConnectionStringBlade.png)

## Requisitos de la cadena de conexión

Es importante tener en cuenta que DocumentDB admite el formato de identificador URI de cadena de conexión de MongoDB estándar, con un par de requisitos específicos: las cuentas de DocumentDB requieren la autenticación y la comunicación segura mediante SSL. Por tanto, el formato de la cadena de conexión es:

	mongodb://username:password@host:port/[database]?ssl=true

Donde los valores de esta cadena están disponibles en la hoja Cadena de conexión mostrada antes.

- Username (obligatorio)
	- Nombre de la cuenta de DocumentDB
- Password (obligatorio)
	- Contraseña de la cuenta de DocumentDB
- Host (obligatorio)
	- FQDN de la cuenta de DocumentDB
- Port (obligatorio)
	- 10250
- Database (opcional)
	- La base de datos predeterminada usada por la conexión
- ssl=true (obligatorio)

Por ejemplo, considere la cuenta que aparece en la hoja Información de cadena de conexión mostrada antes. Una cadena de conexión válida es:
	
	mongodb://contoso123:<password@contoso123.documents.azure.com:10250/mydatabase?ssl=true

## Conexión con el controlador C# para MongoDB
Como ya se mencionó, todas las cuentas de DocumentDB requieren la autenticación y la comunicación segura mediante SSL. Aunque el formato de identificador URI de cadena de conexión de MongoDB es compatible con el parámetro ssl=true de la cadena de consulta, trabajar con el controlador C# para MongoDB requiere el uso del objeto MongoClientSettings al crear una instancia de MongoClient. Dada la información de la cuenta anterior, el siguiente fragmento de código muestra cómo conectarse a la cuenta y trabajar con la base de datos "Tasks".

	        MongoClientSettings settings = new MongoClientSettings();
            settings.Server = new MongoServerAddress("contoso123.documents.azure.com", 10250);
            settings.UseSsl = true;
            settings.SslSettings = new SslSettings();
            settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

            MongoIdentity identity = new MongoInternalIdentity("Tasks", "contoso123");
            MongoIdentityEvidence evidence = new PasswordEvidence("<password>");

            settings.Credentials = new List<MongoCredential>()
            {
                new MongoCredential("SCRAM-SHA-1", identity, evidence)
            };
            MongoClient client = new MongoClient(settings);
            var database = client.GetDatabase("Tasks",);
	

## Pasos siguientes


- Obtenga información acerca de cómo [usar MongoChef](documentdb-mongodb-mongochef.md) con una cuenta de DocumentDB con compatibilidad de protocolo con MongoDB.
- Explore DocumentDB con soporte de protocolo para buscar [ejemplos](documentdb-mongodb-samples.md) de MongoDB.

 

<!---HONumber=AcomDC_0928_2016-->
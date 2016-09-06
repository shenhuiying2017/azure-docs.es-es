<properties
	pageTitle="Creación de una cuenta de DocumentDB | Microsoft Azure"
	description="Creación de una base de datos NoSQL con Azure DocumentDB. Siga estas instrucciones para crear una cuenta de DocumentDB y empezar a crear una base de datos de NoSQL rápidas y de escala global." 
	keywords="generar una base de datos"
	services="documentdb"
	documentationCenter=""
	authors="mimig1"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/25/2016"
	ms.author="mimig"/>

# Creación de una cuenta de DocumentDB mediante el Portal de Azure

> [AZURE.SELECTOR]
- [Portal de Azure](documentdb-create-account.md)
- [ CLI de Azure y ARM](documentdb-automation-resource-manager-cli.md)

Para generar una base de datos con Microsoft Azure DocumentDB, debe:

- Tener una cuenta de Azure. Si no tiene una, puede obtener una [cuenta de Azure gratuita](https://azure.microsoft.com/free).
- Crear una cuenta de DocumentDB.

Puede crear una cuenta de DocumentDB con el Portal de Azure, las plantillas de Azure Resource Manager o la interfaz de línea de comandos (CLI) de Azure. En este artículo se muestra cómo crear una cuenta de DocumentDB con el Portal de Azure. Para crear una cuenta con Administrador de recursos de Azure o CLI de Azure, consulte [Automate DocumentDB account creation using Azure Resource Manager templates and Azure CLI](documentdb-automation-resource-manager-cli.md).

¿Es la primera vez que usa DocumentDB? Vea [este](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) vídeo de cuatro minutos de Scott Hanselman para saber cómo realizar las tareas más comunes en el portal en línea.

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## Pasos siguientes

Ahora que tiene una cuenta de DocumentDB, el siguiente paso es generar una base de datos de DocumentDB. Puede crear una base de datos mediante uno de los siguientes procedimientos:

- El Portal de Azure, tal como se describe en [Creación de una base de datos de DocumentDB en el Portal de Azure](documentdb-create-database.md).
- Los ejemplos de .NET de C# en el proyecto [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) del repositorio [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) de GitHub.
- Los tutoriales integrales: [.NET](documentdb-get-started.md), [.NET MVC](documentdb-dotnet-application.md), [Java](documentdb-java-application.md), [Node.js](documentdb-nodejs-application.md) o [Python](documentdb-python-application.md).
- Los [SDK de DocumentDB](documentdb-sdk-dotnet.md). DocumentDB tiene .NET, Java, Python, Node.js y SDK de la API de JavaScript.


Después de crear la base de datos, tendrá que [agregar una o más colecciones](documentdb-create-collection.md) a la base de datos y después [agregar documentos](documentdb-view-json-document-explorer.md) a las colecciones.

Cuando tenga documentos en una colección, puede usar [SQL de DocumentDB](documentdb-sql-query.md) para [ejecutar consultas](documentdb-sql-query.md#executing-queries) en sus documentos mediante el [Explorador de consultas](documentdb-query-collections-query-explorer.md) del portal, la [API de REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) o uno de los [SDK](documentdb-sdk-dotnet.md).

Para obtener más información acerca de DocumentDB, explore estos recursos:

-	[Ruta de aprendizaje de DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
-	[Modelo de recursos y conceptos de DocumentDB](documentdb-resources.md)

<!---HONumber=AcomDC_0831_2016-->
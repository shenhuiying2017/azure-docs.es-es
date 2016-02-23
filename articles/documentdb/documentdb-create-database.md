<properties 
	pageTitle="Cómo crear una base de datos en DocumentDB | Microsoft Azure" 
	description="Averigüe cómo crear una base de datos administrada mediante el portal de servicios en línea para DocumentDB de Azure, una base de datos de documentos NoSQL para JSON. Obtenga una versión de evaluación gratuita hoy mismo." 
	keywords="cómo crear una base de datos" 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="11/18/2015" 
	ms.author="mimig"/>

# Creación de una base de datos para DocumentDB mediante el Portal de Azure

Para usar Microsoft Azure DocumentDB, debe tener una [cuenta de DocumentDB](documentdb-create-account.md), una base de datos, una colección y documentos. En este tema se describe cómo crear una base de datos para DocumentDB en el Portal de Microsoft Azure.

![Captura de pantalla en la que se muestra cómo crear una base de datos, en la que se resalta el botón Examinar, Cuentas de DocumentDB en la hoja Examinar y una cuenta de DocumentDB en la hoja Cuentas de DocumentDB](./media/documentdb-create-database/docdb-database-creation-1-3.png)

1.  En la barra de accesos directos del [Portal de Azure](https://portal.azure.com/), haga clic en **Cuentas de DocumentDB**. 

2.  En la hoja **Cuentas de DocumentDB**, seleccione la cuenta en la que desea agregar una base de datos de NoSQL de DocumentDB. Si no se muestra ninguna cuenta, deberá [crear una cuenta de DocumentDB](documentdb-create-account.md).

3. En la hoja **Cuenta de DocumentDB**, haga clic en **Agregar base de datos**.

4. En la hoja **Agregar base de datos**, escriba el identificador de la base de datos nueva. Cuando se valida el nombre, aparece una marca de verificación verde en el cuadro **Id.**.

5. Haga clic en **Aceptar** en la parte inferior de la pantalla para crear la nueva base de datos.

7. La base de datos nueva aparece ahora en el modo **Bases de datos** de la hoja **Cuenta de DocumentDB**.
 
	![Screen shot of the new database in the DocumentDB Account blade](./media/documentdb-create-database/docdb-database-creation-7.png)

## Otras formas de crear una base de datos de DocumentDB

No es necesario que las bases de datos se creen en el Portal, también se pueden crear mediante los [SDK de DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). Para un ejemplo de código de C# en el que se muestra cómo crear una colección mediante el SDK de .NET de DocumentDB, vea el archivo [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DatabaseManagement/Program.cs) en el proyecto DatabaseManagement, disponible en el repositorio [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) en [GitHub.com](https://github.com).

## Pasos siguientes

Ahora que sabe cómo crear una base de datos para DocumentDB, el siguiente paso es [crear una colección](documentdb-create-collection.md).

Una vez creada la colección, puede [agregar documentos de JSON](documentdb-view-json-document-explorer.md) mediante el Explorador de documentos en el Portal, [importar documentos](documentdb-import-data.md) en la colección con la Herramienta de migración de datos de DocumentDB o usar uno de los [SDK de DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx) para llevar a cabo las operaciones de CRUD. DocumentDB tiene .NET, Java, Python, Node.js y SDK de la API de JavaScript. Para ejemplos de código de .NET en los que se muestra cómo crear, quitar, actualizar y eliminar documentos, vea [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DocumentManagement/Program.cs) en el proyecto DocumentManagement del repositorio de azure-documentdb-net en GitHub.com.

Cuando tenga documentos en una colección, puede usar [SQL de DocumentDB](documentdb-sql-query.md) para [ejecutar consultas](documentdb-sql-query.md#executing-queries) en sus documentos mediante el [Explorador de consultas](documentdb-query-collections-query-explorer.md) del portal, la [API de REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) o uno de los [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx).

<!---HONumber=AcomDC_0218_2016-->
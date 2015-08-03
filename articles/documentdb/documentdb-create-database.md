<properties 
	pageTitle="Crear una base de datos de DocumentDB NoSQL | Microsoft Azure" 
	description="Aprenda a crear bases de datos administradas mediante el portal de servicios en línea para DocumentDB de Azure, una base de datos de documentos NoSQL para JSON. Obtenga una versión de evaluación gratuita hoy mismo." 
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
	ms.date="06/26/2015" 
	ms.author="mimig"/>

# Creación de una base de datos de DocumentDB en el portal de vista previa de Azure

Para usar Microsoft Azure DocumentDB, debe tener una [cuenta de DocumentDB](documentdb-create-account.md), una base de datos, una colección y documentos. En este tema se describe cómo crear una base de datos de DocumentDB en el portal de vista previa de Microsoft Azure.

No es necesario que las bases de datos se creen en el portal de vista previa, también se pueden crear mediante los [SDK de DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). Para un ejemplo de código de C# en el que se muestra cómo crear una base de datos mediante el SDK de .NET de DocumentDB, vea el archivo [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DatabaseManagement/Program.cs) en el proyecto DatabaseManagement, disponible en el repositorio [azure-documentdb-net](https://github.com/Azure/azure-documentdb-net) en [GitHub.com](https://github.com).

1.  En el [Portal de vista previa de Azure](https://portal.azure.com/), haga clic en **Examinar todo**.

2.  En la hoja **Examinar**, haga clic en **Cuentas de DocumentDB**.

3.  En la hoja **Cuentas de DocumentDB**, seleccione la cuenta en la que desea agregar una base de datos de DocumentDB. Si no se muestra ninguna cuenta, deberá [crear una cuenta de DocumentDB](documentdb-create-account.md).
    
    ![Screen shot highlighting the Browse button, DocumentDB Accounts on the Browse blade, and a DocumentDB account on the DocumentDB Accounts blade](./media/documentdb-create-database/docdb-database-creation-1-3.png)

4. En la hoja **Cuenta de DocumentDB**, haga clic en **Agregar base de datos**.

5. En la hoja **Agregar base de datos**, escriba el identificador de la base de datos nueva. Cuando se valida el nombre, aparece una marca de verificación verde en el cuadro Id.

6. Haga clic en **Aceptar** en la parte inferior de la pantalla para crear la nueva base de datos.

	![Screen shot highlighting the Add Database button on the DocumentDB Account blade, the ID box on the Add Database blade, and the OK button](./media/documentdb-create-database/docdb-database-creation-4-6.png)

8. La base de datos nueva aparece ahora en el modo **Bases de datos** de la hoja **Cuenta de DocumentDB**.
 
	![Screen shot of the new database in the DocumentDB Account blade](./media/documentdb-create-database/docdb-database-creation-7.png)

## Pasos siguientes

Ahora que tiene una base de datos de DocumentDB, el siguiente paso es [crear una colección](documentdb-create-collection.md).

Una vez creada la colección, puede [agregar documentos](../documentdb-view-json-document-explorer.md) mediante la utilización del Explorador de documentos en el portal de vista previa o [importar documentos](documentdb-import-data.md) en la colección con la Herramienta de migración de datos de DocumentDB, o use uno de los [SDK de DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx) para llevar a cabo las operaciones de CRUD. DocumentDB tiene .NET, Java, Python, Node.js y SDK de la API de JavaScript. Para ver ejemplos de código de .NET que muestran cómo crear, eliminar, actualizar y eliminar documentos, vea [Program.cs](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/DocumentManagement/Program.cs) en el proyecto DocumentManagement del repositorio de azure-documentdb-net en GitHub.com.

Cuando tenga documentos en una colección, puede usar [SQL de DocumentDB](documentdb-sql-query.md) para [ejecutar consultas](documentdb-sql-query.md#executing-queries) frente a sus documentos usando el [Explorador de consultas](documentdb-query-collections-query-explorer.md) en el portal de vista previa, la [API de REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) o uno de los [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx).

<!---HONumber=July15_HO4-->
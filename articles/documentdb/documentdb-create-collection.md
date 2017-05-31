---
title: "Creación de una colección y una base de datos de Azure Cosmos DB | Microsoft Docs"
description: "Aprenda a crear bases de datos y un contenedor mediante el portal de servicios en línea para Azure Cosmos DB, una base de datos de documentos basada en la nube. Obtenga una versión de evaluación gratuita hoy mismo."
services: cosmosdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: b81ad2f6-df7f-4c6d-8ca9-f8a9982d647e
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 596a4ce9d315e7c6c41a43860469c339c7e96ac5
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="how-to-create-an-azure-cosmos-db-collection-and-database-using-the-azure-portal"></a>Creación de colecciones y bases de datos de Azure Cosmos DB mediante Azure Portal
Para usar Microsoft Azure Cosmos DB, debe tener una [cuenta de Azure Cosmos DB](documentdb-create-account.md), una base de datos, una colección y documentos. En este tema se describe cómo crear una colección de Azure Cosmos DB en Azure Portal.

¿No está seguro de lo que es una colección? Vea [¿Qué es una colección de Azure Cosmos DB?](#what-is-a-documentdb-collection).

Hay dos maneras de crear una colección en el portal: con el botón Agregar colección o mediante [Explorador de datos (versión preliminar)](#data-explorer).

## <a name="create-a-colletion-using-add-collection-button"></a>Crear una colección con el botón Agregar colección

1. En [Azure Portal](https://portal.azure.com/), en la barra de salto, haga clic en **Azure Cosmos DB** y luego, en la hoja **Azure Cosmos DB**, seleccione la cuenta en la que se va a agregar una colección. Si no se muestra ninguna cuenta, deberá [crear una cuenta de DocumentDB](documentdb-create-account.md).

   ![Captura de pantalla con la opción Azure Cosmos DB Accounts (Cuentas de Azure Cosmos DB) de la barra de salto, la cuenta en la hoja Azure Cosmos DB Accounts (Cuentas de Azure Cosmos DB) y la base de datos en la hoja de la cuenta de Azure Cosmos DB, en la lente Bases de datos resaltadas](./media/documentdb-create-collection/docdb-database-creation-1-2.png)

   Si **Azure Cosmos DB** no está visible en la barra de salto, haga clic en **Más servicios** y luego, en **Azure Cosmos DB**. Si no se muestra ninguna cuenta, deberá [crear una cuenta de DocumentDB](documentdb-create-account.md).
2. En la hoja **Azure Cosmos DB account** (Cuenta de Azure Cosmos DB) de la cuenta seleccionada, haga clic en **Agregar colección**.

    ![Captura de pantalla con la opción Azure Cosmos DB Accounts (Cuentas de Azure Cosmos DB) de la barra de salto, la cuenta en la hoja Azure Cosmos DB Accounts (Cuentas de Azure Cosmos DB) y la base de datos en la hoja de la cuenta de Azure Cosmos DB, en la lente Bases de datos resaltadas](./media/documentdb-create-collection/docdb-database-creation-3.png)
3. En la hoja **Agregar colección**, en el cuadro **Id. de colección**, escriba el identificador de la nueva colección. Los nombres de colección deben tener entre 1 y 255 caracteres y no pueden contener `/ \ # ?` o un espacio al final. Cuando se valida el nombre, aparece una marca de verificación verde en el cuadro Id.

    ![Captura de pantalla con el botón Agregar colección de la hoja Base de datos, la configuración de la hoja Agregar colección y el botón Aceptar resaltados (Azure Portal para Azure Cosmos DB: creador de bases de datos basadas en la nube para bases de datos JSON NoSQL)](./media/documentdb-create-collection/docdb-collection-creation-5-8.png)
4. De forma predeterminada, **Capacidad de almacenamiento** está establecida en **Sin límite** para administrar colecciones particionadas.

    Establezca la capacidad de almacenamiento en **10 GB** si desea una [colección de partición única](documentdb-partition-data.md#single-partition-and-partitioned-collections) con niveles de rendimiento desde 400-10.000 unidades de solicitud/segundo (RU/s). Una RU corresponde al rendimiento de una lectura de un documento de 1 KB. Para más información sobre las unidades de solicitud, consulte [Unidades de solicitud en DocumentDB](documentdb-request-units.md).

    Establezca la capacidad de almacenamiento en **Sin límite** si quiere una [colección particionada](documentdb-partition-data.md#single-partition-and-partitioned-collections) que se pueda escalar para administrar una cantidad ilimitada de almacenamiento en varias particiones y poder tener niveles de rendimiento superiores a 2500 RU/s.

    Establezca la capacidad de almacenamiento en **Personalizada** si desea proporcionar una cantidad diferente de 10 GB o 250 GB. Azure Cosmos DB tiene posibilidades de escalado prácticamente ilimitadas, así que incluya el tamaño de almacenamiento y el valor de rendimiento solicitados en la solicitud de soporte técnico.

6. En el cuadro **Clave de partición**, escriba la clave de partición de la colección. Esto es obligatorio para las colecciones particionadas y opcional para las colecciones de una sola partición. La selección de la clave de partición correcta es importante al crear una colección de rendimiento. Para más información sobre cómo seleccionar una clave de partición, consulte [Diseño de la creación de particiones](../cosmos-db/partition-data.md#designing-for-partitioning).
7. En la hoja **Base de datos**, cree una nueva base de datos o utilice una que ya exista. Los nombres de bases de datos deben tener entre 1 y 255 caracteres y no pueden contener `/ \ # ?` o un espacio al final. Para validar el nombre, haga clic fuera del cuadro de texto. Cuando se valida el nombre, aparece una marca de verificación verde en el cuadro.
8. Haga clic en **Aceptar** en la parte inferior de la pantalla para crear la nueva colección.
9. La nueva colección aparece ahora en el modo **Colecciones** en la hoja **Información general**.

    ![Captura de pantalla de la nueva colección en la hoja Base de datos (Azure Portal para Azure Cosmos DB: creador de bases de datos basadas en la nube para bases de datos JSON NoSQL)](./media/documentdb-create-collection/docdb-collection-creation-9.png)
10. **Opcional:** para modificar el rendimiento de la colección en el portal, haga clic en **Escala** en el menú de recursos.

    ![Captura de pantalla del menú de recursos, con la opción Escala seleccionada](./media/documentdb-create-collection/docdb-collection-creation-scale.png)

<a id="data-explorer"></a>
## <a name="create-a-collection-by-using-data-explorer-preview"></a>Crear una colección mediante el Explorador de datos (versión preliminar)

El otro método para crear una colección en el portal es el Explorador de datos. Para abrir el Explorador de datos, haga clic en **Explorador de datos (versión preliminar)** en la barra de navegación del portal y luego en el botón **Nueva colección**, como se muestra en la siguiente captura de pantalla.

 ![Captura de pantalla que muestra el botón Nueva colección del portal](./media/documentdb-create-collection/azure-documentdb-data-explorer.png)


## <a name="what-is-an-azure-cosmos-db-collection"></a>¿Qué es una colección de Azure Cosmos DB?
Una colección es un contenedor de documentos JSON asociado a la lógica de aplicación de JavaScript. Una colección es una entidad facturable, en la que el [costo](documentdb-performance-levels.md) viene determinado por el rendimiento aprovisionado de la colección. Las colecciones pueden abarcar una o varias particiones o uno o varios servidores y se pueden escalar para administrar volúmenes prácticamente ilimitados de almacenamiento o rendimiento.

Las colecciones se particionan automáticamente en uno o más servidores físicos mediante Azure Cosmos DB. Cuando crea una colección, puede especificar el rendimiento aprovisionado en términos de unidades de solicitud por segundo y una propiedad de clave de partición. El valor de esta propiedad se usará en Azure Cosmos DB para distribuir documentos entre particiones y enrutar solicitudes, como las consultas. El valor de la clave de partición también actúa como límite de la transacción para los procedimientos y los desencadenadores almacenados. Cada colección tiene una cantidad reservada de capacidad de proceso específica para esa colección, que no se comparte con otras colecciones de la misma cuenta. Por lo tanto, puede escalar la aplicación horizontalmente tanto en términos de rendimiento como de almacenamiento.

Las colecciones son distintas de las tablas incluidas en las bases de datos relacionales. Las colecciones no aplican un esquema, de hecho, Azure Cosmos DB no aplica ningún esquema, sino que es una base de datos sin esquemas. Por lo tanto, se pueden almacenar distintos tipos de documentos con esquemas diferentes en la misma colección. Puede optar por utilizar colecciones para almacenar objetos de un solo tipo, como se haría con las tablas. El mejor modelo depende solo de la forma en que aparezcan los datos juntos en las consultas y las transacciones.

## <a name="other-ways-to-create-an-azure-cosmos-db-collection"></a>Otras formas de crear colecciones de Azure Cosmos DB
No es necesario crear las colecciones con el portal, también puede crearlas con los [SDK de DocumentDB](documentdb-sdk-dotnet.md) y la API de REST.

* Para ver un ejemplo de código de C#, consulte los [ejemplos de colección de C#](documentdb-dotnet-samples.md#collection-examples).
* Para ver un ejemplo de código Node.js, consulte los [ejemplos de colección de Node.js](documentdb-nodejs-samples.md#collection-examples).
* Para ver un ejemplo de código Python, consulte los [ejemplos de colección de Python](documentdb-python-samples.md#collection-examples).
* Para ver un ejemplo de API de REST, consulte [Crear una colección](https://msdn.microsoft.com/library/azure/mt489078.aspx).

## <a name="troubleshooting"></a>Solución de problemas
Si la opción **Agregar colección** está deshabilitada en Azure Portal, significa que la cuenta está deshabilitada, lo que normalmente se produce cuando se utilizan todos los créditos de beneficios del mes.    

## <a name="next-steps"></a>Pasos siguientes
Ahora que tiene una colección, el paso siguiente es agregar o importar documentos a la colección. Cuando se trata de agregar documentos a una colección, tiene varias posibilidades:

* Puede [agregar documentos](documentdb-view-json-document-explorer.md) mediante el Explorador de documentos en el Portal.
* También puede [importar documentos y datos](documentdb-import-data.md) mediante la Herramienta de migración de datos de DocumentDB, que le permite importar archivos CSV y JSON, así como datos de SQL Server, MongoDB, almacenamiento de tablas de Azure y otras colecciones de DocumentDB.
* También puede agregar documentos con uno de los [SDK de DocumentDB](documentdb-sdk-dotnet.md). DocumentDB tiene .NET, Java, Python, Node.js y SDK de la API de JavaScript. Para ver ejemplos de código de C# que muestran cómo trabajar con documentos mediante el SDK de .NET para DocumentDB, consulte los [ejemplos de colección de C#](documentdb-dotnet-samples.md#document-examples). Para ver ejemplos de código de Node.js que muestran cómo trabajar con documentos mediante el SDK de Node.js para DocumentDB, consulte los [ejemplos de documentos de Node.js](documentdb-nodejs-samples.md#document-examples).

Cuando tenga documentos en una colección, puede usar [SQL de DocumentDB](documentdb-sql-query.md) para [ejecutar consultas](documentdb-sql-query.md#ExecutingSqlQueries) en sus documentos mediante el [Explorador de consultas](documentdb-query-collections-query-explorer.md) del portal, la [API de REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) o uno de los [SDK](documentdb-sdk-dotnet.md). 


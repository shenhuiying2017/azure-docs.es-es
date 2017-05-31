---
title: 'Herramienta del portal de Azure Cosmos DB: Explorador de consultas | Microsoft Docs'
description: "Obtenga información sobre el Explorador de consultas de Azure Cosmos DB, un Editor de consultas de SQL de Azure Portal para escribir consultas SQL y ejecutarlas en una colección de Azure Cosmos DB."
keywords: escribir consultas sql, editor de consultas de sql
services: cosmosdb
author: kirillg
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: ac378240-b11f-4522-ae9f-09da3a6f9c16
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: kirillg
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 77389c6d0779a08b8d717076fc5678d3d7fb3b0c
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="write-edit-and-run-sql-queries-for-azure-cosmos-db-using-query-explorer-in-the-azure-portal"></a>Escritura, edición y ejecución de consultas SQL para Azure Cosmos DB mediante Explorador de consultas en Azure Portal
En este artículo se proporciona información general sobre el Explorador de consultas de [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/), que es una herramienta de Azure Portal que permite escribir, editar y ejecutar consultas SQL en una [colección de DocumentDB](documentdb-create-collection.md).

1. En [Azure Portal](https://portal.azure.com), en el panel de navegación izquierdo, haga clic en ![icono de Azure Cosmos DB](./media/documentdb-query-collections-query-explorer/nosql-documentdb-portal-icon.png) **Azure Cosmos DB**. 

    Si **Azure Cosmos DB** no se muestra, haga clic en **Más servicios** en la parte inferior y después haga clic en el ![icono de Azure Cosmos DB](./media/documentdb-query-collections-query-explorer/nosql-documentdb-portal-icon.png) **Azure Cosmos DB**.
2. En el menú de recursos, haga clic en **Explorador de consultas**. 
   
    ![Captura de pantalla del Portal de Azure con el Explorador de consultas resaltado](./media/documentdb-query-collections-query-explorer/queryexplorercommand.png)
3. En la hoja **Explorador de consultas**, seleccione las **bases de datos** y **colecciones** en que desee realizar consultas en las listas desplegables y escriba la consulta que desee ejecutar. 
   
    Las listas desplegables **Bases de datos** y **Colecciones** se rellenan previamente según el contexto en el que se inicie el Explorador de consultas. 
   
    Se proporciona la consulta predeterminada `SELECT TOP 100 * FROM c` .  Puede aceptar la consulta predeterminada o crear su propia consulta con el lenguaje de consulta SQL que se describe en la [hoja de referencia rápida de consulta SQL](documentdb-sql-query-cheat-sheet.md) o en el artículo sobre [consulta SQL y sintaxis SQL](documentdb-sql-query.md).
   
    Haga clic en **Ejecutar consulta** para ver los resultados.
   
    ![Captura de pantalla de escritura de consultas SQL en el Explorador de consultas, un Editor de consultas de SQL](./media/documentdb-query-collections-query-explorer/queryexplorerinitial.png)
4. La hoja **Resultados** hoja muestra el resultado de la consulta. 
   
    ![Captura de pantalla de resultados de la escritura de consultas SQL en el Explorador de consultas](./media/documentdb-query-collections-query-explorer/queryresults1.png)

## <a name="work-with-results"></a>Trabajo con resultados
De forma predeterminada, el Explorador de consultas devuelve resultados en grupos de 100.  Si la consulta genera más de 100 resultados, utilice los comandos **Página siguiente** y **Página anterior** para desplazarse por el conjunto de resultados.

![Captura de pantalla de la compatibilidad con la paginación del Explorador de consultas](./media/documentdb-query-collections-query-explorer/queryresultspagination.png)

En las consultas correctas, el panel **Información** contiene métricas como el gasto de la solicitud, el número de recorridos de ida y vuelta que realizó la consulta, el conjunto de resultados que se muestra actualmente y si hay más resultados, a los que se puede acceder mediante el comando **Página siguiente**, tal como se indicó anteriormente.

![Captura de pantalla de información de consultas del Explorador de consultas](./media/documentdb-query-collections-query-explorer/queryinformation.png)

## <a name="use-multiple-queries"></a>Uso de múltiples consultas
Si usa varias consultas y desea cambiar rápidamente de unas a otras, puede especificarlas todas en el cuadro de texto de consulta de la hoja **Explorador de consultas**, luego resaltar la que desee ejecutar y, finalmente, haga clic en **Ejecutar consulta** para ver los resultados.

![Captura de pantalla de la escritura de varias consultas SQL en el Explorador de consultas (un Editor de consultas de SQL) y el resaltado y ejecución de consultas individuales](./media/documentdb-query-collections-query-explorer/queryexplorerhighlightandrun.png)

## <a name="add-queries-from-a-file-into-the-sql-query-editor"></a>Adición de consultas desde un archivo en el Editor de consultas de SQL
Puede cargar el contenido de un archivo existente con el comando **Cargar archivo** .

![Captura de pantalla que muestra cómo cargar consultas SQL desde un archivo en el Explorador de consultas mediante Cargar archivo](./media/documentdb-query-collections-query-explorer/loadqueryfile.png)

## <a name="troubleshoot"></a>Solución de problemas
Si una consulta se completa con errores, el Explorador de consultas muestra una lista de errores que puede ser útil a la hora de solucionar los problemas.

![Captura de pantalla de errores de consulta del Explorador de consultas](./media/documentdb-query-collections-query-explorer/queryerror.png)

## <a name="run-documentdb-api-sql-queries-outside-the-portal"></a>Ejecución de consultas SQL de la API de DocumentDB fuera del portal
El Explorador de consultas de Azure Portal es simplemente una forma de ejecutar consultas SQL en Cosmos DB. Las consultas SQL también se pueden ejecutar mediante la [API de REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) o los [SDK de cliente](documentdb-sdk-dotnet.md). Para obtener más información sobre el uso de estos otros métodos, consulte [Executing SQL queries](documentdb-sql-query.md#ExecutingSqlQueries)

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre la gramática de SQL de la API de DocumentDB compatible con el Explorador de consultas, vea el artículo sobre [consulta SQL y sintaxis SQL](documentdb-sql-query.md) o imprima la [hoja de referencia rápida de consultas SQL](documentdb-sql-query-cheat-sheet.md).
También puede disfrutar de la experimentación con [Query Playground](https://www.documentdb.com/sql/demo) , donde puede probar las consultas en línea con un conjunto de datos de ejemplo.



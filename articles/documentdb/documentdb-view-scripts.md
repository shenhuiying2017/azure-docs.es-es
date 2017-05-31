---
title: 'Herramienta del portal de Azure Cosmos DB: Explorador de scripts | Microsoft Docs'
description: "Obtenga información sobre el Explorador de scripts de Azure Cosmos DB, una herramienta de Azure Portal para administrar los artefactos de programación del lado servidor de Azure Cosmos DB, entre los que se incluyen procedimientos almacenados de JavaScript, desencadenadores y funciones definidas por el usuario."
keywords: editor de javascript
services: cosmosdb
author: kirillg
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 9d0620da-2449-4c17-82a4-24aaa46e9b3e
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: kirillg
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: e9c3e92dd1c35ff6893565e61f7e7b940b6ede7e
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="create-and-run-stored-procedures-triggers-and-user-defined-functions-using-the-azure-cosmos-db-script-explorer"></a>Creación y ejecución de procedimientos almacenados, desencadenadores y funciones definidas por el usuario mediante el Explorador de scripts de Azure Cosmos DB
En este artículo se ofrece información general sobre el Explorador de scripts de [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/), un editor de JavaScript de Azure Portal que le permite ver y ejecutar los artefactos de programación del lado servidor de Cosmos DB, entre los que se incluyen procedimientos almacenados, desencadenadores y funciones definidas por el usuario. Más información sobre programación del lado servidor de Cosmos DB en el artículo [Procedimientos almacenados, desencadenadores de base de datos y UDF](documentdb-programming.md).

## <a name="launch-script-explorer"></a>Inicio del Explorador de scripts
1. En [Azure Portal](https://portal.azure.com), en el panel de navegación izquierdo, haga clic en ![icono de Azure Cosmos DB](./media/documentdb-query-collections-query-explorer/nosql-documentdb-portal-icon.png) **Azure Cosmos DB**. 

    Si **Azure Cosmos DB** no se muestra, haga clic en **Más servicios** en la parte inferior y después haga clic en el ![icono de Azure Cosmos DB](./media/documentdb-query-collections-query-explorer/nosql-documentdb-portal-icon.png) **Azure Cosmos DB**.
2. En el menú de recursos, haga clic en **Explorador de scripts**.
   
    ![Captura de pantalla del comando del Explorador de scripts](./media/documentdb-view-scripts/scriptexplorercommand.png)
   
    Los cuadros de lista desplegable **Base de datos** y **Colección** se rellenan previamente según el contexto en el que se ejecute el Explorador de scripts.  Por ejemplo, si efectúa el inicio desde una hoja de base de datos, la base de datos actual se rellena previamente.  Si el inicio se realiza desde una hoja de colección, la colección actual será la que se rellene previamente.
3. Los cuadros de lista desplegable **Base de datos** y **Colección** pueden usarse para cambiar fácilmente la colección desde la que se ven los scripts en ese momento sin tener que cerrar y reiniciar el Explorador de scripts.  
4. Asimismo, el Explorador de scripts también admite el filtrado del conjunto de scripts cargado actualmente por su propiedad de identificador.  Simplemente escriba en el cuadro de filtro y los resultados de la lista del Explorador de scripts se filtran en función de los criterios indicados.
   
    ![Captura de pantalla del Explorador de scripts con los resultados filtrados](./media/documentdb-view-scripts/scriptexplorerfilterresults.png)

    > [!IMPORTANT] 
    > La funcionalidad de filtro del Explorador de scripts solo filtra desde el conjunto de documentos cargado ***actualmente*** y no actualiza la colección seleccionada actualmente.

1. Para actualizar la lista de scripts cargados en el Explorador de scripts, simplemente haga clic en el comando **Actualizar** de la parte superior de la hoja.
   
    ![Captura de pantalla del comando actualizar del Explorador de scripts](./media/documentdb-view-scripts/scriptexplorerrefresh.png)

## <a name="create-view-and-edit-stored-procedures-triggers-and-user-defined-functions"></a>Procedimientos almacenados, desencadenadores y funciones definidas por el usuario (UDF)
El Explorador de scripts le permite llevar a cabo con facilidad operaciones de CRUD en artefactos de programación del lado servidor de Cosmos DB.  

* Para crear un script, solo tiene que hacer clic en el comando de creación correspondiente del Explorador de scripts, proporcionar un identificador, escribir el contenido del script y hacer clic en **Guardar**.
  
    ![Captura de pantalla de la opción de creación del Explorador de scripts que muestra el editor de JavaScript](./media/documentdb-view-scripts/scriptexplorercreatecommand.png)
* Al crear un desencadenador, debe especificar también la operación del desencadenador y del tipo de desencadenador.
  
    ![Captura de pantalla de la opción de crear desencadenador del Explorador de scripts](./media/documentdb-view-scripts/scriptexplorercreatetrigger.png)
* Para ver un script, basta con hacer clic en el script que le interesa.
  
    ![Captura de pantalla de la experiencia de vista de script del Explorador de scripts](./media/documentdb-view-scripts/scriptexplorerviewscript.png)
* Para editar un script, solo tiene que realizar los cambios que desee en el editor de JavaScript y hacer clic en **Guardar**.
  
    ![Captura de pantalla de la experiencia de vista de script del Explorador de scripts](./media/documentdb-view-scripts/scriptexplorereditscript.png)
* Para descartar los cambios pendientes a un script, solo tiene que hacer clic en el comando **Descartar** .
  
    ![Captura de pantalla de la experiencia de descartar cambios del Explorador de scripts](./media/documentdb-view-scripts/scriptexplorerdiscardchanges.png)
* El Explorador de scripts también le permite ver fácilmente las propiedades del sistema del script cargado actualmente, si hace clic en el comando **Propiedades** .
  
    ![Captura de pantalla de la vista de propiedades de script del Explorador de scripts](./media/documentdb-view-scripts/scriptproperties.png)
  
  > [!NOTE]
  > La propiedad de marca de tiempo (_ts) se representa internamente como tiempo de época, pero el Explorador de scripts muestra el valor en formato GMT en lenguaje natural.
  > 
  > 
* Para eliminar un script, selecciónelo en el Explorador de scripts y haga clic en el comando **Eliminar** .
  
    ![Captura de pantalla del comando eliminar del Explorador de scripts](./media/documentdb-view-scripts/scriptexplorerdeletescript1.png)
* Para confirmar la acción de eliminación, haga clic en **Sí** o para cancelar la acción de eliminación, haga clic en **No**.
  
    ![Captura de pantalla del comando eliminar del Explorador de scripts](./media/documentdb-view-scripts/scriptexplorerdeletescript2.png)

## <a name="execute-a-stored-procedure"></a>Ejecutar un procedimiento almacenado
> [!WARNING]
> En el Explorador de scripts aún no se admite la ejecución de los procedimientos almacenados para colecciones de particiones del servidor. Para obtener más información, visite [Partición y escalado de datos en DocumentDB](documentdb-partition-data.md).
> 
> 

Explorador de scripts permite ejecutar procedimientos almacenados de servidor del Portal de Azure.

* Al abrir una hoja para crear un procedimiento almacenado, ya se proporciona un script predeterminado (*prefix*). Para ejecutar el script *prefix* o su propio script, agregue valores para *id* e *inputs*. Para los procedimientos almacenados que aceptan varios parámetros, todas las entradas deben estar incluidas en una matriz (por ejemplo, *["foo", "bar"]*).
  
    ![Captura de pantalla de la hoja Procedimientos almacenados del Explorador de scripts para agregar la entrada y ejecutar un procedimiento almacenado](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure-input.png)
* Para ejecutar un procedimiento almacenado, simplemente haga clic en el comando **Guardar y ejecutar** en el panel del editor de scripts.
  
  > [!NOTE]
  > El comando **Guardar y ejecutar** guardará el procedimiento almacenado antes de ejecutarlo, lo que significa que sobrescribirá la versión del procedimiento almacenado guardada anteriormente.
  > 
  > 
* Las ejecuciones de procedimientos almacenados correctas tendrán un estado *El procedimiento almacenado se ha guardado y ejecutado correctamente* y los resultados devueltos se rellenarán en el panel *Resultados*.
  
    ![Captura de pantalla de la hoja Procedimientos almacenados del Explorador de scripts para ejecutar un procedimiento almacenado](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure.png)
* Si la ejecución encuentra un error, el error se rellenará en el panel *Resultados* .
  
    ![Captura de pantalla de la vista de propiedades de script del Explorador de scripts Ejecución de un procedimiento almacenado con errores](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure-error.png)

## <a name="work-with-scripts-outside-the-portal"></a>Trabajo con scripts fuera del portal
El Explorador de scripts en el Portal de Azure es simplemente una forma de trabajar con procedimientos almacenados, desencadenadores y funciones definidas por el usuario en DocumentDB. También se puede trabajar con scripts mediante la API de REST y los [SDK de cliente](documentdb-sdk-dotnet.md). La documentación de API de REST incluye ejemplos para trabajar con [procedimientos almacenados mediante REST](https://msdn.microsoft.com/library/azure/mt489092.aspx), [funciones definidas por el usuario mediante REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) y [desencadenadores mediante REST](https://msdn.microsoft.com/library/azure/mt489116.aspx). También hay ejemplos disponibles que muestran cómo [trabajar con scripts mediante C#](documentdb-dotnet-samples.md#server-side-programming-examples) y [trabajar con scripts mediante Node.js](documentdb-nodejs-samples.md#server-side-programming-examples).

## <a name="next-steps"></a>Pasos siguientes
Más información sobre programación del lado servidor de DocumentDB en el artículo [Programación en el servidor de DocumentDB: procedimientos almacenados, desencadenadores de base de datos y UDF](documentdb-programming.md) .




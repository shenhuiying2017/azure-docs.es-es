---
title: "Adición del conector de Azure SQL Database en Logic Apps | Microsoft Docs"
description: "Información general sobre el conector de Azure SQL Database con los parámetros de la API de REST"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: d8a319d0-e4df-40cf-88f0-29a6158c898c
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: def2b65f009c377233c45356f8fa661b86d73f51
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-azure-sql-database-connector"></a>Introducción al conector de Azure SQL Database
Mediante el conector de SQL Database Azure, cree flujos de trabajo para su organización que administren datos de las tablas. 

Con SQL Database:

* Agregue un nuevo cliente a una base de datos de clientes o actualice un pedido en una base de datos de pedidos para crear el flujo de trabajo.
* Use acciones para obtener una fila de datos, insertar una fila nueva e incluso eliminarla. Por ejemplo, al crear un registro en Dynamics CRM Online (desencadenador), inserte una fila en Azure SQL Database (acción). 

En este tema se muestra cómo usar el conector de SQL Database en una aplicación lógica, y se enumeran las acciones.

Para más información sobre Logic Apps, consulte [¿Qué son las aplicaciones lógicas?](../logic-apps/logic-apps-overview.md) y [Creación de una aplicación lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-azure-sql-database"></a>Conexión a una instancia de Azure SQL Database
Antes de que la aplicación lógica pueda acceder a cualquier servicio, cree primero una *conexión* a este. Una conexión proporciona conectividad entre una aplicación lógica y otro servicio. Por ejemplo, para conectarse a SQL Database, primero cree una *conexión* de SQL Database. Para crear una conexión, escriba las credenciales que utiliza normalmente para acceder al servicio al que se está conectando. Por lo tanto, en la SQL Database, escriba sus credenciales de SQL Database para crear la conexión. 

#### <a name="create-the-connection"></a>Creación de la conexión
> [!INCLUDE [Create the connection to SQL Azure](../../includes/connectors-create-api-sqlazure.md)]
> 
> 

## <a name="use-a-trigger"></a>Uso de un desencadenador
Este conector no tiene ningún desencadenador. Utilice otros desencadenadores para iniciar la aplicación lógica; por ejemplo, un desencadenador de periodicidad, un desencadenador HTTP webhook, los desencadenadores disponibles con otros conectores, etc. En [Creación de una nueva aplicación lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md) se puede ver un ejemplo.

## <a name="use-an-action"></a>Uso de una acción
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información acerca de las acciones](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Seleccione el signo más. Aparecen varias opciones: **Agregar una acción**, **Agregar una condición** o una de las opciones de **Más**.
   
    ![](./media/connectors-create-api-sqlazure/add-action.png)
2. Elija **Add an action**(Agregar una acción).
3. En el cuadro de texto, escriba "sql" para obtener una lista de todas las acciones disponibles.
   
    ![](./media/connectors-create-api-sqlazure/sql-1.png) 
4. En nuestro ejemplo, elija **SQL Server - Obtener fila**. Si ya existe una conexión, seleccione el **Nombre de la tabla** en la lista desplegable y escriba el **Identificador de fila** que desee devolver.
   
    ![](./media/connectors-create-api-sqlazure/sample-table.png)
   
    Si se le solicita la información de conexión, escriba los detalles para crear la conexión. Estas propiedades se describen en la sección [Creación de la conexión](connectors-create-api-sqlazure.md#create-the-connection) de este tema. 
   
   > [!NOTE]
   > En este ejemplo, devolvemos una fila de una tabla. Para ver los datos de esta fila, agregue otra acción que cree un archivo con los campos de la tabla. Por ejemplo, agregue una acción de OneDrive que use los campos FirstName y LastName para crear un nuevo archivo en la cuenta de almacenamiento de la nube. 
   > 
   > 
5. **Guarde** los cambios (esquina superior izquierda de la barra de herramientas). La aplicación lógica se guarda y se puede habilitar automáticamente.

## <a name="connector-specific-details"></a>Detalles específicos del conector

Vea los desencadenadores y las acciones definidos en Swagger y vea también todos los límites en los [detalles del conector](/connectors/sql/). 

## <a name="next-steps"></a>pasos siguientes
[Crear una aplicación lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Explore los demás conectores disponibles en Logic Apps en nuestra [lista de API](apis-list.md).


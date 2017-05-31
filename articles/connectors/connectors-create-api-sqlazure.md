---
title: "Adición del conector de Azure SQL Database en Logic Apps | Microsoft Docs"
description: "Información general sobre el conector de Base de datos SQL de Azure con los parámetros de la API de REST"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 3c228be32539050123b01c5ccd74547b0d04ed28
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="get-started-with-the-azure-sql-database-connector"></a>Introducción al conector de Base de datos SQL de Azure
Mediante el conector de Base de datos SQL Azure, cree flujos de trabajo para su organización que administren datos de las tablas. 

Con Base de datos SQL:

* Agregue un nuevo cliente a una base de datos de clientes o actualice un pedido en una base de datos de pedidos para crear el flujo de trabajo.
* Use acciones para obtener una fila de datos, insertar una fila nueva e incluso eliminarla. Por ejemplo, al crear un registro en Dynamics CRM Online (desencadenador), inserte una fila en una base de datos SQL de Azure (acción). 

En este tema se muestra cómo usar el conector de SQL Database en una aplicación lógica, y se enumeran las acciones.

> [!NOTE]
> Esta versión del artículo se aplica a la disponibilidad general de las aplicaciones lógicas. 
> 
> 

Para más información sobre Logic Apps, consulte [¿Qué son las aplicaciones lógicas?](../logic-apps/logic-apps-what-are-logic-apps.md) y [Creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-azure-sql-database"></a>Conexión a una Base de datos SQL de Azure
Antes de que la aplicación lógica pueda acceder a cualquier servicio, cree primero una *conexión* a este. Una conexión proporciona conectividad entre una aplicación lógica y otro servicio. Por ejemplo, para conectarse a SQL Database, primero cree una *conexión* de SQL Database. Para crear una conexión, escriba las credenciales que utiliza normalmente para acceder al servicio al que se está conectando. Por lo tanto, en la base de datos de SQL, escriba sus credenciales de la base de datos SQL para crear la conexión. 

#### <a name="create-the-connection"></a>Creación de la conexión
> [!INCLUDE [Create the connection to SQL Azure](../../includes/connectors-create-api-sqlazure.md)]
> 
> 

## <a name="use-a-trigger"></a>Uso de un desencadenador
Este conector no tiene ningún desencadenador. Utilice otros desencadenadores para iniciar la aplicación lógica; por ejemplo, un desencadenador de periodicidad, un desencadenador HTTP webhook, los desencadenadores disponibles con otros conectores, etc. En [Creación de una nueva aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md) se puede ver un ejemplo.

## <a name="use-an-action"></a>Uso de una acción
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información acerca de las acciones](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

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

## <a name="view-the-swagger"></a>Visualización de Swagger
Vea los [detalles de Swagger](/connectors/sql/). 

## <a name="next-steps"></a>Pasos siguientes
[Creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md). Explore los demás conectores disponibles en Logic Apps en nuestra [lista de API](apis-list.md).



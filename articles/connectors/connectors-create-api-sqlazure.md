---
title: 'Conexión con el servidor SQL Server o Azure SQL Database: Azure Logic Apps | Microsoft Docs'
description: Cree conexiones con el servidor SQL Server local y con Azure SQL Database en la nube desde Azure Logic Apps
services: logic-apps
documentationcenter: ''
author: ecfan
manager: cfowler
editor: ''
tags: connectors
ms.assetid: d8a319d0-e4df-40cf-88f0-29a6158c898c
ms.service: logic-apps
ms.workload: logic-apps
ms.devlang: ''
ms.tgt_pltfrm: ''
ms.topic: article
ms.date: 05/15/2018
ms.author: estfan
ms.openlocfilehash: 4917f784c07919155e006711026899ce7712fecb
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
ms.locfileid: "34164805"
---
# <a name="connect-to-sql-server-or-azure-sql-database-from-azure-logic-apps"></a>Conexión con el servidor SQL Server o Azure SQL Database desde Azure Logic Apps

En este artículo se muestra cómo puede acceder a los datos de la base de datos SQL desde una aplicación lógica con el conector de SQL Server. De este modo, puede crear aplicaciones lógicas que automaticen las tareas y los flujos de trabajo para administrar los datos. El conector funciona tanto para [SQL Server local](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) como para [Azure SQL Database en la nube](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview). 

Puede compilar aplicaciones lógicas que se ejecutan cuando las desencadenan eventos en la base de datos SQL o en otros sistemas,como Dynamics CRM Online. Las aplicaciones lógicas también pueden obtener, insertar o eliminar datos y, además, ejecutar consultas SQL o procedimientos almacenados. Por ejemplo, puede compilar una aplicación lógica que comprueba automáticamente si hay registros nuevos en Dynamics CRM Online, agrega elementos a la base de datos SQL para cualquier registro nuevo y envía alertas de correo electrónico.

Si no tiene una suscripción de Azure, <a href="https://azure.microsoft.com/free/" target="_blank">regístrese para obtener una cuenta gratuita de Azure</a>. Si nunca trabajó con las aplicaciones lógicas, consulte [¿Qué es Azure Logic Apps](../logic-apps/logic-apps-overview.md) y el artículo sobre [Inicio rápido: creación de su primera aplicación lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para información técnica específica del conector, consulte la <a href="https://docs.microsoft.com/connectors/sql/" target="blank">referencia sobre el conector de SQL Server</a>.

## <a name="prerequisites"></a>requisitos previos

* La aplicación lógica en la que necesita acceso a la base de datos SQL. Para iniciar la aplicación lógica con un desencadenador de SQL, necesita una [aplicación lógica en blanco](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* Una [base de datos SQL de Azure](../sql-database/sql-database-get-started-portal.md) o una [base de datos SQL Server](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) 

  Las tablas deben tener datos para que la aplicación lógica pueda devolver los resultados cuando se llame a las operaciones. Si crea una instancia de Azure SQL Database, puede usar las bases de datos de ejemplo incluidas. 

* El nombre del servidor SQL, el nombre de la base de datos, el nombre de usuario y la contraseña. Necesita estas credenciales para poder autorizar que la lógica acceda al servidor SQL. 

  * Para Azure SQL Database, puede encontrar estos detalles en la cadena de conexión o en Azure Portal, en las propiedades de SQL Database:

    "Server=tcp:<*NombreDelServidor*>.database.windows.net,1433;Initial Catalog=<*NombreDeLaBaseDeDatos*>;Persist Security Info=False;User ID=<*NombreDeUsuario*>;Password=<*Contraseña*>;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"

  * Para SQL Server, puede encontrar estos detalles en la cadena de conexión: 

    "Server=<*DirecciónDelServidor*>;Database=<*NombreDeLaBaseDeDatos*>;User Id=<*NombreDeUsuario*>;Password=<*Contraseña*>;"

* Antes de que pueda conectar las aplicaciones lógicas con sistemas locales como SQL Server, debe [configurar una puerta de enlace de datos local](../logic-apps/logic-apps-gateway-install.md). De ese modo, puede seleccionar la puerta de enlace al crear la conexión de SQL de la aplicación lógica.

<a name="add-sql-trigger"></a>

## <a name="add-sql-trigger"></a>Incorporación del desencadenador de SQL

En Azure Logic Apps, cada aplicación lógica debe comenzar con un [desencadenador](../logic-apps/logic-apps-overview.md#logic-app-concepts), que se activa cuando sucede un evento específico o cuando se cumple una condición determinada. Cada vez que el desencadenador se activa, el motor de Logic Apps crea una instancia de aplicación lógica y empieza a ejecutar el flujo de trabajo de la aplicación.

1. En Azure Portal o Visual Studio, cree una aplicación lógica en blanco, con lo que se abre el diseñador de Logic Apps. En este ejemplo se usa Azure Portal.

2. En el cuadro de búsqueda, escriba "sql server" como filtro. En la lista de desencadenadores, seleccione el desencadenador de SQL que desee. 

   En este ejemplo, seleccione el desencadenador: **SQL Server - When an item is created** (SQL Server: cuando se crea un elemento)

   ![Selección del desencadenador "SQL Server - When an item is created" (SQL Server: cuando se crea un elemento)](./media/connectors-create-api-sqlazure/sql-server-trigger.png)

3. Si se le pide indicar los detalles de la conexión, [cree ahora mismo su conexión de SQL](#create-connection). 
   O bien, si la conexión ya existe, seleccione el **nombre de la tabla** que desea en la lista.

   ![Seleccionar tabla](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

4. Establezca las propiedades **Interval** (Intervalo) y **Frequency** (Frecuencia), que especifican la frecuencia con que la aplicación lógica comprueba la tabla.

   En este ejemplo solo se comprueba la tabla seleccionada, ningún otro elemento. 
   Para hacer algo más interesante, agregue acciones que realicen las tareas que quiere. 
   
   Por ejemplo, para ver el elemento nuevo en la tabla, podría agregar otras acciones, como crear un archivo que tenga campos de la tabla y, luego, enviar alertas de correo electrónico. 
   Para información sobre otras acciones de este conector o de otros conectores, consulte los [conectores de Logic Apps](../connectors/apis-list.md).

5. Cuando esté listo, elija **Guardar** en la barra de herramientas del diseñador. 

   Este paso habilita y publica de manera automática la aplicación lógica en vivo en Azure. 

<a name="add-sql-action"></a>

## <a name="add-sql-action"></a>Incorporación de acción de SQL

En Azure Logic Apps, una [acción](../logic-apps/logic-apps-overview.md#logic-app-concepts) es un paso del flujo de trabajo que sigue a un desencadenador u otra acción. En este ejemplo, la aplicación lógica empieza con el [desencadenador de periodicidad](../connectors/connectors-native-recurrence.md) y llama a una acción que obtiene una fila de una base de datos SQL.

1. En Azure Portal o Visual Studio, abra la aplicación lógica el Diseñador de Logic Apps. En este ejemplo se usa Azure Portal.

2. En el Diseñador de Logic Apps, en el desencadenador o la acción, elija **Nuevo paso** > **Agregar una acción**.

   ![Elegir "Nuevo paso", "Agregar una acción"](./media/connectors-create-api-sqlazure/add-action.png)
   
   Para agregar una acción entre los pasos existentes, mueva el mouse sobre la flecha de conexión. 
   Elija el signo más (**+**) que aparece y, luego, elija **Agregar una acción**.

2. En el cuadro de búsqueda, escriba "sql server" como filtro. En la lista de acciones, seleccione la acción SQL que quiera. 

   En este ejemplo seleccione esta acción, que obtiene un solo registro: **SQL Server - Get row** (SQL Server: obtener fila)

   ![Escribir "sql server", seleccionar "SQL Server - Get row" (SQL Server: obtener fila)](./media/connectors-create-api-sqlazure/select-sql-get-row.png) 

3. Si se le pide indicar los detalles de la conexión, [cree ahora mismo su conexión de SQL](#create-connection). 
   O bien, si la conexión existe, seleccione un **nombre de tabla** y escriba el **identificador de fila** del registro que desee.

   ![Escribir el nombre de la tabla y el identificador de la fila](./media/connectors-create-api-sqlazure/table-row-id.png)
   
   En este ejemplo solo se devuelve una fila de la tabla seleccionada, ningún otro elemento. 
   Para ver los datos de esta fila, puede agregar otras acciones que crean un archivo con campos de la fila para una revisión posterior y almacene ese archivo en una cuenta de almacenamiento en la nube. Para información sobre otras acciones de este conector o de otros conectores, consulte los [conectores de Logic Apps](../connectors/apis-list.md).

4. Cuando esté listo, elija **Guardar** en la barra de herramientas del diseñador. 

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Conectarse a la base de datos

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="process-data-in-bulk"></a>Procesamiento de datos de manera masiva

Cuando trabaja con conjuntos de resultados tan grandes que el conector no devuelve todos los resultados al mismo tiempo o si quiere un mejor control sobre el tamaño y la estructura de los conjuntos de resultados, puede usar la *paginación*, que ayuda a administrar los resultados como conjuntos más pequeños. 

[!INCLUDE [Set up pagination for results exceeding default page size](../../includes/connectors-pagination-bulk-data-transfer.md)]

### <a name="create-a-stored-procedure"></a>Creación de un procedimiento almacenado

Al obtener o insertar varias filas, la aplicación lógica puede iterar a través de esos elementos mediante un [*bucle Until*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) dentro de estos [límites](../logic-apps/logic-apps-limits-and-config.md). Pero a veces la aplicación lógica tiene que trabajar con conjuntos de registros tan grandes, con miles o millones de filas, que el usuario quiere minimizar los costos de las llamadas a la base de datos. 

En su lugar, puede crear un <a href="https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine" target="blank">*procedimiento almacenado*</a> que se ejecuta en la instancia de SQL y usa la instrucción **SELECT - ORDER BY** para organizar los resultados de la manera deseada. Esta solución le permite tener mayor control sobre el tamaño y la estructura de los resultados. La aplicación lógica llama al procedimiento almacenado mediante la acción **Ejecutar procedimiento almacenado** del conector de SQL Server. 

Para detalles de las soluciones, consulte estos artículos:

* <a href="https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx" target="_blank">SQL Pagination for bulk data transfer with Logic Apps</a> (Paginación de SQL para la transferencia de datos masiva con Logic Apps)

* <a href="https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql" target="_blank">Cláusula SELECT - ORDER BY</a>

## <a name="connector-specific-details"></a>Detalles específicos del conector

Para información técnica sobre los desencadenadores, las acciones y los límites de este conector, consulte los [detalles de referencia del conector](/connectors/sql/). 

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre otros [conectores de Logic Apps](../connectors/apis-list.md)


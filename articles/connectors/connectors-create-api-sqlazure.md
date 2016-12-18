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
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 003cd98347c2a461f2a3ce78ae7f6a0e6fb2e4a2


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

Para más información sobre Logic Apps, consulte [¿Qué son las aplicaciones lógicas?](../app-service-logic/app-service-logic-what-are-logic-apps.md) y [Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="connect-to-azure-sql-database"></a>Conexión a una Base de datos SQL de Azure
Antes de que la aplicación lógica pueda acceder a cualquier servicio, cree primero una *conexión* a este. Una conexión proporciona conectividad entre una aplicación lógica y otro servicio. Por ejemplo, para conectarse a SQL Database, primero cree una *conexión* de SQL Database. Para crear una conexión, escriba las credenciales que utiliza normalmente para acceder al servicio al que se está conectando. Por lo tanto, en la base de datos de SQL, escriba sus credenciales de la base de datos SQL para crear la conexión. 

#### <a name="create-the-connection"></a>Creación de la conexión
> [!INCLUDE [Create the connection to SQL Azure](../../includes/connectors-create-api-sqlazure.md)]
> 
> 

## <a name="use-a-trigger"></a>Uso de un desencadenador
Este conector no tiene ningún desencadenador. Utilice otros desencadenadores para iniciar la aplicación lógica; por ejemplo, un desencadenador de periodicidad, un desencadenador HTTP webhook, los desencadenadores disponibles con otros conectores, etc. En [Creación de una nueva aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md) se puede ver un ejemplo.

## <a name="use-an-action"></a>Uso de una acción
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información acerca de las acciones](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

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

## <a name="technical-details"></a>Detalles técnicos
## <a name="sql-database-actions"></a>Acciones de la Base de datos SQL
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. El conector de la base de datos SQL incluye las siguientes acciones. 

| Acción | Description |
| --- | --- |
| [ExecuteProcedure](connectors-create-api-sqlazure.md#execute-stored-procedure) |Ejecuta un procedimiento almacenado en SQL |
| [GetRow](connectors-create-api-sqlazure.md#get-row) |Recupera una sola fila de una tabla SQL |
| [GetRows](connectors-create-api-sqlazure.md#get-rows) |Recupera filas de una tabla SQL |
| [InsertRow](connectors-create-api-sqlazure.md#insert-row) |Inserta una nueva fila en una tabla SQL |
| [DeleteRow](connectors-create-api-sqlazure.md#delete-row) |Elimina una fila de una tabla SQL |
| [GetTables](connectors-create-api-sqlazure.md#get-tables) |Recupera tablas de una base de datos SQL |
| [UpdateRow](connectors-create-api-sqlazure.md#update-row) |Actualiza una fila existente en una tabla SQL |

### <a name="action-details"></a>Detalles de la acción
En esta sección, consulte los detalles específicos acerca de cada acción, incluidas las propiedades de entrada obligatorias u opcionales y cualquier salida correspondiente asociada con el conector.

#### <a name="execute-stored-procedure"></a>Ejecutar procedimiento almacenado
Ejecuta un procedimiento almacenado en SQL.  

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| procedure * |Nombre del procedimiento |El nombre del procedimiento almacenado que desea ejecutar |
| parameters * |Parámetros de entrada |Los parámetros son dinámicos y se basan en el procedimiento almacenado que elija. <br/><br/> Por ejemplo, si está utilizando la base de datos de ejemplo Adventure Works, elija el procedimiento almacenado *ufnGetCustomerInformation*. Se mostrará el parámetro de entrada **Id. de cliente**. Escriba "6" o uno de los otros identificadores de cliente. |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
ProcedureResult: lleva el resultado de la ejecución del procedimiento almacenado

| Nombre de propiedad | Tipo de datos | Description |
| --- | --- | --- |
| OutputParameters |objeto |Valores del parámetro de salida |
| ReturnCode |integer |Código de retorno de un procedimiento |
| ResultSets |objeto |Conjuntos de resultados |

#### <a name="get-row"></a>Obtener fila
Recupera una sola fila de una tabla SQL.  

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| table * |Nombre de tabla |Nombre de la tabla SQL |
| id * |Identificador de fila |Identificador único de la fila que se va a recuperar |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
Elemento

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| ItemInternalId |string |

#### <a name="get-rows"></a>Obtener filas
Recupera filas de una tabla SQL.  

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| table* |Nombre de tabla |Nombre de la tabla SQL |
| $skip |Omitir conteo |Número de entradas para omitir (valor predeterminado = 0) |
| $top |Número máximo de entradas |Número máximo de entradas para recuperar (valor predeterminado = 256) |
| $filter |Consulta de filtro |Consulta de filtro de ODATA para restringir el número de entradas |
| $orderby |Ordenar por |Consulta orderBy de ODATA para especificar el orden de las entradas |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
ItemsList

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| value |array |

#### <a name="insert-row"></a>Insertar fila
Inserta una nueva fila en una tabla SQL.  

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| table* |Nombre de tabla |Nombre de la tabla SQL |
| item* |Fila |Fila que se va a insertar en la tabla especificada en SQL |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
Elemento

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| ItemInternalId |string |

#### <a name="delete-row"></a>Eliminar fila
Elimina una fila de una tabla SQL.  

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| table* |Nombre de tabla |Nombre de la tabla SQL |
| id* |Identificador de fila |Identificador único de la fila que se va a eliminar |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
Ninguno.

#### <a name="get-tables"></a>Obtener tablas
Recupera tablas de una base de datos SQL.  

No hay parámetros para esta llamada. 

##### <a name="output-details"></a>Detalles de salida
TablesList

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| value |array |

#### <a name="update-row"></a>Actualizar fila
Actualiza una fila existente en una tabla SQL.  

| Nombre de propiedad | Display Name (Nombre para mostrar) | Description |
| --- | --- | --- |
| table* |Nombre de tabla |Nombre de la tabla SQL |
| id* |Identificador de fila |Identificador único de la fila que se va a actualizar |
| item* |Fila |Fila con valores actualizados |

Un asterisco (*) significa que la propiedad es obligatoria.

##### <a name="output-details"></a>Detalles de salida
Elemento

| Nombre de propiedad | Tipo de datos |
| --- | --- |
| ItemInternalId |string |

### <a name="http-responses"></a>Respuestas HTTP
Al realizar llamadas a las distintas acciones, es posible que obtenga determinadas respuestas. En la tabla siguiente se describen las respuestas y sus descripciones:  

| Nombre | Descripción |
| --- | --- |
| 200 |OK |
| 202 |Accepted |
| 400 |Bad Request |
| 401 |No autorizado |
| 403 |Prohibido |
| 404 |No encontrado |
| 500 |Error interno del servidor. Error desconocido |
| default |Error en la operación. |

## <a name="next-steps"></a>Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md). Explore los demás conectores disponibles en Logic Apps en nuestra [lista de API](apis-list.md).




<!--HONumber=Nov16_HO3-->



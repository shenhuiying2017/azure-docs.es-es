<properties
    pageTitle="Adición del conector de Base de datos SQL de Azure en las aplicaciones lógicas | Microsoft Azure"
    description="Información general sobre el conector de Base de datos SQL de Azure con los parámetros de la API de REST"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/18/2016"
   ms.author="mandia"/>


# Introducción al conector de Base de datos SQL de Azure
Mediante el conector de Base de datos SQL Azure, cree flujos de trabajo para su organización que administren datos de las tablas. También:

- Agregue un nuevo cliente a una base de datos de clientes o actualice un pedido en una base de datos de pedidos para crear el flujo de trabajo.
- Use acciones para obtener una fila de datos, insertar una fila nueva e incluso eliminarla. Por ejemplo, al crear un registro en Dynamics CRM Online (desencadenador), inserte una fila en una Base de datos SQL de Azure (acción).

En este tema se muestra cómo usar el conector de la base de datos SQL en una aplicación lógica y también se enumeran las acciones.

>[AZURE.NOTE] Esta versión del artículo se aplica a la disponibilidad general de las aplicaciones lógicas.

Para más información acerca de las aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

>[AZURE.INCLUDE [Qué necesita para empezar](../../includes/connectors-create-api-sqlazure.md)]

## Conexión a una Base de datos SQL de Azure

Antes de que la aplicación lógica pueda acceder a cualquier servicio, cree primero una *conexión* a este. Una conexión proporciona conectividad entre una aplicación lógica y otro servicio. Por ejemplo, para conectarse a la base de datos SQL, primero cree una *conexión* de base de datos SQL. Para crear una conexión, escriba las credenciales que utiliza normalmente para acceder al servicio al que se está conectando. Por lo tanto, en la base de datos de SQL, escriba sus credenciales de la base de datos SQL para crear la conexión.

Cuando agrega este conector a las aplicaciones lógicas, crea una conexión a la base de datos SQL. La primera vez que agregue este conector, se le pedirá la información de conexión:

![](./media/connectors-create-api-sqlazure/connection-details.png)

#### Creación de la conexión

1. Escriba los detalles de la base de datos SQL. Aquellas propiedades con un asterisco son obligatorias.

	| Propiedad | Detalles |
|---|---|
| Connect via Gateway (Conectar a través de puerta de enlace) | Deje esta opción desactivada. Se utiliza al conectarse a un servidor SQL Server local. |
| Nombre de la conexión * | Escriba cualquier nombre para la conexión. | 
| Nombre de SQL Server * | Escriba el nombre del servidor, que será similar a *servername.database.windows.net*. El nombre del servidor aparece en las propiedades de la base de datos SQL en el Portal de Azure y también se muestra en la cadena de conexión. | 
| Nombre de la base de datos SQL * | Escriba el nombre que asignó a la base de datos SQL. Este nombre se muestra en las propiedades de la base de datos SQL en la cadena de conexión: Initial Catalog=*nombreDeBaseDeDatosSQL*. | 
| Nombre de usuario * | Escriba el nombre de usuario que creó al crear la base de datos SQL. Este nombre se muestra en las propiedades de la base de datos SQL en el Portal de Azure. | 
| Contraseña * | Escriba la contraseña que creó al crear la base de datos SQL. | 

	Estas credenciales se usan para autorizar a la aplicación lógica a conectarse y acceder a sus datos SQL. Una vez completado, los detalles de la conexión presentan un aspecto similar al siguiente:

	![paso de creación de conexión de SQL Azure](./media/connectors-create-api-sqlazure/sample-connection.png)

2. Seleccione **Crear**.

## Uso de un desencadenador

Este conector no tiene ningún desencadenador. Utilice otros desencadenadores para iniciar la aplicación lógica como, por ejemplo, un desencadenador de periodicidad, un desencadenador de Webhook HTTP, los desencadenadores disponibles con otros conectores y otros varios. La [creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md) sirve de ejemplo.

## Uso de una acción
	
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica.

1. Seleccione el signo más. Aparecen varias opciones: **Agregar una acción**, **Agregar una condición** o una de las opciones de **Más**.

	![](./media/connectors-create-api-sqlazure/add-action.png)

2. Elija **Agregar una acción**.

3. En el cuadro de texto, escriba "sql" para obtener una lista de todas las acciones disponibles.

	![](./media/connectors-create-api-sqlazure/sql-1.png)

4. En nuestro ejemplo, elija **SQL Server - Get row** (SQL Server - Obtener fila). Si ya existe una conexión, seleccione el **nombre de la tabla** en la lista desplegable y escriba el **identificador de fila** que desee devolver.

	![](./media/connectors-create-api-sqlazure/sample-table.png)

	Si se le solicita la información de conexión, escriba los detalles para crear la conexión. [Creación de la conexión](connectors-create-api-sqlazure.md#create-the-connection): en este tema se describen estas propiedades.

	> [AZURE.NOTE] En este ejemplo, devolvemos una fila de una tabla. Para ver los datos de esta fila, agregue otra acción que cree un archivo con los campos de la tabla. Por ejemplo, agregue una acción de OneDrive que use los campos FirstName y LastName para crear un nuevo archivo en la cuenta de almacenamiento de la nube.

5. **Guarde** los cambios (esquina superior izquierda de la barra de herramientas). La aplicación lógica se guarda y se puede habilitar automáticamente.


## Detalles técnicos

## Acciones
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. El conector de la base de datos SQL incluye las siguientes acciones.

|Acción|Descripción|
|--- | ---|
|[ExecuteProcedure](connectors-create-api-sqlazure.md#execute-stored-procedure)|Ejecuta un procedimiento almacenado en SQL|
|[GetRow](connectors-create-api-sqlazure.md#get-row)|Recupera una sola fila de una tabla SQL|
|[GetRows](connectors-create-api-sqlazure.md#get-rows)|Recupera filas de una tabla SQL|
|[InsertRow](connectors-create-api-sqlazure.md#insert-row)|Inserta una nueva fila en una tabla SQL|
|[DeleteRow](connectors-create-api-sqlazure.md#delete-row)|Elimina una fila de una tabla SQL|
|[GetTables](connectors-create-api-sqlazure.md#get-tables)|Recupera tablas de una base de datos SQL|
|[UpdateRow](connectors-create-api-sqlazure.md#update-row)|Actualiza una fila existente en una tabla SQL|

### Detalles de la acción

En esta sección, consulte los detalles específicos acerca de cada acción, incluidas las propiedades de entrada obligatorias u opcionales y cualquier salida correspondiente asociada con el conector.


#### Ejecutar procedimiento almacenado
Ejecuta un procedimiento almacenado en SQL.

| Nombre de propiedad| Display Name (Nombre para mostrar) |Descripción|
| ---|---|---|
|procedure * | Nombre del procedimiento | El nombre del procedimiento almacenado que desea ejecutar |
|parameters * | Parámetros de entrada | Los parámetros son dinámicos y se basan en el procedimiento almacenado que elija. <br/><br/> Por ejemplo, si está utilizando la base de datos de ejemplo Adventure Works, elija el procedimiento almacenado *ufnGetCustomerInformation*. Se mostrará el parámetro de entrada **Id. de cliente**. Escriba "6" o uno de los otros identificadores de cliente. |

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
ProcedureResult: lleva el resultado de la ejecución del procedimiento almacenado

| Nombre de propiedad | Tipo de datos | Descripción |
|---|---|---|
|OutputParameters|objeto|Valores del parámetro de salida |
|ReturnCode|integer|Código de retorno de un procedimiento |
|ResultSets|objeto| Conjuntos de resultados|


#### Obtener fila 
Recupera una sola fila de una tabla SQL.

| Nombre de propiedad| Display Name (Nombre para mostrar) |Descripción|
| ---|---|---|
|table * | Nombre de tabla |Nombre de la tabla SQL|
|id * | Identificador de fila |Identificador único de la fila que se va a recuperar|

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
Elemento

| Nombre de propiedad | Tipo de datos |
|---|---|
|ItemInternalId|cadena|


#### Obtener filas 
Recupera filas de una tabla SQL.

|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|table*|Nombre de tabla|Nombre de la tabla SQL|
|$skip|Omitir conteo|Número de entradas para omitir (valor predeterminado = 0)|
|$top|Número máximo de entradas|Número máximo de entradas para recuperar (valor predeterminado = 256)|
|$filter|Consulta de filtro|Consulta de filtro de ODATA para restringir el número de entradas|
|$orderby|Ordenar por|Consulta orderBy de ODATA para especificar el orden de las entradas|

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
ItemsList

| Nombre de propiedad | Tipo de datos |
|---|---|
|value|array|


#### Insertar fila 
Inserta una nueva fila en una tabla SQL.

|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|table*|Nombre de tabla|Nombre de la tabla SQL|
|item*|Fila|Fila que se va a insertar en la tabla especificada en SQL|

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
Elemento

| Nombre de propiedad | Tipo de datos |
|---|---|
|ItemInternalId|cadena|


#### Eliminar fila 
Elimina una fila de una tabla SQL.

|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|table*|Nombre de tabla|Nombre de la tabla SQL|
|id*|Identificador de fila|Identificador único de la fila que se va a eliminar|

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida
Ninguno.

#### Obtener tablas 
Recupera tablas de una base de datos SQL.

No hay parámetros para esta llamada.

##### Detalles de salida 
TablesList

| Nombre de propiedad | Tipo de datos |
|---|---|
|value|array|

#### Actualizar fila 
Actualiza una fila existente en una tabla SQL.

|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|table*|Nombre de tabla|Nombre de la tabla SQL|
|id*|Identificador de fila|Identificador único de la fila que se va a actualizar|
|item*|Fila|Fila con valores actualizados|

Un asterisco (*) significa que la propiedad es obligatoria.

##### Detalles de salida  
Elemento

| Nombre de propiedad | Tipo de datos |
|---|---|
|ItemInternalId|cadena|


### Respuestas HTTP

Al realizar llamadas a las distintas acciones, es posible que obtenga determinadas respuestas. En la tabla siguiente se describen las respuestas y sus descripciones:

|Nombre|Descripción|
|---|---|
|200|OK|
|202|Accepted|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido|
|default|Error en la operación.|


## Pasos siguientes

[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md). Explore los demás conectores disponibles en aplicaciones lógicas en nuestra [lista de API](apis-list.md).

<!---HONumber=AcomDC_0720_2016-->
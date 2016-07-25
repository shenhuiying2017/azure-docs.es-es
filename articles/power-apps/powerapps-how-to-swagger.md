<properties
	pageTitle="Personalización de la definición de Swagger para PowerApps y flujos de lógica| Microsoft Azure"
	description="Consulte las extensiones de esquema que Swagger requiere para trabajar con PowerApps y flujos de lógica"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="sunaysv"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/12/2016"
   ms.author="mandia"/>


# Personalización de la definición de Swagger para PowerApps y flujos de lógica

>[AZURE.IMPORTANT] Este tema se ha movido a powerapps.microsoft.com en [Customize your Swagger definition for PowerApps and Flows](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/) (Personalización de la definición de Swagger para PowerApps y flujos). Vaya a PowerApps para ver la versión más reciente. Este vínculo de Azure está en proceso de archivo.


## Definición de la API de Swagger 2.0 de autor para la API

Para más información sobre cómo agregar Swagger a WebAPI, consulte [Swashbuckle][1].

## Extensiones de esquema
Además de las especificaciones de Swagger estándar, existen algunas extensiones de Swagger adicionales disponibles cuando se crea una API personalizada para PowerApps y flujos de lógica. En esta sección, se enumeran y describen estas extensiones.

##### x-ms-summary
Cadena que describe los nombres para mostrar de las entidades que no tienen un campo `summary` definido en la especificación de Swagger. **Nombres de parámetro** es un ejemplo.

##### x-ms-visibility
Este valor describe si la entidad se muestra en el diseñador de flujo de lógica. Están disponibles los siguientes valores:

- "none" (valor predeterminado)
- "advanced"
- "internal": el diseñador de flujo de lógica no muestra estas operaciones

Si una operación está marcada como "importante", se espera que el cliente de flujo de lógica resalte estas operaciones.

##### x-ms-trigger
Define si esta operación se puede usar como un desencadenador en el flujo de lógica. Las opciones incluyen:
	
- ninguno (predeterminado): la operación no se puede usar como desencadenador.
- single: esta operación también puede usarse como un desencadenador.
- por lotes: esta operación puede usarse como un desencadenador. Además, esta operación responde con una "matriz" de objetos JSON y el flujo de lógica activa un desencadenador para cada elemento de la matriz.


##### x-ms-dynamic-values
Esta es un indicio para el diseñador del flujo de lógica de que la API proporciona una lista de valores permitidos de forma dinámica para este parámetro. El diseñador del flujo de lógica puede invocar una operación definida por el valor de este campo y extraer los valores posibles del resultado. El diseñador del flujo de lógica puede mostrar estos valores como opciones al usuario final.

El valor es un objeto que contiene las siguientes propiedades:
	
- `operationId`: cadena que coincide con el valor operationId de la operación que se invoca.
- `parameters`: objeto cuyas propiedades definen los parámetros necesarios para la operación.
- `value-collection`: cadena de ruta de acceso que se evalúa en una matriz de objetos en la carga útil de respuesta.
- `value-path`: cadena de ruta de acceso en el objeto dentro de "value-collection" que hace referencia al valor del parámetro.
- `value-title`: cadena de ruta de acceso en el objeto dentro de "value-collection" que hace referencia a una descripción del valor.


Ejemplo:

```javascript
"/api/tables/{table}/items": {
  "post": {
    "operationId": "TableData_CreateItem",
    "summary": "Create an object in {Salesforce}",
    "parameters": [
      {
        "name": "table",
        "x-ms-summary": "Object Type",
        "x-ms-dynamic-values": {
          "operationId": "TableMetadata_ListTables",      // operation that needs to be invoked
          "parameters": { },                              // parameters for the above operation, if any
          "value-collection": "values",                   // field that contains the collection
          "value-path": "Name",                           // field that contains the value
          "value-title": "DisplayName"                    // field that contains a display name for the value
      }
      // ...
    ]
    // ...
  }
  // ...
}
```

En este ejemplo, la especificación Swagger define la operación `TableData_CreateItem` que crea un objeto nuevo en Salesforce.

Salesforce dispone de una gran cantidad de objetos integrados. `x-ms-dynamic-values` se usa aquí para ayudar al diseñador a descifrar la lista de los objetos de Salesforce integrados. Para obtener la lista, llama a `TableMetadata_ListTables`.

##### x-ms-dynamic-schema
Este es un indicio para el diseñador de flujo de lógica que el esquema de este parámetro (o respuesta) es de naturaleza dinámica. Puede invocar una operación definida por el valor de este campo y detectar el esquema de forma dinámica. A continuación, puede mostrar una interfaz de usuario adecuada para tomar entradas del usuario o mostrar campos disponibles.

Ejemplo:

```javascript
{
  "name": "item",
  "in": "body",
  "required": true,
  "x-ms-dynamic-schema": {
    "operationId": "Metadata_GetTableSchema",
    "parameters": {
      "tablename": "{table}"              // the value that the user has selected from the above parameter
    },
    "value-path": "Schema"                // the field that contains the JSON schema
  }
},
```

Esto es útil en escenarios en los que las entradas para una operación son dinámicas. Por ejemplo, considere el caso de SQL. El esquema de cada tabla es diferente. Por tanto, cuando un usuario selecciona una tabla determinada, el diseñador de flujo de lógica debe comprender la estructura de la tabla para que pueda mostrar los nombres de las columnas. En este contexto, si la definición de Swagger tiene `x-ms-dynamic-schema`, llama a la operación correspondiente para capturar el esquema.


<!--Reference links in article-->
[1]: https://github.com/domaindrivendev/Swashbuckle/blob/master/README.md

<!---HONumber=AcomDC_0713_2016-->
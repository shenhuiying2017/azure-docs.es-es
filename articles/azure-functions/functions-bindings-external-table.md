---
title: "Enlaces de tablas externas de Azure Functions (versión preliminar) | Microsoft Docs"
description: Uso de enlaces de tablas externas en Azure Functions
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: erikre
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: alkarche
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 716438e5ea490f6716999813112305499dbe61a8
ms.contentlocale: es-es
ms.lasthandoff: 05/02/2017


---
# <a name="azure-functions-external-table-binding-preview"></a>Enlaces de tablas externas de Azure Functions (versión preliminar)
Este artículo muestra cómo manipular los datos tabulares de proveedores de SaaS (por ejemplo, Sharepoint y Dynamics) dentro de la función con enlaces integrados. Azure Functions admite enlaces de entrada y salida para tablas externas.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="api-connections"></a>Conexiones de API

Los enlaces de tabla aprovechan las conexiones externas de API para autenticarse con proveedores de SaaS de terceros. 

Al asignar un enlace, puede crear una nueva conexión de API o usar una conexión de API existente dentro del mismo grupo de recursos

### <a name="supported-api-connections-tables"></a>Conexiones de API compatibles (tablas)

|Conector|Desencadenador|Entrada|Salida|
|:-----|:---:|:---:|:---:|
|[DB2](https://docs.microsoft.com/azure/connectors/connectors-create-api-db2)||x|x
|[Dynamics 365 for Operations](https://ax.help.dynamics.com/wiki/install-and-configure-dynamics-365-for-operations-warehousing/)||x|x
|[Dynamics 365](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[Dynamics NAV](https://msdn.microsoft.com/library/gg481835.aspx)||x|x
|[Google Sheets](https://docs.microsoft.com/azure/connectors/connectors-create-api-googledrive)||x|x
|[Informix](https://docs.microsoft.com/azure/connectors/connectors-create-api-informix)||x|x
|[Dynamics 365 for Financials](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[MySQL](https://docs.microsoft.com/azure/store-php-create-mysql-database)||x|x
|[Oracle Database](https://docs.microsoft.com/azure/connectors/connectors-create-api-oracledatabase)||x|x
|[Common Data Service](https://docs.microsoft.com/common-data-service/entity-reference/introduction)||x|x
|[Salesforce](https://docs.microsoft.com/azure/connectors/connectors-create-api-salesforce)||x|x
|[SharePoint](https://docs.microsoft.com/azure/connectors/connectors-create-api-sharepointonline)||x|x
|[SQL Server](https://docs.microsoft.com/azure/connectors/connectors-create-api-sqlazure)||x|x
|[Teradata](http://www.teradata.com/products-and-services/azure/products/)||x|x
|UserVoice||x|x
|Zendesk||x|x


> [!NOTE]
> También se pueden utilizar conexiones de tablas externas en [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list).

### <a name="creating-an-api-connection-step-by-step"></a>Creación de conexión de API: paso a paso

1. Creación de una función > función personalizada ![Creación de una función personalizada](./media/functions-bindings-storage-table/create-custom-function.jpg)
1. Escenario `Experimental`  >  `ExternalTable-CSharp` plantilla > Creación de una nueva `External Table connection` 
 ![Elección de una plantilla d entrada de tabla](./media/functions-bindings-storage-table/create-template-table.jpg)
1. Elección del proveedor de SaaS > elección/creación de una conexión ![Configuración de la conexión de SaaS](./media/functions-bindings-storage-table/authorize-API-connection.jpg)
1. Selección de la conexión de API > creación de la función ![Creación de la función de tabla](./media/functions-bindings-storage-table/table-template-options.jpg)
1. Selección de `Integrate` > `External Table`
    1. Configure la conexión para utilizar la tabla de destino. Esta configuración dependerá en gran parte de los proveedores de SaaS. Se señalan a continuación en [configuración del origen de datos](#datasourcesettings)
![Configurar tabla](./media/functions-bindings-storage-table/configure-API-connection.jpg)

## <a name="usage"></a>Uso

En este ejemplo, se conecta a una tabla denominada "Contacto" con columnas Id, LastName y FirstName. El código muestra las entidades de contacto en la tabla y registra los nombres y apellidos.

### <a name="bindings"></a>Enlaces
```json
{
  "bindings": [
    {
      "type": "manualTrigger",
      "direction": "in",
      "name": "input"
    },
    {
      "type": "apiHubTable",
      "direction": "in",
      "name": "table",
      "connection": "ConnectionAppSettingsKey",
      "dataSetName": "default",
      "tableName": "Contact",
      "entityId": "",
    }
  ],
  "disabled": false
}
```
`entityId` debe estar vacío para los enlaces de tabla.

`ConnectionAppSettingsKey` identifica la configuración de aplicación que almacena la cadena de conexión de API. La configuración de la aplicación se crea automáticamente cuando se agrega una conexión de API en la interfaz de usuario integrada.

Un conector tabular proporciona conjuntos de datos, y cada conjunto de datos contiene tablas. El nombre del conjunto de datos predeterminado es "default". Los títulos de un conjunto de datos y una tabla en varios proveedores de SaaS se enumeran a continuación:

|Conector|Dataset|Tabla|
|:-----|:---|:---| 
|**SharePoint**|Sitio|Lista de SharePoint
|**SQL**|Base de datos|Tabla 
|**Hoja de cálculo de Google**|Hoja de cálculo|Hoja de cálculo 
|**Excel**|Archivo de Excel|Hoja 

<!--
See the language-specific sample that copies the input file to the output file.

* [C#](#incsharp)
* [Node.js](#innodejs)

-->
<a name="incsharp"></a>

### <a name="usage-in-c"></a>Uso en C# #

```cs
#r "Microsoft.Azure.ApiHub.Sdk"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.ApiHub;

//Variable name must match column type
//Variable type is dynamically bound to the incoming data
public class Contact
{
    public string Id { get; set; }
    public string LastName { get; set; }
    public string FirstName { get; set; }
}

public static async Task Run(string input, ITable<Contact> table, TraceWriter log)
{
    //Iterate over every value in the source table
    ContinuationToken continuationToken = null;
    do
    {   
        //retreive table values
        var contactsSegment = await table.ListEntitiesAsync(
            continuationToken: continuationToken);

        foreach (var contact in contactsSegment.Items)
        {   
            log.Info(string.Format("{0} {1}", contact.FirstName, contact.LastName));
        }

        continuationToken = contactsSegment.ContinuationToken;
    }
    while (continuationToken != null);
}
```

<!--
<a name="innodejs"></a>

### Usage in Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```
-->
<a name="datasourcesettings"></a>
## Configuración de origen de datos

### <a name="sql-server"></a>SQL Server

El script para crear y rellenar la tabla Contact se encuentra a continuación. dataSetName es el "valor predeterminado".

```sql
CREATE TABLE Contact
(
    Id int NOT NULL,
    LastName varchar(20) NOT NULL,
    FirstName varchar(20) NOT NULL,
    CONSTRAINT PK_Contact_Id PRIMARY KEY (Id)
)
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (1, 'Bitt', 'Prad') 
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (2, 'Glooney', 'Ceorge') 
GO
```

### <a name="google-sheets"></a>Hojas de cálculo de Google
En Google Docs, cree una hoja de cálculo con una hoja de cálculo denominada `Contact`. El conector no puede usar el nombre para mostrar de la hoja de cálculo. El nombre interno (en negrita) tiene que usarse como dataSetName, por ejemplo: `docs.google.com/spreadsheets/d/`**`1UIz545JF_cx6Chm_5HpSPVOenU4DZh4bDxbFgJOSMz0`** Agregar los nombres de columna `Id`, `LastName`, `FirstName` a la primera fila, y luego completar los datos en las filas siguientes.

### <a name="salesforce"></a>Salesforce
dataSetName es el "valor predeterminado".

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


---
title: Enlace de tabla externa para Azure Functions (experimental)
description: Uso de enlaces de tablas externas en Azure Functions
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: alkarche
ms.openlocfilehash: 8a4358fa67e45d0b7a2df1519d649099b5ef5850
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="external-table-binding-for-azure-functions-experimental"></a>Enlace de tabla externa para Azure Functions (experimental)

En este artículo se explica cómo trabajar con datos tabulares en los proveedores de SaaS, como SharePoint y Dynamics, en Azure Functions. Azure Functions admite enlaces de entrada y salida para tablas externas.

> [!IMPORTANT]
> El enlace de tabla externa es experimental y es posible que nunca alcance la disponibilidad general (GA). Solo se incluye en Azure Functions 1.x y no hay planes de agregarlo en Azure Functions 2.x. En el caso de los escenarios que necesitan acceso a los datos de los proveedores de SaaS, considere usar las [aplicaciones lógicas que llaman a las funciones](functions-twitter-email.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="api-connections"></a>Conexiones de API

Los enlaces de tabla aprovechan las conexiones de API externas para autenticarse con proveedores de SaaS de terceros. 

Al asignar un enlace, puede crear una nueva conexión de API o usar una conexión de API existente dentro del mismo grupo de recursos.

### <a name="available-api-connections-tables"></a>Conexiones de API disponibles (tablas)

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
> También se pueden usar conexiones de tablas externas en [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="creating-an-api-connection-step-by-step"></a>Creación de conexión de API: paso a paso

1. En la página de Azure Portal correspondiente a la aplicación de función, seleccione el signo más (**+**) para crear una función.

1. En el cuadro **Escenario**, seleccione **Experimental**.

1. Seleccione **Tabla externa**.

1. Seleccione un lenguaje.

2. En **Conexión de tabla externa**, seleccione una conexión existente o seleccione **nueva**.

1. Para una conexión nueva, configure los ajustes y seleccione **Autorizar**.

1. Seleccione **Crear** para crear la función.

1. Seleccione **Integrar > Tabla externa**.

1. Configure la conexión para utilizar la tabla de destino. Esta configuración dependerá en gran parte de los proveedores de SaaS. Se incluyen ejemplos en la sección siguiente.

## <a name="example"></a>Ejemplo

En este ejemplo, se conecta a una tabla denominada "Contacto" con columnas Id, LastName y FirstName. El código muestra las entidades de contacto en la tabla y registra los nombres y apellidos.

Este es el archivo *function.json*:

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

Este es el código de script de C#:

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
        //retrieve table values
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

### <a name="sql-server-data-source"></a>Origen de datos de SQL Server

A continuación, aparece un script para crear una tabla en SQL Server para usar con este ejemplo. `dataSetName` es el "valor predeterminado".

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

### <a name="google-sheets-data-source"></a>Origen de datos de Hojas de cálculo de Google

Para crear una tabla para usar en este ejemplo en Documentos de Google, cree una hoja de cálculo con una hoja denominada `Contact`. El conector no puede usar el nombre para mostrar de la hoja de cálculo. El nombre interno (en negrita) tiene que usarse como dataSetName, por ejemplo: `docs.google.com/spreadsheets/d/`**`1UIz545JF_cx6Chm_5HpSPVOenU4DZh4bDxbFgJOSMz0`** Agregar los nombres de columna `Id`, `LastName`, `FirstName` a la primera fila, y luego completar los datos en las filas siguientes.

### <a name="salesforce"></a>Salesforce

Para usar este ejemplo con Salesforce, `dataSetName` es el "valor predeterminado".

## <a name="configuration"></a>Configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se establecen en el archivo *function.json*.

|Propiedad de function.json | DESCRIPCIÓN|
|---------|----------------------|
|**type** | Se debe establecer en `apiHubTable`. Esta propiedad se establece automáticamente cuando se crea el desencadenador en Azure Portal.|
|**dirección** | Se debe establecer en `in`. Esta propiedad se establece automáticamente cuando se crea el desencadenador en Azure Portal. |
|**name** | Nombre de la variable que representa el elemento de evento en el código de la función. | 
|**conexión**| Identifica la configuración de aplicación que almacena la cadena de conexión de API. La configuración de la aplicación se crea automáticamente cuando se agrega una conexión de API en la interfaz de usuario integrada.|
|**dataSetName**|Nombre del conjunto de datos que contiene la tabla que se va a leer.|
|**tableName**|Nombre de la tabla.|
|**entityId**|El valor debe estar vacío para los enlaces de tabla.

Un conector tabular proporciona conjuntos de datos, y cada conjunto de datos contiene tablas. El nombre del conjunto de datos predeterminado es "default". Los títulos de un conjunto de datos y una tabla en varios proveedores de SaaS se enumeran a continuación:

|Conector|Dataset|Tabla|
|:-----|:---|:---| 
|**SharePoint**|Sitio|Lista de SharePoint
|**SQL**|Base de datos|Tabla 
|**Hoja de cálculo de Google**|Hoja de cálculo|Hoja de cálculo 
|**Excel**|Archivo de Excel|Hoja 

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre desencadenadores y enlaces de Azure Functions](functions-triggers-bindings.md)

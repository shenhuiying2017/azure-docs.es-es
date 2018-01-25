---
title: "Copia de datos desde orígenes ODBC mediante Azure Data Factory | Microsoft Docs"
description: "Obtenga información sobre cómo copiar datos desde orígenes OData a almacenes de datos receptores compatibles a través de una actividad de copia de una canalización de Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
ms.openlocfilehash: 14f654979f004186e81b2f18578ced3c9aab3815
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Copia de datos con almacenes de datos ODBC como origen y destino mediante Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-odbc-connector.md)
> * [Versión 2: versión preliminar](connector-odbc.md)

En este artículo se resume el uso de la actividad de copia de Azure Data Factory para copiar datos con un almacén de datos ODBC como origen o destino. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de la actividad de copia.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, vea el artículo sobre [la versión 1 del conector ODBC](v1/data-factory-odata-connector.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar datos desde un origen ODBC a cualquier almacén de datos de receptor o viceversa. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

En concreto, este conector ODBC admite copiar datos con **cualquier almacén de datos compatible con ODBC** como origen o destino mediante la autenticación **básica** o **anónima**.

## <a name="prerequisites"></a>requisitos previos

Para usar este conector ODBC, necesitará lo siguiente:

- Configurar un entorno Integration Runtime autohospedado. Consulte el artículo sobre [Integration Runtime autohospedado](create-self-hosted-integration-runtime.md) para más información.
- Instale el controlador ODBC para el almacén de datos en la máquina de Integration Runtime.

## <a name="getting-started"></a>Introducción

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Las secciones siguientes proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas del conector ODBC.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado ODBC:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type debe establecerse en: **Odbc** | Sí |
| connectionString | La cadena de conexión que excluye la parte de la credencial. Puede especificar la cadena de conexión con un patrón como `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"` o utilizar el DSN (nombre de origen de datos) de sistema que se ha configurado en la máquina de Integration Runtime con `"DSN=<name of the DSN on IR machine>;"` (se necesita especificar la parte de la credencial en el servicio vinculado según corresponda).| Sí |
| authenticationType | Tipo de autenticación que se usa para conectarse al almacén de datos ODBC.<br/>Los valores permitidos son: **Básica** y **Anónima**. | Sí |
| userName | Especifique el nombre de usuario si usa la autenticación básica. | Sin  |
| contraseña | Especifique la contraseña de la cuenta de usuario que se especificó para el nombre de usuario. Marque este campo como SecureString. | Sin  |
| credential | La parte de la credencial de acceso de la cadena de conexión especificada en formato de valor de propiedad específico del controlador. Ejemplo: `"RefreshToken=<secret refresh token>;"`. Marque este campo como SecureString. | Sin  |
| connectVia | El entorno [Integration Runtime](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Tal y como se mencionó en los [requisitos previos](#prerequisites), se requiere un entorno Integration Runtime autohospedado. |Sí |

**Ejemplo 1: con autenticación básica**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<connection string>"
            },
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Ejemplo 2: con autenticación anónima**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<connection string>"
            },
            "authenticationType": "Anonymous",
            "credential": {
                "type": "SecureString",
                "value": "RefreshToken=<secret refresh token>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre conjuntos de datos. En esta sección se proporciona una lista de las propiedades que admite el conjunto de datos de ODBC.

Para copiar datos con un almacén de datos compatible con ODBC como origen o destino, establezca la propieda type del conjunto de datos en **RelationalTable**. Se admiten las siguientes propiedades:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type del conjunto de datos debe establecerse en: **RelationalTable** | Sí |
| tableName | Nombre de la tabla en el almacén de datos ODBC. | No (si se especifica "query" en el origen de la actividad)<br/>Sí para el receptor |

**Ejemplo**

```json
{
    "name": "ODBCDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<ODBC linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades que admite el origen de ODBC.

### <a name="odbc-as-source"></a>ODBC como origen

Para copiar datos desde un almacén de datos compatible con ODBC, establezca el tipo de origen de la actividad de copia en **RelationalSource**. Se admiten las siguientes propiedades en la sección **source** de la actividad de copia:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type del origen de la actividad de copia debe establecerse en: **RelationalSource** | Sí |
| query | Use la consulta SQL personalizada para leer los datos. Por ejemplo: `"SELECT * FROM MyTable"`. | No (si se especifica "tableName" en el conjunto de datos) |

**Ejemplo:**

```json
"activities":[
    {
        "name": "CopyFromODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ODBC input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "RelationalSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="odbc-as-sink"></a>ODBC como receptor

Para copiar datos a un almacén de datos compatible con ODBC, establezca el tipo de receptor de la actividad de copia en **OdbcSink**. Se admiten las siguientes propiedades en la sección **sink** de la actividad de copia:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type del receptor de la actividad de copia debe establecerse en: **OdbcSink**. | Sí |
| writeBatchTimeout |Tiempo de espera para que la operación de inserción por lotes se complete antes de que se agote el tiempo de espera.<br/>Los valores permitidos son: intervalos de tiempo. Ejemplo: "00:30:00" (30 minutos). |Sin  |
| writeBatchSize |Inserta datos en la tabla SQL cuando el tamaño del búfer alcanza el valor writeBatchSize.<br/>Los valores permitidos son: enteros (número de filas). |No (el valor predeterminado es 0, detectado automáticamente) |
| preCopyScript |Especifique una consulta SQL para que la actividad de copia se ejecute antes de escribir datos en el almacén de datos en cada ejecución. Puede usar esta propiedad para limpiar los datos cargados previamente. |Sin  |

> [!NOTE]
> Si el valor "writeBatchSize" no está establecido (detectado automáticamente), la actividad de copia primero detecta si el controlador admite las operaciones por lotes y lo establece en 10 000 si lo hace, o bien en 1 si no es así. Si establece explícitamente un valor distinto de 0, la actividad de copia respetar el valor y genera un error en tiempo de ejecución si el controlador no admite las operaciones por lotes.

**Ejemplo:**

```json
"activities":[
    {
        "name": "CopyToODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ODBC output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OdbcSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="ibm-informix-source"></a>Origen de IBM Informix

Puede copiar datos de la base de datos de IBM Informix mediante el conector ODBC genérico.

Configure una instancia de Integration Runtime autohospedado en una máquina con acceso al almacén de datos. Integration Runtime usa el controlador ODBC para Informix para conectarse al almacén de datos. Por lo tanto, instale el controlador si todavía no está instalado en la misma máquina. Por ejemplo, puede usar el controlador "IBM INFORMIX ODBC DRIVER (64-bit)". Consulte la sección [Requisitos previos](#prerequisites) para detalles.

Antes de usar el origen de Informix en una solución de Data Factory, compruebe si Integration Runtime se puede conectar al almacén de datos con las instrucciones que aparecen en la sección [Solución de problemas de conectividad](#troubleshoot-connectivity-issues).

Se crea un servicio vinculado ODBC para vincular un almacén de datos de IBM Informix con una instancia de Azure Data Factory como se muestra en el ejemplo siguiente:

```json
{
    "name": "InformixLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<Informix connection string or DSN>"
            },
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Lea el artículo desde el principio para obtener información general detallada de uso de almacenes de datos ODBC como almacenes de datos de origen o receptor en una operación de copia.

## <a name="microsoft-access-source"></a>Origen de Microsoft Access

Puede copiar datos de la base de datos de Microsoft Access mediante el conector ODBC genérico.

Configure una instancia de Integration Runtime autohospedado en una máquina con acceso al almacén de datos. Integration Runtime usa el controlador ODBC para Microsoft Access para conectarse al almacén de datos. Por lo tanto, instale el controlador si todavía no está instalado en la misma máquina. Consulte la sección [Requisitos previos](#prerequisites) para detalles.

Antes de usar el origen de Microsoft Access en una solución de Data Factory, compruebe si Integration Runtime se puede conectar al almacén de datos con las instrucciones que aparecen en la sección [Solución de problemas de conectividad](#troubleshoot-connectivity-issues).

Se crea un servicio vinculado ODBC para vincular un almacén de datos de Microsoft Access con una instancia de Azure Data Factory como se muestra en el ejemplo siguiente:

```json
{
    "name": "MicrosoftAccessLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={Microsoft Access Driver (*.mdb, *.accdb)};Dbq=<path to your DB file e.g. C:\\mydatabase.accdb>;"
            },
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Lea el artículo desde el principio para obtener información general detallada de uso de almacenes de datos ODBC como almacenes de datos de origen o receptor en una operación de copia.

## <a name="ge-historian-source"></a>Origen de GE Historian

Puede copiar datos de GE Historian mediante el conector ODBC genérico.

Configure una instancia de Integration Runtime autohospedado en una máquina con acceso al almacén de datos. Integration Runtime usa el controlador ODBC para que GE Historian se conecte al almacén de datos. Por lo tanto, instale el controlador si todavía no está instalado en la misma máquina. Consulte la sección [Requisitos previos](#prerequisites) para detalles.

Antes de usar el origen de GE Historian en una solución de Data Factory, compruebe si Integration Runtime se puede conectar al almacén de datos con las instrucciones que aparecen en la sección [Solución de problemas de conectividad](#troubleshoot-connectivity-issues).

Se crea un servicio vinculado ODBC para vincular un almacén de datos de Microsoft Access con una instancia de Azure Data Factory como se muestra en el ejemplo siguiente:

```json
{
    "name": "HistorianLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<GE Historian store connection string or DSN>"
            },
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Lea el artículo desde el principio para obtener información general detallada de uso de almacenes de datos ODBC como almacenes de datos de origen o receptor en una operación de copia.

## <a name="sap-hana-sink"></a>Receptor de SAP HANA

>[!NOTE]
>Para copiar datos desde un almacén de datos de SAP HANA, consulte el artículo sobre el [conector SAP HANA](connector-sap-hana.md) nativo. Para copiar datos a SAP HANA, siga estas instrucciones para usar el conector ODBC. Tenga en cuenta que los servicios vinculados para el conector SAP HANA y el conector ODBC tienen tipos distintos y, por tanto, no se pueden reutilizar.
>

Puede copiar datos en la base de datos de SAP HANA mediante el conector ODBC genérico.

Configure una instancia de Integration Runtime autohospedado en una máquina con acceso al almacén de datos. Integration Runtime usa el controlador ODBC para SAP HANA para conectarse al almacén de datos. Por lo tanto, instale el controlador si todavía no está instalado en la misma máquina. Consulte la sección [Requisitos previos](#prerequisites) para detalles.

Antes de usar el receptor de SAP HANA en una solución de Data Factory, compruebe si Integration Runtime se puede conectar al almacén de datos con las instrucciones que aparecen en la sección [Solución de problemas de conectividad](#troubleshoot-connectivity-issues).

Cree un servicio vinculado ODBC para vincular un almacén de datos de SAP HANA con una instancia de Azure Data Factory como se muestra en el ejemplo siguiente:

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={HDBODBC};servernode=<HANA server>.clouddatahub-int.net:30015"
            },
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Lea el artículo desde el principio para obtener información general detallada de uso de almacenes de datos ODBC como almacenes de datos de origen o receptor en una operación de copia.

## <a name="troubleshoot-connectivity-issues"></a>Solución de problemas de conectividad

Para solucionar problemas de conexión, use la pestaña **Diagnósticos** de **Integration Runtime Configuration Manager**.

1. Inicie **Integration Runtime Configuration Manager**.
2. Cambie a la pestaña **Diagnósticos** .
3. En la sección "Prueba de conexión", seleccione el **tipo** de almacén de datos (servicio vinculado).
4. Especifique la **cadena de conexión** que se usa para conectarse al almacén de datos, elija la **autenticación** y escriba el **nombre de usuario**, la **contraseña** o las **credenciales**.
5. Haga clic en **Probar conexión** para probar la conexión con el almacén de datos.

## <a name="next-steps"></a>pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md##supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.
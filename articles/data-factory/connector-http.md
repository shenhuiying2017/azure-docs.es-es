---
title: Copia de datos desde el origen HTTP mediante Azure Data Factory | Microsoft Docs
description: "Obtenga información sobre cómo copiar datos desde un origen HTTP —en la nube o en un entorno local— a almacenes de datos receptores compatibles a través de una actividad de copia de una canalización de Azure Data Factory."
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
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: b27e852b70651cec34b200bde362ff0efae30226
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-from-http-endpoint-using-azure-data-factory"></a>Copia de datos desde el punto de conexión HTTP mediante Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-http-connector.md)
> * [Versión 2: Versión preliminar](connector-http.md)

En este artículo se explica el uso de la actividad de copia de Azure Data Factory para copiar datos desde un punto de conexión HTTP. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de la actividad de copia.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en la versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, vea el artículo sobre [la versión 1 del conector HTTP](v1/data-factory-http-connector.md).

## <a name="supported-scenarios"></a>Escenarios admitidos

Puede copiar datos desde un origen HTTP a cualquier almacén de datos receptor compatible. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

En concreto, este conector HTTP admite las siguientes funcionalidades:

- Recuperar datos desde un punto de conexión HTTP o HTTPS mediante los métodos HTTP **GET** o **POST**.
- Se admiten los siguientes tipos de autenticación: **Anónima**, **Básica**, **Implícita**, **Windows** y **ClientCertificate**.
- Copiar la respuesta HTTP tal cual, o bien analizarla con los [códecs de compresión y los formatos de archivo compatibles](supported-file-formats-and-compression-codecs.md).

La diferencia entre este conector y el de [tabla web](connector-web-table.md) es que el último se usa para extraer contenido de la tabla desde una página web HTML.

## <a name="getting-started"></a>Introducción
Puede crear una canalización con la actividad de copia mediante el SDK de .NET, el SDK de Python, Azure PowerShell, la API de REST o la plantilla de Azure Resource Manager. Consulte el [tutorial de actividad de copia](quickstart-create-data-factory-dot-net.md) para obtener instrucciones paso a paso sobre cómo crear una canalización con una actividad de copia.

En las secciones siguientes se proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas del conector HTTP.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado de HTTP:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type debe establecerse en: **HttpServer**. | Sí |
| url | Dirección URL base para el servidor web | Sí |
| enableServerCertificateValidation | Especifique si desea habilitar la validación de certificados SSL al conectarse al punto de conexión HTTP. | No, el valor predeterminado es True. |
| authenticationType | Especifica el tipo de autenticación. Los valores permitidos son: **Anonymous**, **Basic**, **Digest**, **Windows** y **ClientCertificate**. <br><br> Consulte en las secciones después de esta tabla más propiedades y ejemplos de JSON para esos tipos de autenticación respectivamente. | Sí |
| connectVia | El entorno [Integration Runtime](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Puede usar los entornos Integration Runtime (autohospedado) (si el almacén de datos se encuentra en una red privada) o Azure Integration Runtime. Si no se especifica, se usará Azure Integration Runtime. |No |

### <a name="using-basic-digest-or-windows-authentication"></a>Uso de la autenticación Basic, Digest o Windows

Establezca la propiedad "authenticationType" en **Básica**, **Implícita** o **Windows** y especifique las siguientes propiedades junto con las genéricas descritas en la sección anterior:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| userName | Nombre de usuario para acceder al punto de conexión HTTP. | Sí |
| Contraseña | Contraseña para el usuario (nombre de usuario). Marque este campo como SecureString. | Sí |

**Ejemplo**

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
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

### <a name="using-clientcertificate-authentication"></a>Uso de la autenticación ClientCertificate

Para usar la autenticación ClientCertificate, establezca la propiedad "authenticationType" en **ClientCertificate** y especifique las siguientes propiedades junto con las genéricas descritas en la sección anterior:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| embeddedCertData | Datos del certificado codificados en Base64. | Especifique `embeddedCertData` o `certThumbprint`. |
| certThumbprint | La huella digital del certificado que se instaló en el almacén de certificados de la máquina Integration Runtime (autohospedado). Solo se aplica cuando se especifica un tipo autohospedado de un entorno Integration Runtime en connectVia. | Especifique `embeddedCertData` o `certThumbprint`. |
| contraseña | Contraseña asociada con el certificado. Marque este campo como SecureString. | No |

Si utiliza "certThumbprint" para la autenticación y el certificado está instalado en el almacén personal del equipo local, necesita conceder el permiso de lectura al entorno Integration Runtime (autohospedado):

1. Inicie Microsoft Management Console (MMC). Agregue el complemento **Certificados** que tenga como destino **Equipo local**.
2. Expanda **Certificados**, **Personal** y haga clic en **Certificados**.
3. Haga clic con el botón derecho en el certificado del almacén personal y seleccione **Todas las tareas** -> **Administrar claves privadas**.
3. En la pestaña **Seguridad**, agregue la cuenta de usuario en que se ejecuta el servicio de host del entorno Integration Runtime (DIAHostService) con acceso de lectura al certificado.

**Ejemplo 1: Uso de certThumbprint**

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "HttpServer",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "certThumbprint": "<thumbprint of certificate>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Ejemplo 2: Uso de embeddedCertData**

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "HttpServer",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "embeddedCertData": "<base64 encoded cert data>",
            "password": {
                "type": "SecureString",
                "value": "password of cert"
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

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre conjuntos de datos. En esta sección se proporciona una lista de las propiedades que admite el conjunto de datos HTTP.

Para copiar datos desde HTTP, establezca la propiedad type del conjunto de datos en **HttpFile**. Se admiten las siguientes propiedades:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del conjunto de datos debe establecerse en: **HttpFile**. | Sí |
| relativeUrl | Dirección URL relativa al recurso que contiene los datos. Cuando no se especifica la propiedad, se solo se usa la dirección URL especificada en la definición de servicio vinculado. | No |
| requestMethod | Método HTTP.<br/>Los valores permitidos son **Get** (valor predeterminado) o **Post**. | No |
| additionalHeaders | Encabezados de solicitud HTTP adicionales. | No |
| requestBody | Cuerpo de la solicitud HTTP. | No |
| formato | Si desea **recuperar datos tal cual desde el punto de conexión HTTP** sin analizarlos ni copiarlos en un almacén basado en archivos, ignore la sección de formato de las definiciones de los conjuntos de datos de entrada y salida.<br/><br/>Si desea analizar el contenido de la respuesta HTTP durante la copia, se admiten los siguientes tipos de formato de archivos: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** y **ParquetFormat**. Establezca la propiedad **type** de formato en uno de los siguientes valores. Para obtener más información, consulte las secciones sobre [formato JSON](supported-file-formats-and-compression-codecs.md#json-format), [formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [formato Orc](supported-file-formats-and-compression-codecs.md#orc-format) y [formato Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |No |
| compresión | Especifique el tipo y el nivel de compresión de los datos. Para más información, consulte el artículo sobre [códecs de compresión y formatos de archivo compatibles](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Los tipos admitidos son **GZip**, **Deflate**, **BZip2** y **ZipDeflate**.<br/>Los niveles admitidos son **Optimal** y **Fastest**. |No |

**Ejemplo 1: Uso del método Get (valor predeterminado)**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        }
    }
}
```

**Ejemplo 2: Uso del método Post**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST HTTP request>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades que admite el origen HTTP.

### <a name="http-as-source"></a>HTTP como origen

Para copiar datos desde HTTP, establezca el tipo de origen de la actividad de copia en **HttpSource**. Se admiten las siguientes propiedades en la sección **source** de la actividad de copia:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del origen de la actividad de copia debe establecerse en: **HttpSource**. | Sí |
| httpRequestTimeout | El tiempo que la solicitud HTTP espera (TimeSpan) a obtener una respuesta. Es el tiempo de espera para obtener una respuesta, no para leer los datos de la respuesta.<br/> El valor predeterminado es: 00:01:40.  | No |

**Ejemplo:**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HTTP input dataset name>",
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
                "type": "HttpSource",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```


## <a name="next-steps"></a>Pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md##supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.
---
title: 'Movimiento de datos desde un origen HTTP: Azure | Microsoft Docs'
description: Aprenda a mover datos desde un origen HTTP local o en la nube mediante Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: a6dad8242c709240b57b8a47acc44c5ddfdaa755
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="move-data-from-an-http-source-using-azure-data-factory"></a>Movimiento de datos desde un origen HTTP mediante Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](data-factory-http-connector.md)
> * [Versión 2: versión preliminar](../connector-http.md)

> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory, que está disponible con carácter general. Si usa la versión 2 del servicio Data Factory, que se encuentra en versión preliminar, vea [HTTP connector in V2](../connector-http.md) (Conector de HTTP en V2).


En este artículo se describe el uso de la actividad de copia en Azure Data Factory para mover datos desde un punto de conexión HTTP local o en la nube a un almacén de datos receptor compatible. Este artículo se basa en el artículo sobre las [actividades de movimiento de datos](data-factory-data-movement-activities.md), que presenta una introducción general del movimiento de datos con la actividad de copia la lista de almacenes de datos compatibles como orígenes/receptores.

Data Factory solo admite actualmente el movimiento de datos desde un origen HTTP a otros almacenes de datos, pero no de otros almacenes de datos a un destino HTTP.

## <a name="supported-scenarios-and-authentication-types"></a>Escenarios admitidos y tipos de autenticación
Puede usar este conector HTTP para recuperar datos de **un punto de conexión HTTP/s local o en la nube** mediante el método HTTP **GET** o **POST**. Se admiten los siguientes tipos de autenticación: **Anonymous**, **Basic**, **Digest**, **Windows** y **ClientCertificate**. Observe la diferencia entre este conector y el [conector de tabla web](data-factory-web-table-connector.md): el último se usa para extraer contenido de la tabla desde una página web HTML.

Al copiar datos de un punto de conexión HTTP local, deberá instalar una instancia de Data Management Gateway en la máquina virtual de Azure o en el entorno local. Consulte el artículo sobre cómo [mover datos entre ubicaciones locales y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener información acerca de Data Management Gateway, así como instrucciones paso a paso sobre cómo configurar la puerta de enlace.

## <a name="getting-started"></a>Introducción
Puede crear una canalización con una actividad de copia que mueva datos desde un origen HTTP mediante diferentes herramientas o API.

- La manera más fácil de crear una canalización es usar el **Asistente para copiar**. Consulte [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización mediante el Asistente para copiar datos.

- También puede usar las herramientas siguientes para crear una canalización: **Azure Portal**, **Visual Studio**, **Azure PowerShell**, **plantilla de Azure Resource Manager**, **API de .NET** y **API de REST**. Consulte el [tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener instrucciones paso a paso para crear una canalización con una actividad de copia. Para obtener ejemplos de JSON para copiar datos desde un origen HTTP a Azure Blob Storage, consulte la sección [Ejemplos de JSON](#json-examples) de este artículo.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado
En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado HTTP.

| Propiedad | DESCRIPCIÓN | Obligatorio |
| --- | --- | --- |
| Tipo | La propiedad type debe establecerse en: `Http`. | Sí |
| URL | Dirección URL base para el servidor web | Sí |
| authenticationType | Especifica el tipo de autenticación. Los valores permitidos son: **Anonymous**, **Basic**, **Digest**, **Windows** y **ClientCertificate**. <br><br> Consulte en las secciones después de esta tabla más propiedades y ejemplos de JSON para esos tipos de autenticación respectivamente. | Sí |
| enableServerCertificateValidation | Especifique si desea habilitar la validación del certificado SSL de servidor si el origen es el servidor web HTTPS. | No, el valor predeterminado es True. |
| gatewayName | Nombre de la instancia de Data Management Gateway para conectarse a un origen HTTP local. | Sí si va a copiar datos desde un origen HTTP local. |
| encryptedCredential | Credenciales cifradas para acceder al punto de conexión HTTP. Generadas automáticamente cuando se configura la información de autenticación en el Asistente para copia o en el cuadro de diálogo emergente de ClickOnce. | Nº Se aplica solo cuando se copian datos desde un servidor HTTP local. |

Consulte [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) para más detalles sobre cómo establecer las credenciales para un origen de datos de conector HTTP local.

### <a name="using-basic-digest-or-windows-authentication"></a>Uso de la autenticación Basic, Digest o Windows

Establezca `authenticationType` como `Basic`, `Digest` o `Windows`, y especifique las siguientes propiedades además de las genéricas del conector HTTP descritas antes:

| Propiedad | DESCRIPCIÓN | Obligatorio |
| --- | --- | --- |
| Nombre de usuario | Nombre de usuario para acceder al punto de conexión HTTP. | Sí |
| contraseña | Contraseña para el usuario (nombre de usuario). | Sí |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Ejemplo: Uso de la autenticación Basic, Digest o Windows

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "basic",
            "url" : "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

### <a name="using-clientcertificate-authentication"></a>Uso de la autenticación ClientCertificate

Para usar la autenticación básica, establezca `authenticationType` como `ClientCertificate`, y especifique las siguientes propiedades además de las genéricas del conector HTTP descritas antes:

| Propiedad | DESCRIPCIÓN | Obligatorio |
| --- | --- | --- |
| embeddedCertData | El contenido con codificación Base64 de datos binarios del archivo de intercambio de información personal (PFX). | Especifique `embeddedCertData` o `certThumbprint`. |
| certThumbprint | La huella digital del certificado que se instaló en el almacén de certificados de la máquina de la puerta de enlace. Se aplica solo cuando se copian datos desde un origen HTTP local. | Especifique `embeddedCertData` o `certThumbprint`. |
| contraseña | Contraseña asociada con el certificado. | Sin  |

Si utiliza `certThumbprint` para la autenticación y el certificado está instalado en el almacén personal del equipo local, necesita conceder el permiso de lectura al servicio de puerta de enlace:

1. Inicie Microsoft Management Console (MMC). Agregue el complemento **Certificados** que tenga como destino **Equipo local**.
2. Expanda **Certificados**, **Personal** y haga clic en **Certificados**.
3. Haga clic con el botón derecho en el certificado del almacén personal y seleccione **Todas las tareas**->**Administrar claves privadas**.
3. En la pestaña **Seguridad**, agregue la cuenta de usuario en que se ejecuta el servicio de host de Data Management Gateway con acceso de lectura al certificado.  

#### <a name="example-using-client-certificate"></a>Ejemplo: Uso del certificado de cliente
Este servicio vinculado vincula su factoría de datos a un servidor de web HTTP local. Utiliza un certificado de cliente que está instalado en la máquina con Data Management Gateway instalado.

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"

        }
    }
}
```

#### <a name="example-using-client-certificate-in-a-file"></a>Ejemplo: Uso del certificado de cliente en un archivo
Este servicio vinculado vincula su factoría de datos a un servidor de web HTTP local. Utiliza un archivo de certificado de cliente en la máquina con Data Management Gateway instalado.

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "base64 encoded cert data",
            "password": "password of cert"
        }
    }
}
```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos
Para una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy del código JSON del conjunto de datos son similares para todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.).

La sección **typeProperties** es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección typeProperties del conjunto de datos de tipo **Http** tiene las propiedades siguientes:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | Especifica el tipo del conjunto de datos. Se debe establecer en `Http`. | Sí |
| relativeUrl | Dirección URL relativa al recurso que contiene los datos. Cuando no se especifica la ruta de acceso, se solo se usa la dirección URL especificada en la definición de servicio vinculado. <br><br> Para construir una dirección URL dinámica, puede usar [funciones y variables de sistema de Data Factory](data-factory-functions-variables.md), como "relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)". | Sin  |
| requestMethod | Método HTTP. Los valores permitidos son **GET** o **POST**. | Nº El valor predeterminado es `GET`. |
| additionalHeaders | Encabezados de solicitud HTTP adicionales. | Sin  |
| requestBody | Cuerpo de la solicitud HTTP. | Sin  |
| formato | Si desea simplemente **recuperar los datos del punto de conexión HTTP tal cual** sin analizarlos, omita esta configuración de formato. <br><br> Si desea analizar el contenido de la respuesta HTTP durante la copia, se admiten los siguientes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** y **ParquetFormat**. Para más información, consulte las secciones [Formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [Formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format) y [Formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). |Sin  |
| compresión | Especifique el tipo y el nivel de compresión de los datos. Los tipos admitidos son **GZip**, **Deflate**, **BZip2** y **ZipDeflate**. Los niveles admitidos son **Optimal** y **Fastest**. Para más información, consulte el artículo sobre [formatos de compresión de archivos en Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Sin  |

### <a name="example-using-the-get-default-method"></a>Ejemplo: Uso del método GET (predeterminado)

```JSON
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

### <a name="example-using-the-post-method"></a>Ejemplo: Uso del método POST

```JSON
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
           "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia
Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades (como nombre, descripción, tablas de entrada y salida, y directivas) están disponibles para todos los tipos de actividades.

Por otra parte, las propiedades disponibles en la sección **typeProperties** de la actividad varían con cada tipo de actividad. Para la actividad de copia, varían en función de los tipos de orígenes y receptores.

En este momento, cuando el origen de la actividad de copia es de tipo **HttpSource**, se admiten las siguientes propiedades.

| Propiedad | DESCRIPCIÓN | Obligatorio |
| -------- | ----------- | -------- |
| httpRequestTimeout | El tiempo que la solicitud HTTP espera (TimeSpan) a obtener una respuesta. Es el tiempo de espera para obtener una respuesta, no para leer los datos de la respuesta. | Nº Valor predeterminado: 00:01:40 |

## <a name="supported-file-and-compression-formats"></a>Formatos de archivo y de compresión admitidos
Consulte los detalles en [Formatos de compresión y de archivos en Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

## <a name="json-examples"></a>Ejemplos de JSON
En el siguiente ejemplo, se proporcionan definiciones JSON de ejemplo que puede usar para crear una canalización mediante [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Se muestra cómo copiar datos del origen HTTP a Azure Blob Storage. Sin embargo, los datos se pueden copiar **directamente** de cualquiera de los orígenes a cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia en Azure Data Factory.

### <a name="example-copy-data-from-http-source-to-azure-blob-storage"></a>Ejemplo: Copia de datos desde el origen HTTP a Azure Blob Storage
La solución Data Factory de este ejemplo contiene las siguientes entidades de Data Factory:

1. Un servicio vinculado de tipo [HTTP](#linked-service-properties).
2. Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [Http](#dataset-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Una [canalización](data-factory-create-pipelines.md) con actividad de copia que usa [HttpSource](#copy-activity-properties) y [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

En el ejemplo se copian datos de un origen HTTP a un blob de Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

### <a name="http-linked-service"></a>Servicio vinculado HTTP
En este ejemplo se usa el servicio vinculado HTTP con autenticación anónima. Para los diferentes tipos de autenticación que se pueden usar, consulte la sección [Propiedades del servicio vinculado](#linked-service-properties).

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Servicio vinculado de Azure Storage

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

### <a name="http-input-dataset"></a>Conjunto de datos de entrada HTTP
Si se establece **external** en **true**, se informa al servicio Data Factory que el conjunto de datos es externo a Data Factory y que no lo genera ninguna actividad de la factoría de datos.

```JSON
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}

```

### <a name="azure-blob-output-dataset"></a>Conjunto de datos de salida de blob de Azure

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1).

```JSON
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

### <a name="pipeline-with-copy-activity"></a>Canalización con actividad de copia

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de la canalización JSON, el tipo **source** se establece en **HttpSource** y el tipo **sink**, en **BlobSink**.

Consulte [HttpSource](#copy-activity-properties) para obtener la lista de propiedades admitidas por HttpSource.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "HttpSourceToAzureBlob",
        "description": "Copy from an HTTP source to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "HttpSourceDataInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "HttpSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```

> [!NOTE]
> Para asignar columnas del conjunto de datos de origen a las del conjunto de datos del receptor, consulte el artículo sobre la [asignación de columnas del conjunto de datos en Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Rendimiento y optimización
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Azure Data Factory y las diversas formas de optimizarlo.

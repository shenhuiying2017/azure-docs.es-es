---
title: "Movimiento de datos hacia un sistema de archivos y desde él con Azure Data Factory | Microsoft Docs"
description: "Aprenda a mover datos hacia el sistema de archivos local y desde él con Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: ce19f1ae-358e-4ffc-8a80-d802505c9c84
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 6c26bf3eda7ef15e7a580a31ab7b6624c8a7a826
ms.lasthandoff: 04/12/2017


---
# <a name="move-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Movimiento de datos hacia y desde el sistema de archivos local mediante Azure Data Factory
Puede copiar datos de HDFS en cualquier almacén de datos de receptor admitido. Para ver una lista de almacenes de datos admitidos como receptores por la actividad de copia, consulte la tabla de [almacenes de datos compatibles](data-factory-data-movement-activities.md#supported-data-stores-and-formats). En la actualidad, Factoría de datos solo admite el movimiento de datos desde HDFS local a otros almacenes de datos, pero no el movimiento inverso.

En este artículo se explica el uso de la actividad de copia en Azure Data Factory para mover datos hacia un sistema de archivos local y desde él. Puede copiar datos desde un sistema de archivos local a cualquier almacén de datos receptor o desde cualquier almacén de datos de origen compatible. Para ver una lista de almacenes de datos admitidos como receptores por la actividad de copia, consulte la tabla de [almacenes de datos compatibles](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Se basa en la información general ofrecida por el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md). Consulte los [orígenes y receptores admitidos](data-factory-data-movement-activities.md#supported-data-stores-and-formats) para obtener una lista de almacenes de datos que se pueden utilizar como orígenes o receptores con el sistema de archivos local.

> [!NOTE]
> La actividad de copia no elimina el archivo de origen una vez copiado correctamente en el destino. Si necesita eliminar el archivo de origen tras una copia correcta, cree una actividad personalizada para tal fin y úsela en la canalización. 

## <a name="enabling-connectivity"></a>Habilitación de la conectividad
Data Factory admite la conexión hacia y desde el sistema de archivos local mediante **Data Management Gateway**. Debe instalar Data Management Gateway en el entorno local para que el servicio de Data Factory se conecte a cualquier almacén de datos local compatible, incluido el sistema de archivos. Para más información al respecto y para obtener instrucciones paso a paso sobre la configuración de una puerta de enlace, consulte [Movimiento de datos entre orígenes locales y la nube con la puerta de enlace de administración de datos](data-factory-move-data-between-onprem-and-cloud.md). Aparte de la puerta de enlace de administración de datos, no es necesario instalar otros archivos binarios para la comunicación hacia y desde un sistema de archivos local. Debe instalar y usar Data Management Gateway incluso si el sistema de archivos está en la máquina virtual de IaaS de Azure. Para información más detallada sobre la puerta de enlace, consulte el artículo sobre [Data Management Gateway](data-factory-data-management-gateway.md).

Para usar un recurso compartido de archivos de Linux, instale [Samba](https://www.samba.org/) en el servidor Linux e instale Data Management Gateway en un servidor Windows. No se admite la instalación de la puerta de enlace de administración de datos en un servidor Linux.

## <a name="getting-started"></a>Introducción
Puede crear una canalización con actividad de copia que mueva los datos desde un sistema de archivos o hacia él mediante el uso de distintas herramientas o API.

La manera más fácil de crear una canalización es usar el **Asistente para copiar**. Consulte [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización mediante el Asistente para copiar datos.

También puede usar las herramientas siguientes para crear una canalización: **Azure Portal**, **Visual Studio**, **Azure PowerShell**, **plantilla de Azure Resource Manager**, **API de .NET** y **API de REST**. Consulte el [tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener instrucciones paso a paso sobre cómo crear una canalización con una actividad de copia.

Tanto si usa las herramientas como las API, realice los pasos siguientes para crear una canalización que mueva datos de un almacén de datos de origen a un almacén de datos receptor:

1. Cree **servicios vinculados** para vincular almacenes de datos de entrada y salida a la factoría de datos.
2. Cree **conjuntos de datos** con el fin de representar los datos de entrada y salida para la operación de copia.
3. Cree una **canalización** con una actividad de copia que tome como entrada un conjunto de datos y un conjunto de datos como salida.

Cuando se usa el Asistente, se crean automáticamente definiciones de JSON para estas entidades de Data Factory (servicios vinculados, conjuntos de datos y la canalización). Al usar herramientas o API (excepto la API de .NET), se definen estas entidades de Data Factory con el formato JSON.  Para ejemplos con definiciones de JSON para entidades de Data Factory que se usan para copiar datos hacia un sistema de archivos y desde este, consulte la sección de [ejemplos de JSON](#json-examples) de este artículo.

En las secciones siguientes, se proporcionan detalles sobre las propiedades JSON que se usan para definir entidades de Data Factory específicas de un sistema de archivos:

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado
Un sistema de archivos local se puede vincular a una factoría de datos de Azure con el servicio vinculado del **servidor de archivos local**. En la tabla siguiente se proporciona la descripción de los elementos JSON específicos al servicio vinculado del servidor de archivos local.

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| type |Asegúrese de que la propiedad type esté establecida en **OnPremisesFileServer**. |Sí |
| host |Especifica la ruta de acceso raíz de la carpeta que quiere copiar. Use el carácter de escape "\" para los caracteres especiales de la cadena. Consulte los casos que se exponen en [Ejemplos de definiciones de servicio vinculado y conjunto de datos](#sample-linked-service-and-dataset-definitions) . |Sí |
| userid |Especifique el identificador del usuario que tiene acceso al servidor. |No (si elige encryptedCredential) |
| contraseña |Especifique la contraseña del usuario (identificador de usuario). |No (si elige encryptedCredential) |
| encryptedCredential |Especifique las credenciales cifradas que puede obtener ejecutando el cmdlet New-AzureRmDataFactoryEncryptValue. |No (si opta por especificar el identificador de usuario y la contraseña en texto sin formato) |
| gatewayName |Especifica el nombre de la puerta de enlace que debe usar Data Factory para conectarse al servidor de archivos local. |Sí |


### <a name="sample-linked-service-and-dataset-definitions"></a>Ejemplos de definiciones de servicio vinculado y conjunto de datos
| Escenario | Host en definición de servicio vinculado | folderPath en definición de conjunto de datos |
| --- | --- | --- |
| Carpeta local en la máquina de Data Management Gateway::  <br/><br/>Ejemplos: D:\\\* o D:\folder\subfolder\\* |D:\\\\ (para Data Management Gateway 2.0 y versiones posteriores) <br/><br/> localhost (para versiones anteriores a Data Management Gateway 2.0) |.\\\\ o la carpeta\\\\subcarpeta (Data Management Gateway 2.0 y versiones posteriores) <br/><br/>D:\\\\ o D:\\\\carpeta\\\\subcarpeta (para versiones de la puerta de enlace interiores a 2.0) |
| Carpeta compartida remota:  <br/><br/>Ejemplos: \\\\myserver\\share\\\* o \\\\myserver\\share\\folder\\subfolder\\* |\\\\\\\\myserver\\\\ |.\\\\ o carpeta\\\\subcarpeta |


### <a name="example-using-username-and-password-in-plain-text"></a>Ejemplo: uso de nombre de usuario y contraseña en texto sin formato

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

### <a name="example-using-encryptedcredential"></a>Ejemplo: uso de encryptedcredential

```JSON
{
  "Name": " OnPremisesFileServerLinkedService ",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "D:\\",
      "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
      "gatewayName": "mygateway"
    }
  }
}
```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos
Para ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como estructura, disponibilidad y directiva de un JSON de conjunto de datos son similares para todos los tipos de conjunto de datos.

La sección typeProperties es diferente para cada tipo de conjunto de datos. Proporciona información como la ubicación y el formato de los datos del almacén de datos. La sección typeProperties del conjunto de datos de tipo **FileShare** tiene las propiedades siguientes:

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| folderPath |Especifica la subruta de acceso a la carpeta. Use el carácter de escape "\" para los caracteres especiales de la cadena. Consulte los casos que se exponen en [Ejemplos de definiciones de servicio vinculado y conjunto de datos](#sample-linked-service-and-dataset-definitions) .<br/><br/>Puede combinar esta propiedad con **partitionBy** para que las rutas de acceso de carpeta se basen en las fechas y horas de inicio y finalización del segmento. |Sí |
| fileName |Especifique el nombre del archivo en **folderPath** si quiere que la tabla haga referencia a un archivo específico de la carpeta. Si no especifica ningún valor para esta propiedad, la tabla apunta a todos los archivos de la carpeta.<br/><br/>Si no se especifica fileName para un conjunto de datos de salida, el nombre del archivo generado está en el siguiente formato: <br/><br/>`Data.<Guid>.txt` (Ejemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |No |
| fileFilter |Especifique el filtro que se va a usar para seleccionar un subconjunto de archivos de folderPath, en lugar de todos los archivos. <br/><br/>Valores permitidos son: `*` (varios caracteres) y `?` (un único individual).<br/><br/>Ejemplo 1: "fileFilter": "*.log"<br/>Ejemplo 2: "fileFilter": 2014-1-?.txt"<br/><br/>Tenga en cuenta que fileFilter es aplicable a un conjunto de datos de FileShare de entrada. |No |
| partitionedBy |Puede usar partitionedBy para especificar un valor dinámico de folderPath/fileName para los datos de series temporales. Por ejemplo, folderPath se parametriza por cada hora de datos. |No |
| formato | Se admiten los siguientes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** y **ParquetFormat**. Establezca la propiedad **type** de formato en uno de los siguientes valores. Para más información, consulte las secciones [Formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [Formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [Formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format) y [Formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Si desea **copiar los archivos tal cual** entre los almacenes basados en archivos (copia binaria), omita la sección de formato en las definiciones de los conjuntos de datos de entrada y salida. |No |
| compresión | Especifique el tipo y el nivel de compresión de los datos. Los tipos admitidos son: **GZip**, **Deflate**, **BZip2** y **ZipDeflate**, y los niveles que se admiten son: **Optimal** (Óptimo) y **Fastest** (Más rápido). consulte [Formatos de compresión de archivos en Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

> [!NOTE]
> No se puede usar fileName y fileFilter a la vez.

### <a name="using-partitionedby-property"></a>Uso de la propiedad partitionedBy
Como ya se ha indicado en la sección anterior, se puede especificar un valor dinámico de folderPath y filename para datos de series temporales con la propiedad **partitionedBy**, [funciones de Data Factory y las variables del sistema](data-factory-functions-variables.md).

Para más información sobre los conjuntos de datos de series temporales, la programación y los segmentos, consulte los artículos [Creación de conjuntos de datos](data-factory-create-datasets.md), [Programación y ejecución](data-factory-scheduling-and-execution.md) y [Creación de canalizaciones](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Muestra 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

En este ejemplo, {Slice} se reemplaza por el valor de la variable del sistema SliceStart de Data Factory en el formato (YYYYMMDDHH). SliceStart hace referencia a la hora de inicio del segmento. folderPath es diferente para cada segmento. Por ejemplo: wikidatagateway/wikisampledataout/2014100103 o wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Ejemplo 2:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

En este ejemplo, year, month, day y time de SliceStart se extraen en variables independientes que se usan en las propiedades folderPath y fileName.

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia
Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades (como nombre, descripción, conjuntos de datos de entrada y salida, y directivas) están disponibles para todos los tipos de actividades. Por otra parte, las propiedades disponibles en la sección **typeProperties** de la actividad varían con cada tipo de actividad.

Para la actividad de copia, varían en función de los tipos de orígenes y receptores. Si va a mover datos desde un sistema de archivos local, establezca el tipo de origen en la actividad de copia en **FileSystemSource**. Del mismo modo, si va a mover datos hacia un sistema de archivos local, establezca el tipo de receptor de la actividad de copia en **FileSystemSink**. Esta sección proporciona una lista de las propiedades compatibles con FileSystemSource y FileSystemSink.

**FileSystemSource** admite las siguientes propiedades:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| recursive |Indica si los datos se leen de forma recursiva de las subcarpetas o solo de la carpeta especificada. |True, False (predeterminada) |No |

**FileSystemSink** admite las siguientes propiedades:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| copyBehavior |Define el comportamiento de copia cuando el origen es BlobSource o FileSystem. |**PreserveHierarchy:**: conserva la jerarquía de archivos en la carpeta de destino. Es decir, la ruta de acceso relativa del archivo de origen a la carpeta de origen es la misma que la ruta de acceso relativa del archivo de destino a la carpeta de destino.<br/><br/>**FlattenHierarchy:** todos los archivos de la carpeta de origen se crean en el primer nivel de la carpeta de destino. Los archivos de destino se crean con un nombre generado automáticamente.<br/><br/>**MergeFiles**: combina todos los archivos de la carpeta de origen en un archivo. Si se especifica el nombre o el nombre del blob, el nombre de archivo combinado es el nombre especificado. De lo contrario, es un nombre de archivo generado automáticamente. |No |

### <a name="recursive-and-copybehavior-examples"></a>Ejemplos de recursive y copyBehavior
En esta sección se describe el comportamiento resultante de la operación de copia para diferentes combinaciones de valores recursive y copyBehavior.

| valor recursive | valor copyBehavior | Comportamiento resultante |
| --- | --- | --- |
| true |preserveHierarchy |Para una carpeta de origen Folder1 con la siguiente estructura:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la carpeta de destino Folder1 se crea con la misma estructura que el origen:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy |Para una carpeta de origen Folder1 con la siguiente estructura:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la carpeta de destino Folder1 se crea con la estructura siguiente: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nombre de archivo generado automáticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nombre de archivo generado automáticamente para File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nombre de archivo generado automáticamente para File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nombre de archivo generado automáticamente para File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nombre de archivo generado automáticamente para File5 |
| true |mergeFiles |Para una carpeta de origen Folder1 con la siguiente estructura:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la carpeta de destino Folder1 se crea con la estructura siguiente: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;El contenido de File1 + File2 + File3 + File4 + File 5 se combina en un archivo con un nombre de archivo generado automáticamente. |
| false |preserveHierarchy |Para una carpeta de origen Folder1 con la siguiente estructura:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>se crea la carpeta de destino Folder1 con la estructura siguiente:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>No se selecciona la subcarpeta Subfolder1, con File3, File4 y File5. |
| false |flattenHierarchy |Para una carpeta de origen Folder1 con la siguiente estructura:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>se crea la carpeta de destino Folder1 con la estructura siguiente:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nombre de archivo generado automáticamente para File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nombre de archivo generado automáticamente para File2<br/><br/>No se selecciona la subcarpeta Subfolder1, con File3, File4 y File5. |
| false |mergeFiles |Para una carpeta de origen Folder1 con la siguiente estructura:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>se crea la carpeta de destino Folder1 con la estructura siguiente:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;El contenido de File1 + File2 se combina en un archivo con un nombre de archivo generado automáticamente.<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nombre de archivo generado automáticamente para File1<br/><br/>No se selecciona la subcarpeta Subfolder1, con File3, File4 y File5. |

## <a name="supported-file-and-compression-formats"></a>Formatos de archivo y de compresión admitidos
Consulte los detalles en [Formatos de compresión y de archivos en Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

## <a name="json-examples"></a>Ejemplos de JSON
En los siguientes ejemplos se proporcionan definiciones JSON que puede usar para crear una canalización mediante [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Muestran cómo copiar datos entre el sistema de archivos local y Azure Blob Storage. Sin embargo, puede copiar datos *directamente* desde cualquiera de los orígenes y hasta cualquiera de los receptores enumerados en [orígenes y receptores compatibles](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia de Azure Data Factory.

## <a name="example-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>Ejemplo: Copia de datos de un sistema de archivos local a Azure Blob Storage
En este ejemplo se muestra cómo copiar datos desde un sistema de archivos local hasta Azure Blob Storage. El ejemplo consta de las siguientes entidades de Data Factory:

* Un servicio vinculado de tipo [OnPremisesFileServer](#linked-service-properties).
* Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [FileShare](#dataset-properties).
* Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [FileSystemSource](#copy-activity-properties) y [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

En el siguiente ejemplo se copian datos de series temporales de un sistema de archivos local hasta Azure Blob Storage cada hora. Las propiedades JSON que se usan en estos ejemplos se describen en las secciones después de los ejemplos.

Como primer paso, configure la puerta de enlace de administración de datos según las instrucciones que se describen en [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md).

**Servicio vinculado del servidor de archivos local:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

Además, se recomienda usar la propiedad **encryptedCredential** en lugar de usar las propiedades **userid** y **password**. Consulte [Servicio vinculado del sistema de archivos](#linked-service-properties) para más información sobre este servicio vinculado.

**Servicio vinculado de Almacenamiento de Azure:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Conjunto de datos de entrada del sistema de archivos local:**

Los datos se seleccionan de un archivo nuevo cada hora. Las propiedades folderPath y fileName se determinan en función de la hora de inicio del segmento.  

El valor `"external": "true"` informa a Data Factory de que el conjunto de datos es externo a la factoría de datos y no la produce ninguna actividad de dicha factoría.

```JSON
{
  "name": "OnpremisesFileSystemInput",
  "properties": {
    "type": " FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Conjunto de datos de salida de Azure Blob Storage:**

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta para el blob se evalúa dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Actividad de copia en una canalización con el origen del sistema de archivos y el receptor de blob:**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de JSON de canalización, el tipo **source** se establece en **FileSystemSource** y el tipo **sink** se establece en **BlobSink**.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T19:00:00",
    "description":"Pipeline for copy activity",
    "activities":[  
      {
        "name": "OnpremisesFileSystemtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "OnpremisesFileSystemInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "FileSystemSource"
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

## <a name="example-copy-data-from-azure-sql-database-to-an-on-premises-file-system"></a>Ejemplo: Copia de datos desde Azure SQL Database a un sistema de archivos local
El ejemplo siguiente muestra:

* Un servicio vinculado de tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
* Un servicio vinculado de tipo [OnPremisesFileServer](#linked-service-properties).
* Un conjunto de datos de entrada de tipo [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
* Un conjunto de datos de salida de tipo [FileShare](#dataset-properties).
* Una canalización con una actividad de copia que usa [SqlSource](data-factory-azure-sql-connector.md##copy-activity-properties) y [FileSystemSink](#copy-activity-properties).

En el ejemplo se copian datos de series temporales de una tabla de Azure SQL a un sistema de archivos local cada hora. Las propiedades JSON que se usan en estos ejemplos se describen en las secciones después de los ejemplos.

**Servicio vinculado a Azure SQL Database:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```

**Servicio vinculado del servidor de archivos local:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

Además, se recomienda usar la propiedad **encryptedCredential** en lugar de usar las propiedades **userid** y **password**. Consulte [Servicio vinculado del sistema de archivos](#linked-service-properties) para más información sobre este servicio vinculado.

**Conjunto de datos de entrada SQL de Azure:**

En el ejemplo se supone que ha creado una tabla "MyTable" en Azure SQL y que contiene una columna denominada "timestampcolumn" para los datos de series temporales.

El valor ``“external”: ”true”`` informa a Data Factory de que el conjunto de datos es externo a la factoría de datos y no la produce ninguna actividad de dicha factoría.

```JSON
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Conjunto de datos de salida del sistema de archivos local:**

Los datos se copian en un archivo nuevo cada hora. Los valores de folderPath y fileName del blob se determinan en función de la hora de inicio del segmento.

```JSON
{
  "name": "OnpremisesFileSystemOutput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Actividad de copia en una canalización con el origen de SQL y el receptor de sistema de archivos:**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de JSON de canalización, el tipo **source** se establece en **SqlSource** y el tipo **sink** en **FileSystemSink**. La consulta SQL especificada para la propiedad **SqlReaderQuery** selecciona los datos de la última hora que se van a copiar.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T20:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "AzureSQLtoOnPremisesFile",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "OnpremisesFileSystemOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "FileSystemSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 3,
          "timeout": "01:00:00"
        }
      }
     ]
   }
}
```


También puede asignar columnas del conjunto de datos de origen a las del conjunto de datos receptor en la definición de actividad de copia. Para obtener más información, consulte [Asignación de columnas de conjunto de datos de Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Rendimiento y optimización
 Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Data Factory de Azure y las diversas formas de optimizarlo.


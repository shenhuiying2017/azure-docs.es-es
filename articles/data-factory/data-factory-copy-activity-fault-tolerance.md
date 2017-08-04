---
title: "Tolerancia a errores de actividad de copia de Azure Data Factory: omisión de filas incompatibles | Microsoft Docs"
description: "Obtenga información sobre la tolerancia a errores omitiendo las filas compatibles durante la copia mediante Azure Data Factory"
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
ms.date: 07/19/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: d613537657af3bbe379a53e92532bf6b184d762b
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="copy-activity-fault-tolerance---skip-incompatible-rows"></a>Copia de la tolerancia a errores de actividad de copia: omisión de filas incompatibles

Con la [actividad de copia](data-factory-data-movement-activities.md), tiene distintas opciones para tratar con filas incompatibles cuando copie datos entre los almacenes de datos de receptor y origen. Puede elegir anular y generar un error de la actividad de copia cuando se encuentren datos incompatibles (comportamiento predeterminado) o seguir copiando todos los datos y omitir las filas incompatibles. Además, también tiene la opción de registrar filas incompatibles en Azure Blob, por lo que puede examinar la causa del error, solucionar los datos en el origen de datos y reintentar.

## <a name="supported-scenarios"></a>Escenarios admitidos
Actualmente, la actividad de copia admite la detección, omisión y registro de la siguiente situación incompatible durante la copia:

- **Incompatibilidad del tipo de datos entre los tipos nativos de receptor y origen**

    Ejemplo: copiar desde el archivo CSV en Azure Blob a Azure SQL Database y esquema definido en Azure SQL Database con tres columnas de tipo *INT*. Las filas con datos numéricos (por ejemplo, `123,456,789`) en el archivo CSV de origen se copian correctamente, mientras que las filas que contienen valores no numéricos (por ejemplo, `123,456,abc`) se omiten como filas no compatibles.

- **Error de coincidencia de número de columnas entre el origen y el receptor**

    Ejemplo: copiar desde el archivo CSV en Azure Blob a Azure SQL Database y esquema definido en SQL Azure con seis columnas. Las filas que contienen seis columnas en el archivo CSV de origen se copian correctamente, mientras que las filas con otro número de columnas se omiten como filas incompatibles.

- **Infracción de clave principal al escribir en base de datos relacional**

    Ejemplo: copiar de SQL Server a Azure SQL Database, clave principal definida en Azure SQL Database de receptor, pero esa clave principal no se ha definido en SQL Server de origen. No se permiten las filas duplicadas que puede haber en el origen al escribir en el receptor. La actividad de copia realiza la copia solo de la primera fila en el receptor y omite la segunda fila o más con el valor de clave principal duplicado de origen a receptor.

## <a name="configuration"></a>Configuración
En el ejemplo siguiente se muestra la definición de JSON sobre cómo configurar la omisión de las filas incompatibles en la actividad de copia:

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },         
    "enableSkipIncompatibleRow": true,           
    "redirectIncompatibleRowSettings": {
        "linkedServiceName": "BlobStorage",
        "path": "redirectcontainer/erroroutput"
    }
}
```

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| enableSkipIncompatibleRow | Habilite si lo desea la omisión de filas incompatibles durante la copia. | True<br/>False (valor predeterminado) | No |
| redirectIncompatibleRowSettings | Un grupo de propiedades que puede especificarse cuando quiere registrar las filas incompatibles. | &nbsp; | No |
| linkedServiceName | El servicio vinculado de Azure Storage para almacenar el registro que contiene todas las filas que se omiten. | Especifique el nombre de un servicio vinculado [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) o [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service), que haga referencia a la instancia de Azure Storage que usa para almacenar el archivo de registro. | No |
| path | La ruta de acceso del archivo de registro que contiene todas las filas que se omiten. | Especifique la ruta de acceso de Blob Storage que desee para registrar los datos incompatibles. Si no se proporciona una ruta de acceso, el servicio creará un contenedor para usted. | No |

## <a name="monitoring"></a>Supervisión
Una vez que se haya completado la ejecución de la actividad de copia, podrá ver el número de filas omitidas en la sección de supervisión de la siguiente manera:

![Omisión de la supervisión de filas incompatibles](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

Si configura registrar las filas incompatibles, para ver las que se han omitido y la causa principal de la incompatibilidad, puede ver el archivo de registro en esta ruta de acceso: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`.

Los datos originales y el error correspondiente se registran en el archivo. A continuación se muestra un ejemplo del contenido del archivo de registro:
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).,
```

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre la actividad de copia de Azure Data Factory, vea [Movimiento de datos con la actividad de copia](data-factory-data-movement-activities.md).

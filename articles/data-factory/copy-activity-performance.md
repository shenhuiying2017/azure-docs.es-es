---
title: "Guía de optimización y rendimiento de la actividad de copia en Azure Data Factory | Microsoft Docs"
description: "Conozca los factores más importantes que afectan al rendimiento del movimiento de datos en Azure Data Factory cuando se usa la actividad de copia."
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
ms.date: 01/15/2018
ms.author: jingwang
ms.openlocfilehash: 53f2b59e57d49a409552aebbdb1b0e81ccd5200c
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Guía de optimización y rendimiento de la actividad de copia
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-copy-activity-performance.md)
> * [Versión 2: versión preliminar](copy-activity-performance.md)


Copiar actividad de Azure Data Factory ofrece una solución de carga de datos de alto rendimiento fiable y segura de primera clase. Le permite copiar decenas de terabytes de datos al día en una amplia variedad de almacenes de datos locales y en la nube. Un rendimiento acelerado de la carga de datos es clave para garantizar que puede centrarse en el problema principal de los "macrodatos": crear soluciones de análisis avanzadas y profundizar en todos esos datos.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general (GA), consulte [Rendimiento de la actividad de copia en Data Factory versión 1](v1/data-factory-copy-activity-performance.md).

Azure proporciona un conjunto de soluciones de almacén de datos y almacenamiento de datos de clase empresarial, y la actividad de copia ofrece una experiencia de carga de datos enormemente optimizada que es fácil de configurar e instalar. Con solo una actividad de copia, puede conseguir:

* Cargar datos en **Azure SQL Data Warehouse** a **1,2 GBps**.
* Cargar datos en **Azure Blob Storage** a **1,0 GBps**
* Cargar datos en **Azure Data Lake Store** a **1,0 GBps**

En este artículo se describe:

* [Números de referencia de rendimiento](#performance-reference) , para almacenes de datos origen y receptor que le ayudan a planear su proyecto.
* Características que pueden aumentar el rendimiento de la copia en diferentes escenarios, como [unidades de movimiento de datos de nube](#cloud-data-movement-units), [copia en paralelo](#parallel-copy) y [copia almacenada provisionalmente](#staged-copy);
* [Directrices de ajuste de rendimiento](#performance-tuning-steps) , acerca de cómo optimizar el rendimiento y los factores clave que pueden afectar al rendimiento de la copia.

> [!NOTE]
> Si no está familiarizado con la actividad de copia, consulte [Información general de la actividad de copia](copy-activity-overview.md) antes de leer este artículo.
>

## <a name="performance-reference"></a>Referencia de rendimiento

Como referencia, la tabla siguiente muestra la cantidad de procesamiento de copias **en Mbps** para los pares origen-receptor especificados **en una ejecución de actividad de copia única** en función de pruebas internas. A efectos de comparación, también muestra cómo distintos valores de [unidades de movimiento de datos de nube](#cloud-data-movement-units) o [escalabilidad de Integration Runtime autohospedado](concepts-integration-runtime.md#self-hosted-integration-runtime) (varios nodos) pueden contribuir al rendimiento de copias.

![Matriz de rendimiento](./media/copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>En la versión 2 de Azure Data Factory, cuando la actividad de copia se ejecuta en una instancia de Azure Integration Runtime, la cantidad mínima permitida de unidades de movimiento de datos de nube es dos. Si no se especifica, vea las unidades de movimiento de datos predeterminadas usadas en [Unidades de movimiento de datos de nube](#cloud-data-movement-units).

Puntos a tener en cuenta:

* La capacidad de proceso se calcula con la siguiente fórmula: [tamaño de los datos leídos del origen]/[duración de la ejecución de Copiar actividad].
* Los números de referencia de rendimiento de la tabla se midieron mediante el conjunto de datos [TPC-H](http://www.tpc.org/tpch/) en una ejecución de actividad de copia única.
* En los almacenes de datos de Azure, el origen y el receptor se encuentran en la misma región de Azure.
* Para la copia híbrida entre almacenes de datos locales y en la nube, cada nodo de Integration Runtime autohospedado se ejecutaba en una máquina independiente del almacén de datos con la especificación siguiente. Si se ejecutó una única actividad, la operación de copia solo consumió una pequeña parte del ancho de banda de red, memoria o CPU de la máquina de prueba.
    <table>
    <tr>
        <td>CPU</td>
        <td>Intel Xeon E5-2660 v2 de 32 núcleos a 2,20 GHz</td>
    </tr>
    <tr>
        <td>Memoria</td>
        <td>128 GB</td>
    </tr>
    <tr>
        <td>Red</td>
        <td>Interfaz de Internet: 10 Gbps; interfaz de intranet: 40 Gbps</td>
    </tr>
    </table>


> [!TIP]
> Para lograr un mayor rendimiento, use más unidades de movimiento de datos (DMU) que el número máximo de DMU permitidas predeterminado, que son 32 para una ejecución de actividad de copia de nube a nube. Por ejemplo, con 100 DMU, puede copiar datos de Azure Blob a Azure Data Lake Store a una velocidad de **1 GBps**. Consulte la sección [Unidades de movimiento de datos en la nube](#cloud-data-movement-units) para más detalles sobre esta característica y el escenario admitido. Póngase en contacto con el [soporte técnico de Azure](https://azure.microsoft.com/support/) para solicitar más DMU.

## <a name="cloud-data-movement-units"></a>Unidades de movimiento de datos de nube

Una **unidad de movimiento de datos (DMU) de nube** es una medida que representa la eficacia (una combinación de CPU, memoria y asignación de recursos de red) de una única unidad en Data Factory. Una **DMU solo se aplica a [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime)**, pero no a [Integration Runtime autohospedado](concepts-integration-runtime.md#self-hosted-integration-runtime).

**La cantidad mínima de unidades de movimiento de datos de nube para impulsar la ejecución de la actividad de copia es dos**. Si no se especifica, en la tabla siguiente se muestran las DMU predeterminadas que se usan en distintos escenarios de copia:

| Escenario de copia | DMU predeterminadas que determina el servicio |
|:--- |:--- |
| Copia de datos entre almacenes basados en archivos | Entre 4 y 32 según el número y el tamaño de los archivos. |
| Todos los demás escenarios de copia | 4 |

Para reemplazar esta configuración predeterminada, especifique un valor para la propiedad **cloudDataMovementUnits** de la manera siguiente. Los **valores admitidos** para la propiedad **cloudDataMovementUnits** son 2, 4, 8, 16, 32. El **número real de DMS de nube** que usa la operación de copia en tiempo de ejecución es igual o inferior al valor configurado, según el patrón de datos. Para más información sobre el nivel de ganancia de rendimiento que puede obtener al configurar más unidades para un origen y un receptor de copia específicos, consulte la [referencia de rendimiento](#performance-reference).

Puede ver las unidades de movimiento de datos de nube que realmente se usan en cada ejecución de copia en la salida de la actividad de copia cuando se supervisa una ejecución de copia. Obtenga detalles en [Supervisión de la actividad de copia](copy-activity-overview.md#monitoring).

> [!NOTE]
> Si necesita más DMU de nube para aumentar el rendimiento, póngase en contacto con el [servicio técnico de Azure](https://azure.microsoft.com/support/). La configuración de ocho y más actualmente solo funciona cuando se **copian varias archivos desde Blob Storage, Data Lake Store, Amazon S3, FTP en la nube o SFTP en la nube a cualquier otro almacén de datos en la nube**.
>

**Ejemplo:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "cloudDataMovementUnits": 32
        }
    }
]
```

### <a name="cloud-data-movement-units-billing-impact"></a>Impacto en la facturación de las unidades de movimiento de datos de nube

Es **importante** recordar que se cobra en función del tiempo total de la operación de copia. La duración total que se factura por el movimiento de datos es la suma de la duración de todas las DMU. Si un trabajo de copia solía tardar una hora con dos unidades de nube y ahora tarda 15 minutos con ocho, la factura general sigue siendo casi igual.

## <a name="parallel-copy"></a>Copia en paralelo

Puede usar la propiedad **parallelCopies** para indicar el paralelismo que quiere que use la actividad de copia. Esta propiedad se puede considerar como el número máximo de subprocesos dentro de la actividad de copia que se pueden leer del origen o escribir en los almacenes de datos receptores en paralelo.

Para cada ejecución de actividad de copia, Data Factory determina el número de copias en paralelo que se usarán para copiar datos del almacén de datos de origen al almacén de datos de destino. El número predeterminado de copias en paralelo que use dependerá del tipo de origen y receptor que vaya a usar:

| Escenario de copia | Recuento predeterminado de copias en paralelo determinado por servicio |
| --- | --- |
| Copia de datos entre almacenes basados en archivos |Entre 1 y 64. Depende del tamaño de los archivos y del número de unidades de movimiento de datos (DMU) de nube usadas para copiar datos entre dos almacenes de datos de nube, o de la configuración física de la máquina de Integration Runtime autohospedado. |
| Copia de datos desde cualquier almacén de datos a Azure Table Storage |4 |
| Todos los demás escenarios de copia |1 |

Normalmente, el comportamiento predeterminado debe proporcionar el mejor rendimiento. Sin embargo, para controlar la carga en las máquinas que hospedan los almacenes de datos, o para optimizar el rendimiento de la copia, puede optar por reemplazar el valor predeterminado y especificar un valor para la propiedad **parallelCopies** . El valor debe ser un entero mayor o igual que 1. En tiempo de ejecución, y para obtener el mejor rendimiento, la actividad de copia usa un valor inferior o igual al valor que ha establecido.

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

Puntos a tener en cuenta:

* Cuando copie datos entre almacenes basados en archivos, **parallelCopies** determina el paralelismo en el nivel de archivo. La fragmentación dentro de un único archivo sucedería debajo de forma automática y transparente, y está diseñada para utilizar el tamaño de fragmento más adecuado para un tipo de almacén de datos de origen determinado para cargar datos en paralelo y ortogonales a parallelCopies. El número real de copias en paralelo que usa el servicio de movimiento de datos para la operación de copia en tiempo de ejecución no es superior al número de archivos que tenga. Si el comportamiento de copia es **mergeFile**, la actividad de copia no puede aprovechar las ventajas del paralelismo de nivel de archivo.
* Cuando especifique un valor para la propiedad **parallelCopies**, considere el aumento de carga en los almacenes de datos de origen y receptor, y a Integration Runtime autohospedado lo usa la actividad de copia para, por ejemplo, realizar una copia híbrida. Esto sucede especialmente si tiene varias actividades o ejecuciones simultáneas de las mismas actividades que se ejecutan en el mismo almacén de datos. Si observa que el almacén de datos o Integration Runtime autohospedado están sobrecargados, disminuya el valor de la propiedad **parallelCopies** para aliviar la carga.
* Cuando se copian datos de almacenes no basados en archivos en almacenes basados en archivos, el servicio de movimiento de datos ignora la propiedad **parallelCopies** . Aunque se especifica el paralelismo, no se aplica en este caso.
* **parallelCopies** es ortogonal a **cloudDataMovementUnits**. Lo anterior se cuenta en todas las unidades de movimiento de datos de nube.

## <a name="staged-copy"></a>copia almacenada provisionalmente

Al copiar datos de un almacén de datos de origen a un almacén de datos receptor, podría elegir usar Almacenamiento de blobs como almacenamiento provisional. El almacenamiento provisional es especialmente útil en los siguientes casos:

- **Quiere realizar la ingesta de datos de varios almacenes de datos en SQL Data Warehouse mediante PolyBase**. SQL Data Warehouse emplea PolyBase como mecanismo de alto rendimiento para cargar una gran cantidad de datos en SQL Data Warehouse. Sin embargo, los datos de origen deben estar en Blob Storage o Azure Data Lake Store y deben satisfacer criterios adicionales. Al cargar datos desde un almacén de datos distinto de Blob Storage o Azure Data Lake Store, puede activar la copia de datos mediante Blob Storage de almacenamiento provisional. En ese caso, Data Factory realiza las transformaciones de datos necesarias para garantizar que se cumplen los requisitos de PolyBase. A continuación, se usa PolyBase para cargar datos en SQL Data Warehouse de manera eficaz. Para más información, consulte [Uso de PolyBase para cargar datos en Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **En ocasiones, realizar un movimiento de datos híbridos lleva tiempo (es decir, copiar desde un almacén de datos local a un almacén de datos en la nube) a través de una conexión de red lenta**. Para mejorar el rendimiento, puede usar la copia almacenada provisionalmente para comprimir localmente los datos para que el proceso de mover los datos al almacén de datos provisional en la nube tarde menos tiempo que descomprimir los datos en el almacén provisional antes de cargarlos en el almacén de datos de destino.
- **No quiere abrir otros puertos que no sean el 80 y el 443 en el firewall, debido a las directivas de TI corporativas**. Por ejemplo, al copiar datos de un almacén de datos local a un receptor de Azure SQL Database o a un receptor de Azure SQL Data Warehouse, debe activar la comunicación TCP saliente en el puerto 1433 tanto para el firewall de Windows como para el firewall corporativo. En este escenario, la copia almacenada provisionalmente usa Integration Runtime autohospedado para copiar primero los datos en una instancia provisional de Blob Storage a través de HTTP o HTTPS en el puerto 443 y, luego, cargar los datos en SQL Database o SQL Data Warehouse desde el almacenamiento provisional de Blob Storage. En este flujo, no es necesario habilitar el puerto 1433.

### <a name="how-staged-copy-works"></a>Funcionamiento de las copias almacenadas provisionalmente

Al activar la característica de almacenamiento provisional, primero se copian los datos desde el almacén de datos de origen a la instancia de Blob Storage provisional (use su propio). A continuación, los datos se copian desde el almacén de datos provisional al almacén de datos receptor. Data Factory administra automáticamente el flujo de las dos fases. Data Factory también limpia los datos temporales del almacenamiento provisional una vez finalizado el movimiento de los datos.

![copia almacenada provisionalmente](media/copy-activity-performance/staged-copy.png)

Cuando activa el movimiento de datos mediante un almacenamiento provisional, puede especificar si quiere que los datos se compriman antes de moverlos del almacén de datos de origen al provisional y luego descomprimirlos antes de moverlos desde un almacenamiento de datos provisional a un almacén de datos receptor.

Actualmente, no se pueden copiar datos entre dos almacenes de datos locales mediante almacenamiento provisional.

### <a name="configuration"></a>Configuración

Configure la opción **enableStaging** de la actividad de copia para especificar si quiere que los datos se almacenen provisionalmente en Blob Storage antes de cargarlos en un almacén de datos de destino. Cuando establezca **enableStaging** en `TRUE`, especifique las propiedades adicionales que se muestran en la tabla siguiente. Si no tiene un servicio vinculado a la firma de acceso compartido de Almacenamiento o de Azure Storage para el almacenamiento provisional, deberá crear uno.

| Propiedad | Description | Valor predeterminado | Obligatorio |
| --- | --- | --- | --- |
| **enableStaging** |Especifique si desea copiar los datos a través de un almacén provisional. |False |No |
| **linkedServiceName** |Especifique el nombre de un servicio vinculado [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) que haga referencia a la instancia de Storage que se usa como almacenamiento provisional. <br/><br/> No puede usar Storage con una firma de acceso compartido para cargar datos en SQL Data Warehouse mediante PolyBase. Puede usarlo en todos los demás casos. |N/D |Sí, cuando el valor de **enableStaging** está establecido en True. |
| **path** |Especifique la ruta de acceso de Almacenamiento de blobs que quiere que contenga los datos almacenados provisionalmente. Si no se proporciona una ruta de acceso, el servicio creará un contenedor para almacenar los datos temporales. <br/><br/> Especifique una ruta de acceso solo si usa Almacenamiento con una firma de acceso compartido o si necesita que los datos temporales estén en una ubicación específica. |N/D |No |
| **enableCompression** |Especifica si se deben comprimir los datos antes de copiarlos en el destino. Esta configuración reduce el volumen de datos que se va a transferir. |False |No |

Este es un ejemplo de definición de actividad de copia con las propiedades que se han descrito en la tabla anterior:

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path",
                "enableCompression": true
            }
        }
    }
]
```

### <a name="staged-copy-billing-impact"></a>Impacto en la facturación de copia almacenada provisionalmente

Los cargos que se le realizan se basan en dos elementos: duración de la copia y tipo de copia.

* Cuando usa el almacenamiento provisional durante una copia de nube (copia de datos de un almacén de datos en la nube a otro de este tipo, donde ambos almacenamientos provisionales usan Azure Integration Runtime), se le cobrará de la siguiente manera: [suma de la duración de la copia de los pasos 1 y 2] x [precio unitario de la copia de nube].
* Cuando usa el almacenamiento provisional durante una copia híbrida (copia de datos de un almacén de datos local a uno en la nube, un almacenamiento provisional que usa Integration Runtime autohospedado), se le cobrará de la siguiente manera: [duración de la copia híbrida] x [precio unitario de la copia híbrida] + [duración de la copia de nube] x [precio unitario de la copia de nube].

## <a name="performance-tuning-steps"></a>Pasos de optimización del rendimiento

Para optimizar el rendimiento del servicio Data Factory con la actividad de copia, sugerimos que realice estos pasos:

1. **Establezca una línea base**. Durante la fase de desarrollo, pruebe la canalización usando la actividad de copia en un ejemplo de datos representativo. Recopile detalles de la ejecución y características del rendimiento después de la [supervisión de la actividad de copia](copy-activity-overview.md#monitoring).

2. **Diagnostique y optimice el rendimiento**. Si el rendimiento que observa no satisface sus expectativas, deberá identificar los cuellos de botella. A continuación, optimice el rendimiento para eliminar o reducir el efecto de los cuellos de botella. Aunque la descripción completa del diagnóstico de rendimiento escapa del ámbito de este artículo, aquí hay algunos aspectos comunes a tener en cuenta:

   * Características de rendimiento:
     * [Copia paralela](#parallel-copy)
     * [Unidades de movimiento de datos de nube](#cloud-data-movement-units)
     * [Copias almacenadas provisionalmente](#staged-copy)
     * [Escalabilidad de Integration Runtime autohospedado](concepts-integration-runtime.md#self-hosted-integration-runtime)
   * [Integration Runtime autohospedado](#considerations-for-self-hosted-integration-runtime)
   * [Origen](#considerations-for-the-source)
   * [Sink](#considerations-for-the-sink)
   * [Serialización y deserialización](#considerations-for-serialization-and-deserialization)
   * [Compresión](#considerations-for-compression)
   * [Asignación de columnas](#considerations-for-column-mapping)
   * [Otras consideraciones](#other-considerations)

3. **Expanda la configuración a todo el conjunto de datos**. Cuando esté satisfecho con los resultados de la ejecución y el rendimiento, puede expandir la definición y la canalización para cubrir todo el conjunto de datos.

## <a name="considerations-for-self-hosted-integration-runtime"></a>Consideraciones sobre Integration Runtime autohospedado

Si la actividad de copia se ejecuta en una instancia de Integration Runtime autohospedado, tenga en cuenta lo siguiente:

**Configuración**: se recomienda usar una máquina dedicada para hospedar Integration Runtime. Consulte [Consideraciones sobre el uso de Integration Runtime autohospedado](concepts-integration-runtime.md).

**Escalado horizontal**: una instancia de Integration Runtime autohospedado lógica única con uno o varios nodos puede atender varias ejecuciones de actividad de copia simultáneamente. Si depende mucho del movimiento de datos híbridos con un gran número de ejecuciones de actividad de copia simultáneas o con grandes volúmenes de datos que copiar, considere la posibilidad de [escalar horizontalmente Integration Runtime autohospedado](create-self-hosted-integration-runtime.md#high-availability-and-scalability) con aprovisionar más recursos para ampliar la capacidad de copia.

## <a name="considerations-for-the-source"></a>Consideraciones sobre el origen

### <a name="general"></a>General

Asegúrese de que el almacén de datos subyacente no esté saturado con otras cargas de trabajo que se ejecutan en él o contra él.

Para almacenes de datos de Microsoft, consulte los [temas sobre supervisión y optimización](#performance-reference) específicos de los almacenes de datos que le ayuden a comprender las características de rendimiento del almacén de datos, a reducir los tiempos de respuesta y a maximizar la capacidad de proceso.

* Si copia datos **de Blob Storage a SQL Data Warehouse**, considere el uso de **PolyBase** para mejorar el rendimiento. Consulte [Uso de PolyBase para cargar datos en SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) para obtener más información.
* Si copia datos **de HDFS a Azure Blob o Azure Data Lake Store**, considere el uso de **DistCp** para mejorar el rendimiento. Consulte [Uso de DistCp para copiar datos desde HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs) para más detalles.
* Si copia datos **de Redshift a Azure SQL Data Warehouse, Azure Blob o Azure Data Lake Store**, considere el uso de **UNLOAD** para mejorar el rendimiento. Consulte [Uso de UNLOAD para copiar datos de Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift) para más detalles.

### <a name="file-based-data-stores"></a>Almacenes de datos basados en archivos

* **Tamaño de archivo medio y número de archivos**: la actividad de copia transfiere datos de archivo en archivo. Siendo la misma cantidad de datos la que se va a mover, el rendimiento general es menor si los datos constan de muchos archivos pequeños en lugar de algunos grandes, debido a la fase de arranque de cada archivo. Por lo tanto, si es posible, combine archivos pequeños en archivos de mayor tamaño para obtener una capacidad de proceso mayor.
* **Formato de archivo y compresión**: para ver más formas de mejorar el rendimiento, consulte las secciones [Consideraciones sobre serialización y deserialización](#considerations-for-serialization-and-deserialization) y [Consideraciones sobre la compresión](#considerations-for-compression).

### <a name="relational-data-stores"></a>Almacenes de datos relacionales

* **Patrón de datos**: el esquema de tabla afecta al rendimiento de la copia. Un tamaño de fila grande le ofrece un mejor rendimiento que el tamaño de fila pequeño para copiar la misma cantidad de datos. El motivo es que la base de datos puede recuperar más eficazmente menos lotes de datos que contienen menos filas.
* **Consulta o procedimiento almacenado**: optimice la lógica de la consulta o del procedimiento almacenado que se especifica en el origen de la actividad de copia para que capture los datos de forma más eficiente.

## <a name="considerations-for-the-sink"></a>Consideraciones sobre el receptor

### <a name="general"></a>General

Asegúrese de que el almacén de datos subyacente no esté saturado con otras cargas de trabajo que se ejecutan en él o contra él.

Para información sobre los almacenes de datos de Microsoft, consulte los [temas sobre supervisión y optimización](#performance-reference) que son específicos de los almacenes de datos. Estos temas pueden ayudarle a comprender las características de rendimiento de los almacenes de datos y a saber cómo reducir los tiempos de respuesta y aumentar la capacidad de proceso.

* Si copia datos **de Blob Storage a SQL Data Warehouse**, considere el uso de **PolyBase** para mejorar el rendimiento. Consulte [Uso de PolyBase para cargar datos en SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) para obtener más información.
* Si copia datos **de HDFS a Azure Blob o Azure Data Lake Store**, considere el uso de **DistCp** para mejorar el rendimiento. Consulte [Uso de DistCp para copiar datos desde HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs) para más detalles.
* Si copia datos **de Redshift a Azure SQL Data Warehouse, Azure Blob o Azure Data Lake Store**, considere el uso de **UNLOAD** para mejorar el rendimiento. Consulte [Uso de UNLOAD para copiar datos de Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift) para más detalles.

### <a name="file-based-data-stores"></a>Almacenes de datos basados en archivos

* **Comportamiento de copia**: si copia datos de un almacén de datos basado en archivos diferente, la actividad de copia presenta tres opciones mediante la propiedad **copyBehavior**. Conserva la jerarquía, aplana la jerarquía o combina archivos. Conservar o aplanar la jerarquía supone poca o ninguna sobrecarga sobre el rendimiento, mientras que combinar archivos hace que aumente dicha sobrecarga.
* **Formato de archivo y compresión**: consulte las secciones [Consideraciones sobre serialización y deserialización](#considerations-for-serialization-and-deserialization) y [Consideraciones sobre la compresión](#considerations-for-compression) para conocer más formas de mejorar el rendimiento.

### <a name="relational-data-stores"></a>Almacenes de datos relacionales

* **Comportamiento de copia**: según las propiedades que haya establecido para **sqlSink**, la actividad de copia escribe datos en la base de datos de destino de diferentes formas.
  * De forma predeterminada, el servicio de movimiento de datos usa la API de copia masiva para insertar datos en modo de anexión, lo que proporciona el mejor rendimiento.
  * Si configura un procedimiento almacenado en el receptor, la base de datos aplica los datos de fila en fila en lugar de como una carga masiva. El rendimiento disminuye considerablemente. Si el conjunto de datos es grande, considere la posibilidad de cambiar al uso de la propiedad **preCopyScript**, si corresponde.
  * Si configura la propiedad **preCopyScript** para cada ejecución de la actividad de copia, el servicio desencadena el script y luego el usuario usa la API de copia masiva para insertar los datos. Por ejemplo, para sobrescribir toda la tabla con los datos más recientes, puede especificar un script para eliminar primero todos los registros antes de realizar la carga masiva de los nuevos datos desde el origen.
* **Patrón de datos y tamaño de lote**:
  * El esquema de tabla afecta al rendimiento de la copia. Para copiar la misma cantidad de datos, un tamaño de fila grande proporcionará un mejor rendimiento que un tamaño de fila pequeño porque la base de datos puede confirmar de forma más eficiente menos lotes de datos.
  * La actividad de copia inserta datos en una serie de lotes. Puede establecer el número de filas en un lote mediante la propiedad **writeBatchSize** . Si los datos tienen filas pequeñas, puede establecer la propiedad **writeBatchSize** con un valor más alto para beneficiarse de una menor sobrecarga de los lotes y un mayor rendimiento. Si el tamaño de fila de los datos es grande, tenga cuidado al aumentar **writeBatchSize**. Un valor alto podría provocar un error de copia provocado por la sobrecarga de la base de datos.

### <a name="nosql-stores"></a>Almacenes NoSQL

* Para **Almacenamiento de tablas**:
  * **Partición**: escribir datos en particiones intercaladas degrada considerablemente el rendimiento. Ordene los datos de origen por la clave de partición para que los datos se inserten de forma eficiente en una partición después de otra o ajuste la lógica para escribir los datos en una sola partición.

## <a name="considerations-for-serialization-and-deserialization"></a>Consideraciones sobre serialización y deserialización

La serialización y deserialización pueden tener lugar cuando el conjunto de datos de entrada o el conjunto de datos de salida es un archivo. Consulte [Formatos de archivo y de compresión admitidos](supported-file-formats-and-compression-codecs.md) con detalles sobre los formatos de archivo que admite la actividad de copia.

**Comportamiento de copia**:

* Al copiar archivos entre almacenes de datos basados en archivos:
  * Cuando los conjuntos de datos de entrada y salida tienen la misma configuración de formato de archivo o carecen de dicha configuración, el servicio de movimiento de datos ejecuta una **copia binaria** sin ninguna serialización o deserialización. En este caso, el rendimiento es mayor en comparación con el escenario en el que la configuración del formato de archivo de origen y receptor es diferente.
  * Cuando los conjuntos de datos de entrada y salida están en formato de texto y solo es diferente el tipo de codificación, el servicio de movimiento de datos solo realiza la conversión de la codificación. No se realiza ninguna serialización y deserialización, lo que provoca cierta sobrecarga en el rendimiento en comparación con una copia binaria.
  * Cuando los conjuntos de datos de entrada y salida tienen formatos de archivo diferentes o configuraciones diferentes, por ejemplo, los delimitadores, el servicio de movimiento de datos deserializa los datos de origen para transmitirlos, transformarlos y luego serializarlos en el formato de salida indicado. Esta operación da como resultado una sobrecarga mucho mayor sobre el rendimiento en comparación con otros escenarios.
* Al copiar archivos desde un almacén de datos y hacia un almacén de datos que no se basa en archivos (por ejemplo, desde un almacén basado en archivos hasta un almacén relacional), se requiere el paso de serialización o deserialización. Este paso produce una sobrecarga considerable sobre el rendimiento.

**Formato de archivo**: el formato de archivo que elija puede afectar al rendimiento de la copia. Por ejemplo, Avro es un formato binario compacto que almacena metadatos con datos. Es ampliamente admitido para el procesamiento y la realización de consultas en el ecosistema de Hadoop. Sin embargo, Avro resulta más caro para serialización y deserialización, lo que da lugar a un menor rendimiento de la copia en comparación con el formato de texto. Elija el formato de archivo para el flujo de procesamiento de forma holística. Para comenzar, piense en qué formato se almacenan los datos, los almacenes de datos de origen o si se extraerán de sistemas externos; el mejor formato para el almacenamiento, el procesamiento analítico y la realización de consultas; y en qué formato se deben exportar los datos a data marts para poder usar herramientas de visualización y generación de informes. A veces, un formato de archivo que no es óptimo desde el punto de vista del rendimiento de lectura y escritura podría ser una buena opción al considerar el proceso analítico en general.

## <a name="considerations-for-compression"></a>Consideraciones sobre la compresión

Cuando el conjunto de datos de entrada o salida es un archivo, puede configurar la actividad de copia para realizar la compresión y descompresión de los datos a medida que se escriben en el destino. Si elige compresión, está buscando el equilibrio entre entrada/salida (E/S) y CPU. La compresión de los datos supone un costo adicional en recursos de proceso. Pero, a cambio, se reduce la E/S de red y el almacenamiento. Según los datos, puede que observe un aumento en el rendimiento general de la actividad de copia.

**Códec**: cada códec de compresión tiene sus ventajas. Por ejemplo, bzip2 tiene el rendimiento de copia más bajo, pero obtiene el mejor rendimiento de consulta de Hive porque puede dividirlo para el procesamiento. Gzip es la opción más equilibrada y es la que se usa con mayor frecuencia. Elija el códec que mejor se adapte a su escenario de principio a fin.

**Nivel:**para cada códec de compresión, puede elegir entre dos opciones: compresión más rápida y compresión más óptima. La operación de compresión más rápida comprime los datos tan pronto como sea posible, incluso si el archivo resultante no se comprime de forma óptima. La opción de compresión óptima dedica más tiempo a la compresión y produce una cantidad mínima de datos. Puede probar ambas opciones para ver cuál proporciona un mejor rendimiento general en su caso.

**Una consideración**: para copiar una gran cantidad de datos entre un almacén local y la nube, considere usar la [copia almacenada provisionalmente](#staged-copy) con compresión habilitada. El uso de almacenamiento provisional resulta de utilidad cuando el ancho de banda de la red corporativa y los servicios de Azure son el factor limitador, y si quiere que el conjunto de datos de entrada y el conjunto de datos de salida estén ambos en formato sin comprimir.

## <a name="considerations-for-column-mapping"></a>Consideraciones sobre la asignación de columnas

La propiedad **columnMappings** se puede establecer en la actividad de copia para asignar todas las columnas de entrada, o un subconjunto de ellas, a las columnas de salida. Después de que el servicio de movimiento de datos lee los datos del origen, debe realizar la asignación de columnas en los datos antes de escribirlos en el receptor. Este procesamiento adicional reduce la capacidad de proceso de la copia.

Si el almacén de datos de origen es consultable, por ejemplo, si es un almacén relacional, como SQL Database o SQL Server, o es un almacén NoSQL como Table Storage o Azure Cosmos DB, considere la posibilidad de insertar la lógica de filtrado y reordenación de columnas en la propiedad **query**, en lugar de usar la asignación de columnas. De esta forma, la proyección se produce mientras el servicio de movimiento de datos lee los datos del almacén de datos de origen, donde es mucho más eficaz.

Más información en [Asignación de esquemas de la actividad de copia](copy-activity-schema-and-type-mapping.md).

## <a name="other-considerations"></a>Otras consideraciones

Si los datos que desea copiar son de gran tamaño, puede ajustar la lógica de negocios para realizar una partición adicional de los datos y programar la actividad de copia para que se ejecute con más frecuencia a fin de reducir el tamaño de los datos de cada ejecución de actividad de copia.

Tenga cuidado con el número de conjuntos de datos y actividades de copia que necesitan Data Factory para conectarse al mismo almacén de datos al mismo tiempo. Muchos trabajos de copia simultáneos podrían limitar un almacén de datos y llevar a una degradación en el rendimiento, reintentos internos de trabajos de copia y, en ocasiones, errores de ejecución.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Escenario de ejemplo: Copia de una instancia local de SQL Server a Blob Storage

**Escenario**: se crea una canalización para copiar datos de una instancia de SQL Server local a Almacenamiento de blobs en formato CSV. Para acelerar el trabajo de copia, los archivos CSV se deben comprimir en formato bzip2.

**Análisis y pruebas**: el rendimiento de la actividad de copia es inferior a 2 MBps, que es mucho más lento que la referencia de rendimiento.

**Análisis y ajuste del rendimiento**: para solucionar el problema de rendimiento, primero veremos cómo se procesan y se mueven los datos.

1. **Lectura de datos**: Integration Runtime abre una conexión a SQL Server y envía la consulta. SQL Server responde enviando el flujo de datos a Integration Runtime a través de la intranet.
2. **Serialización y compresión de datos**: Integration Runtime serializa el flujo de datos en formato CSV y comprime los datos en una secuencia bzip2.
3. **Escritura de datos**: Integration Runtime carga la secuencia bzip2 en Blob Storage a través de Internet.

Como puede ver, los datos se procesan y se mueven en streaming de forma secuencial: SQL Server > LAN > Integration Runtime > WAN > Blob Storage. **El rendimiento general viene determinado por el rendimiento mínimo a través de la canalización**.

![flujo de datos](./media/copy-activity-performance/case-study-pic-1.png)

Puede que uno o varios de los siguientes factores provoquen el cuello de botella en el rendimiento:

* **Origen:**el propio SQL Server tiene un rendimiento bajo debido a cargas intensas.
* **Integration Runtime autohospedado**:
  * **LAN**: Integration Runtime se encuentra lejos de la máquina de SQL Server y tiene una conexión de ancho de banda bajo.
  * **Integration Runtime**: Integration Runtime alcanzó sus limitaciones de carga para llevar a cabo las siguientes operaciones:
    * **Serialización:**la serialización del flujo de datos a formato CSV tiene un rendimiento bajo.
    * **Compresión**: ha elegido un códec de compresión lenta (por ejemplo, bzip2, a 2,8 MBps con Core i7).
  * **WAN**: el ancho de banda entre la red corporativa y los servicios de Azure es bajo (por ejemplo, T1 = 1544 kbps. T2 = 6312 kbps).
* **Receptor**: el Almacenamiento de blobs tiene un rendimiento bajo. (Esta situación es improbable porque su SLA garantiza un mínimo de 60 MBps).

En este caso, la compresión de datos bzip2 podría estar ralentizando la canalización entera. El cambio a un códec de compresión gzip podría aliviar este cuello de botella.

## <a name="reference"></a>Referencia

Estas son algunas referencias para la supervisión y la optimización del rendimiento para algunos de los almacenes de datos admitidos:

* Azure Storage (que incluyeBlob Storage y Table Storage): [Objetivos de escalabilidad de Azure Storage](../storage/common/storage-scalability-targets.md) y [Lista de comprobación de rendimiento y escalabilidad de Azure Storage](../storage/common/storage-performance-checklist.md)
* Azure SQL Database: puede [supervisar el rendimiento](../sql-database/sql-database-single-database-monitor.md) y comprobar el porcentaje de unidades de transacción de base de datos (DTU).
* Azure SQL Data Warehouse: su capacidad se mide en unidades de almacenamiento de datos (DWU); consulte [Administración de la potencia de proceso en Azure SQL Data Warehouse (información general)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure Cosmos DB: [Niveles de rendimiento de Azure Cosmos DB](../cosmos-db/performance-levels.md)
* Instancia de SQL Server local: [Supervisión y optimización del rendimiento](https://msdn.microsoft.com/library/ms189081.aspx)
* Servidor de archivos local: [Performance Tuning for File Servers](https://msdn.microsoft.com/library/dn567661.aspx)

## <a name="next-steps"></a>pasos siguientes
Consulte los otros artículos de la actividad de copia:

- [Información general de la actividad de copia](copy-activity-overview.md)
- [Asignación de esquemas de la actividad de copia](copy-activity-schema-and-type-mapping.md)
- [Tolerancia a errores de la actividad de copia](copy-activity-fault-tolerance.md)

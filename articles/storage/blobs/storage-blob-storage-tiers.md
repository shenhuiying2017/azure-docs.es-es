---
title: "Almacenamiento de archivo, esporádico y frecuente para blobs | Microsoft Docs"
description: "Almacenamiento de archivo, esporádico y frecuente para cuentas de Azure Blog Storage."
services: storage
documentationcenter: 
author: michaelhauss
manager: vamshik
editor: tysonn
ms.assetid: eb33ed4f-1b17-4fd6-82e2-8d5372800eef
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/05/2017
ms.author: mihauss
ms.openlocfilehash: 544b11d74a926fe62b8ceca51570ce9d2ee7e6e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-blob-storage-hot-cool-and-archive-preview-storage-tiers"></a>Azure Blob Storage: niveles de almacenamiento de archivo, esporádico y frecuente (versión preliminar)

## <a name="overview"></a>Información general

Azure Storage ofrece tres niveles de almacenamiento para el almacenamiento de objetos Blob, por lo que puede almacenar los datos de forma más rentable en función de cómo los use. La **capa de almacenamiento de acceso frecuente** de Azure está optimizada para almacenar datos que se consultan con frecuencia. El **nivel de almacenamiento de acceso esporádico** de Azure está optimizado para almacenar datos a los que se accede con poca frecuencia y al menos durante un mes. El [nivel de almacenamiento de archivo (versión preliminar)](https://azure.microsoft.com/blog/announcing-the-public-preview-of-azure-archive-blob-storage-and-blob-level-tiering) está optimizado para almacenar datos a los que raramente se tiene acceso y se almacenan durante al menos seis meses con requisitos de latencia flexible (del orden de horas). El nivel de almacenamiento de *archivo* solo puede usarse en los blobs y no en toda la cuenta de almacenamiento. Los datos de la capa de almacenamiento de acceso esporádico toleran una disponibilidad ligeramente inferior, pero aun así requieren una gran durabilidad y un tiempo de acceso y unas características de rendimiento similares a las de los datos de acceso frecuente. En el caso de los datos de acceso esporádico y de archivo, un Acuerdo de Nivel de Servicio con una disponibilidad ligeramente inferior y unos costos de acceso mayores se puede aceptar a cambio de unos costos de almacenamiento mucho menores.

Hoy en día, los datos almacenados en la nube está creciendo a un ritmo exponencial. Para administrar los costos de las crecientes necesidades de almacenamiento, resulta útil organizar los datos en función de atributos como la frecuencia de acceso y el período de retención planeado. Los datos almacenados en la nube pueden ser diferentes en cuanto a la forma en que se generan, se procesan y se accede a ellos a lo largo de su duración. A algunos datos se accede y se modifican activamente a lo largo de su duración. A algunos datos se accede con frecuencia al principio de su duración, mientras que el acceso cae drásticamente a medida que envejecen los datos. Algunos datos permanecen inactivos en la nube y, una vez almacenados, no se accede a ellos prácticamente nunca.

Cada uno de los escenarios de acceso a los datos se beneficia de un nivel de almacenamiento diferenciado que se optimiza para un patrón de acceso concreto. Con los niveles de almacenamiento de archivo, esporádico y frecuente, Azure Blob Storage satisface la necesidad de que haya niveles de almacenamiento diferenciados con modelos de fijación de precios independientes.

## <a name="blob-storage-accounts"></a>Cuentas de Almacenamiento de blobs

**Cuentas de Almacenamiento de blobs** son cuentas de almacenamiento especiales para almacenar los datos no estructurados como blobs (objetos) en Almacenamiento de Azure. Con las cuentas de Blob Storage, ahora puede elegir entre los niveles de almacenamiento esporádico y frecuente en el nivel de cuenta, y los niveles de almacenamiento espóradico, frecuente y de archivo en el nivel de blobs, según los patrones de acceso. Almacene con el menor costo de almacenamiento aquellos datos esporádicos a los que rara vez acceda, con un menor costo de almacenamiento aquellos datos esporádicos con acceso menos frecuente que los datos frecuentes y almacene con el menor costo de todos los datos frecuentes a los que se acceda con más frecuencia. Las cuentas de Blob Storage son similares a las de almacenamiento de uso general existentes y comparten las excelentes características de rendimiento, escalabilidad, disponibilidad y durabilidad que se usan en la actualidad, incluida la coherencia total de la API para blobs en bloques y blobs en anexos.

> [!NOTE]
> Las cuentas de Almacenamiento de blobs solo admiten blobs en bloques y en anexos, pero no blobs en páginas.

Las cuentas de Blob Storage exponen el atributo **Access Tier**, que permite especificar la capa de almacenamiento como **Frecuente** o **Esporádico**, en función de los datos almacenados en la cuenta. Si hay un cambio en el patrón de uso de los datos, también se puede cambiar de capa de almacenamiento en cualquier momento. El nivel de archivo (versión preliminar) solo puede aplicarse en el nivel de blob.

> [!NOTE]
> El cambio de la capa de almacenamiento puede conllevar cargos adicionales. Para más información, consulte la sección [Precios y facturación](#pricing-and-billing).

### <a name="hot-access-tier"></a>Nivel de acceso frecuente

Entre los ejemplos de escenarios de uso de la capa de almacenamiento de acceso frecuente se incluyen:

* Los datos que están en uso o a los que se espera que se acceda (se lean y se escriban) con frecuencia.
* Los datos que se almacenan provisionalmente para el procesamiento y la eventual migración a la capa de almacenamiento esporádico.

### <a name="cool-access-tier"></a>Nivel de acceso esporádico

Entre los ejemplos de escenarios de uso de la capa de almacenamiento de acceso esporádico se incluyen:

* Conjuntos de datos de copia de seguridad y recuperación ante desastres a corto plazo.
* Contenido multimedia antiguo que no se ve con frecuencia, pero que se espera que esté disponible de inmediato cuando se acceda a él.
* Conjuntos de datos grandes que se deben almacenar de forma rentable mientras se recopilan más datos para el procesamiento futuro. (*Por ejemplo,*, almacenamiento a largo plazo de datos científicos, datos de telemetría sin procesar de una instalación de fabricación)

### <a name="archive-access-tier-preview"></a>Nivel de acceso de archivo (versión preliminar)

[El almacenamiento de archivo](https://azure.microsoft.com/blog/announcing-the-public-preview-of-azure-archive-blob-storage-and-blob-level-tiering) tiene el menor costo de almacenamiento y los costos de recuperación de datos más alto en comparación con el almacenamiento frecuente y esporádico.

Mientras un blob está en un almacenamiento de archivo, no se puede leer, copiar, sobrescribir ni modificar. Tampoco puede tomar instantáneas de un blob en almacenamiento de archivo. Sin embargo, puede usar las operaciones existentes para eliminar, enumerar, obtener propiedades y metadatos del blob o cambiar el nivel del blob. Para leer datos de un almacenamiento de archivo, primero debe cambiar el nivel del blob a acceso frecuente o esporádico. Este proceso se conoce como rehidratación y puede tardar hasta 15 horas en completarse para blobs de menos de 50 GB. El tiempo adicional necesario para blobs mayores varía según el límite de rendimiento del blob.

Durante la rehidratación, puede comprobar la propiedad de blob "archive status" para confirmar si el nivel ha cambiado. El estado indica "rehydrate-pending-to-hot" (rehidratación pendiente para acceso frecuente) o "rehydrate-pending-to-cool" (rehidratación pendiente para acceso esporádico), según el nivel de destino. Al finalizar, se quita la propiedad de blob "archive status" (estado de archivo) y la propiedad de blob "access tier" (nivel de acceso) refleja el nivel de acceso frecuente o esporádico.  

Entre los ejemplos de escenarios de uso del nivel de almacenamiento de archivo se incluyen:

* Conjuntos de datos de recuperación ante desastres, archivo y copia de seguridad a largo plazo
* Datos originales (sin procesar) que deben conservarse, incluso después de que se han procesado en un formato útil final. (*Por ejemplo,*, archivos multimedia sin procesar tras la transcodificación en otros formatos)
* Datos de cumplimiento y archivado que se deben almacenar durante un largo período de tiempo y a los que casi nunca se accede. (*Por ejemplo*, grabaciones de cámaras de seguridad, radiografías o resonancias antiguas en centros sanitarios, grabaciones de audio y transcripciones de llamadas de clientes para servicios financieros)

### <a name="recommendations"></a>Recomendaciones

Para más información sobre las cuentas de almacenamiento, consulte [Acerca de las cuentas de almacenamiento de Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) .

Para las aplicaciones que requieren silo el almacenamiento blobs en bloques o en anexos, se recomienda utilizar cuentas de Almacenamiento de blobs, con el fin de aprovechar el modelo de precios diferenciado de almacenamiento en niveles. Sin embargo, sabemos que esto puede no ser posible en determinadas circunstancias, donde la mejor solución es usar cuentas de almacenamiento de uso general, como:

* Necesita usar tablas, colas o archivos, y desea que los blobs se almacenen en la misma cuenta de almacenamiento. Tenga en cuenta que la única ventaja técnica de almacenarlos en la misma cuenta es que tienen las mismas claves compartidas.

* Necesita usar el modelo de implementación clásico. Las cuentas de Almacenamiento de blobs solo están disponibles a través del modelo de implementación de Azure Resource Manager.

* Necesita usar blobs en páginas. Las cuentas de Almacenamiento de blobs no admiten los blobs en páginas. Por lo general, se recomienda usar blobs en bloques, salvo que se necesiten específicamente blobs en páginas.

* Usa una versión de la [API de REST de servicios de almacenamiento](https://msdn.microsoft.com/library/azure/dd894041.aspx) anterior al 14-02-2014 o una biblioteca de cliente con una versión inferior a la 4.x y no puede actualizar la aplicación.

> [!NOTE]
> En la actualidad, las cuentas de Blob Storage se admiten en todas las regiones de Azure.
 

## <a name="blob-level-tiering-feature-preview"></a>Característica de asignación de niveles en blobs (versión preliminar)

La asignación de niveles en blobs ahora le permiten cambiar el nivel de los datos en los objetos mediante una única operación denominada [Set Blob Tier](/rest/api/storageservices/set-blob-tier) (establecimiento de nivel de blob). Puede cambiar fácilmente el nivel de acceso de un blob entre el nivel de archivo, esporádico o frecuente a medida que cambien los patrones de uso, sin tener que mover los datos entre las cuentas. Todos los cambios de nivel se realizan de inmediato, excepto cuando se rehidrata un blob desde el archivo. Los blobs en los tres niveles de almacenamiento pueden coexistir dentro de la misma cuenta. Los blobs que no tienen un nivel asignado explícitamente heredan la de la configuración del nivel de acceso de la cuenta.

Para usar estas características en la versión preliminar, siga las instrucciones que aparecen en el [anuncio en el blog de la asignación de niveles en blobs y en archivo de Azure](https://azure.microsoft.com/blog/announcing-the-public-preview-of-azure-archive-blob-storage-and-blob-level-tiering).

A continuación se enumeran algunas restricciones que se aplican durante la versión preliminar para la asignación de capas en el nivel de blob:

* Solo admiten el almacenamiento de archivo las cuentas nuevas de Blob Storage creadas en el este de EE. UU. 2 después de inscribirse correctamente en la versión preliminar.

* Solo admiten la asignación de capas en el nivel de blob las cuentas nuevas de Blob Storage creadas en regiones públicas después de inscribirse correctamente en la versión preliminar.

* La asignación de niveles en blobs y el almacenamiento de archivo solo se admiten en el almacenamiento [LRS] (../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#locally-redundant-storage). [GRS](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#geo-redundant-storage) y [RA-GRS](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#read-access-geo-redundant-storage) se admitirán en el futuro.

* No puede cambiar el nivel de un blob con instantáneas.

* No puede copiar ni crear instantáneas de un blob en almacenamiento de archivo.

## <a name="comparison-of-the-storage-tiers"></a>Comparación de los niveles de almacenamiento

En la tabla siguiente se muestra una comparación de los niveles de almacenamiento frecuente y esporádico. El nivel de almacenamiento de archivo en blobs está en la versión preliminar, por lo que no se dispone de ningún Acuerdo de Nivel de servicio para ello.

| | **Nivel de almacenamiento frecuente** | **Nivel de almacenamiento esporádico** |
| ---- | ----- | ----- |
| **Disponibilidad** | 99,9 % | 99% |
| **Disponibilidad** <br> **(lecturas de RA-GRS)**| 99,99% | 99,9 % |
| **Cargos de uso** | Mayores costos de almacenamiento, menores costos de acceso y transacciones | Menores costos de almacenamiento, mayores costos de acceso y transacciones |
| **Tamaño mínimo de objeto** | N/D | N/D |
| **Duración mínima del almacenamiento** | N/D | N/D |
| **Latency** <br> **(Tiempo hasta el primer byte)** | milisegundos | milisegundos |
| **Objetivos de escalabilidad y rendimiento** | Igual que las cuentas de almacenamiento de uso general | Igual que las cuentas de almacenamiento de uso general |

> [!NOTE]
> Las cuentas de Almacenamiento de blobs admiten los mismos objetivos de rendimiento y escalabilidad que las cuentas de almacenamiento de uso general. Consulte [Azure Storage Scalability and Performance Targets](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para obtener más información.


## <a name="pricing-and-billing"></a>Precios y facturación
Las cuentas de Blob Storage usan un modelo de fijación de precios para el almacenamiento de blobs que se basa en el nivel de almacenamiento. Si se usa una cuenta de Almacenamiento de blobs, se aplicarán las siguientes consideraciones de facturación:

* **Costos de almacenamiento**: además de la cantidad de datos almacenados, el costo varía en función de la capa de almacenamiento. El costo por gigabyte es inferior en la capa de almacenamiento de acceso esporádico que en la de acceso frecuente.

* **Costos de acceso a datos**: en el nivel de almacenamiento esporádico se cobra un cargo de acceso a datos por gigabyte para lecturas y escrituras.

* **Costos de transacciones**: en ambas capas hay un cargo por transacción. Sin embargo, el costo por transacción en la capa de almacenamiento de acceso esporádico es mayor que en la de acceso frecuente.

* **Costos de transferencia de datos de replicación geográfica**: solo se aplica a las cuentas con replicación geográfica configurada, incluidas GRS y RA-GRS. La transferencia de datos de replicación geográfica incurre en un cargo por gigabyte.

* **Costos de transferencia de datos salientes**: las transferencias de datos salientes (los datos que se transfieren fuera de una región de Azure) conllevan un cargo por el uso del ancho de banda por gigabyte, lo que es coherente con las cuentas de almacenamiento de uso general.

* **Cambio del nivel de almacenamiento**: el cambio del nivel de almacenamiento de esporádico a frecuente conlleva un cargo igual a la lectura de todos los datos existentes en la cuenta de almacenamiento en cada transición. Por otro lado, el cambio del nivel de almacenamiento de frecuente a esporádico es gratuito.

> [!NOTE]
> Para más información sobre el modelo de precios de las cuentas de Blob Storage, consulte la página [Azure Storage Pricing](https://azure.microsoft.com/pricing/details/storage/). Para más información sobre los cargos por la transferencia de datos salientes, consulte la página [Detalles de precios de ancho de banda](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="quickstart"></a>Guía de inicio rápido

En esta sección se muestran los siguientes escenarios con Azure Portal:

* Cómo crear una cuenta de Almacenamiento de blobs.
* Cómo administrar una cuenta de Almacenamiento de blobs.

No se puede establecer el nivel de acceso de archivo en los ejemplos siguientes porque esta configuración se aplica a la cuenta de almacenamiento completa. El nivel de acceso de archivo solo puede establecerse en un blob concreto.

### <a name="create-a-blob-storage-account-using-the-azure-portal"></a>Creación de una cuenta de Almacenamiento de blobs mediante el Portal de Azure

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. En el menú del concentrador, seleccione **Nuevo** > **Datos y almacenamiento** > **Cuenta de almacenamiento**.

3. Escriba un nombre para la cuenta de almacenamiento.
   
    Este nombre debe ser único a nivel global; formará parte de la dirección URL utilizada para acceder a los objetos de la cuenta de almacenamiento.  

4. Seleccione **Resource Manager** como modelo de implementación.
   
    El almacenamiento en capas solo puede utilizarse con cuentas de almacenamiento de Resource Manager, que es el modelo de implementación recomendado para nuevos recursos. Para más información, consulte [Información general de Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).  

5. En la lista desplegable Account Kind (Tipo de cuenta), seleccione **Almacenamiento de blobs**.
   
    Aquí seleccionará el tipo de cuenta de almacenamiento. El almacenamiento en capas no está disponible en cuentas de almacenamiento de uso general, solo está disponible en cuentas de Almacenamiento de blobs.     
   
    Tenga en cuenta que, cuando se selecciona esta opción, se establece el nivel de rendimiento Estándar. El almacenamiento en capas no está disponible con el nivel de rendimiento Premium.

6. Seleccione la opción de replicación de la cuenta de almacenamiento: **LRS**, **GRS** o **RA-GRS**. El valor predeterminado es **RA-GRS**.
   
    LRS = almacenamiento con redundancia local; GRS = almacenamiento con redundancia geográfica (dos regiones); RA-GRS = almacenamiento con redundancia geográfica con acceso de lectura (dos regiones con acceso de lectura a la región secundaria).
   
    Para más información sobre las opciones de replicación del Almacenamiento de Azure, consulte [Replicación de Almacenamiento de Azure](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

7. Seleccione la capa de almacenamiento que más se ajuste a sus necesidades: en **Nivel de acceso** seleccione **Esporádico** o **Frecuente**. El valor predeterminado es **Frecuente**. 

8. Seleccione la suscripción en la que desea crear la nueva cuenta de almacenamiento.

9. Especifique un nuevo grupo de recursos o seleccione un grupo de recursos existente. Para más información sobre los grupos de recursos, consulte [Información general de Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

10. Seleccione la región para la cuenta de almacenamiento.

11. Haga clic en **Crear** para crear la cuenta de almacenamiento.

### <a name="change-the-storage-tier-of-a-blob-storage-account-using-the-azure-portal"></a>Cambio del nivel de almacenamiento de una cuenta de Almacenamiento de blobs mediante el Portal de Azure

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. Para desplazarse a su cuenta de almacenamiento, seleccione Todos los recursos y, después, seleccione la cuenta de almacenamiento.

3. En la hoja Configuración, haga clic en **Configuración** para ver o cambiar la configuración de la cuenta.

4. Seleccione el nivel de almacenamiento que más se ajuste a sus necesidades: en **Nivel de acceso** seleccione **Esporádico** o **Frecuente**.

5. Haga clic en Guardar en la parte superior de la hoja.

> [!NOTE]
> El cambio de la capa de almacenamiento puede conllevar cargos adicionales. Para más información, consulte la sección [Precios y facturación](#pricing-and-billing).


## <a name="evaluating-and-migrating-to-blob-storage-accounts"></a>Evaluación y migración a cuentas de Almacenamiento de blobs
El objetivo de esta sección es ayudar a los usuarios a realizar una transición sin problemas al uso de cuentas de Almacenamiento de blobs. Hay dos escenarios de usuario:

* Tiene una cuenta de almacenamiento de uso general y desea evaluar un cambio en una cuenta de Almacenamiento de blobs con la capa de almacenamiento correcta.
* Ha decidido usar una cuenta de Almacenamiento de blobs o ya tiene una y desea evaluar si debe utilizar la capa de almacenamiento de acceso frecuente o esporádico.

En ambos casos, la primera regla de negocio es calcular el costo de almacenar y acceder a los datos almacenados en una cuenta de Almacenamiento de blobs y compararlo con los costos actuales.

## <a name="evaluating-blob-storage-account-tiers"></a>Evaluación de los niveles de la cuenta de Almacenamiento de blobs

Para calcular el costo de almacenar y acceder a los datos almacenados en una cuenta de Blob Storage, es preciso evaluar el patrón de uso existente o realizar una aproximación al patrón de uso esperado. En general, será preciso conocer:

* El consumo del almacenamiento: ¿cuántos datos se almacenan y cómo cambia esta cifra mensualmente?

* El patrón de acceso de almacenamiento: ¿cuántos datos se leen y se escriben en la cuenta (incluidos los datos nuevos)? ¿Cuántas transacciones se utilizan para el acceso a datos y qué tipos de transacciones son?

## <a name="monitoring-existing-storage-accounts"></a>Supervisión de las cuentas de almacenamiento existentes

Para supervisar las cuentas de almacenamiento existentes y recopilar estos datos, se puede usar Análisis de almacenamiento de Azure, que realiza el registro y proporciona los datos de métricas de una cuenta de almacenamiento. Análisis de almacenamiento puede almacenar métricas que incluyen las estadísticas de transacciones agregadas y los datos de capacidad de las solicitudes al servicio de Almacenamiento de blobs tanto de cuentas de almacenamiento de uso general como cuentas de Almacenamiento de blobs. Estos datos se almacenan en tablas conocidas de la misma cuenta de almacenamiento.

Para más información, consulte [Acerca de las métricas de Storage Analytics](https://msdn.microsoft.com/library/azure/hh343258.aspx) y [Esquema de tabla de métricas de Storage Analytics](https://msdn.microsoft.com/library/azure/hh343264.aspx)

> [!NOTE]
> Las cuentas de Almacenamiento de blobs exponen el punto de conexión de servicio de tabla solo para almacenar y acceder a los datos de métricas de dicha cuenta.

Para supervisar el consumo de almacenamiento de información del servicio Blob Storage, es preciso que habilite las métricas de capacidad.
Con esta opción habilitada, se registran diariamente los datos de capacidad de Blob service de una cuenta de almacenamiento y se registran como una entrada de tabla que se escribe en la tabla *$MetricsCapacityBlob* dentro de la misma cuenta de almacenamiento.

Para supervisar el patrón de acceso de datos del servicio Blob Storage, es preciso que habilite las métricas de transacción horarias a nivel de API. Con esta opción habilitada, las transacciones por API se agregan cada hora y se registran como una entrada de tabla que se escribe en la tabla *$MetricsHourPrimaryTransactionsBlob* dentro de la misma cuenta de almacenamiento. La tabla *$MetricsHourSecondaryTransactionsBlob* registra las transacciones en el punto de conexión secundario cuando se usan cuentas de almacenamiento de RA-GRS.

> [!NOTE]
> Si tiene una cuenta de almacenamiento de uso general en la que ha almacenado blobs de páginas y discos de máquinas virtuales junto con datos de blobs en bloques y anexos, este proceso de estimación no se podrá aplicar, ya que no hay forma de distinguir las métricas de capacidad y de transacción en función del tipo de blob solo para los blobs en bloques y anexos que se pueden migrar a una cuenta de Blob Storage.

Para obtener una buena aproximación a su patrón de acceso y consumo de datos, se recomienda elegir un período de retención de métricas que sea representativo de su uso habitual y extrapolarlo. Una opción es conservar los datos de las métricas de siete días y recopilar los datos todas las semanas, con el fin de realizar el análisis al final del mes. Otra opción es conservar los datos de las métricas de los últimos 30 días y recopilar y analizar los datos al final del período de 30 días.

Para más información acerca de cómo habilitar, recopilar y visualizar datos de métricas, consulte [Habilitar las métricas de Azure Storage y ver sus datos](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> El almacenamiento, acceso y descarga de datos de análisis también se cobra como los datos de usuario normales.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Utilización de métricas de uso para estimar costos

### <a name="storage-costs"></a>Costos de almacenamiento

La entrada más reciente de la tabla de métricas de capacidad *$MetricsCapacityBlob* con la clave de fila *'data'* muestra la capacidad de almacenamiento que han consumido los datos del usuario. La entrada más reciente de la tabla de métricas de capacidad *$MetricsCapacityBlob* con la clave de fila *'analytics'* muestra la capacidad de almacenamiento que han consumido los registros de análisis.

Posteriormente, esta capacidad total consumida por los datos del usuario y los registros de análisis (si está habilitado) se puede utilizar para calcular el costo del almacenamiento de datos en la cuenta de almacenamiento. Este mismo método también se puede utilizar para calcular los costos de almacenamiento de los blobs de bloques y anexos en las cuentas de uso general.

### <a name="transaction-costs"></a>Costos de transacciones

La suma de *'TotalBillableRequests'*, en todas las entradas de una API en la tabla de métricas de transacciones indica el número total de transacciones de dicha API determinada. *Por ejemplo,*, el número total de transacciones *'GetBlob'* en un período dado se puede calcular mediante la suma del número total de solicitudes facturables para todas las entradas con la clave de fila *'user;GetBlob'*.

Para calcular los costos de transacción de las cuentas de Blob Storage, es preciso que desglose las transacciones en tres grupos, ya que tienen precios diferentes.

* Transacciones de escritura como *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *'SnapshotBlob'* y *'CopyBlob'*.
* Transacciones de eliminación como *'DeleteBlob'* y *'DeleteContainer'*.
* Las restantes transacciones.

Para calcular los costos de transacción de las cuentas de almacenamiento de uso general, es preciso agregar todas las transacciones, independientemente de la operación o API.

### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Costos de transferencia de datos de acceso y de replicación geográfica

Aunque el análisis del almacenamiento no proporciona la cantidad de datos leídos de una cuenta de almacenamiento y escritos en ella, se puede realizar un cálculo aproximado mediante el examen de la tabla de métricas de transacciones. La suma de *'TotalIngress'* en todas las entradas de una API de la tabla de métricas de transacciones indica la cantidad total de datos de entrada, en bytes, de dicha API concreta. De igual modo, la suma de *'TotalEgress'* indica la cantidad total de datos de salida, en bytes.

Para calcular los costos de acceso a los datos en las cuentas de Blob Storage, es preciso que desglose las transacciones en dos grupos. 

* La cantidad de datos que se recuperan de la cuenta de almacenamiento se puede calcular examinando la suma de *'TotalEgress'* principalmente en las operaciones *'GetBlob'* y *'CopyBlob'*.

* La cantidad de datos que se escriben en la cuenta de almacenamiento se puede calcular examinando la suma de *'TotalIngress'* principalmente en las operaciones *'PutBlob'*, *'PutBlock'*, *'CopyBlob'* y *'AppendBlock'*.

El costo de transferencia de datos de replicación geográfica para cuentas de Blob Storage también puede calcularse mediante la valoración de la cantidad de datos escritos cuando se usa una cuenta de almacenamiento GRS o RA-GRS.

> [!NOTE]
> Para ver un ejemplo más detallado sobre cómo calcular los costos del uso de la capa de almacenamiento frecuente o esporádico, eche un vistazo a la pregunta *'¿Qué son los niveles de acceso frecuente y esporádico y cómo se puede determinar el que debe usarse?'* de la página [Precios de Almacenamiento de Azure](https://azure.microsoft.com/pricing/details/storage/).
 
## <a name="migrating-existing-data"></a>Migración de datos existentes

Las cuentas de Almacenamiento de blobs son especiales para almacenar solo blobs y anexar blobs. Las cuentas de almacenamiento de uso general existentes, que permiten almacenar tablas, colas, archivos y discos, así como blobs, no se pueden convertir en cuentas de Blob Storage. Para utilizar los niveles de almacenamiento, debe crear nuevas cuentas de Blob Storage y migrar los datos existentes a las cuentas recién creadas.

Puede utilizar los métodos siguientes para migrar los datos existentes a cuentas de Blob Storage desde un dispositivos de almacenamiento local, desde proveedores de almacenamiento en la nube de terceros o desde sus cuentas existentes de almacenamiento de uso general en Azure:

### <a name="azcopy"></a>AzCopy

AzCopy es una utilidad de línea de comandos de Windows diseñada para la copia de datos de alto rendimiento a y desde Almacenamiento de Azure. AzCopy se puede usar para copiar datos en una cuenta de Almacenamiento de blobs desde cuentas de almacenamiento de uso general existentes o para cargar datos desde un sistema de dispositivos de almacenamiento local en una cuenta de Almacenamiento de blobs.

Para más información, consulte [Transferencia de datos con la utilidad en línea de comandos AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Data Movement Library

La biblioteca de movimiento de datos de Almacenamiento de Azure para .NET se basa en el marco de movimiento de datos principal que se utiliza con AzCopy. La biblioteca está diseñada para operaciones de transferencia de datos de alto rendimiento, confiables y fáciles similares a AzCopy. Esto permite sacar todo el partido a las características que proporciona AzCopy en su aplicación nativa sin tener que ejecutar y supervisar instancias externas de AzCopy.

Para más información, consulte [Azure Storage Data Movement Library for .Net](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>API de REST o biblioteca de cliente

Puede crear una aplicación personalizada para migrar sus datos a una cuenta de Almacenamiento de blobs con una de las bibliotecas de cliente de Azure o la API de REST de servicios de almacenamiento de Azure. Almacenamiento de Azure proporciona bibliotecas de cliente enriquecidas para varios lenguajes y aplicaciones como .NET, Java, C++, Node.JS, PHP, Ruby y Python. Las bibliotecas de cliente ofrecen capacidades avanzadas, como lógica de reintentos, registro y cargas paralelas. También se puede desarrollar directamente en la API de REST, a la que se puede llamar con cualquier lenguaje que genere solicitudes HTTP/HTTPS.

Para más información, consulte [Introducción al Almacenamiento de blobs de Azure mediante .NET](storage-dotnet-how-to-use-blobs.md).

> [!NOTE]
> BLOB cifrado mediante el cifrado de cliente para almacenar metadatos relacionados con el cifrado almacenados con el blob. Es absolutamente crítico que cualquier mecanismo de copia se asegure de que los metadatos de blob, y especialmente los metadatos relacionados con el cifrado, se preserven. Si copia los blobs sin estos metadatos, su contenido no puede volver a recuperarse. Para más información acerca de los metadatos relacionados con el cifrado, consulte [Cifrado del lado de cliente y Azure Key Vault para Microsoft Azure Storage](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
 
## <a name="faq"></a>P+F

1. **¿Siguen disponibles las cuentas de almacenamiento existentes?**
   
    Sí, las cuentas de almacenamiento existentes siguen estando disponibles y no se han cambiado sus precios ni sus funciones.  No tienen la capacidad de elegir una capa de almacenamiento y no tendrán funcionalidades de organización en capas en el futuro.

2. **¿Por qué y cuándo conviene empezar a usar cuentas de Almacenamiento de blobs?**
   
    Las cuentas de Almacenamiento de blobs están especializadas en el almacenamiento de blobs y nos permiten introducir nuevas características centradas en los blobs. Es más, las cuentas de Almacenamiento de blobs son el método recomendado para el almacenamiento de blobs, ya que se introducirán funcionalidades futuras como el almacenamiento jerárquico y la organización en niveles basadas en este tipo de cuenta. Sin embargo, cada usuario debe decidir en qué momento desea realizar la migración en función de sus requisitos empresariales.

3. **¿Se puede convertir una cuenta de almacenamiento existente en una cuenta de Almacenamiento de blogs?**
   
    No. Una cuenta de Blob Storage es un tipo de cuenta de almacenamiento diferente y es preciso crearla desde cero y migrar los datos, como ya se ha explicado.

4. **¿Se pueden almacenar objetos en dos capas de almacenamiento en la misma cuenta?**
   
    El atributo *'Access Tier'* que indica el valor del nivel de almacenamiento se establece en las cuentas y se aplica a todos los objetos de dicha cuenta. Sin embargo, la característica de asignación de niveles en blobs (versión preliminar) le permite establecer los niveles de acceso en blobs específicos, y reemplazará la configuración del nivel de acceso en la cuenta. 

5. **¿Se puede cambiar la capa de almacenamiento de una cuenta de Almacenamiento de blobs?**
   
    Sí. Puede cambiar el nivel de almacenamiento estableciendo el atributo *'Access Tier'* en la cuenta de almacenamiento. El cambio del nivel de almacenamiento se aplica a todos los objetos almacenados en la cuenta. El cambio del nivel de almacenamiento de frecuente a esporádico no supone ningún gasto, mientras que el cambio de esporádico a frecuente tiene un costo por GB por la lectura de todos los datos de la cuenta.

6. **¿Con qué frecuencia se puede cambiar la capa de almacenamiento de una cuenta de Almacenamiento de blobs?**
   
    Aunque no hay ningún límite en la frecuencia con que se puede cambiar el nivel de almacenamiento, tenga en cuenta que el cambio del nivel de almacenamiento de esporádico a frecuente supone unos gastos significativos. Se recomienda no cambiar la capa de almacenamiento con frecuencia.

7. **¿Los blobs en el nivel de almacenamiento esporádico se comportan de forma diferente a los del nivel de almacenamiento frecuente?**
   
    Los blobs de la capa de almacenamiento frecuente tienen la misma latencia que los de las cuentas de almacenamiento de uso general. Los blobs de la capa de almacenamiento esporádico tienen una latencia similar (en milisegundos) a los de las cuentas de almacenamiento de uso general.
   
    Los blobs del nivel de almacenamiento esporádico tienen un Acuerdo de Nivel de Servicio (SLA) de disponibilidad ligeramente inferior a los almacenados en el nivel de almacenamiento frecuente. Para más información, consulte [Acuerdo de Nivel de Servicio para Almacenamiento](https://azure.microsoft.com/support/legal/sla/storage).

8. **¿Puedo almacenar blobs en páginas y discos de máquinas virtuales en las cuentas de Almacenamiento de blobs?**
   
    Las cuentas de Almacenamiento de blobs solo admiten blobs en bloques y en anexos, pero no blobs en páginas. Los discos de máquinas virtuales de Azure están respaldados por blobs en páginas y, por tanto, no se pueden utilizar cuentas de Almacenamiento de blobs para almacenar discos de máquinas virtuales. Sin embargo, es posible almacenar copias de seguridad de los discos de máquinas virtuales como blobs en bloques en una cuenta de Almacenamiento de blobs.

9. **¿Es preciso cambiar las aplicaciones existentes para usar las cuentas de Blosb Storage?**
   
    Las cuentas de almacenamiento de blobs tienen una coherencia del 100 % con la API con las cuentas de almacenamiento de uso general para blobs en bloques y en anexos. Si la aplicación usa blobs en bloques o blobs en anexos y el usuario utiliza la versión 2014-02-14 de la [API de REST de servicios de almacenamiento](https://msdn.microsoft.com/library/azure/dd894041.aspx), u otra versión superior, la aplicación debería funcionar. Si se utiliza una versión anterior del protocolo, es preciso actualizar la aplicación para que use la nueva versión, con el fin de poder trabajar sin problemas con ambos tipos de cuentas de almacenamiento. En general, siempre se recomienda utilizar la versión más reciente, independientemente del tipo de cuenta de almacenamiento que se use.

10. **¿Hay un cambio en la experiencia del usuario?**
    
    Las cuentas de Almacenamiento de blobs son muy similares a una cuenta de almacenamiento de uso general para almacenar y anexar blobs, y admite todas las características claves de Almacenamiento de Azure, incluidos un gran rendimiento, durabilidad y disponibilidad, escalabilidad y seguridad. Aparte de las características y restricciones concretas de las cuentas de Almacenamiento de blobs y sus capas de almacenamiento ya indicadas, el resto es igual.

## <a name="next-steps"></a>Pasos siguientes

### <a name="evaluate-blob-storage-accounts"></a>Evaluación de cuentas de Almacenamiento de blobs

[Comprobación de la disponibilidad de las cuentas de Almacenamiento de blobs por región](https://azure.microsoft.com/regions/#services)

[Evaluación del uso de las cuentas de almacenamiento actuales mediante la habilitación de las métricas de Almacenamiento de Azure](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Comprobación de los precios de almacenamiento de blobs por región](https://azure.microsoft.com/pricing/details/storage/)

[Detalles de precios de Transferencias de datos](https://azure.microsoft.com/pricing/details/data-transfers/)

### <a name="start-using-blob-storage-accounts"></a>Empezar a utilizar cuentas de Almacenamiento de blobs

[Introducción al Almacenamiento de blobs de Azure mediante .NET](storage-dotnet-how-to-use-blobs.md)

[Movimiento de datos hacia y desde Almacenamiento de Azure](../common/storage-moving-data.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Transferencia de datos con la utilidad en línea de comandos AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Examen y exploración de cuentas de almacenamiento](http://storageexplorer.com/)
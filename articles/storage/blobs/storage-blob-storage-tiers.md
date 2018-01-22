---
title: "Almacenamiento de archivo, esporádico y frecuente para blobs | Microsoft Docs"
description: "Almacenamiento de archivo, esporádico y frecuente para cuentas de Azure Storage."
services: storage
documentationcenter: 
author: kuhussai
manager: jwillis
editor: 
ms.assetid: eb33ed4f-1b17-4fd6-82e2-8d5372800eef
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/11/2017
ms.author: kuhussai
ms.openlocfilehash: be84f68a044a73673e991f04c7fe36a7787b9c3c
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/17/2018
---
# <a name="azure-blob-storage-hot-cool-and-archive-storage-tiers"></a>Azure Blob Storage: niveles de almacenamiento de archivo, esporádico y frecuente

## <a name="overview"></a>Información general

Azure Storage ofrece tres niveles de almacenamiento para el almacenamiento de objetos Blob, así puede almacenar los datos de la manera más rentable en función de cómo los use. La **capa de almacenamiento de acceso frecuente** de Azure está optimizada para almacenar datos que se consultan con frecuencia. El **nivel de almacenamiento de acceso esporádico** de Azure está optimizado para almacenar datos a los que se accede con poca frecuencia y al menos durante 30 días. El **nivel de almacenamiento de archivo** de Azure está optimizado para almacenar datos a los que raramente se accede. Estos datos se almacenan durante al menos 180 días con requisitos de latencia flexible (del orden de horas). El nivel de almacenamiento de archivo solo está disponible en el nivel de blob, no en el de cuenta de almacenamiento. Los datos de la capa de almacenamiento de acceso esporádico toleran una disponibilidad ligeramente inferior, pero aun así requieren una gran durabilidad y un tiempo de acceso y unas características de rendimiento similares a las de los datos de acceso frecuente. En el caso de los datos de acceso esporádico, un Acuerdo de Nivel de Servicio con una disponibilidad ligeramente inferior y unos costos de acceso mayores, en comparación con los datos de acceso frecuente, es aceptable a cambio de unos costos de almacenamiento menores. El almacenamiento de archivo se realiza sin conexión y ofrece los menores costos de almacenamiento, pero los mayores costos de acceso.

Hoy en día, los datos almacenados en la nube está creciendo a un ritmo exponencial. Para administrar los costos de las crecientes necesidades de almacenamiento, resulta útil organizar los datos en función de atributos como frecuencia de acceso y el período de retención planeado para optimizar costos. Los datos almacenados en la nube pueden ser diferentes en cuanto a la forma en que se generan, se procesan y se accede a ellos a lo largo de su duración. A algunos datos se accede y se modifican activamente a lo largo de su duración. A algunos datos se accede con frecuencia al principio de su duración, mientras que el acceso cae drásticamente a medida que envejecen los datos. Algunos datos permanecen inactivos en la nube y, una vez almacenados, no se accede a ellos prácticamente nunca.

Cada uno de los escenarios de acceso a los datos se beneficia de una capa de almacenamiento diferenciada que se optimiza para un patrón de acceso concreto. Con los niveles de almacenamiento de archivo, esporádico y frecuente, Azure Blob Storage satisface la necesidad de que haya niveles de almacenamiento diferenciados con modelos de precios independientes.

## <a name="storage-accounts-that-support-tiering"></a>Cuentas de almacenamiento que admiten niveles

Solo puede disponer los datos de almacenamiento de objetos en niveles de acceso frecuente, esporádico o de archivo en cuentas de Blob Storage o de General Purpose v2 (GPv2). Las cuentas de General Purpose v1 (GPv1) no admiten niveles. Sin embargo, los clientes pueden convertir fácilmente sus cuentas de GPv1 o de Blob Storage existentes a cuentas de GPv2 mediante un simple clic en Azure Portal. GPv2 proporciona una nueva estructura de precios para blobs, archivos y colas, junto con acceso a otras diversas características de almacenamiento nuevas. Además, en adelante algunas nuevas características y reducciones de precio solo se ofrecerán en cuentas de GPv2. Por lo tanto, los clientes deben valorar el uso de las cuentas de GPv2, pero solo deben usarlas después de revisar el precio de todos los servicios, dado que algunas cargas de trabajo pueden ser más caras en GPv2 que en GPv1. Para más información, consulte [Opciones de la cuenta de Azure Storage](../common/storage-account-options.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Las cuentas de Blob Storage y de GPv2 exponen el atributo **access tier** en el nivel de cuenta, que le permite especificar el nivel de almacenamiento predeterminado como frecuente o esporádico para cualquier blob de la cuenta de almacenamiento que no tenga el nivel establecido en el nivel de objeto. En el caso de objetos con el nivel establecido en el nivel de objeto, el nivel de cuenta no se aplica. El nivel de archivo solo puede aplicarse en el nivel de objeto. Puede cambiar entre estos niveles de almacenamiento en cualquier momento.

## <a name="hot-access-tier"></a>Nivel de acceso frecuente

El almacenamiento frecuente tiene mayores costos que el esporádico o el de archivo, pero menores costos de acceso. Entre los ejemplos de escenarios de uso de la capa de almacenamiento de acceso frecuente se incluyen:

* Los datos que están en uso o a los que se espera que se acceda (se lean y se escriban) con frecuencia.
* Los datos que se almacenan provisionalmente para el procesamiento y la eventual migración a la capa de almacenamiento esporádico.

## <a name="cool-access-tier"></a>Nivel de acceso esporádico

El nivel de almacenamiento de acceso esporádico tiene menores costos de almacenamiento y mayores costos de acceso, en comparación con el almacenamiento de acceso frecuente. Este nivel está diseñado para datos que van a permanecer en el nivel de acceso esporádico durante al menos 30 días. Entre los ejemplos de escenarios de uso de la capa de almacenamiento de acceso esporádico se incluyen:

* Conjuntos de datos de copia de seguridad y recuperación ante desastres a corto plazo.
* Contenido multimedia antiguo que no se ve con frecuencia, pero que se espera que esté disponible de inmediato cuando se acceda a él.
* Conjuntos de datos grandes que se deben almacenar de forma rentable mientras se recopilan más datos para el procesamiento futuro. (*Por ejemplo,*, almacenamiento a largo plazo de datos científicos, datos de telemetría sin procesar de una instalación de fabricación)

## <a name="archive-access-tier"></a>Nivel de acceso de archivo

El almacenamiento de archivo tiene el menor costo de almacenamiento y los costos de recuperación de datos más altos en comparación con el almacenamiento frecuente y esporádico. Este nivel está destinado a los datos que pueden tolerar varias horas de latencia de recuperación y que permanecerán en el nivel de archivo durante un mínimo de 180 días.

Mientras un blob esté en almacenamiento de archivo, estará sin conexión y no se podrá leer (excepto los metadatos, que están en línea y disponibles), copiar, sobrescribir o modificar. Tampoco puede tomar instantáneas de un blob en almacenamiento de archivo. Sin embargo, puede usar las operaciones existentes para eliminar, enumerar, obtener propiedades y metadatos del blob o cambiar el nivel del blob.

Entre los ejemplos de escenarios de uso del nivel de almacenamiento de archivo se incluyen:

* Conjuntos de datos de recuperación ante desastres, archivo y copia de seguridad a largo plazo
* Datos originales (sin procesar) que deben conservarse, incluso después de que se han procesado en un formato útil final. (*Por ejemplo,*, archivos multimedia sin procesar tras la transcodificación en otros formatos)
* Datos de cumplimiento y archivado que se deben almacenar durante un largo período de tiempo y a los que casi nunca se accede. (*Por ejemplo*, grabaciones de cámaras de seguridad, radiografías o resonancias antiguas en centros sanitarios, grabaciones de audio y transcripciones de llamadas de clientes para servicios financieros)

### <a name="blob-rehydration"></a>Rehidratación de blobs
Para leer datos de un almacenamiento de archivo, primero debe cambiar el nivel del blob a acceso frecuente o esporádico. Este proceso se conoce como rehidratación y puede tardar en completarse hasta 15 horas. Para un rendimiento óptimo, es muy recomendable el uso de tamaños de blob grandes. La rehidratación de varios blobs pequeños a la vez puede suponer tiempo adicional.

Durante la rehidratación, puede comprobar la propiedad de blob **Archive Status** para confirmar si el nivel ha cambiado. El estado indica "rehydrate-pending-to-hot" (rehidratación pendiente para acceso frecuente) o "rehydrate-pending-to-cool" (rehidratación pendiente para acceso esporádico), según el nivel de destino. Al finalizar, se quita la propiedad de blob archive status y la propiedad de blob **Access Tier** refleja el nuevo nivel de acceso frecuente o esporádico.  

## <a name="blob-level-tiering"></a>Almacenamiento por niveles de blob

El almacenamiento por niveles de blob permite cambiar el nivel de los datos en el nivel de objeto mediante una única operación denominada [Set Blob Tier](/rest/api/storageservices/set-blob-tier) (establecimiento de nivel de blob). Puede cambiar fácilmente el nivel de acceso de un blob entre el nivel de archivo, esporádico o frecuente a medida que cambien los patrones de uso, sin tener que mover los datos entre las cuentas. Todos los cambios de nivel se realizan de inmediato, excepto cuando se rehidrata un blob desde el nivel de archivo, lo que puede llevar varias horas. La hora del último cambio de nivel de blob se expone a través de la propiedad de blob **access tier change time**. Si un blob está en el nivel de archivo, no se puede sobrescribir y, por consiguiente, la carga del mismo blob no se permite en este escenario. Puede sobrescribir un blob de forma esporádica o permanente y, en este caso, el nuevo blob hereda el nivel del blob anterior que se ha sobrescrito.

Los blobs en los tres niveles de almacenamiento pueden coexistir dentro de la misma cuenta. Los blobs que no tienen un nivel asignado explícitamente infieren el nivel de la configuración del nivel de acceso de la cuenta. Si el nivel de acceso se infiere desde la cuenta, verá que la propiedad de blob **access tier inferred** se establece en "true" y que la propiedad de blob **access tier** coincide con el nivel de cuenta. En Azure Portal, la propiedad inferida del nivel de acceso se muestra con el nivel de acceso de blob (por ejemplo, Permanente (inferido) o Esporádico (inferido)).

> [!NOTE]
> El almacenamiento de archivo y el almacenamiento por niveles de blob solo admiten blobs en bloques. Tampoco se puede cambiar el nivel de un blob en bloques que tiene instantáneas.

### <a name="blob-level-tiering-billing"></a>Facturación del almacenamiento por niveles de blob

Cuando un blob se mueve a un nivel más esporádico (frecuente -> esporádico, frecuente -> archivo o esporádico -> archivo), la operación se factura como una operación de escritura en el nivel de destino, y se aplican los cargos de la operación de escritura (por 10 000) y de la escritura de datos (por GB) del nivel de destino. Si un blob se mueve a un nivel menos esporádico (archivo -> esporádico, archivo -> frecuente -> o esporádico -> frecuente), la operación se factura como lectura desde el nivel de origen, y se aplican los cargos de la operación de lectura (por 10 000) y de la recuperación de datos (por GB) del nivel de origen.

Si cambia el nivel de acceso de la cuenta de frecuente a esporádico, solo se le cobrarán las operaciones de escritura (por 10 000) de todos los blobs sin un nivel establecido en las cuentas de GPv2. En las cuentas de Blob Storage no se realiza ningún cargo por esto. Si cambia el nivel de acceso de su cuenta de Blob Storage o de GPv2 de esporádico a frecuente, se le cobran tanto las operaciones de lectura (por 10 000) como las de recuperación de datos (por GB). También podrían aplicarse cargos por la eliminación temprana de algún blob que se haya trasladado desde el nivel de acceso esporádico o de archivo.

### <a name="cool-and-archive-early-deletion-effective-february-1-2018"></a>Eliminación temprana de niveles de acceso esporádico y de archivo (en vigor el 1 de febrero de 2018)

Además de los cargos por GB y por mes, los blobs que se hayan incorporado al nivel de acceso esporádico (solo cuentas de GPv2) están sujetos a un período de eliminación temprana de este nivel de 30 días, y cualquier blob que se haya incorporado al nivel de acceso de archivo está sujeto a un período de eliminación temprana de este nivel de 180 días. Este cargo se prorratea. Por ejemplo, si un blob se mueve al nivel de acceso de archivo y, a continuación, se elimina o se mueve al nivel de acceso frecuente al cabo de 45 días, se le cobrará una cuota de eliminación temprana equivalente a 135 (180 menos 45) días a partir del almacenamiento de ese blob en el nivel de acceso de archivo.

## <a name="comparison-of-the-storage-tiers"></a>Comparación de los niveles de almacenamiento

En la tabla siguiente se muestra una comparación de los niveles de almacenamiento de acceso frecuente, esporádico y de archivo.

| | **Nivel de almacenamiento frecuente** | **Nivel de almacenamiento esporádico** | **Nivel de almacenamiento de archivo**
| ---- | ----- | ----- | ----- |
| **Disponibilidad** | 99,9 % | 99% | N/D |
| **Disponibilidad** <br> **(lecturas de RA-GRS)**| 99,99% | 99,9 % | N/D |
| **Cargos de uso** | Mayores costos de almacenamiento, menores costos de acceso y transacciones | Menores costos de almacenamiento, mayores costos de acceso y transacciones | Menores costos de almacenamiento, mayores costos de acceso y transacciones |
| **Tamaño mínimo de objeto** | N/D | N/D | N/D |
| **Duración mínima del almacenamiento** | N/D | 30 días (solo GPv2) | 180 días
| **Latency** <br> **(Tiempo hasta el primer byte)** | milisegundos | milisegundos | Menos de 15 horas
| **Objetivos de escalabilidad y rendimiento** | Igual que las cuentas de almacenamiento de uso general | Igual que las cuentas de almacenamiento de uso general | Igual que las cuentas de almacenamiento de uso general |

> [!NOTE]
> Las cuentas de Blob Storage admiten los mismos objetivos de rendimiento y escalabilidad que las cuentas de almacenamiento de uso general. Consulte [Azure Storage Scalability and Performance Targets](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para obtener más información.

## <a name="quickstart-scenarios"></a>Escenarios de inicio rápido

En esta sección se muestran los siguientes escenarios mediante Azure Portal:

* Cómo cambiar el nivel de acceso predeterminado en una cuenta de GPv2 o de Blob Storage
* Cómo cambiar el nivel de acceso de un blob en una cuenta de GPv2 o de Blob Storage

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Cambio del nivel de acceso predeterminado en una cuenta de GPv2 o de Blob Storage

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).

2. Para desplazarse a su cuenta de almacenamiento, seleccione Todos los recursos y, después, seleccione la cuenta de almacenamiento.

3. En la hoja Configuración, haga clic en **Configuración** para ver o cambiar la configuración de la cuenta.

4. Seleccione la capa de almacenamiento que más se ajuste a sus necesidades: en **Nivel de acceso** seleccione **Esporádico** o **Frecuente**.

5. Haga clic en Guardar en la parte superior de la hoja.

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Cambio del nivel de acceso de un blob en una cuenta de GPv2 o de Blob Storage

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).

2. Para ir a un blob en su cuenta de almacenamiento, seleccione Todos los recursos, seleccione la cuenta de almacenamiento y el contenedor y, finalmente, seleccione el blob.

3. En la hoja de propiedades del blob, haga clic en el menú desplegable **Nivel de acceso** para seleccionar el nivel de almacenamiento **Frecuente**, **Esporádico** o **Archivo**.

5. Haga clic en Guardar en la parte superior de la hoja.

## <a name="faq"></a>Preguntas más frecuentes

**¿Debo usar cuentas de Blob Storage o de GPv2 si quiero almacenar mis datos por niveles?**

Se recomienda usar cuentas de GPv2 en lugar de las de Blob Storage para el almacenamiento por niveles. GPv2 admite todas las características de las cuentas de Blob Storage y muchas más. Los precios entre Blob Storage y GPv2 son casi idénticos, pero algunas características nuevas y reducciones de precios solo están disponibles en cuentas de GPv2. Las cuentas de GPv1 no admiten niveles.

La estructura de precios entre las cuentas de GPv1 y GPv2 es diferente y los clientes deben evaluar con cuidado ambas antes de decidirse a usar las cuentas de GPv2. Puede convertir fácilmente una cuenta existente de Blob Storage o de GPv1 en GPv2 con un simple clic. Para más información, consulte [Opciones de la cuenta de Azure Storage](../common/storage-account-options.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

**¿Puedo almacenar objetos en los tres niveles de almacenamiento de acceso (frecuente, esporádico y archivo) en la misma cuenta?**

Sí. El atributo **access tier** establecido en el nivel de cuenta es el nivel predeterminado que se aplica a todos los objetos de esa cuenta sin un nivel establecido explícito. Sin embargo, el almacenamiento por niveles de blob permite establecer el nivel de acceso en el nivel de objeto, independientemente de cuál sea el valor del nivel de acceso en la cuenta. Los blobs de cualquiera de los tres niveles de almacenamiento (permanente, esporádico o archivo) pueden existir en la misma cuenta.

**¿Puedo cambiar el nivel de almacenamiento predeterminado de la cuenta de Blob Storage o de GPv2?**

Sí, puede cambiar el nivel de almacenamiento predeterminado mediante la definición del atributo **access tier** en la cuenta de almacenamiento. El cambio del nivel de almacenamiento se aplica a todos los objetos almacenados en la cuenta que no tengan un nivel explícito establecido. Al cambiar el nivel de almacenamiento de frecuente a esporádico se generan cargos de operaciones de escritura (por 10 000) en todos los blobs sin un nivel establecido en cuentas de GPv2, y al cambiar de esporádico a frecuente se generan cargos de operaciones de lectura (por 10 000) y de recuperación de datos (por GB) en todos los blobs en cuentas de Blob Storage y de GPv2.

**¿Puedo establecer el nivel de acceso de cuenta predeterminado en archivo?**

Nº Los niveles de almacenamiento de acceso frecuente y esporádico se pueden establecer como nivel de acceso de cuenta predeterminado. El nivel de acceso de archivo solo puede establecerse en el nivel de objeto.

**¿En qué regiones están disponibles los niveles de almacenamiento de acceso frecuente, esporádico y de archivo?**

Los niveles de almacenamiento de acceso frecuente y esporádico, junto con el almacenamiento por niveles de blob, están disponibles en todas las regiones. El almacenamiento de archivo solo estará disponible inicialmente en regiones seleccionadas. Para obtener una lista completa, consulte [Productos disponibles por región](https://azure.microsoft.com/regions/services/).

**¿Los blobs en el nivel de almacenamiento esporádico se comportan de forma diferente a los del nivel de almacenamiento frecuente?**

Los blobs del nivel de almacenamiento de acceso frecuente tienen la misma latencia que los blobs de las cuentas de GPv1, GPv2 y Blob Storage. Los blobs del nivel de almacenamiento de acceso esporádico tiene una latencia similar (en milisegundos) a la de los blobs de las cuentas de GPv1, GPv2 y Blob Storage. Los blobs del nivel de almacenamiento de archivo presentan varias horas de latencia en cuentas de GPv1, GPv2 y Blob Storage.

Los blobs del nivel de almacenamiento esporádico tienen un Acuerdo de Nivel de Servicio (SLA) de disponibilidad ligeramente inferior a los almacenados en el nivel de almacenamiento frecuente. Para más información, consulte [Acuerdo de Nivel de Servicio para Almacenamiento](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**¿Las operaciones entre los niveles de acceso frecuente, esporádico y de archivo son las mismas?**

Sí. Todas las operaciones entre los niveles de acceso frecuente y esporádico son coherentes al 100 %. Todas las operaciones de archivo válidas, lo que incluye la eliminación, la enumeración y la obtención de propiedades y metadatos de blobs, junto con el nivel de blob establecido, son 100 % coherentes con el acceso frecuente y esporádico. Un blob no se puede leer ni modificar mientras se encuentre en el nivel de archivo.

**Al rehidratar un blob desde el nivel de acceso de archivo al frecuente o esporádico, ¿cómo se sabe cuándo la rehidratación está completa?**

Durante la rehidratación, se puede usar la operación de obtención de propiedades de blob para sondear el atributo **Archive Status** y confirmar cuándo ha finalizado el cambio de nivel. El estado indica "rehydrate-pending-to-hot" (rehidratación pendiente para acceso frecuente) o "rehydrate-pending-to-cool" (rehidratación pendiente para acceso esporádico), según el nivel de destino. Al finalizar, se quita la propiedad de blob archive status y la propiedad de blob **Access Tier** refleja el nuevo nivel de acceso frecuente o esporádico.  

**Después de establecer el nivel de acceso de un blob, ¿cuándo comienzan a facturarme con la tarifa adecuada?**

Cada blob siempre se factura según el nivel indicado por la propiedad de blob **access tier**. Al establecer un nuevo nivel de acceso en un blob, la propiedad **access tier** refleja inmediatamente el nuevo nivel de acceso en todas las transiciones, excepto al rehidratar un blob de archivo a frecuente o esporádico, lo que puede llevar varias horas. En este caso, se le sigue facturando según las tarifas de archivo hasta que la rehidratación finalice, momento en el cual **access tier** refleja el nuevo nivel. Solo entonces, se le factura según la nueva tarifa de acceso frecuente o esporádico.

**¿Cómo determino si me aplicarán un cargo por eliminación temprana al eliminar o trasladar un blob del nivel de acceso esporádico o de archivo?**

Los blobs que se eliminan o se trasladan del nivel de acceso esporádico (solo cuentas de GPv2) o de archivo antes de 30 días y 180 días, respectivamente, generarán un cargo por eliminación temprana prorrateado (en vigor el 1 de febrero de 2018). Para determinar cuánto tiempo un blob ha estado en el nivel de acceso esporádico o de archivo, compruebe la propiedad del blob **access tier change time** que proporciona una marca del último cambio del nivel de acceso. Para más información, consulte [Eliminación temprana de niveles de acceso esporádico y de archivo](#cool-and-archive-early-deletion).

**¿Qué herramientas y SDK de Azure admiten almacenamiento por niveles de blob y almacenamiento de archivo?**

Las herramientas de Azure Portal, PowerShell y la CLI, y las bibliotecas de cliente de .NET, Java, Python y Node.js admiten todas almacenamiento por niveles de blob y almacenamiento de archivo.  

**¿Cuántos datos se pueden almacenar en los niveles de acceso frecuente, esporádico y de archivo?**

El almacenamiento de datos, junto con otros límites, se establece en el nivel de cuenta y no por nivel de almacenamiento. Por lo tanto, puede decidir usar todo el límite en un nivel o entre los tres niveles. Consulte [Azure Storage Scalability and Performance Targets](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para obtener más información.

## <a name="next-steps"></a>pasos siguientes

### <a name="evaluate-hot-cool-and-archive-in-gpv2-blob-storage-accounts"></a>Evaluación de los niveles de acceso frecuente, esporádico y de archivo en cuentas de GPv2 y de Blob Storage

[Comprobación de la disponibilidad de los niveles de acceso frecuente, esporádico o de archivo por región](https://azure.microsoft.com/regions/#services)

[Evaluación del uso de las cuentas de almacenamiento actuales mediante la habilitación de las métricas de Azure Storage](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Comprobación de los precios de los niveles de acceso frecuente, esporádico y de archivo en cuentas de Blob Storage y de GPv2 por región](https://azure.microsoft.com/pricing/details/storage/)

[Detalles de precios de Transferencias de datos](https://azure.microsoft.com/pricing/details/data-transfers/)

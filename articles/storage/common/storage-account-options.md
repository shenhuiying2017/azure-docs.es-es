---
title: Opciones de cuenta de Azure Storage | Microsoft Docs
description: "Descripción de las opciones de uso de Azure Storage."
services: storage
author: jirwin
manager: jwillis
ms.service: storage
ms.workload: storage
ms.topic: get-started-article
ms.date: 01/17/2018
ms.author: jirwin
ms.openlocfilehash: aa8704b9991dde639b50a5b6c9a27ad5cd08ad02
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="azure-storage-account-options"></a>Opciones de la cuenta de Azure Storage

## <a name="overview"></a>Información general
Azure Storage ofrece tres opciones de cuenta distintas, con diferentes precios y características. Tenga en cuenta estas diferencias antes de crear una cuenta de almacenamiento para determinar la opción más adecuada para sus aplicaciones. Las tres opciones de las cuentas de almacenamiento son:

* Cuentas de **uso general v2 (GPv2)** 
* Cuentas de **uso general v1 (GPv1)**
* Cuentas de **almacenamiento de blobs**

Los distintos tipos de cuenta se describen con más detalle en la siguiente sección:

## <a name="storage-account-options"></a>Opciones de la cuenta de almacenamiento

### <a name="general-purpose-v2"></a>Uso general v2

Las cuentas de uso general v2 son cuentas de almacenamiento que admiten las características mas recientes de blobs, archivos, colas y tablas. Las cuentas de uso general v2 admiten todas las API y características que se admiten tanto en las cuentas de uso general v1 como en las cuentas de almacenamiento de blobs. También admiten las mismas características de durabilidad, disponibilidad, escalabilidad y rendimiento en dichos tipos de cuenta. Las cuentas de uso general v2 se han diseñado para proporcionar los precios más bajos por gigabyte y los precios por transacción más competitivos del sector.

Para actualizar una cuenta de uso general v1 a una cuenta de uso general v2, utilice PowerShell o la CLI de Azure. 

En el caso de los blobs en bloques de una cuenta de almacenamiento de GPv2, puede elegir entre los niveles de almacenamiento de acceso esporádico y frecuente en el nivel de cuenta, o los niveles de acceso esporádico, frecuente y de archivo en el nivel de blob, según los patrones de acceso. Para optimizar los costos, almacene los datos a los que se accede con frecuencia, con poca frecuencia o casi nunca en los niveles de almacenamiento frecuente, esporádico y de archivo, respectivamente. 

Las cuentas de almacenamiento de uso general v2 exponen el atributo **Access Tier** (Nivel de acceso) en el nivel de la cuenta, que especifica el nivel predeterminado de la cuenta de almacenamiento como **Frecuente** o **Esporádico**. El nivel de cuenta de almacenamiento predeterminado se aplica a todos los blobs que no tienen un nivel explícito establecido en el nivel de blob. Si hay un cambio en el patrón de uso de los datos, también se puede cambiar de capa de almacenamiento en cualquier momento. El atributo **archive tier** solo se puede aplicar en el nivel de blob.

> [!NOTE]
> El cambio de la capa de almacenamiento puede conllevar cargos adicionales. Para más información, consulte la sección [Precios y facturación](#pricing-and-billing).
>
> Microsoft recomienda utilizar cuentas de almacenamiento de uso general v2, en lugar de cuentas de almacenamiento de blobs, en la mayoría de los escenarios.

### <a name="upgrade-a-storage-account-to-gpv2"></a>Actualización de una cuenta de almacenamiento a GPv2

Los usuarios pueden actualizar una cuenta de uso general v1 a una cuenta de uso general v2 en cualquier momento mediante PowerShell o la CLI de Azure. Este cambio no se puede revertir, y no se permite ningún otro cambio.

#### <a name="upgrade-with-powershell"></a>Actualización con PowerShell

Para actualizar una cuenta de uso general v1 a una cuenta de uso general v2 mediante PowerShell, primero hay que actualizar PowerShell, con el fin de usar la versión más reciente del módulo **AzureRm.Storage**. Para obtener información acerca de cómo instalar PowerShell, consulte [Instalación y configuración de Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Luego, llame al siguiente comando para actualizar la cuenta, pero sustituya el nombre de su grupo de recursos y la cuenta de almacenamiento:

```powershell
Set-AzureRmStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2
```

#### <a name="upgrade-with-azure-cli"></a>Actualización con la CLI de Azure

Para actualizar una cuenta de uso general v1 a una cuenta de uso general v2 mediante la CLI de Azure, en primer lugar instale la versión más reciente de la CLI de Azure. Para obtener información acerca de cómo instalar la CLI, consulte [Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) (Instalación de la CLI de Azure 2.0). Luego, llame al siguiente comando para actualizar la cuenta, pero sustituya el nombre de su grupo de recursos y la cuenta de almacenamiento:

```cli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2
```` 

### <a name="general-purpose-v1"></a>Uso general v1

Las cuentas de uso general v1 proporcionan acceso a todos los servicios de Azure Storage, pero puede que no incluyan las características más recientes o que no tengan los precios más bajos por gigabyte. Por ejemplo, en las cuentas de uso general v1 no se admiten el almacenamiento de acceso esporádico ni el de archivo. Los precios de las transacciones de las cuentas de uso general v1 son más bajos, por lo que las cargas de trabajo con actividad alta o velocidades de lectura elevadas pueden beneficiarse de este tipo de cuenta.

Las cuentas de uso general v1 son el tipo más antiguo y el único que se puede usar con el modelo de implementación clásica. 

### <a name="blob-storage-accounts"></a>Cuentas de Almacenamiento de blobs

Las cuentas de almacenamiento de blobs admiten las mismas características de blob en bloques que las de uso general v2, pero tienen la limitación de que solo admiten blobs en bloques. Los precios son muy similares a los de las cuentas de uso general v2. Los clientes deben examinar las diferencias de precio entre las cuentas de almacenamiento de blobs y las de uso general v2, y considerar la posibilidad de actualizar a estas ultimas. Esta actualización no se puede deshacer.

La posibilidad de actualizar cuentas de almacenamiento de blobs a cuentas de uso general v2 estará disponible próximamente.

> [!NOTE]
> Las cuentas de Almacenamiento de blobs solo admiten blobs en bloques y en anexos, pero no blobs en páginas.

## <a name="recommendations"></a>Recomendaciones

Para más información acerca de las cuentas de almacenamiento, consulte [Acerca de las cuentas de almacenamiento de Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

En el caso de aplicaciones que requieren solo el almacenamiento de blobs en bloques o en anexos, se recomienda el uso de cuentas de almacenamiento de uso general v2, con el fin de aprovechar el modelo de precios diferenciado de almacenamiento por niveles. Sin embargo, puede que desee usar cuentas de uso general v1 en escenarios concretos, como:

* Aún necesita usar el modelo de implementación clásica. Las cuentas de Almacenamiento de blobs solo están disponibles a través del modelo de implementación de Azure Resource Manager.

* Usa volúmenes elevados de transacciones o ancho de banda de replicación geográfica, que cuestan más en cuentas de uso general v2 y de almacenamiento de blobs que en cuentas de uso general v1, y no tiene almacenamiento suficiente para beneficiarse de los costos más bajos de almacenamiento por GB.

* Usa una versión de la [API de REST de servicios de almacenamiento](https://msdn.microsoft.com/library/azure/dd894041.aspx) anterior al 14-02-2014 o una biblioteca de cliente con una versión inferior a la 4.x y no puede actualizar la aplicación.

> [!NOTE]
> En la actualidad, las cuentas de Blob Storage se admiten en todas las regiones de Azure.

## <a name="pricing-and-billing"></a>Precios y facturación
Todas las cuentas de Blob Storage usan un modelo de precios para el almacenamiento de blobs basado en el nivel de cada blob. Al usar una cuenta de almacenamiento, se aplican las siguientes consideraciones de facturación:

* **Costos de almacenamiento**: además de la cantidad de datos almacenados, el costo varía en función de la capa de almacenamiento. El costo por gigabyte disminuye a medida que el nivel es más esporádico.

* **Data access costs**: los gastos de acceso a los datos aumentan a medida que el nivel es más esporádico. En el nivel de almacenamiento esporádico y de archivo se cobra un cargo de acceso a datos por gigabyte por las operaciones de lectura.

* **Costos de transacciones**: hay un cargo por transacción para todos los niveles, que aumenta a medida que el nivel es más esporádico.

* **Costos de transferencia de datos de replicación geográfica**: este cargo solo se aplica a las cuentas con replicación geográfica configurada, incluidas GRS y RA-GRS. La transferencia de datos de replicación geográfica incurre en un cargo por gigabyte.

* **Costos de transferencia de datos salientes**: las transferencias de datos salientes (los datos que se transfieren fuera de una región de Azure) conllevan un cargo por el uso del ancho de banda por gigabyte, lo que es coherente con las cuentas de almacenamiento de uso general.

* **Cambio del nivel de almacenamiento**: el cambio de la capa de almacenamiento de la cuenta pasa de esporádico a frecuente conlleva un cargo igual a la lectura de todos los datos existentes en la cuenta de almacenamiento. Sin embargo, cambiar el nivel de almacenamiento de la cuenta de frecuente a esporádico genera un cargo igual que escribir todos los datos en el nivel de acceso esporádico (solo cuentas de GPv2).

> [!NOTE]
> Para más información acerca del modelo de precios de las cuentas de Blob Storage, consulte la página [Precios de Azure Storage](https://azure.microsoft.com/pricing/details/storage/). Para más información acercas los cargos por la transferencia de datos salientes, consulte la página [Detalles de precios de ancho de banda](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="quickstart-scenarios"></a>Escenarios de inicio rápido

En esta sección se muestran los siguientes escenarios mediante Azure Portal:

* Creación de una cuenta de almacenamiento de GPv2
* Conversión de una cuenta de uso general v1 o de almacenamiento de blobs a una cuenta de almacenamiento de uso general v2.
* Establecimiento de la cuenta y el nivel de blob en una cuenta de almacenamiento de GPv2

No se puede establecer el nivel de acceso en archivo en los ejemplos siguientes porque este valor se aplica a toda la cuenta de almacenamiento. El nivel de acceso de archivo solo puede establecerse en un blob concreto.

### <a name="create-a-gpv2-storage-account-using-the-azure-portal"></a>Creación de una cuenta de almacenamiento de GPv2 mediante Azure Portal

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).

2. En el menú del concentrador, seleccione **Crear un recurso** > **Datos y almacenamiento** > **Cuenta de almacenamiento**.

3. Escriba un nombre para la cuenta de almacenamiento.

    Este nombre debe ser único a nivel global; formará parte de la dirección URL utilizada para acceder a los objetos de la cuenta de almacenamiento.  

4. Seleccione **Resource Manager** como modelo de implementación.

    El almacenamiento en capas solo puede utilizarse con cuentas de almacenamiento de Resource Manager, que es el modelo de implementación recomendado para los recursos nuevos. Para más información, consulte [Información general de Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).  

5. En la lista desplegable **Tipo de cuenta**, seleccione **Uso general v2**.

    Cuando se selecciona GPv2, el nivel de rendimiento se establece en Estándar. El almacenamiento en capas no está disponible con el nivel de rendimiento Premium.

6. Seleccione la opción de replicación de la cuenta de almacenamiento: **LRS**, **ZRS**, **GRS** o **RA-GRS**. El valor predeterminado es **RA-GRS**.

    LRS = almacenamiento con redundancia local; ZRS = almacenamiento con redundancia de zona; GRS = almacenamiento con redundancia geográfica (dos regiones); RA-GRS = almacenamiento con redundancia geográfica con acceso de lectura (dos regiones con acceso de lectura a la segunda).

    Para obtener más información sobre las opciones de replicación de Azure Storage, consulte [Replicación de Azure Storage](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

7. Seleccione la capa de almacenamiento que más se ajuste a sus necesidades: en **Nivel de acceso** seleccione **Esporádico** o **Frecuente**. El valor predeterminado es **Frecuente**.

8. Seleccione la suscripción en la que desea crear la nueva cuenta de almacenamiento.

9. Especifique un nuevo grupo de recursos o seleccione un grupo de recursos existente. Para más información sobre los grupos de recursos, consulte [Información general de Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

10. Seleccione la región para la cuenta de almacenamiento.

11. Haga clic en **Crear** para crear la cuenta de almacenamiento.

### <a name="convert-a-gpv1-account-to-a-gpv2-storage-account-using-the-azure-portal"></a>Conversión de una cuenta GPv1 a una cuenta de almacenamiento GPv2 mediante Azure Portal

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).

2. Para ir a su cuenta de almacenamiento, seleccione **Todos los recursos** y, después, seleccione la cuenta de almacenamiento.

3. En la sección Configuración, haga clic en **Configuración**.

4. En **Tipo de cuenta**, haga clic en **Actualizar**.

5. En **Confirmar actualización**, escriba el nombre de la cuenta. 

5. Haga clic en Actualizar en la parte inferior de la hoja.

### <a name="change-the-storage-tier-of-a-gpv2-storage-account-using-the-azure-portal"></a>Cambio del nivel de almacenamiento de una cuenta de almacenamiento de GPv2 mediante Azure Portal

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).

2. Para ir a su cuenta de almacenamiento, seleccione **Todos los recursos** y, después, seleccione la cuenta de almacenamiento.

3. En la hoja Configuración, haga clic en **Configuración** para ver o cambiar la configuración de la cuenta.

4. Seleccione la capa de almacenamiento que más se ajuste a sus necesidades: en **Nivel de acceso** seleccione **Esporádico** o **Frecuente**.

5. Haga clic en Guardar en la parte superior de la hoja.

### <a name="change-the-storage-tier-of-a-blob-using-the-azure-portal"></a>Cambio del nivel de almacenamiento de un blob mediante Azure Portal

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).

2. Para ir a un blob en su cuenta de almacenamiento, seleccione **Todos los recursos**, seleccione la cuenta de almacenamiento y el contenedor y, finalmente, seleccione el blob.

3. En la hoja de propiedades del blob, haga clic en el menú desplegable **Nivel de acceso** para seleccionar el nivel de almacenamiento **Frecuente**, **Esporádico** o **Archivo**.

5. Haga clic en Guardar en la parte superior de la hoja.

> [!NOTE]
> El cambio de la capa de almacenamiento puede conllevar cargos adicionales. Para más información, consulte la sección [Precios y facturación](#pricing-and-billing).


## <a name="evaluating-and-migrating-to-gpv2-storage-accounts"></a>Evaluación y migración a cuentas de almacenamiento de GPv2
El objetivo de esta sección es ayudar a los usuarios a realizar una transición sin problemas al uso de cuentas de almacenamiento de GPv2 (por oposición a GPv1). Hay dos escenarios de usuario:

* Tiene una cuenta de almacenamiento existente de GPv1 y quiere evaluar un cambio a una cuenta de almacenamiento de GPv2 con el nivel de almacenamiento correcto.
* Ha decidido usar una cuenta de almacenamiento de GPv2 o ya tiene una y quiere evaluar si debe usar el nivel de almacenamiento de acceso frecuente o esporádico.

En ambos casos, la principal prioridad es estimar el costo de almacenamiento y acceso a los datos almacenados en una cuenta de almacenamiento de GPv2 y compararlo con los costos actuales.

## <a name="evaluating-gpv2-storage-account-tiers"></a>Evaluación de los niveles de la cuenta de almacenamiento de GPv2

Para calcular el costo de almacenamiento y acceso a los datos almacenados en una cuenta de almacenamiento de GPv2, es preciso evaluar el patrón de uso existente o realizar una aproximación al patrón de uso esperado. En general, será preciso conocer:

* El consumo del almacenamiento: ¿cuántos datos se almacenan y cómo cambia esta cifra mensualmente?

* El patrón de acceso de almacenamiento: ¿cuántos datos se leen y se escriben en la cuenta (incluidos los datos nuevos)? ¿Cuántas transacciones se utilizan para el acceso a datos y qué tipos de transacciones son?

## <a name="monitoring-existing-storage-accounts"></a>Supervisión de las cuentas de almacenamiento existentes

Para supervisar las cuentas de almacenamiento existentes y recopilar estos datos, se puede usar Azure Storage Analytics, que realiza el registro y proporciona los datos de métricas de una cuenta de almacenamiento. Storage Analytics puede almacenar métricas que incluyen estadísticas de las transacciones agregadas y datos de capacidad de las solicitudes realizadas al servicio de almacenamiento de los tipos de cuenta de uso general v1, uso general v2 y almacenamiento de blobs. Estos datos se almacenan en tablas conocidas de la misma cuenta de almacenamiento.

Para más información, consulte [About Storage Analytics Metrics](https://msdn.microsoft.com/library/azure/hh343258.aspx) (Acerca de las métricas de Azure Storage Analytics) y [Storage Analytics Metrics Table Schema](https://msdn.microsoft.com/library/azure/hh343264.aspx) (Esquema de tabla de métricas de Storage Analytics)

> [!NOTE]
> Las cuentas de almacenamiento de blobs exponen el punto de conexión de Table service solo para el almacenamiento y el acceso a los datos de métricas de dicha cuenta. Las cuentas de almacenamiento con redundancia de zona (ZRS) admiten la recopilación de datos de métricas, mientras que las cuentas de almacenamiento clásicas de ZRS no lo admiten. Para más información acerca de ZRS, consulte [Almacenamiento con redundancia de zona](storage-redundancy.md#zone-redundant-storage). 

Para supervisar el consumo de almacenamiento de la cuenta de almacenamiento de blobs, es preciso habilitar las métricas de capacidad.
Si está habilitado, los datos de capacidad se registran a diario en el servicio de blobs de una cuenta de almacenamiento y se registran como una entrada de tabla que se escribe en la tabla *$MetricsCapacityBlob*, en la misma cuenta de almacenamiento.

Para supervisar los patrones de acceso a los datos de la cuenta de almacenamiento de blobs, es preciso habilitar la métrica de transacción horaria desde la API. Con la métrica de transacción horaria habilitada, las transacciones por API se agregan cada hora y se registran como una entrada de tabla que se escribe en la tabla *$MetricsHourPrimaryTransactionsBlob* dentro de la misma cuenta de almacenamiento. La tabla *$MetricsHourSecondaryTransactionsBlob* registra las transacciones en el punto de conexión secundario cuando se usan cuentas de almacenamiento de RA-GRS.

> [!NOTE]
> Si tiene una cuenta de almacenamiento de uso general en la que ha almacenado blobs en páginas y discos de máquina virtual, o colas, archivos o tablas, junto con datos de blobs en bloques y en anexos, este proceso de estimación no se puede aplicar. Los datos de capacidad no diferencian los blobs en bloques de otros tipos, por lo que no proporcionan los datos de capacidad de los restantes tipos de datos. Si usa estos tipos, una metodología alternativa consiste en examinar las cantidades en la factura más reciente.

Para obtener una buena aproximación a su patrón de acceso y consumo de datos, se recomienda elegir un período de retención de métricas que sea representativo de su uso habitual y extrapolarlo. Una opción es conservar los datos de las métricas siete días y recopilar los datos todas las semanas, con el fin de realizar el análisis al final del mes. Otra opción es conservar los datos de las métricas de los últimos 30 días y recopilar y analizar los datos al final del período de 30 días.

Para más información acerca de cómo habilitar, recopilar y visualizar datos de métricas, consulte [Habilitación de métricas de Azure Storage y visualización de sus datos](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> El almacenamiento, acceso y descarga de datos de análisis también se cobra como los datos de usuario normales.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Utilización de métricas de uso para estimar costos

#### <a name="storage-costs"></a>Costos de almacenamiento

La entrada más reciente de la tabla de métricas de capacidad *$MetricsCapacityBlob* con la clave de fila *'data'* muestra la capacidad de almacenamiento que han consumido los datos del usuario. La entrada más reciente de la tabla de métricas de capacidad *$MetricsCapacityBlob* con la clave de fila *'analytics'* muestra la capacidad de almacenamiento que han consumido los registros de análisis.

Posteriormente, esta capacidad total consumida por los datos del usuario y los registros de análisis (si está habilitado) se puede utilizar para calcular el costo del almacenamiento de datos en la cuenta de almacenamiento. También se puede usar el mismo método para calcular los costos de almacenamiento de las cuentas de almacenamiento de GPv1.

#### <a name="transaction-costs"></a>Costos de transacciones

La suma de *'TotalBillableRequests'*, en todas las entradas de una API en la tabla de métricas de transacciones indica el número total de transacciones de dicha API determinada. *Por ejemplo,*, el número total de transacciones *'GetBlob'* en un período dado se puede calcular mediante la suma del número total de solicitudes facturables para todas las entradas con la clave de fila *'user;GetBlob'*.

Para calcular los costos de transacción de las cuentas de Blob Storage, es preciso que desglose las transacciones en tres grupos, ya que tienen precios diferentes.

* Transacciones de escritura como *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *'SnapshotBlob'* y *'CopyBlob'*.
* Transacciones de eliminación como *'DeleteBlob'* y *'DeleteContainer'*.
* Las restantes transacciones.

Para calcular los costos de transacción de las cuentas de almacenamiento de GPv1, es preciso agregar todas las transacciones, independientemente de la operación o API.

### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Costos de transferencia de datos de acceso y de replicación geográfica

Aunque el análisis del almacenamiento no proporciona la cantidad de datos leídos de una cuenta de almacenamiento y escritos en ella, se puede realizar un cálculo aproximado mediante el examen de la tabla de métricas de transacciones. La suma de *'TotalIngress'* en todas las entradas de una API de la tabla de métricas de transacciones indica la cantidad total de datos de entrada, en bytes, de dicha API concreta. De igual modo, la suma de *'TotalEgress'* indica la cantidad total de datos de salida, en bytes.

Para calcular los costos de acceso a los datos en las cuentas de Blob Storage, es preciso que desglose las transacciones en dos grupos.

* La cantidad de datos que se recuperan de la cuenta de almacenamiento se puede calcular examinando la suma de *'TotalEgress'* principalmente en las operaciones *'GetBlob'* y *'CopyBlob'*.

* La cantidad de datos que se escriben en la cuenta de almacenamiento se puede calcular examinando la suma de *'TotalIngress'* principalmente en las operaciones *'PutBlob'*, *'PutBlock'*, *'CopyBlob'* y *'AppendBlock'*.

El costo de transferencia de datos de replicación geográfica para cuentas de Blob Storage también puede calcularse mediante la valoración de la cantidad de datos escritos cuando se usa una cuenta de almacenamiento GRS o RA-GRS.

> [!NOTE]
> Para ver un ejemplo más detallado sobre cómo calcular los costos del uso de la capa de almacenamiento frecuente o esporádico, eche un vistazo a la pregunta *'¿Qué son los niveles de acceso frecuente y esporádico y cómo se puede determinar el que debe usarse?'* de la página [Precios de Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

## <a name="migrating-existing-data"></a>Migración de datos existentes

Una cuenta de uso general v1 se puede actualizar fácilmente a uso general v2 sin que se produzca ningún tiempo de inactividad ni sea preciso realizar cambios en la API y sin tener que migrar los datos. Por este motivo, es aconsejable migrar cuentas de uso general v1 a uso general v2, en lugar de a almacenamiento de blobs.

Sin embargo, si tiene que migrar a una cuenta de almacenamiento de blobs, puede usar las instrucciones siguientes.

Las cuentas de Almacenamiento de blobs son especiales para almacenar solo blobs y anexar blobs. Las cuentas de almacenamiento de uso general existentes, que permiten almacenar tablas, colas, archivos y discos, así como blobs, no se pueden convertir en cuentas de Blob Storage. Para utilizar los niveles de almacenamiento, debe crear nuevas cuentas de Blob Storage y migrar los datos existentes a las cuentas recién creadas.

Puede utilizar los métodos siguientes para migrar los datos existentes a cuentas de Blob Storage desde un dispositivos de almacenamiento local, desde proveedores de almacenamiento en la nube de terceros o desde sus cuentas existentes de almacenamiento de uso general en Azure:

### <a name="azcopy"></a>AzCopy

AzCopy es una utilidad de línea de comandos de Windows diseñada para la copia de datos de alto rendimiento a y desde Azure Storage. AzCopy se puede usar para copiar datos en una cuenta de Almacenamiento de blobs desde cuentas de almacenamiento de uso general existentes o para cargar datos desde un sistema de dispositivos de almacenamiento local en una cuenta de Almacenamiento de blobs.

Para más información, vea [Transferencia de datos con la utilidad en línea de comandos AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Data Movement Library

Azure Storage Data Movement Library for .NET se basa en el marco de movimiento de datos principal que se utiliza con AzCopy. La biblioteca está diseñada para operaciones de transferencia de datos de alto rendimiento, confiables y fáciles similares a AzCopy. Se puede usar para sacar partido a las características que proporciona AzCopy en cualquier aplicación de forma nativa, sin tener que ejecutar y supervisar instancias externas de AzCopy.

Para más información, consulte [Azure Storage Data Movement Library for .Net](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>API de REST o biblioteca de cliente

Puede crear una aplicación personalizada para migrar sus datos a una cuenta de Almacenamiento de blobs con una de las bibliotecas de cliente de Azure o la API de REST de servicios de almacenamiento de Azure. Azure Storage proporciona bibliotecas de cliente enriquecidas para varios lenguajes y aplicaciones como .NET, Java, C++, Node.JS, PHP, Ruby y Python. Las bibliotecas de cliente ofrecen capacidades avanzadas, como lógica de reintentos, registro y cargas paralelas. También se puede desarrollar directamente en la API de REST, a la que se puede llamar con cualquier lenguaje que genere solicitudes HTTP/HTTPS.

Para más información, consulte [Introducción a Azure Blob Storage](../blobs/storage-dotnet-how-to-use-blobs.md).

> [!NOTE]
> BLOB cifrado mediante el cifrado de cliente para almacenar metadatos relacionados con el cifrado almacenados con el blob. Es fundamental que cualquier mecanismo de copia se asegure de que los metadatos del blob, especialmente los relacionados con el cifrado, no desaparecen. Si copia los blobs sin estos metadatos, su contenido no puede volver a recuperarse. Para más información acerca de los metadatos relacionados con el cifrado, consulte [Cifrado del lado de cliente y Almacén de claves de Azure para Microsoft Azure Storage](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="faq"></a>Preguntas más frecuentes

**¿Siguen disponibles las cuentas de almacenamiento existentes?**

Sí. Las cuentas de almacenamiento existentes (GPv1) siguen estando disponibles y no se han cambiado sus precios ni sus funciones. Las cuentas GPv1 no ofrecen la posibilidad de elegir un nivel de almacenamiento y no tendrán funcionalidades de organización en niveles en el futuro.

**¿Por qué y cuándo conviene empezar a usar cuentas de almacenamiento de GPv2?**

Las cuentas de almacenamiento de GPv2 están especializadas en proporcionar los costos de almacenamiento más bajos por GB y los costos de acceso a los datos y transacciones más competitivos del sector. En adelante, las cuentas de almacenamiento de GPv2 son el método recomendado de almacenar blobs, dado que las futuras funcionalidades, como las notificaciones de cambios, se introducirán en función de este tipo de cuenta. Sin embargo, cada usuario debe decidir en qué momento desea realizar la actualización según sus requisitos empresariales. Por ejemplo, puede elegir optimizar los patrones de transacciones antes de la actualización.

No se admite bajar de nivel desde GPv2, así que tenga en cuenta todas las implicaciones relacionadas con los precios antes de actualizar sus cuentas a GPv2.

**¿Puedo actualizar la cuenta de almacenamiento existente a una cuenta de almacenamiento de GPv2?**

Sí. Las cuentas GPv1 pueden actualizarse fácilmente a GPv2 en el portal o con PowerShell o la CLI. Las cuentas de almacenamiento de blobs pueden actualizarse a cuentas de uso general v2 mediante PowerShell o CLI. Próximamente se podrá actualizar una cuenta de almacenamiento de blobs a GPv2 en el portal.

No se admite bajar de nivel desde GPv2, así que tenga en cuenta todas las implicaciones relacionadas con los precios antes de actualizar sus cuentas a GPv2.

**¿Se pueden almacenar objetos en dos capas de almacenamiento en la misma cuenta?**

Sí. El atributo **access tier'** establecido en un nivel de cuenta es el nivel predeterminado que se aplica a todos los objetos de dicha cuenta sin un nivel establecido explícito. Sin embargo, el almacenamiento por niveles de blob permite establecer el nivel de acceso en el nivel de objeto, independientemente de cuál sea el valor del nivel de acceso en la cuenta. Los blobs de cualquiera de los tres niveles de almacenamiento (permanente, esporádico o archivo) pueden existir en la misma cuenta.

**¿Puedo cambiar el nivel de almacenamiento de una cuenta GPv2?**

Si. Para cambiar el nivel de almacenamiento, establezca el atributo **access tier** de la cuenta de almacenamiento. El cambio del nivel de almacenamiento de la cuenta se aplica a todos los objetos almacenados en la cuenta que no tengan establecido un nivel explícito. El cambio del nivel de almacenamiento de frecuente a esporádico genera cargos de operaciones de escritura (por 10 000) (solo cuentas de GPv2), mientras que el cambio de esporádico a frecuente genera cargos de operaciones de lectura (por 10 000) y cargos de recuperación de datos (por GB) por la lectura de todos los datos de la cuenta.

**¿Con qué frecuencia se puede cambiar la capa de almacenamiento de una cuenta de Almacenamiento de blobs?**

Aunque no hay ningún límite en la frecuencia con que se puede cambiar el nivel de almacenamiento, tenga en cuenta que el cambio del nivel de almacenamiento de esporádico a frecuente supone unos gastos significativos. Se recomienda no cambiar la capa de almacenamiento con frecuencia.

**¿Los blobs en el nivel de almacenamiento esporádico se comportan de forma diferente a los del nivel de almacenamiento frecuente?**

Los blobs del nivel de almacenamiento de acceso frecuente de las cuentas tanto de uso general v2 como de almacenamiento de blobs tienen la misma latencia que los de las cuentas de almacenamiento de uso general v1. Los blobs del nivel de almacenamiento de acceso esporádico tienen una latencia similar (en milisegundos) a la de los blobs del nivel de acceso frecuente. Los blobs del nivel de almacenamiento de archivo tienen varias horas de latencia.

Los blobs del nivel de almacenamiento esporádico tienen un Acuerdo de Nivel de Servicio (SLA) de disponibilidad ligeramente inferior a los almacenados en el nivel de almacenamiento frecuente. Para más información, consulte [Contrato de nivel de servicio para Storage](https://azure.microsoft.com/support/legal/sla/storage).

**¿Puedo almacenar blobs en páginas y discos de máquinas virtuales en las cuentas de Almacenamiento de blobs?**

Nº Las cuentas de Almacenamiento de blobs solo admiten blobs en bloques y en anexos, pero no blobs en páginas. Los discos de máquinas virtuales de Azure están respaldados por blobs en páginas y, por tanto, no se pueden utilizar cuentas de Almacenamiento de blobs para almacenar discos de máquinas virtuales. Sin embargo, es posible almacenar copias de seguridad de los discos de máquinas virtuales como blobs en bloques en una cuenta de Almacenamiento de blobs. Esta es una de las razones para considerar el uso de GPv2, en lugar de cuentas de almacenamiento de blobs.

**¿Debo cambiar las aplicaciones existentes para usar cuentas de almacenamiento de GPv2?**

Las cuentas de almacenamiento de uso general v2 son totalmente coherentes, en cuanto a las API, tanto con las cuentas de almacenamiento de uso general v1 como con las de almacenamiento de blobs. Si la aplicación usa blobs en bloques o blobs en anexos y el usuario utiliza la versión 2014-02-14 de la [API de REST de servicios de almacenamiento](https://msdn.microsoft.com/library/azure/dd894041.aspx), u otra versión superior, la aplicación debería funcionar. Si se utiliza una versión anterior del protocolo, es preciso actualizar la aplicación para que use la nueva versión, con el fin de poder trabajar sin problemas con ambos tipos de cuentas de almacenamiento. En general, siempre se recomienda utilizar la versión más reciente, independientemente del tipo de cuenta de almacenamiento que se use.

Los precios de las transacciones y del ancho de banda de las cuentas GPv2 son por lo general más altos que los de GPv1. Por tanto, puede que sea necesario optimizar los patrones de transacción antes de realizar la actualización para que la factura total no experimente un incremento.

**¿Hay un cambio en la experiencia del usuario?**

Las cuentas de almacenamiento de GPv2 son muy parecidas a las cuentas de almacenamiento de GPv1 y admiten todas las características principales de Azure Storage, como alta durabilidad y disponibilidad, escalabilidad, rendimiento y seguridad. Aparte de las características y restricciones específicas de las cuentas de almacenamiento de blobs y de sus capas de almacenamiento que ya se han indicado, cuando se realiza la actualización a las cuentas de almacenamiento de uso general v2 o de almacenamiento de blobs no se produce ningún cambio.

## <a name="next-steps"></a>pasos siguientes

### <a name="evaluate-blob-storage-accounts"></a>Evaluación de cuentas de Almacenamiento de blobs

[Comprobación de la disponibilidad de las cuentas de Almacenamiento de blobs por región](https://azure.microsoft.com/regions/#services)

[Evaluación del uso de las cuentas de almacenamiento actuales mediante la habilitación de las métricas de Azure Storage](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Comprobación de los precios de almacenamiento de blobs por región](https://azure.microsoft.com/pricing/details/storage/)

[Detalles de precios de Transferencias de datos](https://azure.microsoft.com/pricing/details/data-transfers/)

### <a name="start-using-gpv2-storage-accounts"></a>Uso inicial de las cuentas de almacenamiento de GPv2

[Introducción al Almacenamiento de blobs de Azure mediante .NET](../blobs/storage-dotnet-how-to-use-blobs.md)

[Movimiento de datos hacia y desde Azure Storage](../common/storage-moving-data.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Transferencia de datos con la utilidad en línea de comandos AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Examen y exploración de cuentas de almacenamiento](http://storageexplorer.com/)

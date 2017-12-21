Existen dos tipos de cuentas de almacenamiento:

### <a name="general-purpose-storage-accounts"></a>Cuentas de almacenamiento de uso general
Las cuentas de almacenamiento de uso general proporcionan acceso a los servicios de Azure Storage, como tablas, colas, archivos, blobs y discos de máquina virtual de Azure en una sola cuenta. Este tipo de cuenta de almacenamiento tiene dos niveles de rendimiento:

* Un nivel de rendimiento de almacenamiento estándar que permite almacenar tablas, colas, archivos, blobs y discos de máquina virtual de Azure.
* Un nivel de rendimiento de almacenamiento premium que actualmente solo admite discos de máquina virtual de Azure. Consulte [Premium Storage: almacenamiento de alto rendimiento para cargas de trabajo de Azure Virtual Machines](../articles/virtual-machines/windows/premium-storage.md) para una información general detallada de Premium Storage.

### <a name="blob-storage-accounts"></a>Cuentas de Blob Storage
Una cuenta de Blob Storage es una cuenta de almacenamiento especializado para almacenar los datos no estructurados como blobs (objetos) en Azure Storage. Las cuentas de Almacenamiento de blobs son similares a las cuentas de almacenamiento de uso general existentes y comparten las excelentes características de rendimiento, escalabilidad, disponibilidad y durabilidad que se usan en la actualidad, incluida la coherencia total de la API para blobs en bloques y blobs en anexos. Para las aplicaciones que requieren solo Almacenamiento de blobs en bloque o en anexos, se recomienda utilizar cuentas de Almacenamiento de blobs.

> [!NOTE]
> Las cuentas de Almacenamiento de blobs solo admiten blobs en bloques y en anexos, pero no blobs en páginas.
> 
> 

Las cuentas de Almacenamiento de blobs exponen el atributo **Nivel de acceso** que se puede especificar en la creación de la cuenta y se puede modificar posteriormente, en caso de que sea necesario. Hay dos tipos de niveles de acceso que se pueden especificar según el patrón de acceso a datos:

* Un nivel de acceso **Frecuente** que indica que se accederá con mayor frecuencia a los objetos de la cuenta de almacenamiento. Esto le permite almacenar datos a un costo de acceso mucho menor.
* Un nivel de acceso **Esporádico** que indica que se accederá con menor frecuencia a los objetos de la cuenta de almacenamiento. Esto le permite almacenar datos a un costo de almacenamiento de datos mucho menor.

Si hay un cambio en el patrón de uso de los datos, también se puede cambiar de nivel de acceso en cualquier momento. El cambio del nivel de acceso puede conllevar cargos adicionales. Para más información, consulte [Precios y facturación](../articles/storage/common/storage-account-options.md#pricing-and-billing) .

Para más información acerca de las cuentas de Blob Storage, consulte [Azure Blob Storage: niveles de acceso Esporádico y Frecuente](../articles/storage/blobs/storage-blob-storage-tiers.md).

Para poder crear una cuenta de almacenamiento, debe tener una suscripción de Azure, que es un plan que le proporciona acceso a diversos servicios de Azure. Para comenzar con Azure, puede usar una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/). Cuando decida adquirir un plan de suscripción, puede elegir entre diversas [opciones de compra](https://azure.microsoft.com/pricing/purchase-options/). Si ya es [suscriptor de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), obtendrá créditos mensuales gratuitos que podrá usar con los servicios de Azure, incluido Azure Storage. Para obtener información acerca de los precios por volumen, consulte [Precios de Azure Storage ](https://azure.microsoft.com/pricing/details/storage/) .

Para más información acerca de cómo crear una cuenta de almacenamiento estándar, consulte [Creación de una cuenta de almacenamiento](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account) . Puede crear un máximo de 200 cuentas de almacenamiento con nombre único con una única suscripción. Para más información acerca de los límites de las cuentas de almacenamiento, consulte [Objetivos de escalabilidad y rendimiento de Azure Storage](../articles/storage/common/storage-scalability-targets.md).


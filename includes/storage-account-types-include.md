Existen dos tipos de cuentas de almacenamiento:

### Cuentas de almacenamiento de uso general

Una cuenta de almacenamiento de uso general le da acceso a las características de Almacenamiento de Azure, como tablas, colas, archivos, blobs y a discos de máquina virtual de Azure en una sola cuenta. Este tipo de cuenta de almacenamiento tiene dos niveles de rendimiento:

- Un nivel de rendimiento estándar, que le permite almacenar tablas, colas, archivos, blobs y discos de máquina virtual de Azure.
- Un nivel de rendimiento premium que actualmente solo admite discos de máquina virtual de Azure. Consulte [Almacenamiento premium: almacenamiento de alto rendimiento para cargas de trabajo de máquinas virtuales de Azure](../articles/storage/storage-premium-storage.md) para una información general detallada del Almacenamiento premium.

### Cuentas de Almacenamiento de blobs

Una cuenta de Almacenamiento de blobs es una cuenta de almacenamiento especializado para almacenar los datos no estructurados como blobs (objetos) en Almacenamiento de Azure. Las cuentas de Almacenamiento de blobs son similares a las cuentas de almacenamiento de uso general existentes y comparten las excelentes características de rendimiento, escalabilidad, disponibilidad y durabilidad que se usan hoy en día, incluida la coherencia de la API de 100 % para blobs en bloques y blobs en anexos. Las cuentas de Almacenamiento de blobs exponen el atributo **Nivel de acceso** que puede especificarse durante la creación de cuentas y modificarse posteriormente según sea necesario. Hay dos tipos de niveles de acceso que se pueden especificar según el patrón de acceso a datos:

- Un nivel de acceso **Frecuente** que indica que los objetos de la cuenta de almacenamiento se accederán con más frecuencia. Esto le permite almacenar datos a un costo de acceso mucho menor.
- Un nivel de acceso **Esporádico** que indica que los objetos de la cuenta de almacenamiento se accederán con menos frecuencia. Esto le permite almacenar datos a un costo de almacenamiento de datos mucho menor.

Para las aplicaciones que requieren solo Almacenamiento de blobs en bloque o en anexos, se recomienda utilizar cuentas de Almacenamiento de blobs. Consulte [Almacenamiento de blobs de Azure: niveles de acceso Esporádico y Frecuente](../articles/storage/storage-blob-storage-tiers.md) para más información.

Para poder crear una cuenta de almacenamiento, debe tener una suscripción de Azure, que es un plan que le proporciona acceso a diversos servicios de Azure. Para comenzar con Azure, puede usar una [cuenta gratis](https://azure.microsoft.com/pricing/free-trial/). Una vez que decida adquirir un plan de suscripción, puede elegir entre diversas [opciones de compra](https://azure.microsoft.com/pricing/purchase-options/). Si ya es [suscriptor de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), obtendrá créditos mensuales gratuitos que podrá usar con los servicios de Azure, incluido Almacenamiento de Azure. Consulte [Precios de Almacenamiento de Azure](https://azure.microsoft.com/pricing/details/storage/) para más información sobre los precios por volumen.

Cuando esté listo para crear una cuenta de almacenamiento estándar, consulte [Creación de una cuenta de almacenamiento](../articles/storage/storage-create-storage-account.md#create-a-storage-account) para más detalles. Puede crear hasta 100 cuentas de almacenamiento con nombre único con una única suscripción. Consulte [Objetivos de escalabilidad y rendimiento del almacenamiento de Azure](../articles/storage/storage-scalability-targets.md) para más información sobre los límites de la cuenta de almacenamiento.

<!---HONumber=AcomDC_0427_2016-->
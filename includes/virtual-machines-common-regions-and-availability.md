# <a name="regions-and-availability-for-virtual-machines-in-azure"></a>Regiones y disponibilidad de máquinas virtuales en Azure
Azure funciona en varios centros de datos del mundo. Estos centros de datos se agrupan en regiones geográficas, proporcionándole flexibilidad al elegir dónde crear sus aplicaciones. Es importante saber cómo y donde operan las máquinas virtuales (VM) en Azure, así como las opciones para maximizar el rendimiento, la disponibilidad y la redundancia. Este artículo proporciona una visión general de las características de disponibilidad y redundancia de Azure.

## <a name="what-are-azure-regions"></a>¿Cuáles son las regiones de Azure?
Los recursos de Azure se crean en regiones geográficas definidas como 'Oeste de EE. UU.', 'Europa del Norte' o 'Sudeste Asiático'. Puede revisar la [lista de regiones y sus ubicaciones](https://azure.microsoft.com/regions/). En cada región existen varios centros de datos que proporcionan redundancia y disponibilidad. Este enfoque proporciona flexibilidad al diseñar aplicaciones para crear máquinas virtuales más cercanas a los usuarios y satisfacer cualquier propósito legal, de cumplimiento normativo o fiscal.

## <a name="special-azure-regions"></a>Regiones de Azure especiales
Azure tiene algunas regiones especiales que se pueden usar al crear las aplicaciones, en lo referente al cumplimiento normativo o a aspectos legales. Entre dichas regiones se incluyen:

* **Virginia Gob. EE. UU.** e **Iowa Gob. EE. UU.**
  * Una instancia física y lógica con aislamiento de red de Azure para asociados y agencias de la administración pública de EE. UU., operada por personal estadounidense seleccionado con rigor. Incluye certificaciones de cumplimiento adicionales como [FedRAMP](https://www.microsoft.com/en-us/TrustCenter/Compliance/FedRAMP) y [DISA](https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA). Más información sobre [Azure Government](https://azure.microsoft.com/features/gov/).
* **China (Este)** y **China (Norte)**
  * Estas regiones están disponibles gracias a una exclusiva asociación entre Microsoft y 21Vianet, por el cual Microsoft no mantiene directamente los centros de datos. Obtenga más información sobre [Microsoft Azure en China](http://www.windowsazure.cn/).
* **Centro de Alemania** y **Noreste de Alemania**
  * Estas regiones están disponibles mediante un modelo de administrador de datos con el cual los datos del cliente permanecen en Alemania bajo el control de T-Systems, una empresa perteneciente a Deutsche Telekom que actúa como administrador de datos en Alemania.

## <a name="region-pairs"></a>Pares de región
Cada región de Azure se empareja con otra región de la misma zona geográfica (por ejemplo, EE. UU., Europa o Asia). Este enfoque permite la replicación de recursos, como el almacenamiento de máquina virtual, en una zona geográfica que debe reducir la probabilidad de desastres naturales, disturbios civiles, cortes del suministro eléctrico o interrupciones de la red física que afectan simultáneamente a ambas regiones. Entre las ventajas adicionales de los pares de región se incluyen:

* En caso de producirse una interrupción de Azure más amplia, una región tiene prioridad por cada pareja para ayudar a reducir el tiempo de restauración de las aplicaciones. 
* Las actualizaciones planeadas de Azure se implementan una a una en regiones emparejadas para minimizar el tiempo de inactividad y el riesgo de interrupción de la aplicación.
* Los datos siguen residiendo en la misma zona geográfica que su pareja (excepto Sur de Brasil) con fines de jurisdicción fiscal y de aplicación de la ley.

Entre los ejemplos de pares de región se incluyen:

| Principal | Secundario |
|:--- |:--- |
| Oeste de EE. UU. |Este de EE. UU. |
| Europa del Norte |Europa occidental |
| Sudeste asiático |Asia oriental |

Puede consultar en su totalidad la [lista de pares regionales aquí](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

## <a name="feature-availability"></a>Disponibilidad de características
Algunos servicios o características de las máquinas virtuales solo están disponibles en determinadas regiones, como, por ejemplo, tipos de almacenamiento o tamaños de VM específicos. También hay algunos servicios globales de Azure que no requieren que seleccione una región concreta, como [Azure Active Directory](../articles/active-directory/active-directory-whatis.md), [Traffic Manager](../articles/traffic-manager/traffic-manager-overview.md) o [DNS de Azure](../articles/dns/dns-overview.md). Para obtener ayuda con el diseño del entorno de la aplicación, puede comprobar la [disponibilidad de servicios de Azure en cada región](https://azure.microsoft.com/regions/#services). También puede [consultar mediante programación los tamaños de máquina virtual admitidos y las restricciones de cada región](../articles/azure-resource-manager/resource-manager-sku-not-available-errors.md).

## <a name="storage-availability"></a>Disponibilidad de almacenamiento
Es importante comprender las regiones y zonas geográficas de Azure a la hora de considerar las opciones de replicación de almacenamiento disponibles. Según el tipo de almacenamiento, tendrá opciones de replicación diferentes.

**Azure Managed Disks**
* Almacenamiento con redundancia local (LRS)
  * Replica los datos tres veces dentro de la región en la que creó su cuenta de almacenamiento.

**Discos basados en cuentas de almacenamiento**
* Almacenamiento con redundancia local (LRS)
  * Replica los datos tres veces dentro de la región en la que creó su cuenta de almacenamiento.
* Almacenamiento con redundancia de zona (ZRS)
  * Replica sus datos tres veces entre dos o tres instalaciones, ya sea dentro de una sola región o entre dos regiones.
* Almacenamiento con redundancia geográfica (GRS)
  * Replica sus datos en una región secundaria que se encuentra a cientos de kilómetros de distancia de la región primaria.
* Almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS).
  * Replica sus datos en una región secundaria, al igual que con GRS, pero también proporciona acceso de solo lectura a los datos en la ubicación secundaria.

La tabla siguiente proporciona una breve descripción de las diferencias entre los tipos de replicación de almacenamiento:

| Estrategia de replicación | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Los datos se replican entre varias instalaciones |No |Sí |Sí |yes |
| Los datos se pueden leer tanto desde la ubicación secundaria como desde la ubicación principal. |No |No |No |Sí |
| Cantidad de copias de datos mantenidas en nodos independientes |3 |3 |6 |6 |

Puede obtener más información sobre las [opciones de replicación de Azure Storage aquí](../articles/storage/common/storage-redundancy.md). Para más información acerca de los discos administrados, consulte [Azure Managed Disks overview](../articles/virtual-machines/windows/managed-disks-overview.md) (Introducción a los discos administrados de Azure).

### <a name="storage-costs"></a>Costos de almacenamiento
Los precios varían según el tipo de almacenamiento y la disponibilidad que seleccione.

**Azure Managed Disks**
* Las copias de seguridad de Managed Disks premium se realizan en unidades de estado sólido (SSD) y las de Managed Disks estándar en discos de rotación normales. Los discos administrados premium y estándar se cobran en función de la capacidad aprovisionada para el disco.

**Discos no administrados**
* Premium Storage está respaldado por unidades de estado sólido (SSD) y se cobra según la capacidad del disco.
* El almacenamiento estándar está respaldado por los discos giratorios habituales y se cobra según la capacidad en uso y la disponibilidad de almacenamiento deseada.
  * Para RA-GRS, existe un cargo adicional por la transferencia de datos de replicación geográfica por el ancho de banda de replicar dichos datos en otra región de Azure.

Consulte [Precios de Azure Storage](https://azure.microsoft.com/pricing/details/storage/) para más información sobre los diferentes tipos de almacenamiento y opciones de disponibilidad.

## <a name="availability-sets"></a>Conjuntos de disponibilidad
Un conjunto de disponibilidad es una agrupación lógica de máquinas virtuales dentro de un centro de datos que permite a Azure conocer cómo se crea su aplicación para proporcionar redundancia y disponibilidad. Se recomienda la creación de dos, o más, máquinas virtuales en un conjunto de disponibilidad no solo para proporcionar una aplicación de alta disponibilidad sino también para cumplir el [99,95 % del Acuerdo de Nivel de Servicio de Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Cuando una sola máquina virtual usa [Azure Premium Storage](../articles/virtual-machines/windows/premium-storage.md), se aplica el Acuerdo de Nivel de Servicio de Azure para los eventos de mantenimiento no planeados. 

El conjunto de disponibilidad consta de dos agrupaciones adicionales que protegen contra errores de hardware y permiten la aplicación segura de las actualizaciones: dominios de error (FD) y dominios de actualización (UD). Puede obtener más información acerca de cómo administrar la disponibilidad de [máquinas virtuales con Linux](../articles/virtual-machines/linux/manage-availability.md) o [máquinas virtuales con Windows](../articles/virtual-machines/windows/manage-availability.md).

### <a name="fault-domains"></a>Dominios de error
Un dominio de error es un grupo lógico de hardware subyacente que comparte la fuente de alimentación y el conmutador de red, similar a un bastidor dentro de un centro de datos local. Cuando se crean máquinas virtuales en un conjunto de disponibilidad, la plataforma de Azure las distribuye automáticamente entre estos dominios de error. Este enfoque limita el impacto de potenciales errores de hardware físico, interrupciones de red o cortes de alimentación eléctrica.

### <a name="update-domains"></a>Dominios de actualización
Un dominio de actualización es un grupo lógico de hardware subyacente que puede someterse a mantenimiento o reiniciarse al mismo tiempo. Cuando se crean máquinas virtuales en un conjunto de disponibilidad, la plataforma de Azure las distribuye automáticamente entre estos dominios de error. Este enfoque garantiza que al menos una instancia de la aplicación sigue ejecutándose cuando se realiza el mantenimiento periódico de la plataforma Azure. Es posible que el orden en que se reinician los dominios de actualización no siga una secuencia durante un mantenimiento planeado, pero se reinician de uno en uno.

### <a name="managed-disk-fault-domains"></a>Dominios de error de Managed Disks
Para las máquinas virtuales que usen [Azure Managed Disks](../articles/virtual-machines/windows/faq-for-disks.md), las máquinas virtuales se alinean con los dominios de error de disco administrado cuando se usa un conjunto de disponibilidad administrada. Esta alineación garantiza que todos los discos administrados conectados a una máquina virtual se encuentran en el mismo dominio de error de disco administrado. Solo se pueden crear máquinas virtuales con discos administrados en un conjunto de disponibilidad administrada. El número de dominios de error de disco administrado varía según la región: dos o tres dominios de error de disco administrado por región. Para más información sobre estos dominios de error de Managed Disks consulte [máquinas virtuales Linux](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) o [máquinas virtuales Windows](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set).

## <a name="availability-zones"></a>Zonas de disponibilidad

Las [zonas de disponibilidad](../articles/availability-zones/az-overview.md) (versión preliminar) son una alternativa a los conjuntos de disponibilidad que permite ampliar el nivel de control que tiene para mantener la disponibilidad de las aplicaciones y los datos en las máquinas virtuales. Una zona de disponibilidad es una zona separada físicamente dentro de una región de Azure. Hay tres zonas de disponibilidad por cada región de Azure admitida. Cada zona de disponibilidad tiene una fuente de alimentación, una red y un dispositivo de enfriamiento independientes y está separada lógicamente de otras zonas de disponibilidad dentro de la región de Azure. Si diseña las soluciones para que utilicen máquinas virtuales replicadas en zonas, podrá proteger sus datos y aplicaciones frente a la pérdida de un centro de datos. Aunque una zona esté en peligro, las aplicaciones y los datos replicados estarán disponibles instantáneamente en otra zona. 

![Zonas de disponibilidad](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

[!INCLUDE [availability-zones-preview-statement.md](availability-zones-preview-statement.md)]

Obtenga más información acerca de cómo implementar una máquina virtual [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) o [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) en una zona de disponibilidad.

## <a name="next-steps"></a>Pasos siguientes
Ya puede empezar a usar estas características de disponibilidad y redundancia para crear un entorno de Azure. Para información sobre los procedimientos recomendados, consulte [Lista de comprobación de disponibilidad](../articles/best-practices-availability-checklist.md).


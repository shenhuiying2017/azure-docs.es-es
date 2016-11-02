# <a name="regions-and-availability-for-virtual-machines-in-azure"></a>Regiones y disponibilidad de máquinas virtuales en Azure
Es importante saber cómo y donde operan las máquinas virtuales (VM) en Azure, así como las opciones para maximizar el rendimiento, la disponibilidad y la redundancia. Azure funciona en varios centros de datos del mundo. Estos centros de datos se agrupan en regiones geográficas, proporcionándole flexibilidad al elegir dónde crear sus aplicaciones. Este artículo proporciona una visión general de las características de disponibilidad y redundancia de Azure.

## <a name="what-are-azure-regions?"></a>¿Cuáles son las regiones de Azure?
Azure permite crear recursos, como las máquinas virtuales, en regiones geográficas definidas como 'oeste de EE. UU.', 'Europa del Norte' o 'Sudeste Asiático'. Actualmente hay 30 regiones de Azure en todo el mundo. Puede revisar la [lista de regiones y sus ubicaciones](https://azure.microsoft.com/regions/). En cada región existen varios centros de datos que proporcionan redundancia y disponibilidad. Este enfoque proporciona flexibilidad al crear aplicaciones para crear máquinas virtuales más cercanas a los usuarios y satisfacer cualquier propósito legal, de cumplimiento normativo o fiscal.

## <a name="special-azure-regions"></a>Regiones de Azure especiales
Hay algunas regiones de Azure especiales, en cuanto a cumplimiento normativo o a aspectos legales, que se pueden usar al crear las aplicaciones. Entre dichas regiones se incluyen:

- **Virginia Gob. EE. UU.** e **Iowa Gob. EE. UU.**
    - Una instancia física y lógica con aislamiento de red de Azure para asociados y agencias de la administración pública de EE. UU., operada por personal estadounidense seleccionado con rigor. Incluye certificaciones de cumplimiento adicionales como [FedRAMP](https://www.microsoft.com/en-us/TrustCenter/Compliance/FedRAMP) y [DISA](https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA). Más información sobre [Azure Government](https://azure.microsoft.com/features/gov/).
- **Centro de la India**, **India del Sur** y **oeste de la India**
    - Estas regiones están disponibles actualmente para clientes de licencias por volumen y asociados inscritos en la India. El acceso se abre a las suscripciones en línea directas a lo largo de 2016.
- **China (Este)** y **China (Norte)**
    - Estas regiones están disponibles gracias a una exclusiva asociación entre Microsoft y 21Vianet, por el cual Microsoft no mantiene directamente los centros de datos. Obtenga más información sobre [Microsoft Azure en China](http://www.windowsazure.cn/).
- **Centro de Alemania** y **Noreste de Alemania**
    - Estás regiones están disponibles actualmente mediante un modelo de administrador de datos mediante el cual los datos del cliente permanecen en Alemania bajo el control de T-Systems, una empresa perteneciente a Deutsche Telekom que actúa como administrador de datos en Alemania.

## <a name="region-pairs"></a>Pares de región
Cada región de Azure se empareja con otra región de la misma zona geográfica (por ejemplo, EE. UU., Europa o Asia). Este enfoque permite la replicación de recursos, como el almacenamiento de máquina virtual, en una zona geográfica que debe reducir la probabilidad de desastres naturales, disturbios civiles, cortes del suministro eléctrico o interrupciones de la red física que afectan simultáneamente a ambas regiones. Entre las ventajas adicionales de los pares de región se incluyen:

- En caso de producirse una interrupción de Azure más amplia, una región tiene prioridad por cada pareja para ayudar a reducir el tiempo de restauración de las aplicaciones. 
- Las actualizaciones planeadas de Azure se implementan una a una en regiones emparejadas para minimizar el tiempo de inactividad y el riesgo de interrupción de la aplicación.
- Los datos siguen residiendo en la misma zona geográfica que su pareja (excepto Sur de Brasil) con fines de jurisdicción fiscal y de aplicación de la ley.

Entre los ejemplos de pares de región se incluyen:

| Principal        | Secundario   |
|:---------------|:------------|
| Oeste de EE. UU.        | Este de EE. UU.     |
| Europa del Norte   | Europa occidental |
| Sudeste asiático | Asia oriental   |

Puede consultar en su totalidad la [lista de pares regionales aquí](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

## <a name="feature-availability"></a>Disponibilidad de características
Algunos servicios o características de las máquinas virtuales solo están disponibles en determinadas regiones, como, por ejemplo, tipos de almacenamiento o tamaños de VM específicos. También hay algunos servicios globales de Azure que no requieren que seleccione una región concreta, como [Azure Active Directory](../articles/active-directory/active-directory-whatis.md), [Traffic Manager](../articles/traffic-manager/traffic-manager-overview.md) o [DNS de Azure](../articles/dns/dns-overview.md). Para obtener ayuda con el diseño del entorno de la aplicación, puede comprobar la [disponibilidad de servicios de Azure en cada región](https://azure.microsoft.com/regions/#services). 


## <a name="storage-availability"></a>Disponibilidad de almacenamiento
Es importante comprender las regiones y zonas geográficas de Azure al considerar las opciones de replicación de Almacenamiento de Azure disponibles. Cuando cree una cuenta de almacenamiento, debe seleccionar una de las siguientes opciones de replicación:

- Almacenamiento con redundancia local (LRS)
    - Replica los datos tres veces dentro de la región en la que creó su cuenta de almacenamiento.
- Almacenamiento con redundancia de zona (ZRS)
    - Replica sus datos tres veces entre dos o tres instalaciones, ya sea dentro de una sola región o entre dos regiones.
- Almacenamiento con redundancia geográfica (GRS)
    - Replica sus datos en una región secundaria que se encuentra a cientos de kilómetros de distancia de la región primaria.
- Almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS).
    - Replica sus datos en una región secundaria, al igual que con GRS, pero también proporciona acceso de solo lectura a los datos en la ubicación secundaria.

La tabla siguiente proporciona una breve descripción de las diferencias entre los tipos de replicación de almacenamiento:

| Estrategia de replicación                                                        | LRS | ZRS | GRS | RA-GRS |
|:----------------------------------------------------------------------------|:----|:----|:----|:-------|
| Los datos se replican entre varias instalaciones                              | No  | Sí | Sí | yes    |
| Los datos se pueden leer tanto desde la ubicación secundaria como desde la ubicación principal. | No  | No  | No  | Sí    |
| Cantidad de copias de datos mantenidas en nodos independientes                      | 3   | 3   | 6   | 6      |

Puede obtener más información sobre las [opciones de replicación de Almacenamiento de Azure aquí](../articles/storage/storage-redundancy.md).

### <a name="storage-costs"></a>Costos de almacenamiento
Los precios varían según el tipo de almacenamiento y la disponibilidad que seleccione. 

- Almacenamiento premium está respaldado por unidades de estado sólido (SSD) y se cobra según la capacidad del disco.
- El almacenamiento estándar está respaldado por los discos giratorios habituales y se cobra según la capacidad en uso y la disponibilidad de almacenamiento deseada.
    - Para RA-GRS, existe un cargo adicional por la transferencia de datos de replicación geográfica por el ancho de banda de replicar dichos datos en otra región de Azure.

Consulte [Precios de Almacenamiento de Azure](https://azure.microsoft.com/pricing/details/storage/) para más información sobre los diferentes tipos de almacenamiento y opciones de disponibilidad.


## <a name="azure-images"></a>Imágenes de Azure
En Azure, las máquinas virtuales se crean a partir de una imagen. Normalmente, las imágenes son de [Azure Marketplace](https://azure.microsoft.com/marketplace/), donde los asociados pueden proporcionar imágenes completas preconfiguradas de aplicaciones o del sistema operativo.

Al crear una máquina virtual desde una imagen en Azure Marketplace, en realidad está trabajando con plantillas. Las plantillas de Azure Resource Manager son archivos de notación de objetos JavaScript (JSON) declarativos que pueden usarse para crear entornos de la aplicación complejos compuestos por máquinas virtuales, almacenamiento, redes virtuales, etc. Puede obtener más información sobre el uso de [plantillas de Azure Resource Manager](../articles/resource-group-overview.md), incluido cómo [crear sus propias plantillas](../articles/resource-group-authoring-templates.md).

También puede crear sus propias imágenes personalizadas y cargarlas mediante la [CLI de Azure](../articles/virtual-machines/virtual-machines-linux-upload-vhd.md) o [Azure PowerShell](../articles/virtual-machines/virtual-machines-windows-upload-image.md) para crear rápidamente máquinas virtuales personalizadas para sus requisitos de creación específicos.

## <a name="availability-sets"></a>Conjuntos de disponibilidad
Un conjunto de disponibilidad es una agrupación lógica de máquinas virtuales que permite a Azure conocer cómo se crea su aplicación para proporcionar redundancia y disponibilidad. Se recomienda la creación de dos, o más, máquinas virtuales en un conjunto de disponibilidad no solo para proporcionar una aplicación de alta disponibilidad sino también para cumplir el [99,95 % del SLA de Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). El conjunto de disponibilidad consta de dos agrupaciones adicionales que protegen contra errores de hardware y permiten la aplicación segura de las actualizaciones: dominios de error (FD) y dominios de actualización (UD).

![Dibujo conceptual de la configuración del dominio de actualización y de error](./media/virtual-machines-common-regions-and-availability/ud-fd-configuration.png)

Puede obtener más información acerca de cómo administrar la disponibilidad de [máquinas virtuales con Linux](../articles/virtual-machines/virtual-machines-linux-manage-availability.md) o [máquinas virtuales con Windows](../articles/virtual-machines/virtual-machines-linux-manage-availability.md).

### <a name="fault-domains"></a>Dominios de error
Un dominio de error es un grupo lógico de hardware subyacente que comparte la fuente de alimentación y el conmutador de red, similar a un bastidor dentro de un centro de datos local. Cuando se crean máquinas virtuales en un conjunto de disponibilidad, la plataforma de Azure las distribuye automáticamente entre estos dominios de error. Este enfoque limita el impacto de potenciales errores de hardware físico, interrupciones de red o cortes de alimentación eléctrica.

### <a name="update-domains"></a>Dominios de actualización
Un dominio de actualización es un grupo lógico de hardware subyacente que puede someterse a mantenimiento o reiniciarse al mismo tiempo. Cuando se crean máquinas virtuales en un conjunto de disponibilidad, la plataforma de Azure las distribuye automáticamente entre estos dominios de error. Este enfoque garantiza que al menos una instancia de la aplicación sigue ejecutándose cuando se realiza el mantenimiento periódico de la plataforma Azure. Es posible que el orden en que se reinician los dominios de actualización no siga una secuencia durante un mantenimiento planeado, pero se reinician de uno en uno.


## <a name="next-steps"></a>Pasos siguientes
Ya puede empezar a usar estas características de disponibilidad y redundancia para crear un entorno de Azure. Para información sobre los procedimientos recomendados, consulte [Lista de comprobación de disponibilidad](../articles/best-practices-availability-checklist.md).

<!--HONumber=Oct16_HO2-->



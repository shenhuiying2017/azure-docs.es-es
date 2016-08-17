# Información general de regiones y disponibilidad
Azure funciona en varios centros de datos del mundo. Estos centros de datos se agrupan en regiones geográficas, proporcionándole flexibilidad al elegir dónde crear sus aplicaciones. También puede crear alta disponibilidad en sus aplicaciones aprovechando las características de redundancia y disponibilidad de la plataforma Azure, por ejemplo, los conjuntos de disponibilidad y replicación de almacenamiento.

## ¿Cuáles son las regiones de Azure?
Azure permite crear recursos, como las máquinas virtuales, en regiones geográficas definidas como 'oeste de EE. UU.', 'Europa del Norte' o 'Sudeste Asiático'. Actualmente hay 26 regiones de Azure en todo el mundo. Puede revisar la [lista de regiones y sus ubicaciones](https://azure.microsoft.com/regions/). En cada región existen varios centros de datos para proporcionar redundancia y disponibilidad. Esto proporciona flexibilidad al crear sus aplicaciones para crear máquinas virtuales (VM) más cercanas a los usuarios y para satisfacer cualquier propósito legal, de conformidad o fiscal.

## Regiones de Azure especiales
Hay algunas regiones de Azure especiales con fines de conformidad o legales que tal vez desee usar al crear sus aplicaciones.

Entre las regiones especiales existentes se incluyen:

- **Virginia Gob. EE. UU.** y **Iowa Gob. EE. UU.**
    - Una instancia física y lógica con aislamiento de red de Azure para asociados y agencias de la administración pública de EE. UU., operada por personal estadounidense seleccionado con rigor. Incluye certificaciones de cumplimiento adicionales como [FedRAMP](https://www.microsoft.com/es-ES/TrustCenter/Compliance/FedRAMP) y [DISA](https://www.microsoft.com/es-ES/TrustCenter/Compliance/DISA). Obtenga más información sobre [Azure Government](https://azure.microsoft.com/features/gov/).
- **Este de Australia** y **sudeste de Australia**
    - Estas regiones están disponibles para clientes con presencia empresarial en Australia o Nueva Zelanda.
- **Centro de la India**, **India del Sur** y **oeste de la India**
    - Estas regiones están actualmente disponibles para clientes y asociados con licencias por volumen cuyas empresas están inscritas en la India. El acceso a estas se abrirá a suscripciones directas en línea durante todo 2016.
- **China (Este)** y **Norte de China**
    - Estas regiones están disponibles gracias a una exclusiva asociación entre Microsoft y 21Vianet, por el cual Microsoft no mantiene directamente los centros de datos. Obtenga más información sobre [Microsoft Azure en China](http://www.windowsazure.cn/).

Entre las regiones especiales anunciadas se incluyen:

- **Centro de Alemania** y **Noreste de Alemania**
    - Azure estará disponible mediante un nuevo modelo de administrador de datos mediante el cual los datos del cliente permanecen en Alemania bajo el control de T-Systems, una empresa perteneciente a Deutsche Telekom que actúa como el administrador de los datos en Alemania.

## Pares de región
Cada región de Azure se empareja con otra región de la misma zona geográfica (por ejemplo, EE. UU., Europa o Asia). Esto permite la replicación de recursos, como almacenamiento de máquina virtual, en una zona geográfica que debe reducir la probabilidad de desastres naturales, disturbios civiles, cortes del suministro eléctrico o interrupciones de la red física que afectan simultáneamente a ambas regiones. Entre las ventajas adicionales de los pares de región se incluyen:

- En caso de producirse una interrupción de Azure más amplia, una región tiene prioridad por cada pareja para ayudar a reducir el tiempo de restauración de las aplicaciones.
- Las actualizaciones planeadas de Azure se implementan una a una en regiones emparejadas para minimizar el tiempo de inactividad y el riesgo de interrupción de la aplicación.
- Los datos seguirán residiendo dentro de la misma zona geográfica que su pareja (a excepción del Sur de Brasil) con fines de jurisdicción de impuestos y aplicación de la ley.

Entre los ejemplos de pares de región se incluyen:

| Principal | Secundario |
|:---------------|:------------|
| Oeste de EE. UU. | Este de EE. UU. |
| Europa del Norte | Europa occidental |
| Sudeste asiático | Asia oriental |

Puede consultar en su totalidad la [lista de pares regionales aquí](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

## Disponibilidad de características
Algunos servicios o características de las máquinas virtuales solo están disponibles en determinadas regiones, como, por ejemplo, tipos de almacenamiento o tamaños de VM específicos. También hay algunos servicios de Azure que no exigen seleccionar una región particular, como [Azure Active Directory](../articles/active-directory/active-directory-whatis.md), [Administrador de tráfico](../articles/traffic-manager/traffic-manager-overview.md) o [DNS de Azure](../articles/dns/dns-overview.md). Para obtener ayuda con el diseño del entorno de la aplicación, puede comprobar la [disponibilidad de servicios de Azure en cada región](https://azure.microsoft.com/regions/#services).


## Disponibilidad de almacenamiento
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

| Estrategia de replicación | LRS | ZRS | GRS | RA-GRS |
|:-----------------------------------------------------------------------------------|:----|:----|:----|:-------|
| Los datos se replican entre varias instalaciones | No | Sí | Sí | Sí |
| Los datos se pueden leer desde la ubicación secundaria al igual que desde la ubicación principal | No | No | No | Sí |
| Cantidad de copias de datos mantenidas en nodos independientes | 3 | 3 | 6 | 6 |

Puede obtener más información sobre las [opciones de replicación de Almacenamiento de Azure aquí](../articles/storage/storage-redundancy.md).

### Costos de almacenamiento
Los precios varían según el tipo de almacenamiento y la disponibilidad que seleccione.

- El almacenamiento estándar está respaldado por los discos giratorios habituales y se cobra según la capacidad en uso y la disponibilidad de almacenamiento deseada.
    - Para RA-GRS, existe un cargo adicional por la transferencia de datos de replicación geográfica por el ancho de banda de replicar dichos datos en otra región de Azure.
- Almacenamiento premium está respaldado por unidades de estado sólido (SSD) y se cobra según la capacidad del disco.

Consulte [Precios de Almacenamiento de Azure](https://azure.microsoft.com/pricing/details/storage/) para más información sobre los diferentes tipos de almacenamiento y opciones de disponibilidad.


## Imágenes de Azure
En Azure, las máquinas virtuales se crean a partir de una imagen. Normalmente, será una imagen creada desde [Azure Marketplace](https://azure.microsoft.com/marketplace/), donde los asociados pueden proporcionar imágenes completas preconfiguradas de aplicaciones o del sistema operativo.

Al crear una máquina virtual desde una imagen en Azure Marketplace, en realidad está trabajando con plantillas. Las plantillas de Azure Resource Manager son archivos de notación de objetos JavaScript (JSON) declarativos que pueden usarse para crear entornos de la aplicación complejos compuestos por máquinas virtuales, almacenamiento, redes virtuales, etc. Puede obtener más información sobre el uso de [plantillas de Azure Resource Manager](../articles/resource-group-overview.md), incluido cómo [crear sus propias plantillas](../articles/resource-group-authoring-templates.md).

También puede crear sus propias imágenes personalizadas y cargarlas mediante la [CLI de Azure](../articles/virtual-machines/virtual-machines-linux-upload-vhd.md) o [Azure PowerShell](../articles/virtual-machines/virtual-machines-windows-upload-image.md) para crear rápidamente máquinas virtuales personalizadas para sus requisitos de creación específicos. Al usar imágenes personalizadas, las máquinas virtuales deben almacenarse en la misma cuenta de almacenamiento que la propia imagen. No puede cargar una imagen en una sola región y, a continuación, crear máquinas virtuales a partir de ella en otras regiones de Azure.


## Conjuntos de disponibilidad
Un conjunto de disponibilidad es una agrupación lógica de máquinas virtuales que permiten a Azure conocer cómo se crea su aplicación para proporcionar redundancia y disponibilidad. Se recomienda la creación de dos o más máquinas virtuales en un conjunto de disponibilidad para proporcionar una aplicación de alta disponibilidad y cumplir el [99,95 % del Acuerdo de Nivel de Servicio de Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). El conjunto de disponibilidad consta de dos agrupaciones adicionales que protegen contra errores de hardware y permiten la aplicación segura de las actualizaciones: dominios de error (FD) y dominios de actualización (UD).

![Dibujo conceptual de la configuración del dominio de actualización y de error](./media/virtual-machines-common-regions-and-availability/ud-fd-configuration.png)

Puede obtener más información sobre cómo administrar la disponibilidad de [máquinas virtuales Linux](../articles/virtual-machines/virtual-machines-linux-manage-availability.md) o [máquinas virtuales con Windows](../articles/virtual-machines/virtual-machines-linux-manage-availability.md).

### Dominios de error
Un dominio de error es un grupo lógico de hardware subyacente que comparte la fuente de alimentación y el conmutador de red, similar a un bastidor dentro de un centro de datos local. A medida que crea máquinas virtuales dentro de un conjunto de disponibilidad, la plataforma Azure distribuye automáticamente sus máquinas virtuales en estos dominios de error para limitar el impacto de posibles errores de hardware físico, interrupciones de red o cortes de alimentación.

### Dominios de actualización
Un dominio de actualización es un grupo lógico de hardware subyacente que puede someterse a mantenimiento o reiniciarse al mismo tiempo. A medida que crea máquinas virtuales dentro de un conjunto de disponibilidad, la plataforma Azure distribuye automáticamente sus máquinas virtuales en estos dominios de actualización para asegurarse de que al menos una instancia de su aplicación siga siempre ejecutándose a medida que la plataforma Azure se somete a mantenimiento periódico. Tenga en cuenta que el orden en que los dominios de actualización se reiniciarán tal vez no siga una secuencia durante un mantenimiento planeado, sino que solamente se reiniciarán uno por uno.


## Pasos siguientes
Puede leer detalles más específicos sobre [procedimientos recomendados de disponibilidad de Azure](../articles/best-practices-availability-checklist.md).

<!---HONumber=AcomDC_0803_2016-->
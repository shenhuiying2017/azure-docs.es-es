<properties 
   pageTitle="¿Qué es StorSimple? | Microsoft Azure" 
   description="Describe la administración de datos de StorSimple y el proceso de protección, las ventajas y la arquitectura y presenta los componentes de StorSimple." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carolz" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="09/25/2015"
   ms.author="v-sharos@microsoft.com"/>

# Serie StorSimple 8000: una solución de almacenamiento en la nube híbrida 

## Información general

Microsoft Azure StorSimple es una solución SAN eficaz, rentable y fácilmente administrable que elimina muchos de los problemas y gastos asociados con el almacenamiento empresarial y la protección de datos. Usa un dispositivo de su propiedad (el dispositivo Microsoft Azure StorSimple), se integra con servicios en la nube y proporciona un conjunto de herramientas de administración para proporcionar una vista transparente de todo el almacenamiento empresarial, incluido el almacenamiento en la nube.

StorSimple usa niveles de almacenamiento para administrar los datos almacenados en diversos medios de almacenamiento. El espacio de trabajo actual está almacenado localmente en unidades de estado sólido (SSD), los datos que se usan con menos frecuencia se almacenan en unidades de disco duro (HDD) y los datos de archivo se insertan en la nube. Además, StorSimple usa la desduplicación y la compresión para reducir la cantidad de almacenamiento que los datos consumen. El proceso de organización en niveles del almacenamiento se produce como sigue:

1. Un administrador del sistema configura una cuenta de almacenamiento en la nube de Microsoft Azure.
2. El administrador usa la consola en serie y el servicio StorSimple Manager (que se ejecuta en el Portal de administración de Azure) para configurar el dispositivo y crear directivas de protección de datos y volúmenes. Las máquinas locales (como servidores de archivo) usan Interfaz estándar de equipos pequeños de Internet (iSCSI) para acceder al dispositivo StorSimple.
3. Inicialmente, StorSimple almacena los datos en el nivel de SSD rápido del dispositivo.
4. A medida que el nivel de SSD empieza a carecer de capacidad, StorSimple desduplica y comprime los bloques de datos más antiguos y los mueve al nivel de unidades de disco duro.
5. Si el nivel de unidades de disco duro comienza a carecer de capacidad, StorSimple cifra los bloques de datos más antiguos y los envía de forma segura a la cuenta de almacenamiento de Microsoft Azure a través de HTTPS.
6. Microsoft Azure crea varias réplicas de los datos en su centro de datos y en un centro de datos remoto, lo que garantiza que los datos puedan recuperarse si se produce un desastre. 
7. Cuando el servidor de archivos solicita datos almacenados en la nube, StorSimple los devuelve sin problemas y almacena una copia en el nivel de SSD del dispositivo StorSimple.

Además de la administración de almacenamiento, las características de protección de datos de StorSimple le permiten crear copias de seguridad a petición y programadas, así como almacenarlas localmente o en la nube. Las copias de seguridad se realizan en forma de instantáneas incrementales, lo que significa que se pueden crear y restaurar rápidamente. Las instantáneas en la nube pueden resultar esenciales en escenarios de recuperación ante desastres, ya que reemplazan a los sistemas de almacenamiento secundario (por ejemplo, la copia de seguridad en cinta) y le permiten restaurar los datos a su centro de datos o a sitios alternativos si es necesario.

>[AZURE.NOTE]La serie StorSimple 8000 con la actualización de software 1 o posterior admite Amazon S3 con servicios en la nube RRS, HP y OpenStack, así como Microsoft Azure. (Todavía necesitará una cuenta de almacenamiento de Microsoft Azure para administrar los dispositivos). Para obtener más información, consulte [Configurar una nueva cuenta de almacenamiento para el servicio](storsimple-deployment-walkthrough.md#configure-a-new-storage-account-for-the-service).

## ¿Por qué usar StorSimple?

Microsoft Azure StorSimple proporciona los siguientes beneficios:

- **Integración transparente**: Microsoft Azure StorSimple usa el protocolo iSCSI para vincular de manera invisible instalaciones de almacenamiento de datos. Esto garantizar que los datos almacenados en la nube, en el centro de datos o en servidores remotos parezcan estar almacenados en una ubicación única.
- **Menores costes de almacenamiento**: Microsoft Azure StorSimple asigna almacenamiento local o en la nube suficiente para cumplir las actuales exigencias y extiende el almacenamiento en la nube solo cuando es necesario. Disminuye aún más los requisitos de almacenamiento y los gastos al eliminar las versiones redundantes de los mismos datos (desduplicación) y mediante la compresión.
- **Administración simplificada del almacenamiento**: Microsoft Azure StorSimple proporciona herramientas de administración del sistema que puede usar para configurar y administrar datos almacenados localmente, en un servidor remoto y en la nube. Además, puede administrar copias de seguridad y restaurar funciones desde un complemento de Microsoft Management Console (MMC). StorSimple proporciona una interfaz independiente opcional que puede usar para extender los servicios de administración y protección de datos de StorSimple al contenido almacenado en servidores de SharePoint. 
- **Mejor recuperación ante desastres y cumplimiento normativo**: Microsoft Azure StorSimple no requiere largos tiempos de recuperación. En lugar de eso, restaura los datos según sea necesario. Esto significa que las operaciones normales pueden seguir con un mínimo de interrupción. Además, puede configurar directivas para especificar programaciones de copias de seguridad y retención de datos.
- **Movilidad de datos**: es posible tener acceso a los datos cargados a los servicios en la nube de Microsoft Azure desde otros sitios para fines de recuperación y migración. Además, puede usar StorSimple para configurar dispositivos virtuales de StorSimple en máquinas virtuales que se ejecutan en Microsoft Azure. Luego, las máquinas virtuales pueden utilizar dispositivos virtuales para obtener acceso a los datos almacenados para fines de prueba o recuperación. 

El siguiente diagrama proporciona una vista de alto nivel de la solución Microsoft Azure StorSimple.

![Arquitectura de StorSimple](./media/storsimple-overview/hcs-data-services-storsimple-system-architecture.png)

**Arquitectura de Microsoft Azure StorSimple**

## Componentes StorSimple

La solución de Microsoft Azure StorSimple incluye los siguientes componentes:

- **Dispositivo de Microsoft Azure StorSimple**: una matriz de almacenamiento híbrida local que contiene unidades de estado sólido y discos duros, en conjunto con controladores redundantes y funcionalidades de conmutación automática por error. Los controladores administran la organización en niveles del almacenamiento y ponen los datos actualmente utilizados (o activos) en el almacenamiento local (en el dispositivo o en servidores locales), mientras que mueven los datos usados con menos frecuencia a la nube.
- **Dispositivo virtual de StorSimple**: también conocido como Aplicación virtual de StorSimple, se trata de una versión de software del dispositivo de StorSimple que replica la arquitectura y la mayoría de las funcionalidades del dispositivo de almacenamiento híbrido físico. El dispositivo virtual de StorSimple se ejecuta en un solo nodo en una máquina virtual de Azure. El dispositivo virtual es adecuado para usarlo en escenarios de prueba y en escenarios piloto de menor tamaño. No es posible crear un dispositivo virtual de StorSimple en un dispositivo de StorSimple o en un servidor local.
- **Windows PowerShell para StorSimple**: una interfaz de línea de comandos que puede usar para administrar el dispositivo de StorSimple. Windows PowerShell para StorSimple tiene características que le permite registrar su dispositivo de StorSimple, configurar la interfaz de red en su dispositivo, instalar ciertos tipos de actualizaciones, solucionar problemas con su dispositivo mediante el acceso a la sesión de soporte y cambiar el estado del dispositivo. Puede tener acceso a Windows PowerShell para StorSimple si se conecta a la consola serial o si usa la comunicación remota de Windows PowerShell.
- **Cmdlets de Azure PowerShell StorSimple**: un conjunto de cmdlets de Windows PowerShell que permiten automatizar las tareas de nivel de servicio y de migración desde la línea de comandos. Para obtener más información sobre los cmdlets de Azure PowerShell, consulte la [referencia de los cmdlets](https://msdn.microsoft.com/library/dn920427.aspx).
- **Servicio StorSimple Manager**: extensión del Portal de administración de Azure que permite administrar un dispositivo de StorSimple o un dispositivo virtual de StorSimple desde una única interfaz web. Puede utilizar el servicio StorSimple Manager para crear y administrar servicios, ver y administrar dispositivos, ver alertas, administrar volúmenes y ver y administrar las directivas de copia de seguridad y el catálogo de copias de seguridad.
- **Snapshot Manager de StorSimple**: un complemento de MMC que usa grupos de volúmenes y el Servicio de instantáneas de volumen de Windows para generar copias de seguridad coherentes con la aplicación. Además, puede usar StorSimple Snapshot Manager para crear programaciones de copias de seguridad y clonar o restaurar volúmenes. 
- **Adaptador de StorSimple para SharePoint**: una herramienta que extiende de manera transparente el almacenamiento y la protección de datos de Microsoft Azure StorSimple a las granjas de SharePoint Server, a la vez que hace que sea posible ver y administrar el almacenamiento de StorSimple desde el Portal de administración de SharePoint.

## Pasos siguientes

Obtenga más información sobre [StorSimple](https://azure.microsoft.com/documentation/services/storsimple/).

Obtenga más información sobre los [componentes y terminología de StorSimple](storsimple-components.md).


 

<!---HONumber=Oct15_HO1-->
<properties 
   pageTitle="¿Qué es StorSimple?" 
   description="Describa las características, la arquitectura y los componentes de StorSimple." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="AdinaH" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="02/17/2015"
   ms.author="v-sharos@microsoft.com"/>

# ¿Qué es StorSimple? 

Microsoft Azure StorSimple es una solución eficaz, rentable y administrable que elimina muchos de los problemas y gastos asociados con el almacenamiento empresarial y la protección de datos. Utiliza un dispositivo de su propiedad (el dispositivo de Microsoft Azure StorSimple) y las herramientas de administración integradas para proporcionar una vista transparente de todo el almacenamiento empresarial, incluido el almacenamiento en la nube.

## ¿Por qué usar StorSimple?

Microsoft Azure StorSimple proporciona los siguientes beneficios:

- **Integración transparente**: Microsoft Azure StorSimple utiliza el protocolo Interfaz estándar de equipos pequeños de Internet (iSCSI) para vincular de manera invisible instalaciones de almacenamiento de datos. Esto garantizar que los datos almacenados en la nube, en el centro de datos o en servidores remotos parezcan estar almacenados en una ubicación única.
- **Menores costes de almacenamiento**: Microsoft Azure StorSimple asigna almacenamiento local o en la nube suficiente para cumplir las actuales exigencias y extiende el almacenamiento en la nube solo cuando es necesario. Disminuye aún más los requisitos de almacenamiento y los gastos al eliminar las versiones redundantes de los mismos datos (desduplicación) y mediante la compresión.
- **Administración simplificada del almacenamiento**: Microsoft Azure StorSimple proporciona herramientas de administración del sistema que puede utilizar para configurar y administrar datos almacenados localmente, en un servidor remoto y en la nube. De manera adicional, puede administrar funciones de copia de seguridad y restauración desde un complemento de Microsoft Management Console (MMC). StorSimple proporciona una interfaz independiente opcional que puede usar para extender los servicios de administración y protección de datos de StorSimple al contenido almacenado en servidores de SharePoint. 
- **Mejor recuperación ante desastres y cumplimiento**: Microsoft Azure StorSimple no requiere un tiempo de recuperación extendido. En lugar de eso, restaura los datos según sea necesario. Esto significa que las operaciones normales pueden seguir con un mínimo de interrupción. Además, puede configurar directivas para especificar programaciones de copias de seguridad y retención de datos.
- **Movilidad de datos**: es posible tener acceso a los datos cargados a los servicios en la nube de Microsoft Azure desde otros sitios para fines de recuperación y migración. Además, puede usar StorSimple para configurar dispositivos virtuales de StorSimple en máquinas virtuales que se ejecutan en Microsoft Azure. Luego, las máquinas virtuales pueden utilizar dispositivos virtuales para obtener acceso a los datos almacenados para fines de prueba o recuperación. 

El siguiente diagrama proporciona una vista de alto nivel de la solución Microsoft Azure StorSimple.

![StorSimple architecture](./media/storsimple-overview/hcs-data-services-storsimple-system-architecture.png)

**Arquitectura de Microsoft Azure StorSimple**

## Componentes StorSimple

La solución de Microsoft Azure StorSimple incluye los siguientes componentes:

- **Dispositivo de Microsoft Azure StorSimple**: una matriz de almacenamiento híbrida local que contiene unidades de estado sólido y discos duros, en conjunto con controladores redundantes y funcionalidades de conmutación automática por error. Los controladores administran la organización en niveles del almacenamiento y ponen los datos actualmente utilizados (o activos) en el almacenamiento local (en el dispositivo o en servidores locales), mientras que mueven los datos usados con menos frecuencia a la nube.
- **Dispositivo virtual de StorSimple**: también conocido como Aplicación virtual de StorSimple, se trata de una versión de software del dispositivo de StorSimple que replica la arquitectura y las funcionalidades del dispositivo de almacenamiento híbrido físico. El dispositivo virtual de StorSimple se ejecuta en un solo nodo en una máquina virtual de Azure. El dispositivo virtual es adecuado para usarlo en escenarios de prueba y en escenarios piloto de menor tamaño. No es posible crear un dispositivo virtual de StorSimple en un dispositivo de StorSimple o en un servidor local.
- **Windows PowerShell para StorSimple**: una interfaz de línea de comandos que puede utilizar para administrar el dispositivo de StorSimple. Windows PowerShell para StorSimple tiene características que le permite registrar su dispositivo de StorSimple, configurar la interfaz de red en su dispositivo, instalar ciertos tipos de actualizaciones, solucionar problemas con su dispositivo mediante el acceso a la sesión de soporte y cambiar el estado del dispositivo. Puede tener acceso a Windows PowerShell para StorSimple si se conecta a la consola serial o si usa la comunicación remota de Windows PowerShell.
- **Servicio StorSimple Manager**: una extensión del Portal de administración de Azure que le permite administrar un dispositivo de StorSimple o un dispositivo virtual de StorSimple desde una interfaz web única. Puede utilizar el servicio StorSimple Manager para crear y administrar servicios, ver y administrar dispositivos, ver alertas, administrar volúmenes y ver y administrar las directivas de copia de seguridad y el catálogo de copias de seguridad.
- **StorSimple Snapshot Manager**: un complemento de MMC que utiliza grupos de volúmenes y el Servicio de instantáneas de volumen de Windows para generar copias de seguridad coherentes con la aplicación. Además, puede usar StorSimple Snapshot Manager para crear programaciones de copias de seguridad y clonar o restaurar volúmenes. 
- **Adaptador de StorSimple para SharePoint**: una herramienta que extiende de manera transparente el almacenamiento y la protección de datos de Microsoft Azure StorSimple a las granjas de SharePoint Server, a la vez que hace que sea posible ver y administrar el almacenamiento de StorSimple desde el Portal de administración de SharePoint.

## Pasos siguientes

Lea sobre los [componentes de StorSimple](https://technet.microsoft.com/library/cc754482.aspx) y revise las [notas de la versión de StorSimple](https://msdn.microsoft.com/library/azure/dn772367.aspx)





<!--HONumber=52--> 
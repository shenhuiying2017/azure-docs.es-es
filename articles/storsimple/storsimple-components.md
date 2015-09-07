<properties 
   pageTitle="¿Cuáles son los componentes de StorSimple? | Microsoft Azure"
	description="Describe el dispositivo StorSimple, el dispositivo virtual, los servicios y las tecnologías de administración y define términos clave usados en la solución."
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
	ms.date="08/26/2015"
	ms.author="v-sharos"/>


# ¿Cuáles son los componentes de StorSimple y cómo funcionan? 

## Información general

Bienvenido a Microsoft Azure StorSimple, una solución de almacenamiento integrada que administra tareas de almacenamiento entre dispositivos locales y el almacenamiento en la nube de Microsoft Azure. StorSimple está diseñado para reducir los costes de almacenamiento, simplificar la administración del almacenamiento, mejorar la funcionalidad y eficacia de la recuperación ante desastres y proporcionar movilidad de datos.

Las secciones siguientes describen los componentes de Microsoft Azure StorSimple y explican la manera en que la solución ordena los datos, asigna el almacenamiento y facilita la administración del almacenamiento y la protección de los datos. La última sección proporciona definiciones de algunos términos importantes relacionados con los componentes de StorSimple y su administración.

> [AZURE.NOTE]La información de implementación de StorSimple publicada en el sitio web de Microsoft Azure se aplica solo a los dispositivos de la serie 8000 de StorSimple. Para obtener información acerca de los dispositivos de la serie 7000, visite: [StorSimple Help](http://onlinehelp.storsimple.com/)

## Dispositivo de StorSimple

El dispositivo de Microsoft Azure StorSimple es una matriz de almacenamiento híbrida local que proporciona almacenamiento principal y acceso iSCSI a los datos almacenados en él. Administra la comunicación con el almacenamiento en la nube y ayuda a garantizar la seguridad y confidencialidad de todos los datos que están almacenados en la solución de Microsoft Azure StorSimple.

El dispositivo de StorSimple incluye unidades de estado sólido (SSD) y discos duros (HDD), además de compatibilidad para agrupación en clústeres y conmutación automática por error. Contiene un procesador compartido, almacenamiento compartido y dos controladores reflejados. Cada controlador proporciona lo siguiente:

- Conexión a un equipo host
- Hasta seis puertos de red para conectar a la red de área local (LAN)
- Supervisión de hardware
- Memoria de acceso aleatorio no volátil (NVRAM), que conserva la información incluso cuando se interrumpe la energía
- Actualización compatible con clústeres para administrar actualizaciones de software en servidores en un clúster de conmutación por error para que las actualizaciones tengan un efecto mínimo o nulo en la disponibilidad de servicio
- Servicio de clúster, que funciona como un clúster back-end, proporciona alta disponibilidad y minimiza todos los efectos adversos que podrían producirse si un HDD o una SSD presentan errores o quedan sin conexión

En cualquier punto del tiempo solo hay un controlador activo. Si se produce un error en el controlador activo, el segundo controlador se activa de manera automática.

Para obtener más información, consulte [Componentes y estado de hardware StorSimple](storsimple-monitor-hardware-status.md).

## Dispositivo virtual de StorSimple

Puede usar StorSimple para crear un dispositivo virtual que replica la arquitectura y las funcionalidades del dispositivo de almacenamiento híbrido real.

El dispositivo virtual de StorSimple (también conocido como la aplicación virtual de StorSimple) se ejecuta en un solo nodo en una máquina virtual de Azure. (Un dispositivo virtual solo se puede crear en una máquina virtual de Azure. No puede crear uno en un dispositivo de StorSimple o en un servidor local). Un dispositivo virtual de StorSimple difiere de un dispositivo de StorSimple físico en lo siguiente:

- El dispositivo virtual solo tiene una interfaz, mientras que el dispositivo físico tiene seis interfaces de red. 
- El dispositivo virtual se registra durante la configuración del dispositivo y no como una tarea independiente.
- No es posible volver a generar la clave de cifrado de datos de servicio desde un dispositivo virtual. Durante la sustitución de clave, vuelve a generar la clave en el dispositivo físico y, a continuación, actualiza el dispositivo virtual con la clave nueva.
- Si necesita aplicar actualizaciones al dispositivo virtual, experimentará cierto tiempo de inactividad. Esto no se produce con un dispositivo de StorSimple físico.

Se recomienda usar el dispositivo virtual StorSimple para escenarios de recuperación de desastres en los cuales no hay disponible un dispositivo físico, como en implementaciones de prueba de la nube.

Para obtener más información, consulte [Dispositivo virtual StorSimple](storsimple-virtual-device.md).


## Tecnologías de administración de almacenamiento
 
Además del dispositivo virtual y del dispositivo dedicado de StorSimple, Microsoft Azure StorSimple utiliza las siguientes tecnologías de software para proporcionar acceso rápido a los datos y disminuir el consumo de almacenamiento:

- Organización automática del almacenamiento en niveles 
- Aprovisionamiento fino 
- Desduplicación y compresión 

### Organización automática del almacenamiento en niveles

Microsoft Azure StorSimple ordena automáticamente los datos en niveles lógicos según su uso actual, su antigüedad y la relación con otros datos. Los datos más activos se almacenan de manera local, mientra que los datos menos activos y los datos inactivos se migran automáticamente a la nube. La ilustración 1 muestra este enfoque de almacenamiento.
 
![Niveles de almacenamiento de StorSimple](./media/storsimple-components/hcs-data-services-storsimple-components-tiers.png)

**Figura 1: Almacenamiento de StorSimple**

Para habilitar el acceso rápido, StorSimple almacena datos muy activos (datos activos) en SSD en el dispositivo StorSimple. Almacena datos que se usan de manera ocasional (datos semiactivos) en discos duros en el dispositivo o en los servidores del centro de datos. Mueve a la nube datos inactivos, datos de copia de seguridad y datos retenidos para fines de archivo o de cumplimiento.

StorSimple ajusta y reordena las asignaciones de datos y almacenamiento a medida que cambian los patrones de uso. Por ejemplo, cierta información puede volverse menos activo con el tiempo. A medida que se vuelve cada vez menos activa, migra de SSD a HDD y, a continuación, a la nube. Si esos mismos datos vuelven a activarse, se migran de vuelta al dispositivo de almacenamiento.

El proceso de organización en niveles del almacenamiento se produce como sigue:

1. Un administrador del sistema configura una cuenta de almacenamiento en la nube de Microsoft Azure.
2. El administrador usa la consola en serie y el servicio StorSimple Manager (que se ejecuta en el Portal de administración de Azure) para configurar el servidor de dispositivo y archivos, y crear directivas de protección de datos y volúmenes. El servidor de archivos local usa Interfaz estándar de equipos pequeños de Internet (iSCSI) para acceder al dispositivo StorSimple.
3. Inicialmente, StorSimple almacena los datos en el nivel de SSD rápido del dispositivo.
4. A medida que el nivel de SSD empieza a carecer de capacidad, StorSimple desduplica y comprime los bloques de datos más antiguos y los mueve al nivel de unidades de disco duro.
5. Si el nivel de unidades de disco duro comienza a carecer de capacidad, StorSimple cifra los bloques de datos más antiguos y los envía de forma segura a la cuenta de almacenamiento de Microsoft Azure a través de HTTPS.
6. Microsoft Azure crea varias réplicas de los datos en su centro de datos y en un centro de datos remoto, lo que garantiza que los datos puedan recuperarse si se produce un desastre. 
7. Cuando el servidor de archivos solicita datos almacenados en la nube, StorSimple los devuelve sin problemas y almacena una copia en el nivel de SSD del dispositivo StorSimple.

### Aprovisionamiento fino

El aprovisionamiento fino es una tecnología de virtualización en que el almacenamiento disponible parece superar los recursos físicos. En lugar de reservar almacenamiento suficiente por adelantado, StorSimple utiliza el aprovisionamiento fino para asignar solo el espacio suficiente para cumplir con los requisitos actuales. La naturaleza elástica del almacenamiento en la nube facilita este enfoque porque StorSimple puede aumentar o disminuir el almacenamiento en la nube para cumplir con las exigencias cambiantes.

### Desduplicación y compresión

Microsoft Azure StorSimple utiliza la desduplicación y la compresión de datos para reducir aún más los requisitos de almacenamiento.

La desduplicación disminuye la cantidad general de datos almacenados al eliminar la redundancia en el conjunto de datos almacenados. A medida que cambia la información, StorSimple omite los datos no modificados y solo captura los cambios. Además, StorSimple reduce la cantidad de datos almacenados al identificar y eliminar información innecesaria.

## Windows PowerShell para StorSimple

Windows PowerShell para StorSimple proporciona una interfaz de línea de comandos que puede utilizar para crear y administrar el servicio de Microsoft Azure StorSimple y configurar y supervisar los dispositivos de StorSimple. Se trata de una interfaz de la línea de comandos basada en Windows PowerShell que incluye cmdlets dedicados para administrar su dispositivo de StorSimple. Windows PowerShell para StorSimple tiene características que le permiten:

- Registrar un dispositivo.
- Configurar la interfaz de red en un dispositivo.
- Instalar ciertos tipos de actualizaciones.
- Solucionar problemas del dispositivo mediante el acceso a la sesión de soporte.
- Cambiar el estado del dispositivo.

Puede tener acceso a Windows PowerShell para StorSimple desde una consola serial (en un equipo host conectado directamente al dispositivo) o de manera remota mediante el uso de la comunicación remota de Windows PowerShell. Observe que algunas tareas de Windows PowerShell para StorSimple, como el registro inicial del dispositivo, solo se pueden realizar en la consola serial.

Para obtener más información, consulte [Uso de Windows PowerShell para StorSimple para administrar su dispositivo](storsimple-windows-powershell-administration.md).

## Cmdlets de StorSimple de Azure PowerShell

Los cmdlets de StorSimple de Azure PowerShell son un conjunto de cmdlets de Windows PowerShell que permiten automatizar las tareas migración y de nivel de servicio desde la línea de comandos. Para obtener más información sobre los cmdlets de Azure PowerShell, consulte la [referencia de los cmdlets](https://msdn.microsoft.com/library/dn920427.aspx).

## Servicio StorSimple Manager

Microsoft Azure StorSimple proporciona una interfaz de usuario basada en Web (el servicio StorSimple Manager) que le permite administrar de manera central el centro de datos y el almacenamiento en la nube. Puede usar el servicio StorSimple Manager para realizar las siguientes tareas:

- Ajustar la configuración del sistema para dispositivos de StorSimple.
- Configurar y administrar ajustes de seguridad para dispositivos de StorSimple.
- Configurar credenciales y propiedades de la nube.
- Configurar y administrar volúmenes en un servidor.
- Configurar grupos de volúmenes.
- Crear copias de seguridad y restaurar datos.
- Supervisar el rendimiento.
- Revisar la configuración del sistema e identificar posibles problemas.

Puede utilizar el servicio StorSimple Manager para realizar todas las tareas de administración, excepto las que requieren tiempo de inactividad del sistema, como la configuración inicial y la instalación de actualizaciones.

Para obtener más información, consulte [Uso del servicio StorSimple Manager para administrar su dispositivo StorSimple](storsimple-manager-service-administration.md).

## StorSimple Snapshot Manager

StorSimple Snapshot Manager es un complemento de Microsoft Management Console (MMC) que se puede usar para crear copias de seguridad puntuales y coherentes de datos locales y en la nube. El complemento se ejecuta en un host basado en Windows Server. Puede utilizar StorSimple Snapshot Manager para:

- Configurar, crear copias de seguridad y eliminar volúmenes.
- Configurar grupos de volúmenes para asegurarse de que los datos con copia de seguridad son coherentes con la aplicación.
- Administrar directivas de copia de seguridad para que se creen copias de seguridad de los datos según una programación predeterminada y se almacenen en una ubicación designada (localmente o en la nube).
- Restaurar volúmenes y archivos individuales.

Las copias de seguridad se capturan como instantáneas, las que solo registran los cambios desde que se capturó la última instantánea y requieren mucho menos espacio de almacenamiento que las copias de seguridad completas. Puede crear programaciones de copia de seguridad o crear copias de seguridad inmediatas, según sea necesario. De manera adicional, puede usar StorSimple Snapshot Manager para establecer directivas de retención que controlan la cantidad de instantáneas que se guardarán. Si más adelante necesita restaurar datos desde una copia de seguridad, StorSimple Snapshot Manager le permite seleccionar desde el catálogo de instantáneas locales o en la nube.

Si se produce un desastre o si debe restaurar datos por cualquier otro motivo, StorSimple Snapshot Manager los restaura de manera incremental según sea necesario. La restauración de los datos no requiere que apague todo el sistema mientras restaura un archivo, reemplaza equipo o mueve operaciones a otro sitio.

Para obtener más información, consulte [Qué es StorSimple Snapshot Manager](storsimple-what-is-snapshot-manager.md).

## Adaptador de StorSimple para SharePoint

Microsoft Azure StorSimple incluye el adaptador de StorSimple para SharePoint, un componente opcional que extiende de manera transparente el almacenamiento de StorSimple y las características de protección de datos a las granjas de servidores de SharePoint. El adaptador funciona con un proveedor de Almacenamiento remoto de blobs (RBS) y la característica RBS de SQL Server, lo que le permite mover blobs a un servidor con copia de seguridad realizada por el sistema Microsoft Azure StorSimple. Microsoft Azure StorSimple luego almacena los datos de blob de manera local o en la nube, según el uso.

El adaptador de StorSimple para SharePoint se administra desde el Portal de administración central de SharePoint. Por consiguiente, la administración de SharePoint sigue siendo centralizada y todo el almacenamiento pareciera encontrarse en la granja de SharePoint.

Para obtener más información, consulte [Adaptador de StorSimple para SharePoint](storsimple-adapter-for-sharepoint.md).


## Terminología de StorSimple 

Antes de implementar la solución Microsoft Azure StorSimple, se recomienda que revise los siguientes términos y definiciones.

### Términos clave y definiciones

| Término (acrónimo o abreviatura) | Descripción |
| ------------------------------ | ---------------- |
| registros de control de acceso (ACR) | Un registro asociado a un volumen en el dispositivo de Microsoft Azure StorSimple que determina qué hosts pueden conectarse a él. La determinación está basada en el Nombre calificado iSCSI (IQN) de los hosts (contenidos en el ACR) que se conectan al dispositivo StorSimple.|
| AES-256 | Un algoritmo Estándar de cifrado avanzado (AES) de 256 bits para cifrar los datos cuando se desplaza hacia y desde la nube. |
| tamaño de unidad de asignación (AUS) | La menor cantidad de espacio en disco que se puede asignar para contener un archivo en los sistemas de archivos de Windows. Si un tamaño de archivo no es un múltiplo par del tamaño del clúster, debe utilizarse el espacio adicional para almacenar el archivo (hasta el siguiente múltiplo del tamaño del clúster), lo que genera una pérdida de espacio y la fragmentación del disco duro. <br>El AUS recomendado para volúmenes StorSimple de Azure es de 64 KB porque funciona bien con los algoritmos de desduplicación.|
| organización automática del almacenamiento en niveles | Mover automáticamente los datos menos activos a un nivel en la nube y, a continuación, habilitar la administración de todo el almacenamiento de una interfaz de usuario central.|
| catálogo de copias de seguridad | Un conjunto de copias de seguridad, normalmente relacionadas con el tipo de aplicación que se utilizó. Esta colección se muestra en la página Catálogo de copias de seguridad de la interfaz de usuario del servicio StorSimple Manager.|
| archivo del catálogo de copias de seguridad | Un archivo que contiene una lista de instantáneas disponibles almacenadas actualmente en la base de datos de copias de seguridad de StorSimple Snapshot Manager. |
| directiva de copia de seguridad | Una selección de volúmenes, tipo de copia de seguridad y un calendario que le permite crear copias de seguridad en un programa definido.|
| objetos grandes binarios (BLOB) | Una colección de datos binarios almacenados como una sola entidad en un sistema de administración de base de datos. Los BLOB suelen ser imágenes, audio u otros objetos multimedia, aunque a veces el código binario ejecutable se almacena como un BLOB.|
| Protocolo de autenticación por desafío mutuo (CHAP) | Un protocolo utilizado para autenticar al mismo nivel de una conexión, basada en el mismo nivel comparten una contraseña o secreto. CHAP puede ser unidireccional o mutuo. Conel CHAP unidireccional el destino autentica un iniciador. El CHAP mutuo requiere que el destino autentique el iniciador y, a continuación, que el iniciador autentique al destino. | 
| clon | Una copia duplicada de un volumen. |
|Nube como nivel (CaaT) | Almacenamiento integrado en la nube como un nivel en la arquitectura de almacenamiento de modo que todo el almacenamiento parece ser parte de una red de almacenamiento empresarial.|
| proveedor de servicios de nube (CSP) | Un proveedor de servicios de informática en la nube.|
| instantánea de la nube | Una copia de punto en el tiempo de los datos del volumen que se almacenan en la nube. Una instantánea de nube es equivalente a una instantánea replicada en un sistema de almacenamiento externo. Las instantáneas en la nube son especialmente útiles en escenarios de recuperación ante desastres.|
| clave de cifrado de almacenamiento en la nube | Una contraseña o clave utilizada por su dispositivo StorSimple para tener acceso a los datos cifrados enviados por el dispositivo a la nube.|
| actualización de clústeres | Administrar actualizaciones de software en servidores en un clúster de conmutación por error para que las actualizaciones tengan un efecto mínimo o nulo en la disponibilidad de servicio.|
| ruta de datos | Una colección de unidades funcionales que realizan operaciones interconectadas de procesamiento de datos.|
| desactivar | Una acción permanente que interrumpe la conexión entre el dispositivo StorSimple y el servicio de nube asociada. Las instantáneas en la nube del dispositivo permanecen después de este proceso y se pueden clonar o usar para la recuperación de desastres.|
| creación de reflejo de disco | Replicación de volúmenes de disco lógicos en distintas unidades en tiempo real para garantizar la disponibilidad continua.|
| creación de reflejo de disco dinámico | Replicación de volúmenes de discos lógicos en discos dinámicos.|
| discos dinámicos | Un formato de volumen de disco que utiliza el Administrador de discos lógicos (LDM) para almacenar y administrar datos en varios discos físicos. Los discos dinámicos se pueden ampliar para proporcionar más espacio libre.|
| Gabinete Extended Bunch of Disks (EBOD) | Un gabinete secundario del dispositivo StorSimple de Microsoft Azure que contiene discos duros adicionales para almacenamiento adicional.|
| aprovisionamiento grueso | Un aprovisionamiento de almacenamiento convencional mediante el que se asigna espacio de almacenamiento en base a necesidades previstas (y por lo general más allá de la necesidad actual). Consulte también *aprovisionamiento fino*.|
| unidad de disco duro (HDD) | Una unidad que usa placas giratorias para almacenar datos.|
| almacenamiento de nube híbrida | Una arquitectura de almacenamiento que usa los recursos locales y remotos, incluidos el almacenamiento en la nube.|
| Internet Small Computer System Interface (iSCSI) | Un estándar de red de almacenamiento basado en el Protocolo de Internet (IP) para vincular el equipo de almacenamiento de datos o funciones.|
| iniciador iSCSI | Un componente de software que permite a un equipo host que ejecuta Windows conectarse a una red de almacenamiento externo basada en iSCSI.|
| Nombre calificado iSCSI (IQN) | Un nombre único que identifica un destino o iniciador iSCSI.|
| destino iSCSI | Un componente de software que proporciona subsistemas de disco iSCCSI centralizados en redes de área de almacenamiento.|
| archivo de datos | Un enfoque de almacenamiento en el que los datos de archivo están accesibles todo el tiempo (no se almacena fuera del sitio en la cinta, por ejemplo). Microsoft Azure StorSimple usa el archivo vivo.|
| instantánea local | Una copia de punto en el tiempo de los datos del volumen que se almacenan en el dispositivo StorSimple de Microsoft Azure.|
| Microsoft Azure StorSimple | Una solución eficaz que consta de un dispositivo de almacenamiento del centro de datos y el software que permite a las organizaciones de TI aprovechar el almacenamiento en la nube como si fuese un almacenamiento de centro de datos y simplificar la administración y protección de datos al mismo tiempo que reduce los costos. Esta solución consolida el almacenamiento principal, archivo, copia de seguridad y recuperación de desastres (DR) a través de la integración sin problemas con la nube. Mediante la combinación de administración de datos de almacenamiento SAN y de nube en una plataforma empresarial, los dispositivos StorSimple habilitan la velocidad, la sencillez y confiabilidad para todas las necesidades relacionadas con el almacenamiento.|
| Módulo de alimentación y refrigeración (PCM) | Componentes de hardware del dispositivo StorSimple que constan de fuentes de alimentación y el ventilador de refrigeración, por este motivo el nombre Módulo de alimentación y refrigeración. El gabinete principal del dispositivo tiene dos PCM de 764 W, mientras que el gabinete EBOD tiene dos PCM de 580 W.|
| gabinete principal | El gabinete principal del dispositivo StorSimple que contiene los controladores de la plataforma de la aplicación.|
| objetivo de tiempo de recuperación (RTO) | La cantidad máxima de tiempo que debe emplearse antes de que un proceso o sistema empresarial esté completamente restaurado tras un desastre.| 
|SCSI conectado en serie (SAS) | Un tipo de unidad de disco duro (HDD).|
| clave de cifrado de datos de servicio | Una clave a disposición de cualquier nuevo dispositivo StorSimple que se registra en el servicio StorSimple Manager. Los datos de configuración transferidos entre el servicio StorSimple Manager y el dispositivo se cifran con una clave pública y, a continuación, se pueden descifrar únicamente en el dispositivo mediante una clave privada. La clave de cifrado de datos de servicio permite al servicio obtener esta clave privada para el descifrado.|
| clave de registro del servicio | Una clave que le ayuda a registrar el dispositivo StorSimple con el servicio StorSimple Manager para que aparezca en el Portal de administración para diversas tareas administrativas.|
| Small Computer System Interface (SCSI) | Un conjunto de estándares para conectar físicamente equipos y pasar datos entre ellos.|
| unidad de estado sólido (SSD) | Un disco que no contiene piezas móviles; por ejemplo, una unidad flash.|
| Cuenta de almacenamiento | Un conjunto de credenciales de acceso vinculado a la cuenta de almacenamiento para un proveedor de servicios de nube determinado.| 
| Adaptador de StorSimple para SharePoint| Un componente de Microsoft Azure StorSimple que extiende de manera transparente el almacenamiento de StorSimple y la protección de datos a las granjas de servidores de SharePoint.|
| Servicio StorSimple Manager | Una extensión del Portal de administración de Azure que le permite administrar su Azure StorSimple en las instalaciones y dispositivos virtuales.|
| StorSimple Snapshot Manager | Un complemento de Microsoft Management Console (MMC) para administrar las operaciones de copia de seguridad y restauración en Microsoft Azure StorSimple.|
| realizar copia de seguridad | Una característica que permite al usuario realizar una copia de seguridad interactiva de un volumen. Es una forma alternativa de realizar una copia de seguridad manual de un volumen en lugar de realizar una copia de seguridad automatizada a través de una directiva definida.|
| aprovisionamiento fino | Un método para optimizar la eficacia con la que se utiliza el espacio de almacenamiento disponible en sistemas de almacenamiento. En el aprovisionamiento fino, el almacenamiento se asigna entre varios usuarios según el espacio mínimo requerido por cada usuario en un momento dado. Consulte también *aprovisionamiento grueso*.|
| volumen | Áreas de almacenamiento lógico presentadas en forma de unidades de disco. Los volúmenes StorSimple corresponden a los volúmenes montados por el host, incluidos aquellos detectados mediante el uso de iSCSI y un dispositivo StorSimple.|
 | contenedor de volúmenes | Un grupo de volúmenes y la configuración que se aplica a ellos. Todos los volúmenes en el dispositivo StorSimple se agrupan en contenedores de volúmenes. La configuración del contenedor de volumen incluye cuentas de almacenamiento, la configuración de cifrado para los datos enviados a la nube con las claves de cifrado asociada y el ancho de banda consumido en operaciones relacionadas con la nube.|
| grupo de volúmenes | En StorSimple Snapshot Manager, un grupo de volúmenes es un conjunto de volúmenes configurados para facilitar el proceso de copia de seguridad.|
| Volume Shadow Copy Service (VSS)| Un servicio del sistema operativo de Windows Server que facilita la consistencia de aplicaciones, ya que se comunica con las aplicaciones para VSS para coordinar la creación de instantáneas incrementales. VSS garantiza que las aplicaciones están temporalmente inactivas cuando se realizan las instantáneas.|
| Windows PowerShell para StorSimple | Una interfaz de línea de comandos basada en Windows PowerShell utilizada para controlar y administrar un dispositivo StorSimple. Además de conservar algunas de las capacidades básicas de Windows PowerShell, esta interfaz tiene cmdlets adicionales dedicados orientados a administrar el dispositivo StorSimple.|


## Pasos siguientes

Obtenga más información acerca de la [Seguridad de StorSimple](storsimple-security.md).




 

<!---HONumber=August15_HO9-->
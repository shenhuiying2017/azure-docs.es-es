<properties 
   pageTitle="¿Cuáles son los componentes de StorSimple?" 
   description="Describe el dispositivo, los servicios y las tecnologías de administración de StorSimple." 
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


# ¿Cuáles son los componentes de StorSimple? 

Bienvenido a Microsoft Azure StorSimple, una solución de almacenamiento integrada que administra tareas de almacenamiento entre dispositivos locales y el almacenamiento en la nube de Microsoft Azure.  StorSimple está diseñado para reducir los costes de almacenamiento, simplificar la administración del almacenamiento, mejorar la funcionalidad y eficacia de la recuperación ante desastres y proporcionar movilidad de datos.

Las secciones siguientes describen los componentes de Microsoft Azure StorSimple y explican la manera en que la solución ordena los datos, asigna el almacenamiento y facilita la administración del almacenamiento y la protección de los datos. 

> [AZURE.NOTE] La información de implementación de StorSimple publicada en el sitio web de Microsoft Azure se aplica solo a los dispositivos de la serie 8000 de StorSimple. Para obtener información acerca de los dispositivos de la serie 7000, visite: [Ayuda de StorSimple](http://onlinehelp.storsimple.com/).

## Dispositivo de StorSimple

El dispositivo de Microsoft Azure StorSimple es una matriz de almacenamiento híbrida local que proporciona almacenamiento principal y acceso de iSCSI a los datos almacenados en otro lugar. Administra la comunicación con el almacenamiento en la nube y ayuda a garantizar la seguridad y confidencialidad de todos los datos que están almacenados en la solución de Microsoft Azure StorSimple.

El dispositivo de StorSimple incluye unidades de estado sólido (SSD) y discos duros (HDD), además de compatibilidad para agrupación en clústeres y conmutación automática por error. Contiene un procesador compartido, almacenamiento compartido y dos controladores reflejados. Cada controlador proporciona lo siguiente:

- Conexión a un equipo host
- Hasta seis puertos de red para conectar a la red de área local (LAN)
- Supervisión de hardware
- Memoria de acceso aleatorio no volátil (NVRAM), que conserva la información incluso cuando se interrumpe la energía
- Actualización compatible con clústeres para administrar actualizaciones de software en servidores en un clúster de conmutación por error para que las actualizaciones tengan un efecto mínimo o nulo en la disponibilidad de servicio
- Servicio de clúster, que funciona como un clúster back-end, proporciona alta disponibilidad y minimiza todos los efectos adversos que podrían producirse si un HDD o una SSD presentan errores o quedan sin conexión

En cualquier punto del tiempo solo hay un controlador activo. Si se produce un error en el controlador activo, el segundo controlador se activa de manera automática. 

Para obtener más información, consulte [Dispositivos de StorSimple](https://msdn.microsoft.com/library/azure/dn772363.aspx).

## Dispositivo virtual de StorSimple

Puede usar StorSimple para crear un dispositivo virtual que replica la arquitectura y las funcionalidades del dispositivo de almacenamiento híbrido real. 

El dispositivo virtual de StorSimple (también conocido como la aplicación virtual de StorSimple) se ejecuta en un solo nodo en una máquina virtual de Azure. (Un dispositivo virtual solo se puede crear en una máquina virtual de Azure. No puede crear uno en un dispositivo de StorSimple o en un servidor local). Un dispositivo virtual de StorSimple difiere de un dispositivo de StorSimple físico en lo siguiente: 

- El dispositivo virtual solo tiene una interfaz, mientras que el dispositivo físico tiene seis interfaces de red. 
- El dispositivo virtual se registra durante la configuración del dispositivo y no como una tarea independiente.
- No es posible volver a generar la clave de cifrado de datos de servicio desde un dispositivo virtual. Durante la sustitución de clave, vuelve a generar la clave en el dispositivo físico y, a continuación, actualiza el dispositivo virtual con la clave nueva.
- Si necesita aplicar actualizaciones al dispositivo virtual, experimentará cierto tiempo de inactividad. Esto no se produce con un dispositivo de StorSimple físico.

Se recomienda usar el dispositivo virtual de StorSimple para escenarios de recuperación ante desastres en los cuales no hay disponible un dispositivo físico, como en implementaciones de prueba e implementaciones piloto de menor tamaño.

Para obtener más información, consulte [Dispositivo virtual de StorSimple](https://msdn.microsoft.com/library/azure/dn772390.aspx).


## Tecnologías de administración de almacenamiento
 
Además del dispositivo virtual y del dispositivo dedicado de StorSimple, Microsoft Azure StorSimple utiliza las siguientes tecnologías de software para proporcionar acceso rápido a los datos y disminuir el consumo de almacenamiento:

- Organización automática del almacenamiento en niveles 
- Aprovisionamiento fino 
- Desduplicación y compresión 

### Organización automática del almacenamiento en niveles

Microsoft Azure StorSimple ordena automáticamente los datos en niveles lógicos según su uso actual, su antigüedad y la relación con otros datos. Los datos más activos se almacenan de manera local, mientra que los datos menos activos y los datos inactivos se migran automáticamente a la nube. El siguiente diagrama ilustra este enfoque de almacenamiento.
 
![StorSimple storage tiers](./media/storsimple-components/hcs-data-services-storsimple-components-tiers.png)

**Niveles de almacenamiento de StorSimple**

Para habilitar el acceso rápido, StorSimple almacena datos muy activos (datos activos) en SSD en el dispositivo StorSimple. Almacena datos que se usan de manera ocasional (datos semiactivos) en discos duros en el dispositivo o en los servidores del centro de datos. Mueve a la nube datos inactivos, datos de copia de seguridad y datos retenidos para fines de archivo o de cumplimiento. 

StorSimple ajusta y reordena las asignaciones de datos y almacenamiento a medida que cambian los patrones de uso. Por ejemplo, cierta información puede volverse menos activo con el tiempo. A medida que se vuelve cada vez menos activa, migra de SSD a HDD y, a continuación, a la nube. Si esos mismos datos vuelven a activarse, se migran de vuelta al dispositivo de almacenamiento.

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

Para obtener más información, consulte [Windows PowerShell para StorSimple](https://msdn.microsoft.com/library/azure/dn772425.aspx).

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

Para obtener más información, consulte [Servicio StorSimple Manager](https://msdn.microsoft.com/library/azure/dn772396.aspx).

## StorSimple Snapshot Manager

StorSimple Snapshot Manager es un complemento de Microsoft Management Console (MMC) que se puede usar para crear copias de seguridad puntuales y coherentes de datos locales y en la nube. El complemento se ejecuta en un host basado en Windows Server. Puede utilizar StorSimple Snapshot Manager para:

- Configurar, crear copias de seguridad y eliminar volúmenes.
- Configurar grupos de volúmenes para asegurarse de que los datos con copia de seguridad son coherentes con la aplicación.
- Administrar directivas de copia de seguridad para que se creen copias de seguridad de los datos según una programación predeterminada y se almacenen en una ubicación designada (localmente o en la nube).
- Restaurar volúmenes y archivos individuales.

Las copias de seguridad se capturan como instantáneas, las que solo registran los cambios desde que se capturó la última instantánea y requieren mucho menos espacio de almacenamiento que las copias de seguridad completas. Puede crear programaciones de copia de seguridad o crear copias de seguridad inmediatas, según sea necesario. De manera adicional, puede usar StorSimple Snapshot Manager para establecer directivas de retención que controlan la cantidad de instantáneas que se guardarán. Si más adelante necesita restaurar datos desde una copia de seguridad, StorSimple Snapshot Manager le permite seleccionar desde el catálogo de instantáneas locales o en la nube. 

Si se produce un desastre o si debe restaurar datos por cualquier otro motivo, StorSimple Snapshot Manager los restaura de manera incremental según sea necesario. La restauración de los datos no requiere que apague todo el sistema mientras restaura un archivo, reemplaza equipo o mueve operaciones a otro sitio.

Para obtener más información, consulte [StorSimple Snapshot Manager](https://msdn.microsoft.com/library/azure/dn772365.aspx).

## Adaptador de StorSimple para SharePoint

Microsoft Azure StorSimple incluye el adaptador de StorSimple para SharePoint, un componente opcional que extiende de manera transparente el almacenamiento de StorSimple y las características de protección de datos a las granjas de servidores de SharePoint. El adaptador funciona con un proveedor de Almacenamiento remoto de blobs (RBS) y la característica RBS de SQL Server, lo que le permite mover blobs a un servidor con copia de seguridad realizada por el sistema Microsoft Azure StorSimple. Microsoft Azure StorSimple luego almacena los datos de blob de manera local o en la nube, según el uso.

El adaptador de StorSimple para SharePoint se administra desde el Portal de administración central de SharePoint. Por consiguiente, la administración de SharePoint sigue siendo centralizada y todo el almacenamiento pareciera encontrarse en la granja de SharePoint.

Para obtener más información, consulte [Adaptador de StorSimple para SharePoint](https://msdn.microsoft.com/library/azure/dn757737.aspx). 


## Pasos siguientes

Revise las [notas de la versión de StorSimple](https://msdn.microsoft.com/library/azure/dn772367.aspx)






<!--HONumber=52-->
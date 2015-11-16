<properties 
   pageTitle="Requisitos del sistema StorSimple | Microsoft Azure" 
   description="Describe los requisitos del sistema y procedimientos recomendados para software, alta disponibilidad y conexión a la de red de una solución Azure StorSimple." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="alkohli" 
   manager="carolz" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="10/30/2015"
   ms.author="alkohli"/>

# Software de StorSimple, alta disponibilidad y requisitos de red

## Información general

Bienvenido a Microsoft Azure StorSimple. Este artículo describe los requisitos del sistema importantes y las prácticas recomendadas para el dispositivo StorSimple y para los clientes de almacenamiento que acceden al dispositivo. Es recomendable que revise cuidadosamente la siguiente información antes de implementar el sistema Azure StorSimple y que luego la consulte según sea necesario durante la implementación y funcionamiento posterior.

Los requisitos del sistema incluyen:

- **Requisitos de software para clientes de almacenamiento**: describe los sistemas operativos compatibles y cualquier requisito adicional para esos sistemas operativos.
- **Requisitos de red para el dispositivo StorSimple**: proporciona información acerca de los puertos que deben estar abiertos en el firewall para permitir el tráfico iSCSI, de nube o de administración.
- **Requisitos de alta disponibilidad para StorSimple**: describe los requisitos de alta disponibilidad y las prácticas recomendadas del equipo host y del dispositivo StorSimple. 


## Requisitos de software para los clientes de almacenamiento 

Los siguientes requisitos de software son para los clientes de almacenamiento que tienen acceso a su dispositivo StorSimple.

| Sistemas operativos compatibles | Versión requerida | Requisitos/notas adicionales |
| --------------------------- | ---------------- | ------------- |
| Windows Server | 2008R2 SP1, 2012, 2012R2 |Se admiten volúmenes iSCSI de StorSimple para su exclusivamente en los siguientes tipos de discos de Windows:<ul><li>Volumen simple en disco básico</li><li>Simple y reflejado en disco dinámico</li></ul>Se admiten el aprovisionamiento fino de Windows Server 2012 y las características ODX si usa un volumen iSCSI de StorSimple.<br><br>StorSimple solo puede crear volúmenes con aprovisionamiento fino. No puede crear volúmenes de aprovisionamiento completo o parcial.<br><br>El proceso de volver a formatear un volumen con aprovisionamiento fino puede tardar mucho tiempo en completarse. Se recomienda eliminar el volumen y luego crear uno nuevo en lugar de volverlo a formatear. Sin embargo, si aun así prefiere volver a formatear un volumen:<ul><li>Ejecute el siguiente comando antes de volver a formatearlo para evitar retrasos de recuperación de espacio: <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>Después de finalizar el formato, use el siguiente comando para volver a habilitar la recuperación de espacio:<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Aplique la revisión de Windows Server 2012 al equipo de Windows Server, como se describe en el artículo de KB [2878635](https://support.microsoft.com/kb/2870270).</li></ul></li></ul></ul> Si va a configurar Snapshot Manager de StorSimple o el Adaptador de StorSimple para SharePoint, vaya a los [requisitos de software para ver los componentes opcionales](#software-requirements-for-optional-components).|
| VMWare ESX | 5\.1 | Admitido con VMware vSphere 5.1 como cliente iSCSI La característica VAAI-block se admite con VMware vSphere v.5.1 en dispositivos de StorSimple. 
| Linux RHEL/CentOS | 5 y 6 | Compatibilidad con clientes iSCSI de Linux con versiones del iniciador Open-iSCSI 5 y 6. |
| Linux | SUSE Linux 11 | |
 >[AZURE.NOTE]IBM AIX no es compatible actualmente con StorSimple.

## Requisitos de software de componentes opcionales

Los siguientes requisitos de software son para los componentes de StorSimple opcionales (Snapshot Manager de StorSimple y Adaptador de StorSimple para SharePoint).

| Componente | Plataforma de host | Requisitos/notas adicionales |
| --------------------------- | ---------------- | ------------- |
| StorSimple Snapshot Manager | Windows Server 2008 R2 SP1, 2012, 2012 R2 | El uso de Snapshot Manager de StorSimple en Windows Server es necesario para realizar la copia de seguridad y restauración de los discos dinámicos reflejados y para realizar copias de seguridad coherentes con la aplicación.<br> Snapshot Manager de StorSimple solo se admite en Windows Server 2008 R2 SP1 (64 bits), Windows 2012 R2 y Windows Server 2012.<ul><li>Si va a usar Window Server 2012, debe instalar .NET 3.5 o 4.5 antes de instalar Snapshot Manager de StorSimple.</li><li>Si va a usar Windows Server 2008 R2 SP1, debe instalar Windows Management Framework 3.0 antes de instalar Snapshot Manager de StorSimple.</li></ul> |
| Adaptador de StorSimple para SharePoint | Windows Server 2008 R2 SP1, 2012, 2012 R2 |<ul><li>El Adaptador de StorSimple para SharePoint solo se admite en SharePoint 2010 y SharePoint 2013.</li><li>RBS requiere SQL Server Enterprise Edition, versión 2008 R2 o 2012.</li></ul>|
 
## Requisitos de red para el dispositivo StorSimple

El dispositivo StorSimple es un dispositivo bloqueado. Sin embargo, los puertos deben abrirse en el firewall para permitir el tráfico de administración, de nube o iSCSI. En la tabla siguiente se enumeran los puertos que deben estar abiertos en el firewall. En esta tabla, *dentro* o *entrante* hace referencia a la dirección desde la que el cliente entrante solicita acceso al dispositivo. *Fuera* o *saliente* hace referencia a la dirección en la que el dispositivo StorSimple envía datos externamente, más allá de la implementación: por ejemplo, saliente a Internet.

| Nº de puerto<sup>1,2</sup> | Dentro o fuera | Ámbito de puerto | Obligatorio | Notas |
|------------------------|-----------|------------|----------|-------| 
|TCP 80 (HTTP)<sup>3</sup>| Fuera | WAN | No |<ul><li>El puerto de salida se usa para tener acceso a Internet para recuperar las actualizaciones.</li><li>El proxy web de salida es configurable por el usuario.</li><li>Para permitir las actualizaciones del sistema, este puerto también debe estar abierto para las direcciones IP fijas del controlador.</li></ul> |
|TCP 443 (HTTPS)<sup>3</sup>| Fuera | WAN | Sí |<ul><li>El puerto de salida se usa para tener acceso a los datos de la nube.</li><li>El proxy web de salida es configurable por el usuario.</li><li>Para permitir las actualizaciones del sistema, este puerto también debe estar abierto para las direcciones IP fijas del controlador.</li></ul>|
|UDP 53 (DNS) | Fuera | WAN | En algunos casos; consulte las notas. |Este puerto es necesario solo si está utilizando un servidor DNS basado en Internet. |
| UDP 123 (NTP) | Fuera | WAN | En algunos casos; consulte las notas. |Este puerto solo es necesario si está utilizando un servidor DNS basado en Internet. |
| TCP 9354 | Fuera | WAN | En algunos casos; consulte las notas. |El dispositivo StorSimple usa el puerto de salida para comunicarse con el servicio StorSimple Manager. Este puerto es necesario si la red actual no admite el uso de HTTP 1.1 para conectarse a Internet; por ejemplo, si utiliza un servidor proxy basado en HTTP 1.0.<br> Si se conecta a través de un servidor proxy, consulte [requisitos del bus de servicio](https://msdn.microsoft.com/library/azure/ee706729.aspx) para obtener información detallada. |
| 3260 (iSCSI) | En el | LAN | No | Este puerto se utiliza para tener acceso a datos a través de iSCSI.|
| 5985 | En el | LAN | No | El puerto de entrada se usa en Snapshot Manager de StorSimple para comunicarse con el dispositivo de StorSimple.<br>También se usa cuando se conecta de forma remota a Windows PowerShell para StorSimple a través de HTTP. |
| 5986 | En el | LAN | No | Este puerto se usa al conectarse de forma remota a Windows PowerShell para StorSimple a través de HTTPS. |

<sup>1</sup> Ningún puerto de entrada debe estar abierto en la red Internet pública.

<sup>2</sup> Si varios puertos llevan una configuración de puerta de enlace, el orden de tráfico enrutado saliente se determinará según el orden de enrutamiento del puerto que se describe a continuación en [Enrutamiento del puerto](#port-routing).

<sup>3</sup> Las direcciones IP fijas del controlador del dispositivo StorSimple deben ser enrutables y capaces de conectarse a Internet. Las direcciones IP fijas se utilizan para el mantenimiento de las actualizaciones del dispositivo. Si los controladores del dispositivo no pueden conectarse a Internet a través de direcciones IP fijas, no podrá actualizar el dispositivo StorSimple.

> [AZURE.IMPORTANT]Asegúrese de que el firewall no modifica ni descifra ningún tráfico SSL entre el dispositivo de StorSimple y Azure.

### Enrutamiento de puerto

El enrutamiento de puerto es diferente según la versión de software que se ejecute en el dispositivo StorSimple.

- Si el dispositivo está ejecutando una versión del software anterior a Update 1, por ejemplo, el administrador global, versión 0.1, 0.2 o 0.3, el enrutamiento de puerto se decidirá del modo indicado a continuación:

     Última interfaz de red de 10 GbE configurada > Otra interfaz de red de 10 GbE > Última interfaz de red de 1 GbE configurada > Otra interfaz de red de 1 GbE

- Si el dispositivo está ejecutando Update 1, a continuación, el enrutamiento del puerto se decidirá del modo indicado a continuación:

     DATA 0 > Última interfaz de red de 10 GbE configurada > Otra interfaz de red de 10 GbE > Última interfaz de red de 1 GbE configurada > Otra interfaz de red de 1 GbE

    En Update 1, se establece la métrica de enrutamiento de DATA 0 más baja; por lo tanto, todo el tráfico en la nube se enruta a través de DATA 0. Tome nota de esto si hay más de una interfaz de red habilitada para la nube en el dispositivo de StorSimple.

### Prácticas recomendadas de redes

Además de los requisitos de redes anteriores, para obtener un rendimiento óptimo de la solución StorSimple, cumpla las siguientes prácticas recomendadas:

- Asegúrese de que el dispositivo StorSimple tenga un ancho de banda de 40 Mbps (o más) disponible en todo momento. Este ancho de banda no debe compartirse con otras aplicaciones.

- Asegúrese de que la conectividad de red a Internet está disponible en todo momento. Las conexiones a Internet esporádicas o poco confiables de los dispositivos, incluida la falta total de conectividad a Internet, dará como resultado una configuración no admitida.

- Aísle el tráfico iSCSI y de la nube mediante interfaces de red específicas en el dispositivo para el acceso a iSCSI y a la nube. Para obtener más información, consulte cómo [modificar interfaces de red](storsimple-modify-device-config.md#modify-network-interfaces) en el dispositivo de StorSimple.

- No utilice una configuración de protocolo de agregación de vínculos (LACP) para las interfaces de red. Se trata de una configuración no admitida.


## Requisitos de alta disponibilidad para StorSimple

La plataforma de hardware que se incluye con la solución de StorSimple tiene características de confiabilidad y disponibilidad que proporcionan la base para una infraestructura de almacenamiento de alta disponibilidad con tolerancia a errores en su centro de datos. Sin embargo, existen requisitos y procedimientos recomendados que debe cumplir para ayudar a garantizar la disponibilidad de la solución Azure StorSimple. Antes de implementar Azure StorSimple, revise cuidadosamente los siguientes requisitos y recomendaciones para el dispositivo StorSimple y los equipos host conectados.

Para obtener más información sobre la supervisión y el mantenimiento de los componentes de hardware del dispositivo de StorSimple, vaya a [Uso del servicio StorSimple Manager para supervisar el estado y los componentes de hardware](storsimple-monitor-hardware-status.md) y [Reemplazo de componentes de hardware de StorSimple](storsimple-hardware-component-replacement.md).

### Requisitos de alta disponibilidad y procedimientos para el dispositivo StorSimple

Revise la siguiente información con cuidado para garantizar la alta disponibilidad del dispositivo StorSimple.

#### PCM

Los dispositivos de StorSimple incluyen módulos de alimentación y refrigeración (PCM) redundantes intercambiables en caliente. Cada PCM tiene suficiente capacidad para proporcionar un servicio para el chasis completo. Para garantizar una alta disponibilidad, debe instalar ambos PCM.

- Conecte los PCM a diferentes fuentes de alimentación para proporcionar disponibilidad si se produce un error en una fuente de alimentación.
- Si se produce un error en un PCM, solicite su sustitución inmediatamente.
- Retire el PCM que presenta el error unicamente cuando tenga el recambio y esté listo para instalarlo.
- No quite los PCM simultáneamente. El módulo PCM incluye el módulo de batería de reserva. La eliminación de los PCM dará lugar a un apagado sin protección de la batería y no se guardará el estado del dispositivo. Para obtener más información sobre la batería, vaya a [Mantenimiento del módulo de batería de reserva](storsimple-battery-replacement.md#maintain-the-backup-battery-module).

#### Módulos de controlador

Los dispositivos StorSimple incluyen módulos de controlador intercambiables en caliente. Los módulos de controlador funcionan de manera activa/pasiva. En un momento dado, un módulo de controlador está activo y proporcionando servicio, mientras que el módulo del controlador permanece pasivo. El módulo de controlador pasivo está encendido y pasa a estar operativo si el módulo del controlador activo falla o es retirado. Cada módulo de controlador dispone de suficiente capacidad para proporcionar un servicio para el chasis completo. Los módulos de controlador deben instalarse para garantizar una alta disponibilidad.

- Asegúrese de que ambos módulos de controlador estén instalados en todo momento.

- Si se produce un error en un módulo de controlador, solicite su sustitución inmediatamente.

- Retire el módulo de controlador que presenta el error unicamente cuando tenga el recambio y esté listo para instalarlo. La extracción de un módulo durante períodos prolongados afectará al flujo de aire y, por tanto, al enfriamiento del sistema.

- Asegúrese de que las conexiones de red a los módulos de controlador sean idénticas, y que las interfaces de red conectadas tengan una configuración de red idéntica.

- Si un módulo del controlador falla o necesita ser sustituido, asegúrese de que el otro módulo del controlador esté en estado activo antes de reemplazar el módulo del controlador que presenta el error. Para comprobar que un controlador está activo, vaya a [Identificación del controlador activo en el dispositivo](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).

- No quite ambos módulos del controlador al mismo tiempo. Si se está efectuando una conmutación por error en un controlador, no apague el módulo del controlador en modo de espera ni lo retire del chasis.

- Después de una conmutación por error del controlador, espere al menos cinco minutos antes de quitar cualquier módulo del controlador.

#### Interfaces de red

Los módulos del controlador del dispositivo StorSimple tienen cada uno cuatro interfaces de red Ethernet de 1 Gigabit y dos de 10 Gigabits.

- Asegúrese de que las conexiones de red a los módulos de controlador sean idénticas y que las interfaces de red interconectadas por el módulo del controlador estén conectadas para disponer de una configuración de red idéntica.

- Cuando sea posible, implemente conexiones de red a través de diferentes conmutadores para garantizar la disponibilidad del servicio si se produce un error del dispositivo de red.

- Antes de desconectar la única o la última interfaz restante compatible con iSCSI (con direcciones IP asignadas), deshabilite la interfaz primero y, a continuación, desconecte los cables. Si la interfaz se desconecta en primer lugar, hará que el controlador activo conmute por error al controlador pasivo. Si el controlador pasivo también tiene sus interfaces correspondientes desconectadas, ambos controladores se reiniciarán varias veces antes de establecerse en un controlador.

- Conecte al menos dos interfaces de datos a la red desde cada módulo del controlador.

- Si ha habilitado las dos interfaces de 10 GbE, impleméntelas en distintos conmutadores.

- Cuando sea posible, use MPIO en servidores para asegurarse de que los servidores pueden tolerar un vínculo, red o errores de interfaz.

Para obtener más información sobre cómo conectar en red su dispositivo para obtener alta disponibilidad y rendimiento, vaya a [Instalación del dispositivo de StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) o [Instalación del dispositivo de StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### SSD y unidades de disco duro

Los dispositivos StorSimple incluyen discos de estado sólido (SSD) y unidades de disco duro (HDD) que están protegidas mediante espacios reflejados y se proporciona una reserva activa para las unidades de disco duro. El uso de espacios reflejados garantiza que el dispositivo sea capaz de tolerar el error de uno o más SSD o unidades de disco duro.

- Asegúrese de que estén instalados todos los módulos SSD y de disco duro.

- Si se produce un error en un PCM o en un disco duro, solicite su sustitución inmediatamente.

- Si un SSD o una unidad de disco duro falla o requiere su sustitución, asegúrese de retirar solo los SSD o unidades de disco duro que requieran ser reemplazados.

- No retire más de un SSD o unidad de disco duro del sistema en un determinado momento. Es posible que un error de 2 o más discos de cierto tipo (disco duro, SSD) o errores consecutivos dentro de un período de tiempo breve provoquen un fallo de funcionamiento en el sistema y una posible pérdida de datos. Si esto ocurre, [póngase en contacto con el servicio de soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para obtener ayuda.

- Durante la sustitución, supervise el **Estado del hardware** en la página **Mantenimiento** para las unidades de SSD y unidades de disco duro. Un estado de verificación verde indica que los discos están en buen estado o correctos, mientras que un signo de exclamación rojo indica un error de SSD o de unidad de disco duro.

- Se recomienda configurar instantáneas en la nube para todos los volúmenes que deberá proteger en caso de que se produzca un error del sistema.

#### Receptáculo EBOD

El modelo 8600 del dispositivo StorSimple incluye un receptáculo Extended Bunch of Disks (EBOD) además del receptáculo principal. Un EBOD contiene controladores EBOD y unidades de disco duro (HDD) que están protegidas mediante espacios reflejados. El uso de espacios reflejados garantiza que el dispositivo sea capaz de tolerar el error de uno o más unidades de disco duro. El receptáculo EBOD está conectado al receptáculo principal a través de cables SAS redundantes.

- Asegúrese de que los módulos del controlador del receptáculo EBOD, los cables SAS y todas las unidades de disco duro estén instalado en todo momento.

- Si se produce un error en un módulo de controlador de receptáculo EBOD, solicite su sustitución inmediatamente.

- Si se produce un error en un módulo de controlador de receptáculo EBOD, asegúrese de que el módulo del controlador esté activo antes de reemplazar el módulo con error. Para comprobar que un controlador está activo, vaya a [Identificación del controlador activo en el dispositivo](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).

- Durante la sustitución de un módulo del controlador EBOD, supervise de manera continua el estado del componente en el servicio StorSimple Manager mediante el acceso a **Mantenimiento** - **Estado del hardware**.

- Si se produce un error en un cable SAS o requiere ser sustituido (el Soporte de Microsoft deberá estar implicado en la toma de dicha determinación), asegúrese de quitar solamente el cable de SAS que deba sustituirse.

- No retire simultáneamente ambos cables SAS del sistema en un determinado momento.

### Recomendaciones de alta disponibilidad para los equipos host

Revise cuidadosamente estos procedimientos recomendados para garantizar la alta disponibilidad de los hosts conectados al dispositivo de StorSimple.

- Configure StorSimple con [configuraciones de clúster de servidor de archivos de dos nodos][1]. Mediante la eliminación de puntos individuales de error y el aumento de la redundancia en el lado del host, la solución completa pasará a presentar una alta disponibilidad.

- Use recursos compatibles de disponibilidad continua (CA) disponibles con Windows Server 2012 (SMB 3.0) para lograr una alta disponibilidad durante la conmutación por error de los controladores de almacenamiento. Para obtener información adicional para configurar los clústeres de servidores de archivos y de recursos compartidos disponibles continuamente con Windows Server 2012, consulte este [vídeo de demostración](http://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## Pasos siguientes

- [Obtenga más información sobre de los límites del sistema StorSimple](storsimple-limits.md).
- [Obtenga más información sobre cómo implementar la solución StorSimple](storsimple-deployment-walkthrough.md).
 
<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx

<!---HONumber=Nov15_HO2-->
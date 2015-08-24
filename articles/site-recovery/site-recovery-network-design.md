<properties
	pageTitle="Consideraciones sobre la infraestructura de red para Site Recovery" 
	description="En este artículo se describen las consideraciones de diseño de red prácticas que hay que tener el cuenta para la conmutación por error con Site Recovery" 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="08/10/2015" 
	ms.author="raynew"/>

#  Consideraciones sobre la infraestructura de red para Site Recovery

El servicio Azure Site Recovery contribuye a una solución sólida de continuidad empresarial y recuperación ante desastres (BCDR) mediante la protección y recuperación de servidores físicos locales y máquinas virtuales con la coordinación y automatización de replicación y conmutación por error en Azure o en centro de datos local secundario.

Este artículo va dirigido al equipo de virtualización responsable de diseñar, implementar y admitir una solución e infraestructura BCDR que incluye System Center VMM y Azure Site Recovery.

## Información general

El propósito de la estrategia de BCDR es mantener sus aplicaciones empresariales en funcionamiento, y restaurar las cargas de trabajo y servicios con errores y para que la organización pueda reanudar rápidamente el funcionamiento normal. El desarrollo de estrategias de recuperación ante desastres es un reto, debido a las dificultades inherentes para predecir imprevistos y los altos costes de la implementación de una protección adecuada contra errores de gran alcance. Azure Site Recovery le ayuda a implementar la protección y la conmutación por error desde el centro de datos principal a un centro de datos secundario (o a Azure) copiando inicialmente (replicando) los datos principales y, a continuación, actualizando periódicamente las réplicas.

Como parte fundamental del plan de BCDR tendrá que definir su objetivo de tiempo de recuperación (RTO) y objetivo de punto de recuperación (RPO) para poder obtener en línea la recuperación de los datos de la organización tan pronto como sea posible (con un RTO bajo) y con pérdida mínima de datos (RPO baja). El diseño de la red de su organización es un posible cuello de botella para el RTO, por lo tanto, un plan de diseño sólido y objetivos de RPO pueden ayudar a evitar este cuello de botella.


Si ha decidido utilizar el servicio de Azure Site Recovery para ayudar a implementar la protección y la conmutación por error, debería tener en cuenta un par de cuestiones importantes acerca del diseño de la red para BCDR:

- **Diseño VMM**: estoy usando System Center VMM. ¿Cómo debo controlar el diseño de red para integrar VMM y Site Recovery?
- **La conectividad después de la conmutación por error**: utilizo Site Recovery para ejecutar una conmutación por error. ¿Cómo puedo asegurarme de que las aplicaciones y cargas de trabajo están disponibles y accesibles una vez completada la conmutación por error?

## Diseño de VMM

Puede disponer Site Recovery en capas sobre el diseño actual de VMM, independientemente de qué diseño se esté utilizando en uno o varios servidores VMM.

### Servidor VMM independiente

En esta topología, implementará un servidor VMM independiente en una máquina virtual del sitio principal y después replicará esta máquina virtual en un sitio secundario con Site Recovery y Hyper-V Replica. Tenga en cuenta que instalar el servidor VMM y su servidor SQL Server compatible en la misma máquina virtual puede reducir el tiempo de inactividad, porque solo una máquina virtual tiene que crear instancias. Cuando el servicio de VMM utiliza un servidor SQL Server remoto necesitará recuperar la instancia de este servidor antes de recuperar el servidor VMM.

Para implementar un solo VMM en una máquina virtual con Hyper-V Replica:

1. Configure el servidor VMM en una máquina virtual con el servidor SQL Server instalado.
2. Agregue los hosts que se administrarán en las nubes en este servidor VMM.
3. Inicie sesión en el portal de Azure y, a continuación, configure la protección para las nubes.
4. Habilite la replicación de todas las máquinas virtuales que deben estar protegidas por el servidor VMM.
5. Vaya a la consola de administrador de Hyper-V, elija Hyper-V Replica y, a continuación, habilite la replicación en la máquina virtual de VMM.
6. Asegúrese de que la máquina virtual de VMM no se agrega a las nubes que están protegidas por el servicio de ASR para que no se reemplace la configuración de replicación de Hyper-V mediante ASR.

En caso de desastre, las cargas de trabajo pueden recuperarse siguiendo estos pasos:

1. Conmute por error la réplica de la máquina virtual de VMM en el sitio de recuperación con el administrador de Hyper-V.
2. Una vez recuperada la máquina virtual de VMM, el usuario puede iniciar sesión en el administrador de recuperación de Hyper-V desde el sitio secundario.
3. Una vez completada la conmutación por error no planeada, los usuarios pueden acceder a todos los recursos en el sitio principal.
4. Tenga en cuenta que la máquina virtual de VMM debe conmutar por error manualmente en el sitio secundario antes de que las cargas de trabajo pueden conmutar por error. 


### Servidor VMM en clúster


[La implementación de VMM en un clúster](https://technet.microsoft.com/es-es/library/gg610675.aspx) proporciona alta disponibilidad y protección contra la conmutación por error de hardware. Si va a implementar el clúster de VMM con Recovery Site tenga en cuenta lo siguiente:

El servidor VMM debe implementarse en un clúster estirado en sitios separados geográficamente. La Base de datos de SQL Server utilizada por VMM debe estar protegida con grupos de disponibilidad AlwaysOn de SQL Server con una réplica en el sitio secundario. Si se produce un desastre, el servidor VMM y su correspondiente SQL Server conmutarán por error automáticamente en el sitio de recuperación. A continuación, puede conmutar las cargas de trabajo con Site Recovery.

![Clúster de VMM estirado](./media/site-recovery-network-design/ASR_NetworkDesign1.png)

## La conectividad después de la conmutación por error

Las aplicaciones modernas hasta cierto punto dependen casi siempre de la red, por lo que desplazar físicamente un servicio de un sitio a otro garantizando que las aplicaciones y cargas de trabajo son accesibles después de la conmutación por error representa un reto de red. Hay dos principales soluciones a este problema:

- **Direcciones IP fijas**: la primera opción consiste en mantener direcciones IP fijas. A pesar de que los servicios cambien de sitio y los servidores de hospedaje se encuentren en distintas ubicaciones físicas, las aplicaciones llevan consigo la configuración de dirección IP a la nueva ubicación.
- **Direcciones IP modificadas**: la segunda opción requiere cambiar la dirección IP durante la transición hacia el sitio recuperado. 

### Opción 1: direcciones IP fijas

Desde una perspectiva de recuperación ante desastres, el método más simple de implementación parece ser utilizar direcciones IP fijas. Sin embargo, existen una serie de retos potenciales que, en la práctica, lo convierten en la opción menos popular. Azure Site Recovery proporciona la función de conservar las direcciones IP en todas las situaciones. Antes de decidirse por esta opción, debe observar qué tener en cuenta en las principales situaciones (subred estirada y conmutación por error de subred).

#### Subred estirada

En una subred estirada, la subred está disponible al mismo tiempo tanto en ubicaciones principales como de destino. En términos simples, esto significa que puede mover un servidor y su configuración de IP (nivel 3) al sitio secundario, y la red enrutará el tráfico a la nueva ubicación automáticamente. Esto es muy fácil de tratar desde la perspectiva del servidor, pero presenta una serie de retos:



- Desde una perspectiva de nivel 2 (nivel de vínculo de datos) requiere equipos de red que puedan administrar una VLAN estirada, aunque este tipo de equipo ya está ampliamente disponible.
- El segundo problema y más difícil es que mediante la expansión de la VLAN el dominio de error potencial se extiende a ambos sitios, básicamente convirtiéndose en un único punto de error. Aunque es poco probable, podría ocurrir que se iniciara una tormenta de difusión y no se pudiera aislar. Hemos visto opiniones diferentes sobre este problema, que van desde una implementación correcta a "nunca".
- Una subred estirada no es posible si está conmutando por error a Azure como sitio de recuperación ante desastres.

#### Conmutación por error de subred

Es posible implementar la conmutación por error de subred con el fin de obtener los beneficios de una subred estirada sin estirarla realmente. Con esta configuración cualquier subred determinada está presente en el sitio 1 o en el sitio 2, pero nunca en ambos sitios simultáneamente. Para mantener el espacio de direcciones IP en el caso de una conmutación por error, puede organizar mediante programación de la infraestructura de enrutador para mover las subredes de un sitio a otro. En una situación de conmutación por error, las subredes se mueven con sus máquinas virtuales protegidas asociadas. El principal inconveniente de esta opción es que en caso de error debe mover la subred completa, lo que podría ser una solución razonable, pero podría afectar las consideraciones de granularidad de conmutación por error.

Veamos cómo una empresa ficticia (Contoso) es capaz de replicar sus máquinas virtuales en una ubicación de recuperación, mientras se conmuta por error toda la subred. Se examinará cómo Contoso es capaz de administrar sus subredes durante la replicación de máquinas virtuales entre dos ubicaciones locales y se explicará el funcionamiento de la conmutación por error de subred cuando Azure se utiliza como sitio de recuperación ante desastres.

##### Por ejemplo, conmutación por error de subred en una empresa
 
- El sitio principal tiene aplicaciones que se ejecutan en la subred 192.168.1.0/24.
- Toda la subred se conmutará por error, y todas las máquinas virtuales en la subred se conmutarán por error en el sitio de recuperación y conservarán sus direcciones IP
- Como se muestra en el diagrama a continuación las rutas entre el sitio principal y el sitio de recuperación, tercer sitio y el sitio principal y tercer sitio y el sitio de recuperación tendrán que modificarse apropiadamente para reflejar el hecho de que todas las máquinas virtuales que pertenecen a la subred 192.168.1.0/24 se han movido al sitio de recuperación.
- Este diagrama asume que:
	-  Cada centro de datos es atendido por su propia instancia de VMM. No habrá ninguna replicación de las bases de datos de System Center VMM entre centros de datos.
	-  Cada centro de datos utiliza direcciones IP estáticas para las máquinas virtuales.
	-  La conectividad entre los centros de datos es mediante un circuito dedicado y no mediante conexiones VPN a través de Internet.

**Antes de la conmutación por error**

![Antes de la conmutación por error](./media/site-recovery-network-design/ASR_NetworkDesign2.png)

**Después de la conmutación por error**

![Después de la conmutación por error](./media/site-recovery-network-design/ASR_NetworkDesign3.png)


Al habilitar la protección de una máquina virtual específica, Site Recovery asigna recursos de red de la siguiente forma:

1. Site Recovery asigna una dirección IP a cada interfaz de red en la máquina virtual del grupo de direcciones IP estáticas definido en la red correspondiente para cada instancia de VMM.
2. Si el administrador define el mismo conjunto de direcciones IP que se utiliza en el sitio principal para la red en el sitio de recuperación, Site Recovery asignará la misma dirección IP que se ha asignado a la máquina virtual principal, a la máquina virtual de la réplica. La dirección IP está reservada en VMM pero no se ha configurado como la dirección IP de conmutación por error. La dirección IP de conmutación por error se establece justo antes de la conmutación por error. Esta captura de pantalla muestra la configuración de TCP/IP para la máquina virtual de réplica de la conmutación por error (en la consola de Hyper-V). Estos valores de configuración se replican justo entre los inicios de conmutación por error de la máquina virtual.

	![Establecer dirección IP](./media/site-recovery-network-design/ASR_NetworkDesign4.png)

3. Si no está disponible la misma dirección IP, Site Recovery asignará una dirección diferente del grupo.
4. Después de habilitar la protección de la máquina virtual, puede utilizar el siguiente script de ejemplo para comprobar la dirección IP que se ha asignado a la máquina virtual. La misma dirección IP se establecería como dirección IP de conmutación por error y se asignaría a la máquina virtual en el momento de la conmutación por error.

    $vm = Get-SCVirtualMachine -Name $na = $vm[0].VirtualNetworkAdapters $ip = Get-SCIPAddress -GrantToObjectID $na[0].id $ip.address

Tenga en cuenta que si las máquinas virtuales usan DHCP, la administración de direcciones IP no se controla mediante Site Recovery. Deberá asegurarse de que el servidor DHCP que asigne direcciones IP en el sitio de recuperación pueda asignar direcciones del mismo rango que el sitio principal.

##### Por ejemplo, la conmutación por error de subredes en Azure

Si está conmutando por error en Azure existen algunas restricciones más. Veamos una empresa ficticia (Woodgrove Bank) que tiene una infraestructura local que hospeda su línea de aplicaciones empresariales y Azure hospeda sus aplicaciones móviles.


- La conectividad entre las máquinas virtuales de Woodgrove Bank en servidores locales y Azure se realiza a través de una VPN de sitio a sitio, que muestra la red virtual en Azure como extensión de la red local de Woodgrove Bank. 
- Woodgrove desea usar Site Recovery para replicar sus cargas de trabajo locales en Azure. 
- Woodgrove tiene que tratar con aplicaciones y configuraciones que dependen de direcciones IP codificadas de forma rígida, por lo que necesitan conservar las direcciones IP para sus aplicaciones después de la conmutación por error en Azure.
- La administración de la infraestructura local de Woodgrove Bank la lleva a cabo un servidor de VMM 2012 R2.
- Hay una red lógica basada en VLAN (red de aplicaciones) que se ha creado en el servidor VMM.![Red lógica](./media/site-recovery-network-design/ASR_NetworkDesign5.png)
- Una red de máquina virtual (red de máquinas virtuales de aplicación) se ha creado por medio de la red lógica.![Red de máquina virtual](./media/site-recovery-network-design/ASR_NetworkDesign6.png)
- Todas las máquinas virtuales en la aplicación utilizan direcciones IP estáticas, por lo que también se define un grupo IP estático para la red lógica. 
- Woodgrove está asignando direcciones IP del rango de direcciones IP (172.16.1.0/24, 172.16.2.0/24) a los recursos que se ejecutan en Azure.


Para que Woodgrove implemente la replicación y mantenga las direcciones IP se requiere lo siguiente:

- Una máquina virtual de Azure que es una extensión de la red local para que las aplicaciones puedan conmutar por error sin problemas.
- Tenga en cuenta que al establecer la conexión de sitio a sitio en Azure, una red de Azure le permite enrutar el tráfico a la ubicación local (Azure lo llama red local) solo si el intervalo de direcciones IP es diferente del intervalo de direcciones IP locales, ya que Azure no admite la expansión de las subredes. Esto significa que si tiene una subred local 192.168.1.0/24, no puede agregar una red local 192.168.1.0/24 en la red de Azure. Esto es normal, ya que Azure no sabe que no hay ninguna máquina virtual activa en la subred y que se está creando la subred solo para fines de recuperación ante desastres. Para poder distribuir correctamente el tráfico de red de una red Azure, las subredes de la red y la red local no deben estar en conflicto. 
- Necesitaremos crear una red adicional en Azure (red de recuperación) donde se crearán las máquinas virtuales que conmuten por error .

	![Redes de Azure](./media/site-recovery-network-design/ASR_NetworkDesign7.png)

- Para asegurarse de que la dirección IP de la máquina virtual se conserva, en propiedades de la máquina virtual de Site Recovery especificaremos que debe utilizarse la misma dirección IP. A continuación, después de la conmutación por error, Site Recovery asignará la dirección IP especificada a la máquina virtual.![Redes de Azure](./media/site-recovery-network-design/ASR_NetworkDesign8.png)


- Cuando se activa la conmutación por error y las máquinas virtuales se crean en la red de recuperación con la dirección IP necesaria, es posible establecer la conectividad a la máquina virtual utilizando una. Esta acción puede ejecutarse mediante script. Como se explicó en la sección anterior acerca de la conmutación por error de subred, también en el caso de conmutación por error en Azure las rutas tendrían que modificarse apropiadamente para reflejar que 192.168.1.0/24 ahora se ha movido a Azure.![Redes de Azure](./media/site-recovery-network-design/ASR_NetworkDesign9.png)

### Opción 2: direcciones IP modificadas

Esta opción parece ser la más habitual y significa que la dirección IP de cada una de las máquinas virtuales que conmute por error cambiará. La principal desventaja de esta opción es que la infraestructura de red debe ser consciente de que las direcciones IP han cambiado y que normalmente tendrán que cambiarse las entradas DNS o vaciar éstas en toda la red, de la misma forma que las entradas almacenadas en caché de las tablas de red. Esto podría suponer tiempo de inactividad dependiendo de cómo la infraestructura DNS esté configurada. Estos problemas se pueden mitigar mediante valores de TTL bajos en el caso de aplicaciones de intranet y mediante [Azure Traffic Manager con Site Recovery](http://azure.microsoft.com/blog/2015/03/03/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) para aplicaciones basadas en Internet.

#### Ejemplo: direcciones IP modificadas

Veamos esta situación con un ejemplo que presenta un tercer sitio desde el cual se puede tener acceso a las aplicaciones alojadas en el sitio principal o en el sitio de recuperación.

![Redes de Azure](./media/site-recovery-network-design/ASR_NetworkDesign10.png)

- Algunas aplicaciones se hospedan en la subred 192.168.1.0/24 del sitio principal, y se han configurado para actuar en el sitio de recuperación en la subred 172.16.1.0/24 después de una conmutación por error.
- Las rutas de red/conexiones de VPN se han configurado correctamente de forma que los tres sitios son accesibles entre sí. 
- Después de la conmutación por error de una o varias aplicaciones, éstas se restaurarán en la subred de recuperación. En este caso no necesitamos conmutar por error toda la subred al mismo tiempo y no se requiere ningún cambio para volver a configurar las rutas de red o VPN.
- Una conmutación por error y algunas actualizaciones DNS mantendrán las aplicaciones accesibles. Si el servidor DNS está configurado para permitir actualizaciones dinámicas, entonces las máquinas virtuales se registrarán a sí mismas con la nueva dirección IP, una vez que se inician después de una conmutación por error.

	![Redes de Azure](./media/site-recovery-network-design/ASR_NetworkDesign11.png)

- Después de la conmutación por error, es posible que la máquina virtual de réplica tenga una dirección IP que no es la misma que la dirección IP de la máquina virtual principal.
- Las máquinas virtuales actualizarán el servidor DNS que están utilizando después de iniciarse. Las entradas DNS normalmente tendrán que cambiarse o vaciarse en toda la red y las entradas en caché de las tablas de red tienen que actualizarse o vaciarse, por lo que no es raro tener que hacer frente al tiempo de inactividad mientras se producen estos cambios de estado. Esto se puede mitigar mediante lo siguiente:

	- Utilizando valores de TTL bajos para las aplicaciones de intranet.
	- Utilizando [Azure Traffic Manager con Site Recovery] (http://azure.microsoft.com/blog/2015/03/03/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/ para aplicaciones basadas en Internet).
	- Por medio del siguiente script en el plan de recuperación para actualizar el servidor DNS y garantizar la oportuna actualización (el script no es necesario si se configura el registro DNS dinámico)

    [string]$Zone, [string]$name, [string]$IP ) $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name $newrecord = $record.clone() $newrecord.RecordData[0].IPv4Address = $IP Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

#### Por ejemplo: conmutación por error en Azure

La [entrada de blog](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) sobre la configuración de la infraestructura de redes de Azure como sitio de recuperación ante desastres explica cómo configurar la infraestructura de red de Azure apropiada cuando no es imprescindible conservar las direcciones IP. Comienza describiendo la aplicación y, a continuación, se examina cómo configurar redes locales y en Azure. Concluye con instrucciones para ejecutar una prueba de conmutación por error y una conmutación por error planeada.

## Pasos siguientes

[Aprenda](site-recovery-network-mapping.md) cómo Site Recovery asigna las redes de origen y de destino.

<!---HONumber=August15_HO7-->
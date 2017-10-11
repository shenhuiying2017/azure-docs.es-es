---
title: "Diseño de una infraestructura de red para la recuperación ante desastres | Microsoft Docs"
description: "En este artículo se describen las consideraciones de diseño de red para Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: prateek9us
manager: jwhit
editor: 
ms.assetid: ce787731-d930-4f00-a309-e2fbc2e1f53b
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/20/2017
ms.author: pratshar
ms.openlocfilehash: 90ffd3dd1cf5068359afa1b60892cdee43ec0658
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="designing-your-network-for-disaster-recovery"></a>Diseñe su red para la recuperación ante desastres.

Este artículo va dirigido a los profesionales de TI responsables de la elaboración, implementación y soporte técnico de la infraestructura de continuidad empresarial y recuperación ante desastres (BCDR), y que desean sacar provecho de Microsoft Azure Site Recovery (ASR) para dar soporte técnico a sus servicios de BCDR y mejorarlos. En este artículo se tratan consideraciones prácticas para estructurar la red del sitio de recuperación ante desastres, ya sea en Azure o en otro sitio local. 

## <a name="overview"></a>Información general
<seg>
  [Azure Site Recovery (ASR)](https://azure.microsoft.com/services/site-recovery/) es un servicio de Microsoft Azure que organiza la protección y recuperación de las aplicaciones virtualizadas con fines de continuidad empresarial y recuperación ante desastres (BCDR)..</seg> El fin de este documento es guiar al lector a través del proceso de diseño de las redes, y se centra en la elaboración de intervalos IP y subredes en el sitio de recuperación ante desastres, al replicar máquinas virtuales (VM) mediante Site Recovery.

Además, este artículo muestra la forma en que Site Recovery permite elaborar e implementar un centro de datos virtual multisitio para admitir los servicios BCDR en pruebas o ante un desastre.

En un mundo en el que todos esperan conectividad ininterrumpida, es más importante que nunca mantener la infraestructura y las aplicaciones en funcionamiento. El propósito de continuidad del negocio y recuperación ante desastres (BCDR) es restaurar componentes con errores para que la organización puede reanudar rápidamente las operaciones normales. El desarrollo de estrategias de recuperación ante desastres para tratar los eventos improbables y devastadores es muy complicado. Esto se debe a la dificultad inherente de predecir el futuro, especialmente en lo referente a eventos improbable, y al alto costo de proporcionar las medidas adecuadas de protección contra catástrofes de largo alcance.

Es crucial para la planeamiento de BCDR que Objetivo de tiempo de recuperación (RTO) y Objetivo de punto de recuperación (RPO) se definan como parte de un plan de recuperación ante desastres. Cuando se produzca un desastre en el centro de datos del cliente, con Azure Site Recovery, los clientes pueden poner en línea rápidamente (RTO bajo) sus máquinas virtuales replicadas ubicados en el centro de datos secundario o en Microsoft Azure con pérdida mínima de datos (RPO bajo).

La conmutación por error se realiza gracias a ASR, que inicialmente copia las máquinas virtuales designadas del centro de datos principal al centro de datos secundario o a Azure (según el escenario) y luego actualiza periódicamente las réplicas. Durante el planeamiento de la infraestructura, el diseño de red debe considerarse un potencial cuello de botella que puede evitar que cumpla los objetivos de RTO y RPO de la compañía.  

Cuando los administradores planean implementar una solución de recuperación ante desastres, una de las preguntas claves que se plantean es cómo se podrá acceder a la máquina virtual una vez que se complete la conmutación por error. ASR permite al administrador elegir la red a la que se conectará una máquina virtual después de la conmutación por error. Si el sitio principal es Azure o se encuentra en un sitio local administrado por un servidor VMM, esto se realiza mediante la asignación de red. Más información sobre [asignación de red en Azure a Azure DR](site-recovery-network-mapping-azure-to-azure.md) y [asignación de red de VMM](site-recovery-network-mapping.md)


Al diseñar la red del sitio de recuperación, el administrador tiene dos opciones:

* Utilizar un intervalo de direcciones IP diferente para la red del sitio de recuperación. En este escenario, después de la conmutación por error la máquina virtual obtendrá una nueva dirección IP y el administrador tendría que realizar una actualización de DNS. 
* Utilizar el mismo intervalo de direcciones IP diferente para la red del sitio de recuperación. En ciertos escenarios, los administradores prefieren conservar las direcciones IP que tienen en el sitio principal, incluso después de la conmutación por error. En un escenario normal, los administradores tendrían que actualizar las rutas para que indiquen la nueva ubicación de las direcciones IP. Pero en el escenario en el que hay una subred ampliada entre el sitio principal y el de recuperación, conservar la dirección IP de las máquinas virtuales se convierte en una opción atractiva. No es posible expandir una subred de una red local a una red de Azure o entre dos redes de Azure.  

Cuando los administradores planean implementar una solución de recuperación ante desastres, una de las preguntas claves que se plantean es cómo se podrá acceder a las aplicaciones una vez que se complete la conmutación por error. Las aplicaciones modernas casi siempre dependen hasta cierto punto de las redes, por lo que mover físicamente un servicio de un sitio a otro representa un reto de redes. Hay dos formas principales de abordar este problema en las soluciones de recuperación ante desastres. El primer enfoque es mantener direcciones IP fijas. A pesar de que los servicios cambien de sitio y los servidores de hospedaje se encuentren en distintas ubicaciones físicas, las aplicaciones llevan consigo la configuración de dirección IP a la nueva ubicación. El segundo enfoque implica cambiar totalmente la dirección IP durante la transición hacia el sitio recuperado. Cada enfoque tiene distintas variaciones de implementación, que se resumen a continuación.

Al diseñar la red del sitio de recuperación, el administrador tiene dos opciones:

## <a name="option-1-retain-ip-addresses"></a>Opción 1: Conservar las direcciones IP
Desde la perspectiva del proceso de recuperación ante desastres, el uso de una dirección IP fija parece ser el método más fácil de implementar, pero tiene varios retos potenciales que, en la práctica, hacen que sea el enfoque menos popular. Azure Site Recovery proporciona la función de conservar las direcciones IP en todas las situaciones. Antes de decidir conservar la IP, es preciso pensar en las restricciones que impone a las funcionalidades de la conmutación por error. Examinemos los factores que pueden ayudarle a decidir si conserva las direcciones IP, o no. Esto se puede realizar de dos maneras: mediante una subred estirada o mediante una conmutación por error de subred completa.

### <a name="stretched-subnet"></a>Subred estirada
Aquí la subred está disponible simultáneamente en la ubicación principal y en la de recuperación ante desastres. En términos simples, esto significa que puede mover un servidor y su configuración de IP (nivel 3) al segundo sitio, y la red enrutará el tráfico automáticamente a la nueva ubicación. Esto es muy fácil de tratar desde la perspectiva del servidor, pero presenta una serie de retos:

* Desde una perspectiva del nivel 2 (nivel de vínculo de datos) requerirá equipamiento de redes que pueda administrar una VLAN estirada, pero esto es cada vez menos problemático ya que en la actualidad está ampliamente disponible. El segundo problema, y el más difícil de resolver, es que mediante la ampliación de la VLAN el dominio de error potencial se extiende a ambos sitios, con lo que se convierte en un único punto de error. Aunque es improbable que suceda, lo que ha ocurrido es que se ha iniciado una tormenta de difusión que se ha podido aislar. Hay distintas opiniones sobre este problema y unos han realizado muchas implementaciones correctas, mientras que otros han indicado que nunca implementarán esta tecnología.
* La subred ampliada no es posible si se usa Microsoft Azure como sitio de recuperación ante desastres.

### <a name="subnet-failover"></a>Conmutación por error de subred
Es posible implementar la conmutación por error de subred para obtener los beneficios de la solución de subred ampliada descritos anteriormente sin ampliar la subred entre varios sitios. Aquí, cualquier subred determinada estaría presente en el sitio 1 o en el sitio 2, pero nunca en ambos sitios simultáneamente. Para mantener el espacio de direcciones IP en caso de una conmutación por error, es posible organizar mediante programación la infraestructura del enrutador para mover las subredes de un sitio al otro. En un escenario de conmutación por error, las subredes se moverían con las máquinas virtuales protegidas asociadas. La principal desventaja de este opción es que en caso de error es preciso mover toda la subred, lo que puede ser razonable, pero puede afectar a las consideraciones de granularidad de la conmutación por error.

Examinemos cómo una empresa ficticia denominada Contoso puede replicar sus máquinas virtuales en una ubicación de recuperación mientras se conmuta por error toda la subred. En primer lugar, se verá cómo Contoso puede administrar sus subredes durante la replicación de máquinas virtuales entre dos ubicaciones locales y se explicará el funcionamiento de la conmutación por error de subred cuando [Azure se usa como sitio de recuperación ante desastres](#failover-to-azure).

#### <a name="failover-from-on-premises-to-azure"></a>Recuperación del entorno local a Azure 
Azure Site Recovery (ASR) permite que Azure se utilice como sitio de recuperación ante desastres para las máquinas virtuales.  

Examinemos un escenario en el que una compañía ficticia denominada Woodgrove Bank tiene una infraestructura local que hospeda su línea de aplicaciones empresariales y que hospeda sus aplicaciones móviles en Azure. La conectividad entre las máquinas virtuales de Woodgrove Bank en los servidores locales y de Azure se proporciona mediante una red privada virtual (VPN) de sitio a sitio (S2S) o mediante ExpressRoute. La VPN de S2S permite que la red virtual de Woodgrove Bank que se encuentra en Azure se considere una extensión de la red local de Woodgrove Bank. Esta comunicación la habilita una VPN de S2S entre el borde de Woodgrove Bank y la red virtual de Azure. Ahora, Woodgrove desea usar ASR para replicar sus cargas de trabajo ejecutando la región de Azure principal en otra región de Azure. Esta opción cubre las necesidades de Woodgrove, que desea una opción de recuperación ante desastres que sea económica y capaz de almacenar datos en entornos de nube pública. Woodgrove tiene que tratar con aplicaciones y configuraciones que dependen de direcciones IP codificadas de forma rígida, de ahí que tengan el requisito de conservar las direcciones IP de sus aplicaciones después de la conmutación por error a otra región de Azure.

Woodgrove ha decidido asignar direcciones IP del intervalo (172.16.1.0/24, 172.16.2.0/24) a sus recursos que se ejecutan en Azure.

Para que Woodgrove pueda replicar sus máquinas virtuales en Azure sin perder las direcciones IP, es preciso crear una Red virtual de Azure. Debe ser una extensión de la red local, con el fin de que las aplicaciones puedan conmutar por error desde el sitio local a Azure sin ningún problema. Azure permite agregar conectividad VPN tanto de sitio a sitio como de punto a sitio a las redes virtuales creadas en Azure. Al configurar una conexión de sitio a sitio, una red de Azure solo permite enrutar el tráfico a la ubicación local (Azure la llama red local) si el intervalo de direcciones IP es diferente del intervalo de direcciones IP locales, ya que Azure no admite la ampliación de subredes.  Esto significa que si tiene una subred local 192.168.1.0/24, no puede agregar una red local 192.168.1.0/24 en la red de Azure. Esto es normal, ya que Azure no sabe que no hay ninguna máquina virtual activa en la subred y que se está creando la subred solo para fines de recuperación ante desastres. Para poder distribuir correctamente el tráfico de red de una red Azure, las subredes de la red y la red local no deben estar en conflicto.

![Antes de la conmutación por error de la subred](./media/site-recovery-network-design/network-design7.png)

Antes de la conmutación por error

Para ayudar a Woodgrove a cumplir sus requisitos empresariales, es preciso implementar los flujos de trabajo siguientes:

* Cree una red adicional, que se denominará Red de recuperación, donde se crearán las máquinas virtuales en que se ha producido la conmutación por error.
* Para asegurarse de que la dirección IP de una máquina virtual se conserva después de una conmutación por error, vaya a la pestaña Configurar de las propiedades de la máquina virtual, especifique la misma IP que la máquina virtual tiene localmente y haga clic en Guardar. Cuando se produzca la conmutación por error de la máquina virtual, Azure Site Recovery asignará a la máquina virtual la IP proporcionada.

![Propiedades de red](./media/site-recovery-network-design/network-design8.png)

Una vez que se desencadena la conmutación por error y que las máquinas virtuales se crean en la Red de recuperación con la IP deseada, se puede establecer la conectividad con esta red mediante una [conexión de red virtual a red virtual](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Si se requiere, esta acción puede ejecutarse mediante script.  Como se explicó en la sección anterior acerca de la conmutación por error de subred, incluso en el caso de conmutación por error en Azure las rutas tendrían que modificarse apropiadamente para reflejar que 192.168.1.0/24 se ha movido a Azure.

![Después de la conmutación por error de la subred](./media/site-recovery-network-design/network-design9.png)

Después de la conmutación por error

Si no tiene una ‘red de Azure' como se muestra en la imagen anterior. Puede crear una conexión VPN de sitio a sitio entre el 'Sitio principal' y la ‘Red de recuperación’ después de la conmutación por error.  


#### <a name="failover-to-a-secondary-on-premises-site"></a>Conmutación por error a un sitio local secundario
Examinemos un escenario en el que deseamos conservar la dirección IP de cada una de las máquinas virtuales y conmutar por error la subred completa. El sitio principal tiene aplicaciones que se ejecutan en la subred 192.168.1.0/24. Cuando se produce la conmutación por error, todas las máquinas virtuales que formen parte de esta subred se conmutarán por error en el sitio de recuperación y conservarán sus direcciones IP. Las rutas tendrán que modificarse correctamente para reflejar el hecho de que todas las máquinas virtuales que pertenezcan a la subred 192.168.1.0/24 se han movido al sitio de recuperación.

En la siguiente ilustración, las rutas entre el sitio principal y el sitio de recuperación, el tercer sitio y el sitio principal, y el tercer sitio y el sitio de recuperación tendrán que modificarse correctamente.

Las siguientes imágenes muestran las subredes antes de la conmutación por error. La subred 192.168.0.1/24 está activa en el sitio principal antes de la conmutación por error y se activa en el sitio de recuperación después de la conmutación por error

![Antes de la conmutación por error](./media/site-recovery-network-design/network-design2.png)

Antes de la conmutación por error

La siguiente imagen muestra las redes y subredes después de la conmutación por error.

![Después de la conmutación por error](./media/site-recovery-network-design/network-design3.png)

Después de la conmutación por error

Si el sitio secundario es local y está usando un servidor VMM para administrarlo, al habilitar la protección de una máquina virtual específica, ASR asignará los recursos de red según el flujo de trabajo siguiente:

* ASR asigna una dirección IP a cada interfaz de red de la máquina virtual del grupo de direcciones IP estáticas definido en la red pertinente para cada instancia de System Center VMM.
* Si el administrador define el mismo grupo de direcciones IP para la red del sitio de recuperación que para el grupo de direcciones IP de la red del sitio principal, mientras que la asignación de la dirección IP a la máquina virtual de réplica haría que ASR asignara la misma dirección IP que la máquina virtual principal.  La IP está reservada en VMM, pero no se ha establecido como una IP de conmutación por error en el host Hyper-v. La IP de conmutación por error en un host Hyper-v se establece inmediatamente antes de la conmutación por error.


Si no está disponible la misma IP, ASR asignaría otra dirección IP disponible en el grupo de direcciones IP definido.

Después de habilitar la máquina virtual por protección puede utilizar el siguiente script de ejemplo para comprobar la IP que se ha asignado a la máquina virtual. La misma IP se establecería como IP de conmutación por error y se asignaría a la máquina virtual en el momento de la conmutación por error:

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

> [!NOTE]
> En el escenario en que las máquinas virtuales usan DHCP, la administración de las direcciones IP está completamente fuera del control de ASR. Un administrador tiene que asegurarse de que el servidor DHCP que sirve las direcciones IP del sitio de recuperación pueda servir desde el mismo intervalo que el sitio principal.
>
>



## <a name="option-2-changing-ip-addresses"></a>Opción 2: Cambio de las direcciones IP
Por lo que hemos visto, este enfoque parece ser el más frecuente. Adopta la forma de cambiar la dirección IP de todas las máquinas virtuales implicadas en la conmutación por error. Una desventaja de este enfoque requiere que la red entrante 'aprenda' que la aplicación que estaba en IPx está ahora en IPy. Aunque IPx e IPy sean nombres lógicos, las entradas de DNS normalmente tienen que cambiarse o vaciarse en toda la red, y las entradas de la memoria caché de las tablas de red tienen que actualizarse o vaciarse; por consiguiente, podría producirse un tiempo de inactividad en función de cómo se haya configurado la infraestructura de DNS. Estos problemas se pueden mitigar con el uso de valores de TTL bajos en el caso de las aplicaciones de intranet y mediante [Azure Traffic Manager con ASR](http://azure.microsoft.com/blog/2015/03/03/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) en las aplicaciones basadas en Internet.

### <a name="changing-the-ip-addresses---illustration"></a>Cambio de las direcciones IP: ilustración
Examinemos el escenario donde planea usar varias IP en los sitios principal y de recuperación. En el siguiente ejemplo también hay un tercer sitio desde el que se puede acceder a las aplicaciones hospedadas en los sitios principal o de recuperación.

![Otra dirección IP: antes de la conmutación por error](./media/site-recovery-network-design/network-design10.png)


En la imagen anterior hay algunas aplicaciones que se hospedan en la subred 192.168.1.0/24 del sitio principal que se han configurado para actuar en el sitio de recuperación en la subred 172.16.1.0/24 después de una conmutación por error. Las rutas de red/conexiones de VPN se han configurado correctamente de forma que los tres sitios son accesibles entre sí.

Como muestra la imagen anterior, después de la conmutación por error de una o varias aplicaciones, se restaurarán en la subred de recuperación. En este caso, no estamos limitados a conmutar por error toda la red al mismo tiempo. Se requieren cambios para volver a configurar la VPN o las rutas de red. Una conmutación por error y algunas actualizaciones de DNS garantizarán que las aplicaciones seguirán siendo accesibles. Si el DNS está configurado para permitir actualizaciones dinámicas, las máquinas virtuales se registrarán a sí mismas con la nueva IP una vez que se inician después de una conmutación por error.

![Otra dirección IP: después de la conmutación por error](./media/site-recovery-network-design/network-design11.png)


Después de la conmutación por error, es posible que la máquina virtual de réplica tenga una dirección IP que no sea la misma que la de la máquina virtual principal. Las máquinas virtuales actualizarán el servidor DNS que están utilizando después de iniciarse. Las entradas DNS normalmente tendrán que cambiarse o vaciarse en toda la red y las entradas en caché de las tablas de red tienen que actualizarse o vaciarse, por lo que no es raro tener que hacer frente al tiempo de inactividad mientras se producen estos cambios de estado. Este problema se puede mitigar con las acciones siguientes:

* Utilizando valores de TTL bajos para las aplicaciones de intranet.
* Utilizando el Administrador de tráfico de Azure con ASR para aplicaciones basadas en Internet.
* Por medio del siguiente script en el plan de recuperación para actualizar el servidor DNS y garantizar la oportuna actualización (el script no es necesario si se configura el registro DNS dinámico)

        param(
        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

### <a name="changing-the-ip-addresses--dr-to-azure"></a>Cambio de las direcciones IP: recuperación ante desastres en Azure
La entrada de blog [Networking Infrastructure Setup for Microsoft Azure as a Disaster Recovery Site](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) (Configuración de la infraestructura de redes de Microsoft Azure como sitio de recuperación ante desastres) explica que configurar la infraestructura de red de Azure requerida al conservar las direcciones IP no es un requisito. Dicha entrada comienza con la descripción de la aplicación y luego examina cómo configurar las redes locales y en Azure, y concluye con la forma de realizar una conmutación por error de prueba y una conmutación por error planeada.

## <a name="next-steps"></a>Pasos siguientes
[Obtenga información](site-recovery-vmm-to-vmm.md#prepare-for-network-mapping) sobre cómo Site Recovery asigna las redes de origen y de destino cuando se usa un servidor VMM para administrar el sitio principal.

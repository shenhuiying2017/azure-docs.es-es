
<properties 
   pageTitle="Información general sobre el equilibrador de carga accesible desde Internet | Microsoft Azure"
   description="Información general sobre el equilibrador de carga accesible desde Internet y sus características. Cómo funciona un Equilibrador de carga de Azure mediante máquinas virtuales y servicios en la nube."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/16/2015"
   ms.author="joaoma" />


# Equilibrador de carga accesible desde Internet entre varias máquinas virtuales o servicios

Uno de los usos de los extremos es la configuración del Equilibrador de carga de Azure para distribuir un tipo específico de tráfico entre varios servicios o máquinas virtuales. Por ejemplo, puede extender la carga del tráfico de solicitudes web entre varios servidores web o roles web.

El Equilibrador de carga de Azure asigna la dirección IP y el número de puerto públicos del tráfico entrante a la dirección IP y el número de puerto privados de la máquina virtual, y viceversa, para el tráfico de respuesta desde la máquina virtual.

>[AZURE.NOTE]Al configurar el equilibrio de carga del tráfico entre varias máquinas virtuales o servicios mediante la configuración predeterminada, se proporcionará una distribución aleatoria del tráfico entrante. Si busca la afinidad de sesión, consulte [modo de distribución de equilibrador de carga](load-balancer-distribution-mode.md)

Para los servicios en la nube que contienen instancias de roles web o de roles de trabajo, puede definir un extremo público en la definición de servicio (.csdef).
 
El archivo servicedefinition.csdef contendrá la configuración del extremo, y cuando tenga varias instancias de rol para una implementación de rol web o de trabajo, el equilibrador de carga se configurará para ello. La forma de agregar instancias a su implementación en la nube está cambiando el recuento de instancias en el archivo de configuración de servicio (.csfg).

En la siguiente ilustración se muestra un extremo con equilibrio de carga para el tráfico web cifrado que se comparte entre tres máquinas virtuales en el puerto TCP público y privado de 443. Estas tres máquinas virtuales se encuentran en un conjunto con equilibrio de carga.


![ejemplo de equilibrador de carga público](./media/load-balancer-internet-overview/IC727496.png))



Cuando los clientes de Internet envían solicitudes de página web a la dirección IP pública del servicio en la nube y el puerto TCP 443, el equilibrador de carga realiza un equilibrio de carga basado en hash de esas solicitudes entre las tres máquinas virtuales del conjunto con equilibrio de carga. Puede obtener más información sobre el algoritmo del equilibrador de carga en [Página de información general del equilibrador de carga](load-balancer-overview#load-balancer-features).


## Pasos siguientes

[Información general sobre el equilibrador de carga interno](load-balancer-internal-overview.md)

[Introducción a la configuración de un equilibrador de carga accesible desde Internet](load-balancer-internet-getstarted.md)

[Configuración de un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)


 

<!---HONumber=Oct15_HO4-->
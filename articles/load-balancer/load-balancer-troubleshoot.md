---
title: "Solución de problemas de Azure Load Balancer | Microsoft Docs"
description: "Solución de problemas conocidos de Azure Load Balancer"
services: load-balancer
documentationcenter: na
author: RamanDhillon
manager: timlt
editor: 
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: bc059221656a695bb43af0dca06df941ca77c73d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-load-balancer"></a>Solución de problemas de Azure Load Balancer

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

En esta página se proporcionan soluciones de problemas para preguntas frecuentes sobre Azure Load Balancer. Cuando el equilibrador de carga no tenga conectividad, los síntomas más comunes son los siguientes: 
- Las máquinas virtuales detrás del equilibrador de carga no responden a los sondeos de estado 
- Las máquinas virtuales detrás del equilibrador de carga no responden al tráfico del puerto configurado

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Síntoma: las máquinas virtuales detrás del equilibrador de carga no responden a los sondeos de estado
Para que los servidores back-end participen en el conjunto del equilibrador de carga, deben pasar la comprobación del sondeo. Para más información sobre los sondeos de estado, consulte [Descripción de los sondeos del equilibrador de carga](load-balancer-custom-probe-overview.md). 

Es posible que las máquinas virtuales del grupo de back-end del equilibrador de carga no responda a los sondeos por alguno de los motivos siguientes: 
- Que una máquina virtual del grupo de back-end del equilibrador de carga esté dañada 
- Que una máquina virtual del grupo de back-end del equilibrador de carga no escuche en el puerto de sondeo 
- Que el firewall o un grupo de seguridad de red esté bloqueando el puerto de las máquinas virtuales del grupo de back-end del equilibrador de carga 
- Otros errores de configuración del equilibrador de carga

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Motivo 1: una máquina virtual del grupo de back-end del equilibrador de carga está dañada 

**Validación y resolución**

Para resolver este problema, inicie sesión en las máquinas virtuales participantes, compruebe si el estado de la máquina virtual es correcto y responde a **PsPing** o **TCPing** de otra máquina virtual del grupo. Si la máquina virtual está dañada o no responde al sondeo, debe corregir el problema y reparar la máquina virtual para que pueda volver a participar en el equilibrio de carga.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Motivo 2: una máquina virtual del grupo de back-end del equilibrador de carga no escucha en el puerto de sondeo
Si la máquina virtual está correcta, pero no responde al sondeo, una razón podría ser que el puerto de sondeo no esté abierto en la máquina virtual participante o que esta no escuche en ese puerto.

**Validación y resolución**

1. Inicie sesión en la máquina virtual de back-end. 
2. Abra un símbolo del sistema y ejecute el siguiente comando para validar que existe una aplicación de escucha en el puerto de sondeo:   
            netstat -an
3. Si el estado del puerto no aparece como **LISTENING** (ESCUCHANDO), configure el puerto correcto. 
4. Como alternativa, seleccione otro puerto que aparezca como **LISTENING** (ESCUCHANDO) y actualice en consecuencia la configuración del equilibrador de carga.              

###<a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Motivo 3: el firewall o un grupo de seguridad de red está bloqueando el puerto de las máquinas virtuales del grupo de back-end del equilibrador de carga  
Si el firewall de la máquina virtual está bloqueando el puerto de sondeo, o uno o varios grupos de seguridad de red configurados en la subred o en la máquina virtual no permite que el sondeo alcance el puerto, la máquina virtual no puede responder al sondeo de estado.          

**Validación y resolución**

* Si el firewall está habilitado, compruebe si está configurado para permitir el puerto de sondeo. De lo contrario, configure el firewall para permitir el tráfico en el puerto de sondeo y pruebe de nuevo. 
* En la lista de grupos de seguridad de red, compruebe si el tráfico entrante o saliente del puerto de sondeo tiene interferencias. 
* Además, compruebe si hay una regla de grupos de seguridad de red **Deny All** en la NIC de la máquina virtual o la subred que tenga una prioridad mayor que la regla predeterminada que permite los sondeos de equilibrio de carga y tráfico (los grupos de seguridad de red deben permitir la dirección IP del equilibrador de carga 168.63.129.16). 
* Si cualquiera de estas reglas bloquea el tráfico del sondeo, elimine y vuelva a configurar las reglas para permitirlo.  
* Pruebe si la máquina virtual ahora responde a los sondeos de estado. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Motivo 4: otros errores de configuración del equilibrador de carga
Si parece que los motivos anteriores se han validado y resuelto correctamente, y aun así la máquina virtual de back-end sigue sin responder al sondeo de estado, compruebe manualmente la conectividad y recopile algunos seguimientos para entenderla.

**Validación y resolución**

* Use **Psping** de una de las otras máquinas virtuales dentro de la red virtual para probar la respuesta del puerto de sondeo (ejemplo: .\psping.exe -t 10.0.0.4:3389) y registre los resultados. 
* Use **TCPing** de una de las otras máquinas virtuales dentro de la red virtual para probar la respuesta del puerto de sondeo (ejemplo: .\tcping.exe 10.0.0.4 3389) y registre los resultados. 
* Si no se recibe respuesta con estas pruebas de ping:
    - Ejecute un seguimiento Netsh en la máquina virtual del grupo de back-end de destino y en otra máquina virtual de prueba de la misma red virtual simultáneamente. Ahora, ejecute una prueba de PsPing durante algún tiempo, recopile seguimientos de red y detenga la prueba. 
    - Analizar la captura de red y observe si hay paquetes entrantes y salientes relacionados con la consulta de ping. 
        - Si no se observan paquetes entrantes se observan en la máquina virtual del grupo de back-end, puede haber grupos de seguridad de red o una configuración incorrecta de UDR que bloquee el tráfico. 
        - Si no se observan paquetes salientes en la máquina virtual del grupo de back-end, la máquina virtual podría tener otros problemas ajenos (por ejemplo, que la aplicación bloquee el puerto de sondeo). 
    - Compruebe si los paquetes de sondeo se fuerzan a otro destino (probablemente mediante la configuración de UDR) antes de alcanzar el equilibrador de carga. Esto puede hacer que el tráfico nunca alcance la máquina virtual de back-end. 
* Cambiar el tipo de sondeo (por ejemplo, de HTTP a TCP) y configure el puerto correspondiente en las ACL de los grupos de seguridad de red y firewall para comprobar si el problema está relacionado con la configuración de respuesta del sondeo. Para más información acerca de la configuración del sondeo de estado, consulte [Endpoint Load Balancing health probe configuration](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/) (Configuración del sondeo de estado en el punto de conexión del equilibrador de carga).

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Síntoma: las máquinas virtuales detrás del equilibrador de carga no responden al tráfico del puerto de datos configurado

Si una máquina virtual del grupo de back-end aparece como correcta y responde a los sondeos de estado, pero aun así no participa en el equilibrio de carga o no responde al tráfico de datos, puede deberse a alguno de los motivos siguientes: 
* Que una máquina virtual del grupo de back-end del equilibrador de carga no escuche en el puerto de datos 
* Que un grupo de seguridad de red esté bloqueando el puerto de la máquina virtual del grupo de back-end del equilibrador de carga  
* Que se acceda al equilibrador de carga desde la misma máquina virtual y NIC 
* Que se acceda a la VIP del equilibrador de carga en Internet desde la máquina virtual del grupo de back-end del equilibrador de carga participante 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Motivo 1: una máquina virtual del grupo de back-end del equilibrador de carga no escucha en el puerto de datos 
Si una máquina virtual no responde al tráfico de datos, puede deberse a que el puerto de destino no esté abierto en la máquina virtual participante, o a que la máquina virtual no escuche en ese puerto. 

**Validación y resolución**

1. Inicie sesión en la máquina virtual de back-end. 
2. Abra un símbolo del sistema y ejecute el siguiente comando para validar que existe una aplicación de escucha en el puerto de datos:  
            netstat -an 
3. Si el puerto no aparece con el estado "LISTENING" (ESCUCHANDO), configure el puerto de escucha 
4. Si el puerto está marcado como Listening (Escuchando), compruebe si hay problemas en la aplicación de destino de ese puerto. 

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Motivo 2: un grupo de seguridad de red está bloqueando el puerto de la máquina virtual del grupo de back-end del equilibrador de carga  

Si uno o varios grupos de seguridad de red configurados en la subred o en la máquina virtual bloquean la dirección IP de origen o el puerto, la máquina virtual no puede responder.

* Enumere los grupos de seguridad de red configurados en la máquina virtual de back-end. Para más información, consulte:
    -  [Administración de grupos de seguridad de red mediante el portal](../virtual-network/virtual-network-manage-nsg-arm-portal.md)
    -  [Administración de grupos de seguridad de red mediante PowerShell](../virtual-network/virtual-network-manage-nsg-arm-ps.md)
* En la lista de grupos de seguridad de red, compruebe si:
    - el tráfico entrante o saliente del puerto de datos tiene interferencias. 
    - hay una regla de grupos de seguridad de red **Deny All** en la NIC de la máquina virtual o la subred que tenga una prioridad mayor que la regla predeterminada que permite los sondeos del equilibrador de carga y de tráfico (los grupos de seguridad de red deben permitir la dirección IP del equilibrador de carga 168.63.129.16, que es el puerto de sondeo) 
* Si cualquiera de estas reglas bloquea el tráfico, elimine y vuelva a configurar las reglas para permitir el tráfico de datos.  
* Pruebe si la máquina virtual ahora responde a los sondeos de estado.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Motivo 3: acceso al equilibrador de carga desde la misma máquina virtual e interfaz de red 

Que la aplicación hospedada en la máquina virtual de back-end del equilibrador de carga intente acceder a otra aplicación que se hospeda en la misma máquina virtual de back-end a través de la misma interfaz de red es un escenario incompatible y producirá un error. 

**Resolución**: Este problema se puede resolver por una de las siguientes vías:
* Configure las máquinas virtuales del grupo de back-end por separado para cada aplicación. 
* Configure la aplicación en máquinas virtuales de NIC doble para que cada aplicación use su propia interfaz de red y dirección IP. 

### <a name="cause-4-accessing-the-internal-load-balancer-vip-from-the-participating-load-balancer-backend-pool-vm"></a>Motivo 4: acceso a la dirección VIP del equilibrador de carga interno desde la máquina virtual del grupo de back-end del equilibrador de carga participante

Si se configura una dirección VIP para el equilibrador de carga interno en una red virtual y una de las máquinas virtuales de back-end participantes intenta acceder a ella, se produce un error. Se trata de un escenario no admitido.
**Resolución**: evalúe Application Gateway u otros servidores proxy (por ejemplo, nginx o haproxy) para que admitan esta variante de escenario. Para más información acerca de Application Gateway, consulte [Introducción a Application Gateway](../application-gateway/application-gateway-introduction.md)

## <a name="additional-network-captures"></a>Capturas de red adicionales
Si decide abrir un caso de soporte técnico, recopile la información siguiente para una resolución más rápida. Elija una sola máquina virtual de back-end para realizar las pruebas siguientes:
- Use Psping de una de las máquinas virtuales de back-end de la red virtual para probar la respuesta del puerto de sondeo (ejemplo: psping 10.0.0.4:3389) y registre los resultados. 
- Use TCPing de una de las máquinas virtuales de back-end de la red virtual para probar la respuesta del puerto de sondeo (ejemplo: psping 10.0.0.4:3389) y registre los resultados.
- Si no se recibe respuesta con estas pruebas de ping, ejecute un seguimiento Netsh simultáneo en la máquina virtual de back-end y la máquina virtual de prueba de la red virtual mientras ejecuta PsPing y detenga el seguimiento Netsh. 
  
## <a name="next-steps"></a>Pasos siguientes

Si los pasos anteriores no resuelven el problema, abra una [incidencia de soporte técnico](https://azure.microsoft.com/support/options/).


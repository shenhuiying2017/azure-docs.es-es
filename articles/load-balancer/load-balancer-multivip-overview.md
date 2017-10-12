---
title: Varias IP virtuales para Azure Load Balancer | Microsoft Docs
description: "Información general de varias IP virtuales para Azure Load Balancer"
services: load-balancer
documentationcenter: na
author: chkuhtz
manager: narayan
editor: 
ms.assetid: 748e50cd-3087-4c2e-a9e1-ac0ecce4f869
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: chkuhtz
ms.openlocfilehash: 1045a18f5fd9739a6028198deea129e9e621f127
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="multiple-vips-for-azure-load-balancer"></a>Varias IP virtuales para Azure Load Balancer

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Azure Load Balancer permite utilizar servicios de equilibrio de carga en varios puertos, varias direcciones IP, o en ambos. Puede usar las definiciones de equilibrador de carga públicas e internas para flujos de equilibrio de carga entre un conjunto de máquinas virtuales.

En este artículo se describen los fundamentos de esta capacidad, los conceptos importantes y las restricciones. Si solo desea exponer los servicios en una dirección IP, puede encontrar instrucciones simplificadas para configuraciones [públicas](load-balancer-get-started-internet-portal.md) o [internas](load-balancer-get-started-ilb-arm-portal.md) del equilibrador de carga. Agregar varias direcciones IP virtuales es una acción incremental de la configuración de una única IP virtual. Mediante los conceptos de este artículo, puede expandir una configuración simplificada en cualquier momento.

Al definir un Azure Load Balancer, las configuraciones front-end y back-end están conectadas con reglas. El sondeo de estado a que hace referencia la regla se utiliza para determinar cómo se envían nuevos flujos a un nodo en el grupo de back-end. El front-end se define mediante una IP Virtual (VIP), que es una tupla de 3 que consta de una dirección IP (pública o interna), un protocolo de transporte (UDP o TCP) y un número de puerto. Una DIP es una dirección IP de un NIC virtual de Azure conectado a una máquina virtual en el grupo de back-end.

La tabla siguiente contiene algunas configuraciones de front-end de ejemplo:

| IP virtual | Dirección IP | protocolo | puerto |
| --- | --- | --- | --- |
| 1 |65.52.0.1 |TCP |80 |
| 2 |65.52.0.1 |TCP |*8080* |
| 3 |65.52.0.1 |*UDP* |80 |
| 4 |*65.52.0.2* |TCP |80 |

En la tabla se muestran cuatro front-end diferentes. Los front-end 1, 2 y 3 son una sola dirección IP virtual con varias reglas. Se utiliza la misma dirección IP, pero el puerto o el protocolo es diferente para cada front-end. Los front-end 1 y 4 son un ejemplo de varias direcciones IP virtuales, donde se reutilizan el mismo protocolo front-end y el puerto a través de varias direcciones IP virtuales.

Azure Load Balancer proporciona flexibilidad para definir las reglas de equilibrio de carga. Una regla declara cómo se asigna una dirección y el puerto en el front-end a la dirección de destino y al puerto en el back-end. El hecho de que los puertos back-end se reutilicen o no a través de las reglas depende del tipo de regla. Cada tipo de regla tiene requisitos específicos que pueden afectar al diseño del sondeo y a la configuración del host. Existen dos tipos de reglas:

1. La regla predeterminada sin la reutilización de un puerto back-end
2. La regla de dirección IP flotante donde se reutilizan puertos back-end

Azure Load Balancer permite combinar ambos tipos de regla en la misma configuración de equilibrador de carga. El equilibrador de carga puede utilizarlos simultáneamente para una máquina virtual determinada, o cualquier combinación, siempre y cuando se cumplan las restricciones de la regla. El tipo de regla que elija depende de los requisitos de la aplicación y la complejidad de la compatibilidad con dicha configuración. Debe evaluar qué tipos de reglas son mejores para su escenario.

Se analizan aún más estos escenarios empezando con el comportamiento predeterminado.

## <a name="rule-type-1-no-backend-port-reuse"></a>Tipo de regla 1: No reutilización de puerto back-end

![Ilustración de varias IP virtuales](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

En este escenario, las IP virtuales front-end están configuradas como sigue:

| IP virtual | Dirección IP | protocolo | puerto |
| --- | --- | --- | --- |
| ![IP virtual](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![IP virtual](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

La DIP es el destino del flujo de entrada. En el grupo back-end, cada máquina virtual expone el servicio deseado en un puerto único en una DIP. Este servicio está asociado con el front-end a través de una definición de regla.

Se definen dos reglas:

| Regla | Asignación de front-end | Para grupo back-end |
| --- | --- | --- |
| 1 |![IP virtual](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) VIP1:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2:80 |
| 2 |![IP virtual](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) VIP2:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2:81 |

La asignación completa en Azure Load Balancer ahora se realiza como sigue:

| Regla | Dirección IP de IP virtual | protocolo | puerto | Destino | puerto |
| --- | --- | --- | --- | --- | --- |
| ![Regla](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |Dirección IP de DIP |80 |
| ![Regla](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |Dirección IP de DIP |81 |

Cada regla debe generar un flujo con una combinación única de dirección IP de destino y puerto de destino. Al variar el puerto de destino del flujo, varias reglas pueden entregar flujos en la misma DIP en puertos diferentes.

Los sondeos de estado siempre se dirigen a la DIP de una máquina virtual. Debe asegurarse de que el sondeo refleja el estado de la máquina virtual.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>Tipo de regla 2: reutilización de puerto back-end mediante IP flotante

Azure Load Balancer proporciona la flexibilidad para reutilizar el puerto front-end en varias direcciones IP virtuales independientemente del tipo de regla que se utiliza. Además, algunos escenarios de aplicación prefieren o requieren que varias instancias de la aplicación usen el mismo puerto en una sola máquina virtual en el grupo back-end. Entre los ejemplos comunes de reutilización de puertos se incluyen la agrupación en clústeres para alta disponibilidad, dispositivos de red virtuales y la exposición de varios puntos de conexión TLS sin volver a cifrar.

Si desea reutilizar el puerto back-end en varias reglas, debe habilitar la IP flotante en la definición de la regla.

La IP flotante es una parte de lo que se conoce como Direct Server Return (DSR). DSR consta de dos partes: una topología de flujo y un esquema de asignación de direcciones IP. En un nivel de plataforma, Azure Load Balancer siempre funciona en una topología de flujo DSR independientemente de si la dirección IP flotante está habilitada o no. Esto significa que la parte de salida de un flujo siempre se reescribe correctamente para que se dirija de nuevo al origen.

Con el tipo de regla predeterminada, Azure expone un esquema de asignación de direcciones IP de equilibrio de carga tradicional a efectos de facilitar el uso. Habilitar la dirección IP flotante cambia el esquema de asignación de direcciones IP para permitir una flexibilidad adicional, como se explica a continuación.

En el siguiente diagrama, se ilustra esta configuración:

![Ilustración de varias IP virtuales](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

En este escenario, cada máquina virtual del grupo back-end tiene tres interfaces de red:

* DIP: una NIC virtual asociada a la máquina virtual (configuración IP del recurso NIC de Azure)
* VIP1: una interfaz de bucle invertido en el SO invitado que se configura con la dirección IP de VIP1
* VIP2: una interfaz de bucle invertido en el SO invitado que se configura con la dirección IP de VIP2

> [!IMPORTANT]
> La configuración de las interfaces lógicas se ejecuta en el SO invitado. Esta configuración no se ejecuta ni administra en Azure. Sin esta configuración, las reglas no funcionarán. Las definiciones de sondeo de estado usan la DIP de la máquina virtual en lugar de la dirección IP virtual lógica. Por lo tanto, el servicio debe proporcionar las respuestas del sondeo en un puerto DIP que reflejen el estado del servicio ofrecido en la dirección IP virtual lógica.

Se asume que la configuración front-end es la misma que en el escenario anterior:

| IP virtual | Dirección IP | protocolo | puerto |
| --- | --- | --- | --- |
| ![IP virtual](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![IP virtual](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

Se definen dos reglas:

| Regla | Asignación de front-end | Para grupo back-end |
| --- | --- | --- |
| 1 |![Regla](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) VIP1:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) VIP1:80 (en VM1 y VM2) |
| 2 |![Regla](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) VIP2:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) VIP2:80 (en VM1 y VM2) |

En la tabla siguiente se muestra la asignación completa en el equilibrador de carga:

| Regla | Dirección IP de IP virtual | protocolo | puerto | Destino | puerto |
| --- | --- | --- | --- | --- | --- |
| ![IP virtual](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |igual que la dirección VIP (65.52.0.1) |igual que la dirección VIP (80) |
| ![IP virtual](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |igual que la dirección VIP (65.52.0.2) |igual que la dirección VIP (80) |

El destino del flujo de entrada es la dirección IP virtual en la interfaz de bucle invertido en la máquina virtual. Cada regla debe generar un flujo con una combinación única de dirección IP de destino y puerto de destino. Al variar la dirección IP de destino del flujo, se puede reutilizar el puerto en la misma máquina virtual. El servicio se expone al equilibrador de carga enlazándolo con la dirección IP de la IP virtual y al puerto de la interfaz de bucle invertido correspondiente.

Observe que este ejemplo no cambia el puerto de destino. Aunque se trata de un escenario de IP flotante, Azure Load Balancer también admite la definición de una regla para volver a escribir el puerto de destino back-end y para que sea diferente del puerto de destino front-end.

El tipo de regla de dirección IP flotante es el fundamento de varios modelos de configuración del equilibrador de carga. Un ejemplo que está disponible actualmente es la configuración [SQL AlwaysOn con varios agentes de escucha](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) . Con el tiempo, se documentarán varios de estos escenarios.

## <a name="limitations"></a>Limitaciones

* Solo se admiten varias configuraciones de IP virtual con máquinas virtuales de IaaS.
* Con la regla de dirección IP flotante, la aplicación debe utilizar la DIP para los flujos salientes. Si la aplicación se enlaza a la dirección IP virtual configurada en la interfaz de bucle invertido en el sistema operativo invitado, a continuación, SNAT no está disponible para volver a escribir el flujo de salida y, por tanto, se produce un error en el flujo.
* Las direcciones IP públicas repercuten en la facturación. Para obtener más información, vea [Precios de las direcciones IP](https://azure.microsoft.com/pricing/details/ip-addresses/)
* Se aplican los límites de suscripción. Para más información, vea los [límites de servicio](../azure-subscription-service-limits.md#networking-limits) .

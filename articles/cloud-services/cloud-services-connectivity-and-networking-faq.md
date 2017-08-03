---
title: P+F sobre los problemas de conectividad y redes en Microsoft Azure Cloud Services | Microsoft Docs
description: "En este artículo se enumeran las preguntas frecuentes sobre conectividad y redes para Microsoft Azure Cloud Services."
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/10/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 3fc0d34fdb617ebb1af9c9f33e018d5fe6ec9a7d
ms.contentlocale: es-es
ms.lasthandoff: 06/15/2017

---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemas de conectividad y redes en Azure Cloud Services: preguntas más frecuentes (P+F)

Este artículo incluye las preguntas frecuentes sobre conectividad y redes para [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). También puede consultar la [página Tamaños de los servicios en la nube](cloud-services-sizes-specs.md) para obtener información de tamaño.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>No se puede reservar una dirección IP en un servicio en la nube con varias direcciones IP virtuales
En primer lugar, asegúrese de que la instancia de máquina virtual para la que está intentando reservar la dirección IP está activada. En segundo lugar, asegúrese de que utiliza direcciones IP reservadas para las implementaciones de ensayo y de producción. **No** cambie la configuración mientras se está actualizando la implementación.

## <a name="how-do-i-remote-desktop-when-i-have-an-nsg"></a>¿Cómo se usa el escritorio remoto con un NSG?
Agregue reglas al NSG que permitan el tráfico en los puertos **3389** y **20000**.  El Escritorio remoto usa el puerto **3389**.  Las instancias del servicio en la nube tienen la carga equilibrada, por lo que no se puede controlar directamente a qué instancia conectarse.  Los agentes *RemoteForwarder* y *RemoteAccess* administran el tráfico RDP y permiten al cliente enviar una cookie de RDP y especificar una instancia concreta a la que conectarse.  Los agentes *RemoteForwarder* y *RemoteAccess* requieren que ese puerto **20000** se abra (puede estar bloqueado si tiene un NSG).

## <a name="can-i-ping-a-cloud-service"></a>¿Se puede hacer ping a un servicio de nube?

No, no usando un "ping" normal / protocolo ICMP. No se permite el protocolo ICMP a través de Azure Load Balancer.

Para probar la conectividad, se recomienda que realice un ping de puerto. Mientras que Ping.exe usa ICMP, otras herramientas, como PSPing, Nmap, y telnet permiten probar la conectividad con un puerto TCP específico.

Para más información, consulte [Use port pings instead of ICMP to test Azure VM connectivity](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/) (uso de pings de puerto en lugar de ICMP para probar la conectividad de la máquina virtual de Azure).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-indicate-some-sort-of-malicious-attack-to-the-cloud-service"></a>¿Cómo se puede evitar la recepción de miles de aciertos de direcciones IP desconocidas que indican algún tipo de ataque malintencionado al servicio de nube?
Azure implementa una seguridad de red multicapa para proteger sus servicios de plataforma contra los ataques de denegación de servicio distribuido (DDoS). El sistema de defensa DDoS de Azure forma parte del proceso de supervisión continuo de Azure, que mejora continuamente a través de pruebas de penetración. Este sistema de defensa DDoS está diseñado para resistir ataques no solo desde el exterior, sino también de otros inquilinos de Azure. Para información más detallada, consulte el documento sobre [Seguridad de red de Microsoft Azure](http://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf).

También puede crear una tarea de inicio para bloquear de manera selectiva algunas direcciones IP específicas. Para más información, consulte [Bloqueo de una dirección IP específica](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>Cuando intento ejecutar el protocolo de escritorio remoto (RDP) para la instancia de servicio de nube, obtengo el mensaje, "La cuenta de usuario ha expirado".
Puede obtener el mensaje de error "Esta cuenta de usuario expiró" cuando ignora la fecha de expiración que está configurada en los ajustes de RDP. Puede cambiar la fecha de expiración desde el portal siguiendo estos pasos:
1. Inicie sesión en la consola de administración de Azure (https://manage.windowsazure.com), vaya a su servicio en la nube y seleccione la pestaña **Configurar**.
2. Seleccione **Remoto**.
3. Cambie la fecha "Expira en" y, a continuación, guarde la configuración.

Ahora podrá ejecutar el RDP en el equipo.

## <a name="why-is-loadbalancer-not-balancing-traffic-equally"></a>¿Por qué LoadBalancer no equilibra el tráfico de forma equitativa?
Para información acerca de cómo funciona de equilibrador de carga interno, consulte [Azure Load Balancer new distribution mode](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/) (Nuevo modo de distribución de Azure Load Balancer).

El algoritmo de distribución usado para asignar el tráfico a los servidores disponibles es una tupla de 5 componentes (IP de origen, puerto de origen, IP de destino, puerto de destino, tipo de protocolo). Dicho algoritmo solo proporciona adherencia dentro de una sesión de transporte. Los paquetes de la misma sesión TCP o UDP se dirigirán a la misma instancia de IP del centro de datos (DIP) tras el extremo con equilibrio de carga. Cuando el cliente se cierra y vuelve a abrir la conexión o inicia una nueva sesión desde la misma IP de origen, el puerto de origen cambia y provoca que el tráfico vaya hacia otro extremo DIP.


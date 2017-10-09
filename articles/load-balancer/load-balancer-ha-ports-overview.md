---
title: "Introducción a los puertos de alta disponibilidad en Azure | Microsoft Docs"
description: Conozca el equilibrio de carga de los puertos de alta disponibilidad en un equilibrador de carga interno.
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: 2219aeb725b207fd92ff3e7603d7ee9c78f2844c
ms.contentlocale: es-es
ms.lasthandoff: 09/28/2017

---

# <a name="high-availability-ports-overview-preview"></a>Introducción a los puertos de alta disponibilidad (versión preliminar)

La SKU estándar de Azure Load Balancer introduce los puertos de alta disponibilidad (HA), una funcionalidad para distribuir el tráfico desde todos los puertos y para todos los protocolos admitidos. Durante la configuración de un equilibrador de carga interno, los usuarios pueden configurar una regla de puertos HA que establezca los puertos de front-end y back-end en **0** y el protocolo en **all** y, de esta forma, permitir el flujo de todo el tráfico a través de este equilibrador.

>[!NOTE]
> La característica Puertos de alta disponibilidad actualmente está en versión preliminar. Durante la versión preliminar, la característica podría no tener el mismo nivel de disponibilidad y confiabilidad que las características que se encuentran en las versiones de disponibilidad general. Para obtener más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

El algoritmo de equilibrio de carga sigue siendo el mismo y el destino se selecciona en función de las cinco tuplas <Dirección IP de origen, Puerto de origen, Dirección IP de destino, Puerto de destino, Protocolo>. Sin embargo, esta configuración permite que una única regla LB procese todo el tráfico disponible y reduce la complejidad de la configuración, además de los límites impuestos por el número máximo de reglas de equilibrio de carga que se pueden agregar.

Uno de los escenarios críticos que permiten los puertos HA es la implementación de alta disponibilidad de las aplicaciones virtuales de red en redes virtuales de Azure. Además, otro escenario común que posibilitan los puertos HA es el equilibrio de carga en un intervalo de puertos. 

## <a name="why-use-high-ha-ports"></a>Por qué usar los puertos HA

Los clientes de Azure dependen en gran medida de las aplicaciones virtuales de red (NVA) para proteger sus cargas de trabajo frente a varios tipos de amenazas de seguridad. Además, las NVA deben ser confiable y tener una alta disponibilidad.  

Los puertos HA reducen la complejidad del escenario HA de NVA al eliminar la necesidad de soluciones más complejas, como Zookeeper, y aumentan la confiabilidad gracias a las opciones de conmutación por error más rápida y el escalado horizontal. Ahora, es posible lograr HA de NVA con solo agregar NVA al grupo de back-end del equilibrador de carga interno de Azure, y mediante la posterior configuración de la regla de Load Balancer de puertos HA.

En el ejemplo siguiente se presenta una implementación de red virtual de concentrador y radio, donde los radios fuerzan la tunelización de su tráfico a la red virtual del concentrador y mediante la NVA antes de abandonar el espacio de confianza. Las NVA están detrás de un equilibrador de carga interno con la configuración de puertos HA, así que pueden procesar todo el tráfico y reenviarlo en consecuencia. 

![Ejemplo de puertos HA](./media/load-balancer-ha-ports-overview/nvaha.png)

Figura 1: Red virtual de concentrador y radio con NVA implementada en modo de alta disponibilidad


## <a name="region-availability"></a>Disponibilidad en regiones

Actualmente, los puertos de alta disponibilidad están disponibles en las regiones siguientes:
- Este de EE. UU. 2
- Central EE. UU.:
- Europa del Norte
- Centro occidental de EE.UU.
- Europa occidental
- Sudeste asiático 

## <a name="preview-sign-up"></a>Registro en versión preliminar

Para participar en la versión preliminar de la característica de puertos de alta disponibilidad en la SKU estándar de Load Balancer, registre su suscripción para obtener acceso mediante PowerShell o la CLI de Azure 2.0.

- Registro con PowerShell

    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```

- Registro con la CLI de Azure 2.0

    ```cli
    az feature register --name AllowILBAllPortsRule --namespace Microsoft.Network 
    ```
## <a name="caveats"></a>Advertencias

A continuación se indican las configuraciones o excepciones admitidas en puertos HA:

- Una única configuración IP de front-end puede tener una única regla de equilibrio de carga DSR o no DRS con puertos HA (todos los puertos). No puede tener ambas.
- Una única configuración IP de interfaz de red solo puede tener una regla de equilibrio de carga DRS con puertos HA. Ninguna otra regla se puede configurar para esta configuración IP.
- Una única configuración IP de interfaz de red puede tener una o más reglas de equilibrio de carga DRS con puertos HA, siempre y cuando todas sus configuraciones IP de front-end respectivas sean exclusivas.
- Es posible la coexistencia de dos o más reglas que apunten al mismo grupo de back-end si todas las reglas de equilibrio de carga son puertos HA (solo DSR) o no HA (DSR y no DSR). Esta coexistencia no es posible si hay una combinación de reglas de puertos HA y no HA.
- El puerto HA en inquilinos habilitados para IPv6 no está disponible.


## <a name="next-steps"></a>Pasos siguientes

[Configuración de puertos de alta disponibilidad para el equilibrador de carga interno](load-balancer-configure-ha-ports.md)



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
ms.openlocfilehash: 62d78e067e50183f25af84e547db2e11c0014f5d
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/20/2017
---
# <a name="high-availability-ports-overview-preview"></a>Introducción a los puertos de alta disponibilidad (versión preliminar)

Azure Load Balancer Standard presenta una nueva capacidad para equilibrar la carga de los flujos TCP y UDP en todos los puertos simultáneamente al usar un equilibrador de carga interno. 

>[!NOTE]
> La característica de puertos de alta disponibilidad está disponible con Load Balancer estándar y se encuentra actualmente en versión preliminar. Durante la versión preliminar, la característica podría no tener el mismo nivel de disponibilidad y confiabilidad que las características que se encuentran en las versiones de disponibilidad general. Para obtener más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Es necesario registrarse en la versión preliminar de Load Balancer estándar para usar los puertos de alta disponibilidad con recursos de Load Balancer estándar. Siga también las instrucciones para el registro adicional en Load Balancer [estándar (versión preliminar)](https://aka.ms/lbpreview#preview-sign-up).

Una regla de puertos de alta disponibilidad es una variante de una regla de equilibrio de carga configurada en una instancia interna de Load Balancer estándar.  Los escenarios se simplifican proporcionando una única regla de Load Balancer para equilibrar la carga de todos los flujos TCP y UDP que llegan a todos los puertos de un frontend interno de Load Balancer estándar. La decisión de equilibrio de carga se toma por cada flujo en función de la tupla de cinco formada por la dirección IP de origen, el puerto de origen, la dirección IP de destino, el puerto de destino y el protocolo.

Los puertos de alta disponibilidad permiten escenarios críticos como los de alta disponibilidad y escalado de aplicaciones virtuales de red (NVA) dentro de redes virtuales así como otros escenarios en los que se debe equilibrar la carga de un gran número de puertos. 

Los puertos de alta disponibilidad se configuran estableciendo los puertos de front-end y back-end en **0** y el protocolo en **Todos**.  El recurso del equilibrador de carga interno equilibra ahora todos los flujos TCP y UDP, independientemente del número de puerto.

## <a name="why-use-ha-ports"></a>¿Por qué usar puertos de alta disponibilidad?

### <a name="network-virtual-appliances"></a>Aplicaciones virtuales de red

Puede usar aplicaciones virtuales de red (NVA) para proteger la carga de trabajo de Azure frente a varios tipos de amenazas de seguridad. Cuando se utilizan NVA en estos escenarios, estas deben ser confiables, de alta disponibilidad y con escalabilidad horizontal a petición.

Puede lograr estos objetivos en el escenario simplemente agregando instancias de NVA en el grupo back-end del equilibrador de carga interno de Azure y configurando una regla de equilibrador de carga de puertos de alta disponibilidad.

Los puertos de alta disponibilidad ofrecen varias ventajas para los escenarios de alta disponibilidad de las NVA:
- conmutación por error rápida para instancias en buen estado con sondeos de estado por instancia
- mayor rendimiento con escalado horizontal para instancias n-activas
- escenarios n-activos y activo-pasivo
- eliminación de la necesidad de soluciones complejas como los nodos de Zookeeper para la supervisión de aplicaciones

En el ejemplo siguiente se presenta una implementación de red virtual de concentrador y radio, donde los radios fuerzan la tunelización de su tráfico a la red virtual del concentrador y mediante la NVA antes de abandonar el espacio de confianza. Las NVA están detrás de una instancia interna de Load Balancer estándar con configuración de puertos de alta disponibilidad.  En consecuencia, se puede procesar y reenviar todo el tráfico. 

![Ejemplo de puertos HA](./media/load-balancer-ha-ports-overview/nvaha.png)

Figura 1: Red virtual de concentrador y radio con NVA implementada en modo de alta disponibilidad

Si va a usar aplicaciones virtuales de red, confirme con el proveedor respectivo cómo aprovechar mejor los puertos de alta disponibilidad y cuáles son los escenarios admitidos.

### <a name="load-balancing-large-numbers-of-ports"></a>Equilibrado de carga de un gran número de puertos

También puede usar puertos de alta disponibilidad para los escenarios de aplicaciones que requieren el equilibrado de carga de un gran número de puertos. Estos escenarios se pueden simplificar mediante una instancia interna de [Load Balancer estándar](https://aka.ms/lbpreview) con puertos de alta disponibilidad en los que una sola regla de equilibrio de carga reemplaza varias reglas individuales, una para cada puerto.

## <a name="region-availability"></a>Disponibilidad en regiones

Los puertos de alta disponibilidad se encuentran en las [mismas regiones que Load Balancer estándar](https://aka.ms/lbpreview#region-availability).  

## <a name="preview-sign-up"></a>Registro en versión preliminar

Para participar en la versión preliminar de la característica de puertos de alta disponibilidad en Load Balancer estándar, registre su suscripción para obtener acceso mediante PowerShell o la CLI de Azure 2.0.  Siga estos tres pasos:

>[!NOTE]
>Para usar esta característica, es necesario registrarse en la versión preliminar de Load Balancer [estándar](https://aka.ms/lbpreview#preview-sign-up) además de para los puertos de alta disponibilidad. El registro de las versiones preliminares de los puertos de alta disponibilidad o Load Balancer estándar puede tardar hasta una hora.

### <a name="sign-up-using-azure-cli-20"></a>Registro con la CLI de Azure 2.0

1. Registre la característica con el proveedor
    ```cli
    az feature register --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
2. La operación anterior puede tardar hasta 10 minutos en completarse.  Puede comprobar el estado de la operación con el siguiente comando:

    ```cli
    az feature show --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
    Continúe con el paso 3 si el estado de registro de la característica devuelve "Registered" (Registrado) tal y como se muestra a continuación:
   
    ```json
    {
       "id": "/subscriptions/foo/providers/Microsoft.Features/providers/Microsoft.Network/features/AllowLBPreview",
       "name": "Microsoft.Network/AllowILBAllPortsRule",
       "properties": {
          "state": "Registered"
       },
       "type": "Microsoft.Features/providers/features"
    }
    ```
    
3. Complete el registro de la versión preliminar volviendo a registrar la suscripción con el proveedor de recursos:

    ```cli
    az provider register --namespace Microsoft.Network
    ```
    
### <a name="sign-up-using-powershell"></a>Registro con PowerShell

1. Registre la característica con el proveedor
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    
2. La operación anterior puede tardar hasta 10 minutos en completarse.  Puede comprobar el estado de la operación con el siguiente comando:

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    Continúe con el paso 3 si el estado de registro de la característica devuelve "Registered" (Registrado) tal y como se muestra a continuación:
   
    ```
    FeatureName          ProviderName      RegistrationState
    -----------          ------------      -----------------
    AllowILBAllPortsRule Microsoft.Network Registered
    ```
    
3. Complete el registro de la versión preliminar volviendo a registrar la suscripción con el proveedor de recursos:

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```


## <a name="limitations"></a>Limitaciones

A continuación se indican las configuraciones o excepciones admitidas en puertos HA:

- Una única configuración IP de front-end puede tener una única regla de Load Balancer DSR o no DRS con puertos de alta disponibilidad. No puede tener ambas.
- Una única configuración IP de interfaz de red solo puede tener una regla de equilibrio de carga DRS con puertos de alta disponibilidad. Ninguna otra regla se puede configurar para esta configuración IP.
- Una única configuración IP de interfaz de red puede tener una o más reglas de equilibrio de carga DRS con puertos de alta disponibilidad, siempre y cuando todas sus configuraciones IP de front-end respectivas sean exclusivas.
- Es posible la coexistencia de dos o más reglas que apunten al mismo grupo de back-end si todas las reglas de equilibrio de carga son puertos de alta disponibilidad (solo DSR) o no lo son (DSR y no DSR). Esta coexistencia de reglas de equilibrio de carga no es posible si hay una combinación de reglas de puertos de alta disponibilidad y puertos que no son de alta disponibilidad.
- Los puertos de alta disponibilidad no están disponibles para IPv6.


## <a name="next-steps"></a>Pasos siguientes

- [Configuración de puertos de alta disponibilidad para la instancia interna de Load Balancer estándar](load-balancer-configure-ha-ports.md)
- [Más información acerca de la versión preliminar de Load Balancer estándar](https://aka.ms/lbpreview)


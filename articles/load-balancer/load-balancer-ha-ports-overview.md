---
title: "Introducción a los puertos de alta disponibilidad en Azure | Microsoft Docs"
description: Conozca el equilibrio de carga de los puertos de alta disponibilidad en una instancia interna de Load Balancer.
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
ms.openlocfilehash: 7a77e6ecbf59944c62aa4ae014bf5b8a5a7f7f1f
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2017
---
# <a name="high-availability-ports-overview"></a>Introducción a los puertos de alta disponibilidad

Azure Load Balancer Standard le permite equilibrar la carga de los flujos TCP y UDP en todos los puertos simultáneamente al usar una instancia interna de Load Balancer. 

>[!NOTE]
> La característica de puertos de alta disponibilidad (HA) está disponible con Load Balancer Standard y se encuentra actualmente en versión preliminar. Durante la versión preliminar, la característica podría no tener el mismo nivel de disponibilidad y confiabilidad que las características de las versiones de disponibilidad general. Para obtener más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Es necesario registrarse en la versión preliminar de Load Balancer Standard para usar los puertos de alta disponibilidad con recursos de Load Balancer Standard. Siga también las instrucciones para el registrarse en la [versión preliminar](https://aka.ms/lbpreview#preview-sign-up) de Load Balancer Standard.

Una regla de puertos de alta disponibilidad es una variante de una regla de equilibrio de carga configurada en una instancia interna de Load Balancer Standard. Puede simplificar el modo en que usa Load Balancer proporcionando una única regla para equilibrar la carga de todos los flujos TCP y UDP que llegan a todos los puertos de Load Balancer Standard . La decisión de equilibrio de carga se toma por cada flujo. Esta decisión se toma en función de la conexión de tupla de cinco siguiente: la dirección IP de origen, el puerto de origen, la dirección IP de destino, el puerto de destino y el protocolo.

La característica de puertos de alta disponibilidad le será de ayuda a la hora de usar escenarios críticos como, por ejemplo, aquellos que tengan alta disponibilidad y escalabilidad para dispositivos virtuales de red (NVA) que estén en redes virtuales. Asimismo, podrá serle de ayuda cuando necesite equilibrar la carga de un gran número de puertos. 

La característica de puertos de alta disponibilidad se configura al establecer los puertos de front-end y back-end en **0**y el protocolo en **Todos**. Es entonces cuando el recurso de Load Balancer interno equilibra todos los flujos TCP y UDP, independientemente del número de puerto.

## <a name="why-use-ha-ports"></a>¿Por qué usar puertos de alta disponibilidad?

### <a name="nva"></a>Dispositivos virtuales de red

Puede usar los dispositivos virtuales de red (NVA) para proteger la carga de trabajo de Azure frente a varios tipos de amenazas de seguridad. Cuando se usan los NVA en estos escenarios, deben ser confiables y de alta disponibilidad, y tener escalabilidad horizontal para la demanda.

Puede lograr estos objetivos si agrega instancias de NVA en el grupo de back-end de la instancia interna de Azure Load Balancer y configura una regla de Load Balancer en puertos de alta disponibilidad.

Los puertos de alta disponibilidad ofrecen varias ventajas para los escenarios de alta disponibilidad de NVA:
- Conmutación por error rápida para instancias en buen estado con sondeos de estado por instancia.
- Mayor rendimiento con escalabilidad horizontal para instancias *n*-activas.
- Escenarios *N*-activos y activo-pasivos.
- Eliminar la necesidad de usar soluciones complejas, como los nodos de Apache Zookeeper, para la supervisión de dispositivos.

En el diagrama siguiente se muestra una implementación de la red virtual de tipo hub-and-spoke. Los radios dirigen su tráfico a la red virtual del concentrador y a través de los NVA, antes de abandonar el espacio de confianza. Los NVA están detrás de una instancia interna de Load Balancer Standard con configuración de puertos de alta disponibilidad. En consecuencia, se puede procesar y reenviar todo el tráfico.

![Diagrama de la red virtual de tipo hub-and-spoke con los NVA implementados en modo de alta disponibilidad.](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Si va a usar NVA, confirme con su proveedor cómo aprovechar mejor los puertos de alta disponibilidad y cuáles son los escenarios admitidos.

### <a name="load-balancing-large-numbers-of-ports"></a>Equilibrado de carga de un gran número de puertos

También puede usar puertos de alta disponibilidad para aplicaciones que requieran equilibrar la carga de un gran número de puertos. Igualmente, puede simplificar estos escenarios mediante una instancia interna de [Load Balancer Standard](https://aka.ms/lbpreview) con puertos de alta disponibilidad. Una regla de equilibrio de carga única reemplaza varias reglas individuales de equilibrio de carga: una por cada puerto.

## <a name="region-availability"></a>Disponibilidad en regiones

Los puertos de alta disponibilidad se encuentran en las [mismas regiones que Load Balancer Standard](https://aka.ms/lbpreview#region-availability).  

## <a name="preview-sign-up"></a>Registro en versión preliminar

Para participar en la versión preliminar de la característica de puertos de alta disponibilidad de Load Balancer Standard, registre su suscripción para obtener acceso. Puede usar la CLI 2.0 de Azure o PowerShell.

>[!NOTE]
>Para usar esta característica, es necesario registrarse en la [versión preliminar](https://aka.ms/lbpreview#preview-sign-up) de Load Balancer Standard, además de en la característica de los puertos de alta disponibilidad. El registro puede tardar hasta una hora en completarse.

### <a name="sign-up-by-using-azure-cli-20"></a>Registro mediante la CLI de Azure 2.0

1. Registro de la característica con el proveedor:
    ```cli
    az feature register --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
2. La operación anterior puede tardar hasta 10 minutos en completarse. Puede comprobar el estado de la operación con el siguiente comando:

    ```cli
    az feature show --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
    La operación se habrá completado correctamente cuando el estado de registro de la característica devuelva el valor **Registered** (Registrado) tal y como se muestra a continuación:
   
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
    
### <a name="sign-up-by-using-powershell"></a>Registro mediante PowerShell

1. Registro de la característica con el proveedor:
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    
2. La operación anterior puede tardar hasta 10 minutos en completarse. Puede comprobar el estado de la operación con el siguiente comando:

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    La operación se habrá completado correctamente cuando el estado de registro de la característica devuelva el valor **Registered** (Registrado) tal y como se muestra a continuación:
   
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

A continuación, se indican las configuraciones o excepciones admitidas en puertos alta disponibilidad:

- Una única configuración IP de front-end puede tener una única regla de equilibrador de carga DSR (o que no sea DRS) con puertos de alta disponibilidad. No puede tener ambas.
- Una única configuración IP de interfaz de red solo puede tener una regla de equilibrador de carga DRS con puertos de alta disponibilidad. No se puede configurar ninguna otra regla para este comando ipconfig.
- Una única configuración IP de interfaz de red puede tener una o más reglas de equilibrador de carga DRS con puertos de alta disponibilidad, siempre y cuando todas sus configuraciones IP de front-end sean exclusivas.
- Si todas las reglas de equilibrio de carga son puertos de alta disponibilidad (solo DSR), es posible la coexistencia de dos o más reglas de Load Balancer que apunten al mismo grupo de back-end. Lo mismo puede decirse si ninguna regla es un puerto de alta disponibilidad (DSR y no DSR). Esta coexistencia de reglas de equilibrio de carga no es posible si hay una combinación de reglas de puertos de alta disponibilidad y puertos que no son de alta disponibilidad.
- La característica de los puertos de alta disponibilidad no está disponible para IPv6.
- La simetría de flujo para escenarios de NVA solo es compatible con una NIC única. Consulte la descripción y el diagrama de los [dispositivos virtuales de red](#nva). 



## <a name="next-steps"></a>Pasos siguientes

- [Configurar puertos de alta disponibilidad en una instancia interna de Load Balancer Standard](load-balancer-configure-ha-ports.md)
- [Más información acerca de la versión preliminar de Load Balancer estándar](https://aka.ms/lbpreview)


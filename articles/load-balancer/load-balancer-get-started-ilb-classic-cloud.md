---
title: "Creación de un equilibrador de carga interno para Azure Cloud Services | Microsoft Docs"
description: "Información sobre cómo crear un equilibrador de carga interno mediante PowerShell con el modelo de implementación clásica"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-service-management
ms.assetid: 57966056-0f46-4f95-a295-483ca1ad135d
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
ms.openlocfilehash: 6616c26ede13919b94a098dc38bdd6e2f0fc0b5b
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="get-started-creating-an-internal-load-balancer-classic-for-cloud-services"></a>Introducción a la creación de un equilibrador de carga interno (clásico) para servicios en la nube

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-classic-ps.md)
> * [CLI de Azure](../load-balancer/load-balancer-get-started-ilb-classic-cli.md)
> * [Cloud Services](../load-balancer/load-balancer-get-started-ilb-classic-cloud.md)

> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../azure-resource-manager/resource-manager-deployment-model.md).  Este artículo trata del modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos. Obtenga información sobre cómo [realizar estos pasos con el modelo de Resource Manager](load-balancer-get-started-ilb-arm-ps.md).

## <a name="configure-internal-load-balancer-for-cloud-services"></a>Configuración del equilibrador de carga interno para los servicios en la nube

El equilibrador de carga interno es compatible tanto con las máquinas virtuales como con los servicios en la nube. Un punto de conexión del equilibrador de carga interno creado en un servicio en la nube que está fuera de una red virtual regional solo será accesible dentro del servicio en la nube.

La configuración del equilibrador de carga interno se debe establecer durante la creación de la primera implementación en el servicio en la nube, como se muestra en el ejemplo siguiente.

> [!IMPORTANT]
> Un requisito previo para ejecutar los pasos siguientes es tener ya creada una red virtual para la implementación en la nube. Necesitarás el nombre de red virtual y el nombre de la subred para crear el Equilibrio de carga interno.

### <a name="step-1"></a>Paso 1

Abre el archivo de configuración de servicio (.cscfg) para la implementación en la nube en Visual Studio y agrega la siguiente sección para crear el Equilibrio de carga interno en el último elemento «`</Role>`» para la configuración de red.

```xml
<NetworkConfiguration>
    <LoadBalancers>
    <LoadBalancer name="name of the load balancer">
        <FrontendIPConfiguration type="private" subnet="subnet-name" staticVirtualNetworkIPAddress="static-IP-address"/>
    </LoadBalancer>
    </LoadBalancers>
</NetworkConfiguration>
```

Vamos a agregar los valores para que el archivo de configuración de red muestre su aspecto. En el ejemplo, supongamos que creó una red virtual denominada "test_vnet" con una subred 10.0.0.0/24 denominada test_subnet y la dirección IP estática 10.0.0.4. El equilibrador de carga se denominará testLB.

```xml
<NetworkConfiguration>
    <LoadBalancers>
    <LoadBalancer name="testLB">
        <FrontendIPConfiguration type="private" subnet="test_subnet" staticVirtualNetworkIPAddress="10.0.0.4"/>
    </LoadBalancer>
    </LoadBalancers>
</NetworkConfiguration>
```

Para obtener más información sobre el esquema del equilibrador de carga, consulta [Agregar equilibrador de carga](https://msdn.microsoft.com/library/azure/dn722411.aspx)

### <a name="step-2"></a>Paso 2

Cambia los archivos de definición (.csdef) para agregar extremos al Equilibrio de carga interno. Cuando se crea una instancia de rol, el archivo de definición de servicio agregará las instancias de rol al Equilibrio de carga interno.

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancer="load-balancer-name" />
    </Endpoints>
</WorkerRole>
```

Vamos a agregar los valores del archivo de definición de servicio siguiendo los mismos valores del ejemplo anterior.

```xml
<WorkerRole name="WorkerRole1" vmsize="A7" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="endpoint1" protocol="http" localPort="80" port="80" loadBalancer="testLB" />
    </Endpoints>
</WorkerRole>
```

La carga del tráfico de red se equilibrará mediante el equilibrador de carga testLB, en el que se usa el puerto 80 para las solicitudes entrantes y se envía a instancias de rol de trabajo también en el puerto 80.

## <a name="next-steps"></a>Pasos siguientes

[Configurar un modo de distribución del equilibrador de carga mediante la afinidad IP de origen](load-balancer-distribution-mode.md)

[Configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)


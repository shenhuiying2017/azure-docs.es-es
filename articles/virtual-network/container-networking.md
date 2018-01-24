---
title: Azure Virtual Network para contenedores | Microsoft Docs
description: "Obtenga información sobre el complemento de CNI para los clústeres de Kubernetes, que permite que los contenedores se comuniquen entre sí y con otros recursos en una red virtual."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: jdial
ms.openlocfilehash: f70afa8ff69b6c79363313c0f2df3b6785da8d81
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/19/2017
---
# <a name="container-networking"></a>Redes de contenedores

Azure proporciona un [complemento de interfaz de red de contenedor (CNI)](https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md) que permite implementar y administrar su propio clúster de Kubernetes, con la funcionalidad nativa de redes de Azure. El complemento está habilitado de forma predeterminada al implementar los clústeres de Kubernetes con el [motor de Azure Container Service](https://github.com/Azure/acs-engine), o motor de ACS.

## <a name="networking-capabilities"></a>Funcionalidades de red

Los contenedores pueden utilizar el amplio conjunto de funcionalidades que ofrece una red virtual, como:
-   Puede crear una red virtual independiente para el clúster o implementar el clúster en una red virtual existente. 
-   Cada pod del clúster recibe una dirección IP desde dentro de la red virtual y puede comunicarse directamente con otros pods del clúster y con cualquier máquina virtual de la red virtual. 
-   Un pod puede conectarse a otros pod y máquinas virtuales en redes virtuales del mismo nivel y a redes locales, a través de ExpressRoute y conexiones VPN de sitio a sitio. Los recursos locales pueden comunicarse con los pods. 
-   Puede exponer un servicio de Kubernetes a Internet mediante Azure Load Balancer.  
-   Los pods de una subred que tiene puntos de conexión de servicio habilitados pueden conectarse de forma segura a los servicios de Azure (Storage y SQL Database, por ejemplo).
-   Puede usar las rutas definidas por el usuario para redirigir el tráfico desde los pods a una aplicación virtual de la red. 
-   Los pods pueden acceder a recursos públicos de Internet.
-   Puede asignar a un pod una dirección IP pública, que puede asociarse con un nombre DNS.
 
## <a name="limits"></a>límites
Puede implementar hasta 4000 nodos en un clúster de Kubernetes y hasta 250 pods por nodo, con un límite total de 16 000 pods por clúster, cuando se usa el complemento.

## <a name="constraints"></a>Restricciones
- El complemento no está habilitado al implementar un clúster de Kubernetes con el clúster de [Azure Container Service (AKS)](../aks/intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [ACS](../container-service/kubernetes/container-service-intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) con Kubernetes.
- No hay ninguna compatibilidad nativa para las directivas de red de Kubernetes, incluidas las directivas de acceso o DNS.
- El complemento no es compatible con las directivas de redes por pod.

## <a name="pricing"></a>Precios
No se aplica ningún cargo por usar el complemento de CNI.

## <a name="next-steps"></a>pasos siguientes

Obtenga información sobre cómo [implementar un clúster de Kubernetes](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/deploy.md) en su [propia red virtual](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/features.md#using-azure-integrated-networking-cni).

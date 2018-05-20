---
title: Configuración de red en Azure Kubernetes Service (AKS)
description: Obtenga información acerca de la configuración de red básica y avanzada en Azure Kubernetes Service (AKS).
services: container-service
author: mmacy
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/07/2018
ms.author: marsma
ms.openlocfilehash: 80d12d1f5d6b388c46ed90eb84b7bc00250e17ff
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="network-configuration-in-azure-kubernetes-service-aks"></a>Configuración de red en Azure Kubernetes Service (AKS)

Cuando se crea un clúster de Azure Kubernetes Service (AKS), puede seleccionar entre dos opciones de redes: **Básica** o **Avanzada**.

## <a name="basic-networking"></a>Red básica

La opción de redes **Básica** es la configuración predeterminada para la creación del clúster de AKS. La configuración de red del clúster y sus pods son administradas completamente por Azure y es adecuada para implementaciones que no requieren configuración de red virtual personalizada. Cuando selecciona la red Básica, no tiene control sobre la configuración de red, como las subredes o los intervalos de direcciones IP asignados al clúster.

Los nodos de un clúster de AKS configurado para usar la red Básica utilizan el complemento de Kubernetes [kubenet][kubenet].

## <a name="advanced-networking"></a>Redes avanzadas

La configuración de redes **Avanzada** coloca sus pods en una red virtual de Azure (VNet) configurada por el usuario, proporcionando a los pods conectividad automática con los recursos de la red virtual e integración con el amplio conjunto de funcionalidades que ofrecen las redes virtuales.
La red Avanzada está disponible actualmente solo al implementar clústeres de AKS en [Azure Portal][portal] o con una plantilla de Resource Manager.

Los nodos de un clúster de AKS configurado para el uso de red Avanzada utilizan el complemento de Kubernetes [Azure Container Networking Interface (CNI)][cni-networking].

![Diagrama que muestra dos nodos con puentes que conectan cada uno a una única red virtual de Azure][advanced-networking-diagram-01]

## <a name="advanced-networking-features"></a>Características de redes avanzadas

Las redes avanzadas proporcionan las siguientes ventajas:

* Implementar el clúster de AKS en una red virtual existente o crear una nueva red virtual y subred para el clúster.
* Cada pod del clúster tiene asignada una dirección IP en la red virtual y puede comunicarse directamente con los otros pods del clúster y otras máquinas virtuales de la red virtual.
* Un pod se puede conectar con otros servicios de una red virtual emparejada y con redes locales a través de ExpressRoute y conexiones VPN de sitio a sitio (S2S). Los pods también son accesibles desde el entorno local.
* Exponer un servicio de Kubernetes de forma externa o de forma interna mediante Azure Load Balancer. También es una característica de la red Básica.
* Los pods de una subred que tienen puntos de conexión de servicio habilitados pueden conectarse de forma segura a los servicios de Azure, como Azure Storage o SQL Database.
* Usar rutas definidas por el usuario (UDR) para redirigir el tráfico desde los pods a una aplicación virtual de red.
* Los pods pueden acceder a recursos públicos de Internet. También es una característica de la red Básica.

> [!IMPORTANT]
> Cada nodo de un clúster de AKS configurado para redes avanzadas puede hospedar un máximo de **30 pods**. Cada red virtual aprovisionada para su uso con el complemento Azure CNI está limitada a **4096 direcciones IP** (/ 20).

## <a name="configure-advanced-networking"></a>Configuración de redes avanzada

Al [crear un clúster de AKS](kubernetes-walkthrough-portal.md) en Azure Portal, los parámetros siguientes son configurables para redes avanzadas:

**Red virtual**: la red virtual en la que desea implementar el clúster de Kubernetes. Si desea crear una red virtual nueva para el clúster, seleccione *Crear nueva* y siga los pasos descritos en la sección *Creación de red virtual*.

**Subred**: la subred dentro de la red virtual en la que desea implementar el clúster. Si desea crear una nueva subred en la red virtual para el clúster, seleccione *Crear nueva* y siga los pasos descritos en la sección *Creación de subred*.

**Intervalo de direcciones del servicio Kubernetes**: el intervalo de direcciones IP para las direcciones IP de servicio del clúster de Kubernetes. Este intervalo no debe estar dentro del intervalo de direcciones IP de la red virtual del clúster.

**Dirección IP del servicio DNS de Kubernetes**: la dirección IP del servicio DNS del clúster. Esta dirección debe estar dentro del  *intervalo de direcciones del servicio Kubernetes*.

**Dirección del puente de Docker**: la dirección IP y la máscara de red que se van a asignar al puente de Docker. Esta dirección IP no debe estar dentro del intervalo de direcciones IP de la red virtual del clúster.

La siguiente captura de pantalla de Azure Portal muestra un ejemplo de la configuración de estos valores durante la creación del clúster de AKS:

![Configuración de redes avanzada en Azure Portal][portal-01-networking-advanced]

## <a name="plan-ip-addressing-for-your-cluster"></a>Planeamiento de direccionamiento IP del clúster

Los clústeres configurados con redes avanzadas requieren planeamiento adicional. El tamaño de la red virtual y la subred debe ajustarse al número de pods que tiene previsto ejecutar simultáneamente en el clúster, así como a los requisitos de escalado.

Se asignan direcciones IP para los pods y los nodos del clúster desde la subred especificada dentro de la red virtual. Cada nodo se configura con una dirección IP principal, que es la dirección IP del propio nodo y 30 direcciones IP adicionales preconfiguradas por Azure CNI que se asignan a los pods programados para el nodo. Cuando se escala horizontalmente el clúster, cada nodo del mismo modo se configura de modo similar con direcciones IP de la subred.

Como se mencionó anteriormente, cada red virtual aprovisionada para su uso con el complemento Azure CNI está limitada a **4096 direcciones IP** (/ 20). Cada nodo de un clúster configurado para redes avanzadas puede hospedar un máximo de **30 pods**.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

Las siguientes preguntas y respuestas se aplican a la configuración de red **Avanzada**.

* *¿Puedo configurar redes avanzadas con la CLI de Azure?*

  Nº Las redes avanzadas están disponibles actualmente solo al implementar clústeres de AKS en Azure Portal o con una plantilla de Resource Manager.

* *¿Puedo implementar máquinas virtuales en la subred del clúster?*

  Nº No se admite la implementación de máquinas virtuales en la subred usada por el clúster Kubernetes. Las máquinas virtuales se pueden implementar en la misma red virtual, pero en una subred diferente.

* *¿Puedo configurar las directivas de red por pod?

  Nº Las directivas de redes por pod no se admiten actualmente.

* *¿Es configurable el número máximo de pods que se puede implementar en un nodo ?*

  De forma predeterminada, cada nodo puede hospedar un máximo de 30 pods. Actualmente, solo puede cambiar el valor máximo modificando la propiedad `maxPods` al implementar un clúster con una plantilla de Resource Manager.

* *¿Cómo se pueden configurar propiedades adicionales para la subred que he creado durante la creación del clúster de AKS? Por ejemplo, los puntos de conexión de servicio.*

  La lista completa de propiedades de la red virtual y las subredes que se crean durante la creación del clúster de AKS puede configurarse en la página de configuración de red virtual estándar en Azure Portal.

## <a name="next-steps"></a>Pasos siguientes

### <a name="networking-in-aks"></a>Redes en AKS

Más información acerca de las redes en AKS en los siguientes artículos:

[Uso de una dirección IP estática con el equilibrador de carga de Azure Kubernetes Service (AKS)](static-ip.md)

[Entrada HTTPS en Azure Container Service (AKS)](ingress.md)

[Uso de un equilibrador de carga interno con Azure Container Service (AKS)](internal-lb.md)

### <a name="acs-engine"></a>ACS Engine

[Azure Container Service Engine (ACS Engine)][acs-engine] es un proyecto de código abierto que genera plantillas de Azure Resource Manager que puede usar para implementar clústeres habilitados para Docker en Azure. Se pueden implementar los orquestadores Kubernetes, DC/OS, Swarm Mode y Swarm con ACS Engine.

Los clústeres de Kubernetes creados con ACS Engine admiten los complementos [kubenet][kubenet] y [Azure CNI][cni-networking]. Por lo tanto, los escenarios de redes básico y avanzado son compatibles con ACS Engine.

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[acs-engine]: https://github.com/Azure/acs-engine
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[aks-ssh]: aks-ssh.md

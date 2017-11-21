---
title: "Cambiar un emparejamiento público en Azure ExpressRoute a emparejamiento de Microsoft | Microsoft Docs"
description: "En este artículo se detallan los pasos para cambiar un emparejamiento público a emparejamiento de Microsoft en ExpressRoute."
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/07/2017
ms.author: cherylmc
ms.openlocfilehash: 311e1de3200cd684bbec1329ebd5217b4fb3a2e2
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2017
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Cambiar un emparejamiento público a emparejamiento de Microsoft

ExpressRoute ahora admite servicios de PaaS de Azure, como el almacenamiento de Azure y Azure SQL Database, a través del emparejamiento de Microsoft con filtros de ruta. Ahora solo se necesita un dominio de enrutamiento para tener acceso a PaaS de Microsoft y a los servicios de SaaS. Puede aprovechar los filtros de ruta para anunciar los prefijos del servicio de PaaS de forma selectiva en las regiones de Azure que quiere que se usen.

Este artículo le servirá para cambiar una configuración de emparejamiento público a emparejamiento de Microsoft sin sufrir ningún tiempo de inactividad. Para más información sobre cómo enrutar emparejamientos y dominios de enrutamiento, vea [Circuitos ExpressRoute y dominios de enrutamiento](expressroute-circuit-peerings.md).

> [!IMPORTANT]
> Debe tener el complemento premium de ExpressRoute para poder usar el emparejamiento de Microsoft. Para más información sobre el complemento premium, vea las [P+F de ExpressRoute](expressroute-faqs.md#expressroute-premium).

## <a name="before-you-begin"></a>Antes de empezar

* Para conectarse al emparejamiento de Microsoft, necesita configurar y administrar NAT. Puede que su proveedor de conectividad haya configurado y administrado NAT como un servicio administrado. Si tiene previsto tener acceso a PaaS de Azure y a los servicios de SaaS de Azure en el emparejamiento de Microsoft, es importante cambiar correctamente el tamaño del grupo de direcciones IP de NAT. Para más información sobre NAT para ExpressRoute, vea [Requisitos NAT para el emparejamiento de Microsoft](expressroute-nat.md#nat-requirements-for-microsoft-peering).

* Si actualmente tiene una lista de control de acceso (ACL) de red para los puntos de conexión del servicio de PaaS de Azure, cuando use el emparejamiento público de Azure, deberá asegurarse de que el grupo de direcciones IP de NAT configurado con el emparejamiento de Microsoft aparece en la lista de control de acceso configurada para el punto de conexión del servicio.

Para cambiar a un emparejamiento de Microsoft sin sufrir tiempos de inactividad, deberá realizar los pasos de este artículo en el orden en el que aparecen.

## <a name="1-create-microsoft-peering"></a>1. Crear el emparejamiento de Microsoft

Si no ha creado el emparejamiento de Microsoft, use cualquiera de los siguientes artículos para crearlo. Si su proveedor de conectividad presta servicios administrados de nivel 3, puede pedirle que habilite el emparejamiento de Microsoft en su circuito.

  * [Creación del emparejamiento de Microsoft con Azure Portal](expressroute-howto-routing-portal-resource-manager.md#msft)
  * [Creación del emparejamiento de Microsoft con Azure PowerShell](expressroute-howto-routing-arm.md#msft)
  * [Creación del emparejamiento de Microsoft con la CLI de Azure](howto-routing-cli.md#msft)

## <a name="2-validate-microsoft-peering-is-enabled"></a>2. Confirmar que el emparejamiento de Microsoft está habilitado

Compruebe que el emparejamiento de Microsoft está habilitado y que los prefijos públicos anunciados están en estado configurado.

  * [Portal de Azure](expressroute-howto-routing-portal-resource-manager.md#getmsft)
  * [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)
  * [CLI de Azure](howto-routing-cli.md#getmsft)

## <a name="3-configure-and-attach-a-route-filter-to-the-circuit"></a>3. Configurar un filtro de ruta y adjuntarlo al circuito

De forma predeterminada, los emparejamientos de Microsoft nuevos no publican ningún prefijo hasta que se adjunta un filtro de ruta al circuito. Cuando se crea una regla de filtro de ruta, puede especificar la lista de comunidades de servicio correspondientes a las regiones de Azure que quiere usar para los servicios de PaaS de Azure, como se muestra en la siguiente captura de pantalla:

![Combinar emparejamientos públicos](.\media\how-to-move-peering\public.png)

Usar cualquiera de los siguientes artículos para configurar filtros de ruta.

  * [Configuración de filtros de ruta para el emparejamiento de Microsoft: Azure Portal](how-to-routefilter-portal.md)
  * [Configuración de filtros de ruta para el emparejamiento de Microsoft: PowerShell](how-to-routefilter-powershell.md)
  * [Configuración de filtros de ruta para el emparejamiento de Microsoft: CLI de Azure](how-to-routefilter-cli.md)

## <a name="4-delete-the-public-peering"></a>4. Eliminar el emparejamiento público

Tras constatar que el emparejamiento de Microsoft está configurado y que los prefijos que se van a usar se anuncian correctamente en el emparejamiento de Microsoft, puede eliminar el emparejamiento público. Para eliminar un emparejamiento público, use cualquiera de los siguientes artículos:

  * [Eliminación del emparejamiento público de Azure con Azure Portal](expressroute-howto-routing-portal-resource-manager.md#deletepublic)
  * [Eliminación del emparejamiento público de Azure con Azure PowerShell](expressroute-howto-routing-arm.md#deletepublic)
  * [Eliminación del emparejamiento público de Azure con la CLI](howto-routing-cli.md#deletepublic)

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información acerca de ExpressRoute, consulte [P+F de ExpressRoute](expressroute-faqs.md).
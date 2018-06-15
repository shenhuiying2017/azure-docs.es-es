---
title: Escenarios de Azure DNS Private Zones | Microsoft Docs
description: Información general sobre escenarios comunes de uso de Azure DNS Private Zones.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: kumud
ms.openlocfilehash: de543913d4f8264fa8e5b3bca0c510c99c479cae
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/03/2018
ms.locfileid: "32771878"
---
# <a name="azure-dns-private-zones-scenarios"></a>Escenarios de Azure DNS Private Zones
Azure DNS Private Zones proporcionan la resolución de nombres dentro de una red virtual, así como entre redes virtuales. En este artículo, echaremos un vistazo a algunos escenarios comunes que se pueden poner en funcionamiento con esta característica. 

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>Escenario: Resolución de nombres con ámbito de una única red virtual
En este escenario, tiene una red virtual en Azure que contiene una serie de recursos, entre ellos máquinas virtuales (VM). Quiere resolver los recursos desde dentro de la red virtual mediante un nombre de dominio específico (zona DNS) y necesita que la resolución del nombre sea privada y no accesible desde Internet. Además, para las máquinas virtuales que se encuentran dentro la red virtual, necesita que Azure las registre automáticamente en la zona DNS. 

Este escenario se describe a continuación. La red virtual llamada "A" contiene dos máquinas virtuales (VNETA-VM1 y VNETA-VM2). Cada una de ellas tiene direcciones IP privadas asociadas. Una vez que se crea una zona privada denominado contoso.com y se vincula esta red virtual como una red virtual de registro, Azure DNS crea automáticamente dos registros A en la zona, como se ilustra. Ahora, las consultas de VNETA-VM1 para resolver VNETA-VM2.contoso.com recibirán una respuesta DNS que contiene la dirección IP privada de VNETA-VM2. Además, una consulta DNS inversa (PTR) de la dirección IP privada de VNETA-VM1 (10.0.0.1) emitida desde VNETA-VM2 recibirá una respuesta DNS que contiene el nombre de VNETA-VM1, según lo previsto. 

![Resolución de una única red virtual](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>Escenario: Resolución de nombres entre redes virtuales

Este escenario es el caso más común donde es necesario asociar una zona privada a varias redes virtuales. Se puede ajustar a arquitecturas tales como el modelo de concentrador y radio donde hay una red virtual central que es el concentrador a la que están conectadas otras tantas redes virtuales que son los radios. La red virtual central que funciona como concentrador se puede vincular como red virtual de registro a una zona privada, y las redes virtuales que son los radios se pueden vincular como redes virtuales de resolución. 

En el diagrama siguiente se muestra una versión sencilla de este escenario donde solo hay dos redes virtuales: A y B. A se ha designado como red virtual de registro y B como red virtual de resolución. Lo que se pretende es que ambas redes virtuales compartan una zona común: contoso.com. Cuando se crea la zona y las redes virtuales de resolución y registro están vinculadas a ella, Azure anota automáticamente los registros DNS de las máquinas virtuales (VNETA-VM1 y VNETA-VM2) de la red virtual A. También puede agregar manualmente registros DNS a la zona de las máquinas virtuales de la red virtual de resolución B. Con esta configuración, observará el comportamiento siguiente en las consultas DNS de reenvío e inversas:
* Una consulta DNS desde VNETB-VM1 en la red virtual de resolución B, de VNETA-VM1.contoso.com, recibirá una respuesta DNS que contiene la dirección IP privada de VNETA-VM1.
* Una consulta DNS inversa (PTR) desde VNETB-VM2 en la red virtual de resolución B, de 10.1.0.1, recibirá una respuesta DNS que contiene el FQDN VNETB-VM1.contoso.com. El motivo es que el ámbito de las consultas DNS inversas es la misma red virtual. 
* Una consulta DNS inversa (PTR) desde VNETB-VM3 en la red virtual de resolución B, de 10.0.0.1, recibirá NXDOMAIN. El motivo es que el ámbito de las consultas DNS inversas es únicamente la misma red virtual. 


![Resoluciones de varias redes virtuales](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>Escenario: Funcionalidad de horizonte dividido

En este escenario, tiene un caso de uso en el que quiere realizar distintos comportamientos de resolución DNS, en función de dónde se ubique el cliente (dentro de Azure o fuera, en Internet), en la misma zona DNS. Por ejemplo, puede que tenga una versión pública y privada de la aplicación con diferente funcionalidad o comportamiento, pero quiera usar el mismo nombre de dominio con ambas versiones. Este escenario puede llevarse a cabo con Azure DNS mediante la creación de una zona DNS pública, así como una zona privada, con el mismo nombre.

En el siguiente diagrama se muestra este escenario. Dispone de una red virtual A que tiene dos máquinas virtuales (VNETA-VM1 y VNETA-VM2), que tienen asignadas tanto direcciones IP privadas como públicas. Creará una zona DNS pública llamada contoso.com y registrará las direcciones IP públicas de estas máquinas virtuales como registros DNS dentro de la zona. Asimismo, creará una zona DNS privada también llamada contoso.com mediante la especificación de la red virtual A como red virtual de registro. Azure registra automáticamente las máquinas virtuales como registros A en la zona privada y apunta a sus direcciones IP privadas.

Ahora, cuando un cliente de Internet emita una consulta DNS para buscar VNETA-VM1.contoso.com, Azure devolverá el registro de dirección IP pública de la zona pública. Si se emite la misma consulta DNS desde otra máquina virtual (por ejemplo, VNETA-VM2) de la misma red virtual A, Azure devolverá el registro de dirección IP privada de la zona privada. 

![Resolución de cerebro dividido](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de las zonas DNS privadas, consulte [Using Azure DNS for private domains](private-dns-overview.md) (Uso de Azure DNS para dominios privados).

Más información sobre cómo [crear una zona DNS privada](./private-dns-getstarted-powershell.md) en Azure DNS.

Visite [Información general sobre zonas y registros de DNS](dns-zones-records.md) para obtener más información sobre zonas y registros DNS.

Obtenga información sobre las demás [funcionalidades de red](../networking/networking-overview.md) clave de Azure.


---
title: "Información general de DNS de Azure | Microsoft Docs"
description: "Información general del servicio de hospedaje de DNS en Microsoft Azure. Hospede el dominio en Microsoft Azure."
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
ms.assetid: 68747a0d-b358-4b8e-b5e2-e2570745ec3f
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: kumud
ms.openlocfilehash: f255fd9621ff90bfbb3ad193faa64495acf7ecd7
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/19/2017
---
# <a name="azure-dns-overview"></a>Introducción a DNS de Azure

El sistema de nombres de dominio, o DNS, es responsable de traducir (o resolver) el nombre del sitio web o del servicio en su dirección IP. DNS de Azure es un servicio de hospedaje para los dominios DNS, que permite resolver nombres mediante la infraestructura de Microsoft Azure. Al hospedar dominios en Azure, puede administrar los registros DNS con las mismas credenciales, API, herramientas y facturación que con los demás servicios de Azure. Azure DNS ahora es compatible con dominios DNS privados. Para obtener más información, vea [Uso de Azure DNS para dominios privados](private-dns-overview.md).

![Información general de DNS](./media/dns-overview/scenario.png)

## <a name="features"></a>Características

* **Fiabilidad y rendimiento**: los dominios DNS de DNS de Azure se hospedan en la red global de servidores de nombres DNS de Azure. Azure DNS utilicza redes de difusión por proximidad, por lo que cada consulta de DNS la responde el servidor DNS disponible más cercano. Esto ofrece un rendimiento rápido y alta disponibilidad para el dominio.

* **Integración sin problemas**: el servicio DNS de Azure puede usarse para administrar registros DNS de los servicios de Azure y para proporcionar DNS también para los recursos externos. Azure DNS está integrado en Azure Portal y usa las mismas credenciales, la misma facturación y el mismo contrato de soporte técnico que los demás servicios de Azure.

* **Seguridad**: el servicio DNS de Azure se basa en Azure Resource Manager. Como tal, se beneficia de características Resource Manager, como control de acceso basado en roles, registros de auditoría y bloqueo de recursos. Los dominios y registros pueden administrarse mediante Azure Portal, cmdlets de Azure PowerShell y la CLI de Azure multiplataforma. Las aplicaciones que requieren la administración automática de DNS pueden integrarse con el servicio a través de los SDK y API de REST.

DNS de Azure actualmente no admite la adquisición de nombres de dominio. Si desea adquirir dominios, debe usar un registrador de nombres de dominio de un tercero. El registrador suele cobrar una tarifa anual reducida. Los dominios se pueden hospedar en DNS de Azure para la administración de registros de DNS. Consulte [Delegación de un dominio en DNS de Azure](dns-domain-delegation.md) para más información.

## <a name="pricing"></a>Precios

La facturación de Azure se basa en el número de zonas DNS hospedadas en Azure y en el número de consultas DNS. Para más información sobre precios, visite [DNS de Azure Precios](https://azure.microsoft.com/pricing/details/dns/).

## <a name="faq"></a>Preguntas más frecuentes

Para ver las preguntas más frecuentes sobre DNS de Azure, vea [Azure DNS FAQ (P+F de DNS de Azure)](dns-faq.md).

## <a name="next-steps"></a>pasos siguientes

Visite [Información general sobre zonas y registros de DNS](dns-zones-records.md) para obtener más información sobre zonas y registros DNS.

Más información sobre cómo [crear una zona DNS](./dns-getstarted-create-dnszone-portal.md) en DNS de Azure.

Aprenda sobre las demás [funcionalidades de red](../networking/networking-overview.md) clave de Azure.


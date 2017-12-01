---
title: Uso de Azure DNS para dominios privados | Microsoft Docs
description: "Información general del servicio de hospedaje de DNS privado en Microsoft Azure."
services: dns
documentationcenter: na
author: KumudD
manager: jennoc
editor: 
ms.assetid: 
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2017
ms.author: kumud
ms.openlocfilehash: 95cf8ab2bd34e698e12452e062687219bad49eb6
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/21/2017
---
# <a name="using-azure-dns-for-private-domains"></a>Uso de Azure DNS para dominios privados
El Sistema de nombres de dominio, o DNS, es responsable de traducir o resolver el nombre del servicio en su dirección IP. Azure DNS es un servicio de hospedaje para dominios DNS que permite resolver nombres mediante la infraestructura de Microsoft Azure.  Además de los dominios DNS con conexión a Internet, Azure DNS ahora también admite dominios DNS privados como una característica en versión preliminar.  
 
Azure DNS proporciona un servicio DNS confiable y seguro para administrar y resolver los nombres de dominio en una red virtual sin necesidad de agregar una solución DNS personalizada. Las zonas DNS privadas permiten usar nombres de dominio personalizados en lugar de los nombres proporcionados por Azure que están disponibles actualmente.  El uso de los nombres de dominio personalizados facilitan la adaptación de la arquitectura de red virtual para satisfacer mejor las necesidades de su organización. Proporciona la resolución de nombres para las máquinas virtuales dentro de una red virtual y entre redes virtuales. Además, puede configurar nombres de zonas con una vista de horizonte dividido que permite que una zona DNS privada y pública compartan el mismo nombre.

![Información general de DNS](./media/private-dns-overview/scenario.png)

[!INCLUDE [private-dns-preview-notice](../../includes/private-dns-preview-notice.md)]

## <a name="benefits"></a>Ventajas

* **Eliminación de la necesidad de contar con soluciones DNS personalizadas.** Anteriormente, muchos clientes crearon soluciones DNS personalizadas para administrar zonas DNS en su red virtual.  La administración de zonas DNS ahora se puede realizar con la infraestructura nativa de Azure, lo que elimina la carga de tener que crear y administrar soluciones DNS personalizadas.

* **Uso de todos los tipos de registros de DNS comunes.**  Azure DNS admite los registros A, AAAA, CNAME, MX, NS, PTR, SOA, SRV y TXT.

* **Administración automática de registros de nombres de host.** Además de hospedar los registros de DNS personalizados, Azure mantiene automáticamente los registros de nombres de host de las máquinas virtuales en las redes virtuales especificadas.  Esto permite optimizar los nombres de dominio usados sin necesidad de crear soluciones DNS personalizadas ni de modificar la aplicación.

* **Resolución de nombres de host entre redes virtuales.** A diferencia de los nombres de host proporcionados por Azure, las zonas DNS privadas pueden compartirse entre redes virtuales.  Esta funcionalidad simplifica los escenarios de detección de servicios y de distintas redes, como el emparejamiento de red virtual.

* **Experiencia del usuario y herramientas familiares.** Para reducir la curva de aprendizaje, esta nueva oferta utiliza las herramientas de Azure DNS ya establecidas (PowerShell, plantillas de Resource Manager y API de REST).

* **Compatibilidad con DNS de horizonte dividido.** Azure DNS permite crear zonas con el mismo nombre que se resuelve en diferentes respuestas desde dentro de una red virtual y desde la red pública de Internet.  Un escenario típico de DNS de horizonte dividido es proporcionar una versión dedicada de un servicio para usarla dentro de la red virtual.


## <a name="pricing"></a>Precios

Las zonas DNS privadas son gratuitas durante la versión preliminar administrada. Con disponibilidad general, esta característica usará un modelo de precios basado en el uso similar a la oferta existente de Azure DNS. 


## <a name="next-steps"></a>Pasos siguientes

Más información sobre cómo [crear una zona DNS privada](./private-dns-getstarted-powershell.md) en Azure DNS.

Visite [Información general sobre zonas y registros de DNS](dns-zones-records.md) para obtener más información sobre zonas y registros DNS.

Aprenda sobre las demás [funcionalidades de red](../networking/networking-overview.md) clave de Azure.


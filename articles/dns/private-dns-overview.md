---
title: Uso de Azure DNS para dominios privados | Microsoft Docs
description: Información general del servicio de hospedaje de DNS privado en Microsoft Azure.
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
ms.openlocfilehash: 1c805819a22d26e650d13b0e41ebac00c4e52d91
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/03/2018
---
# <a name="using-azure-dns-for-private-domains"></a>Uso de Azure DNS para dominios privados
El Sistema de nombres de dominio, o DNS, es responsable de traducir o resolver el nombre del servicio en su dirección IP. Azure DNS es un servicio de hospedaje para dominios DNS que permite resolver nombres mediante la infraestructura de Microsoft Azure.  Además de los dominios DNS con conexión a Internet, Azure DNS ahora también admite dominios DNS privados como una característica en versión preliminar.  
 
Azure DNS proporciona un servicio DNS confiable y seguro para administrar y resolver los nombres de dominio en una red virtual sin necesidad de agregar una solución DNS personalizada. Las zonas DNS privadas permiten usar nombres de dominio personalizados en lugar de los nombres proporcionados por Azure que están disponibles actualmente.  El uso de los nombres de dominio personalizados facilitan la adaptación de la arquitectura de red virtual para satisfacer mejor las necesidades de su organización. Proporciona la resolución de nombres para las máquinas virtuales dentro de una red virtual y entre redes virtuales. Además, puede configurar nombres de zonas con una vista de horizonte dividido que permite que una zona DNS privada y pública compartan el mismo nombre.

![Información general de DNS](./media/private-dns-overview/scenario.png)

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="benefits"></a>Ventajas

* **Eliminación de la necesidad de contar con soluciones DNS personalizadas.** Anteriormente, muchos clientes crearon soluciones DNS personalizadas para administrar zonas DNS en su red virtual.  La administración de zonas DNS ahora se puede realizar con la infraestructura nativa de Azure, lo que elimina la carga de tener que crear y administrar soluciones DNS personalizadas.

* **Uso de todos los tipos de registros de DNS comunes.**  Azure DNS admite los registros A, AAAA, CNAME, MX, NS, PTR, SOA, SRV y TXT.

* **Administración automática de registros de nombres de host.** Además de hospedar los registros de DNS personalizados, Azure mantiene automáticamente los registros de nombres de host de las máquinas virtuales en las redes virtuales especificadas.  Esto permite optimizar los nombres de dominio usados sin necesidad de crear soluciones DNS personalizadas ni de modificar la aplicación.

* **Resolución de nombres de host entre redes virtuales.** A diferencia de los nombres de host proporcionados por Azure, las zonas DNS privadas pueden compartirse entre redes virtuales.  Esta funcionalidad simplifica los escenarios de detección de servicios y de distintas redes, como el emparejamiento de red virtual.

* **Experiencia del usuario y herramientas familiares.** Para reducir la curva de aprendizaje, esta nueva oferta utiliza las herramientas de Azure DNS ya establecidas (PowerShell, plantillas de Resource Manager y API de REST).

* **Compatibilidad con DNS de horizonte dividido.** Azure DNS permite crear zonas con el mismo nombre que se resuelve en diferentes respuestas desde dentro de una red virtual y desde la red pública de Internet.  Un escenario típico de DNS de horizonte dividido es proporcionar una versión dedicada de un servicio para usarla dentro de la red virtual.

* **Disponibilidad en todas las regiones de Azure.** Azure DNS Private Zones está disponible en todas las regiones de Azure de la nube pública de Azure. 


## <a name="capabilities"></a>Capacidades 
* Registro automático de máquinas virtuales desde una única red virtual vinculada a una zona privada como una red virtual de registro. Las máquinas virtuales se registrarán (agregarán) a la zona privada como registros A que apuntan a sus direcciones IP privadas. Además, cuando una máquina virtual en una red virtual de registro se elimina, Azure también quita automáticamente el registro DNS correspondiente de la zona privada vinculada. Tenga en cuenta que las redes virtuales de registro también funcionan de forma predeterminada como redes virtuales de resolución en el sentido de que la resolución de DNS en la zona funcionará desde cualquiera de las máquinas virtuales de la red virtual de registro. 
* Se admite la resolución DNS de reenvío entre redes virtuales vinculadas a la zona privada como redes virtuales de resolución. Para la resolución DNS entre redes virtuales, no hay ninguna dependencia explícita de que las redes virtuales se puedan emparejar unas con otras. Sin embargo, puede que los clientes deseen emparejar redes virtuales en otras situaciones (por ejemplo, tráfico HTTP).
* Se admite la búsqueda DNS inversa dentro del ámbito de la red virtual. La búsqueda inversa de DNS de una dirección IP privada dentro de la red virtual asignada a una zona privada devolverá el FQDN que incluye el nombre de host/registro, así como el nombre de zona como sufijo. 


## <a name="limitations"></a>Limitaciones
* 1 red virtual de registro por zona privada.
* Hasta 10 redes virtuales de resolución por zona privada.
* Una red virtual dada solo se puede vincular a una zona privada como red virtual de registro.
* Una red virtual determinada puede vincularse con un máximo de diez zonas privada como red virtual de resolución.
* Si se especifica una red virtual de registro, los registros DNS de las máquinas virtuales de dicha red que se registren en la zona privada no se podrán ver o recuperar desde Powershell, la CLI o las API, pero los registros de máquina virtual se registrarán y resolverán correctamente.
* El DNS inverso solo funciona en el espacio de direcciones IP privadas de la red virtual de registro.
* El DNS inverso de una dirección IP privada que no está registrada en la zona privada (por ejemplo, IP privada de una máquina virtual de una red virtual vinculada como una red virtual de resolución a una zona privada) devolverá "internal.cloudapp.net" como sufijo de DNS; sin embargo, este sufijo no se podrá resolver.   
* La red virtual debe estar vacía (es decir, sin registros de máquina virtual) cuando se vinculó inicialmente (es decir, por primera vez) a una zona privada como red virtual de registro o de resolución. Sin embargo, la red virtual podría no estar vacía para una futura vinculación como red virtual de registro o resolución con otras zonas privadas. 
* Actualmente, no se admite el reenvío condicional, por ejemplo, para habilitar la resolución entre redes de Azure y locales. Puede encontrar documentación sobre cómo los clientes pueden poner en funcionamiento este escenario con otros mecanismos en [Resolución de nombres para las máquinas virtuales e instancias de rol](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

También se recomienda leer las [preguntas frecuentes](./dns-faq.md#private-dns) para ver algunas preguntas y respuestas comunes sobre las zonas privadas en Azure DNS, como el comportamiento específico de registro y resolución DNS que se puede esperar con determinadas clases de operaciones. 


## <a name="pricing"></a>Precios

Las zonas DNS privadas son gratuitas durante la versión preliminar pública. Con disponibilidad general, esta característica usará un modelo de precios basado en el uso similar a la oferta existente de Azure DNS. 


## <a name="next-steps"></a>Pasos siguientes

Aprenda a crear una zona privada en Azure DNS mediante [PowerShell](./private-dns-getstarted-powershell.md) o la [CLI](./private-dns-getstarted-cli.md).

Obtenga información sobre algunos escenarios comunes [Escenarios de zona privada](./private-dns-scenarios.md) que pueden realizarse con zonas privadas en Azure DNS.

Lea las [preguntas frecuentes](./dns-faq.md#private-dns) para ver algunas preguntas y respuestas comunes sobre las zonas privadas en Azure DNS, como el comportamiento específico que se puede esperar con determinadas clases de operaciones. 

Visite [Información general sobre zonas y registros de DNS](dns-zones-records.md) para obtener más información sobre zonas y registros DNS.

Obtenga información sobre las demás [funcionalidades de red](../networking/networking-overview.md) clave de Azure.


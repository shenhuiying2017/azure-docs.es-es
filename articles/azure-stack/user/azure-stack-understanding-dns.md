---
title: "Información acerca de DNS en Azure Stack | Microsoft Docs"
description: "Información acerca de las características y funcionalidades de DNS en Azure Stack"
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: darmour
editor: 
ms.assetid: 60f5ac85-be19-49ac-a7c1-f290d682b5de
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: scottnap
ms.openlocfilehash: 8c023eda179ace41a082bf4a4fadc281c14db7ba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="introducing-idns-for-azure-stack"></a>Presentación de iDNS para Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

iDNS es una característica de Azure Stack que permite resolver nombres DNS externos (por ejemplo, http://www.bing.com).
También permite registrar los nombres de redes virtuales internas. Al hacerlo, puede resolver las máquinas virtuales en la misma red virtual por nombre, en lugar de por dirección IP, sin tener que especificar entradas del servidor DNS personalizadas.

Es algo con lo que siempre ha contado Azure, pero ahora también está disponible en Windows Server 2016 y Azure Stack.

## <a name="what-does-idns-do"></a>¿Para qué sirve iDNS?
Con iDNS en Azure Stack, dispone de las siguientes funcionalidades sin tener que especificar entradas del servidor DNS personalizadas.

* Servicios de resolución de nombres Compartir los servicios de resolución de nombres DNS compartidos para las cargas de trabajo del inquilino.
* Servicio DNS autoritativo para la resolución de nombres y el registro DNS en la red virtual del inquilino.
* Servicio DNS recursivo para la resolución de los nombres de Internet de las máquinas virtuales de los inquilinos. Los inquilinos ya no se necesitan especificar entradas de DNS personalizadas para resolver nombres de Internet (por ejemplo, www.bing.com).

Si lo desea, puede traer su propio DNS y utilizar servidores DNS personalizados. Pero si solo desea poder resolver nombres de DNS de Internet y conectarse a otras máquinas virtuales de la misma red virtual, aunque no especifique nada funcionará correctamente.

## <a name="what-does-idns-not-do"></a>¿Qué es lo que iDNS no hace?
Lo que iDNS no permite hacer es crear un registro DNS para un nombre que se pueda resolver desde fuera de la red virtual.

En Azure, tiene la opción de especificar una etiqueta de nombre DNS que se puede asociar con una dirección IP pública. Puede elegir la etiqueta (prefijo), pero Azure elige el sufijo, que depende de la región en la que crea la dirección IP pública.

![Captura de pantalla de etiqueta de nombre DNS](media/azure-stack-understanding-dns-in-tp2/image3.png)

En la imagen anterior, Azure creará un registro "A" en DNS para la etiqueta de nombre DNS especificada en la zona **westus.cloudapp.azure.com**. De forma conjunta, el prefijo y el sufijo constituyen un dominio nombre completo (FQDN) que se pueda resolver desde cualquier lugar de la red Internet pública.

Azure Stack solo admite iDNS para el registro de nombres interno, por lo que no puede hacer lo siguiente:

* Crear un registro DNS en una zona DNS hospedada existente (por ejemplo, local.azurestack.external).
* Crear una zona DNS (como Contoso.com).
* Crear un registro en su propia zona DNS personalizada.
* Admitir la compra de nombres de dominio.


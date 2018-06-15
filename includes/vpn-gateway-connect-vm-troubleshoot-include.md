---
title: archivo de inclusión
description: archivo de inclusión
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a22cc1f1bffb0126c4fe23bd9f28b292bfdc48a8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
ms.locfileid: "30198050"
---
Si tiene problemas para conectarse a una máquina virtual a través de su conexión VPN, compruebe los siguientes factores:

- Compruebe que la conexión VPN se ha establecido correctamente.
- Compruebe que se conecta a la dirección IP privada de la máquina virtual.
- Si puede conectarse a la máquina virtual mediante la dirección IP privada, pero no el nombre del equipo, compruebe que ha configurado el DNS correctamente. Para más información acerca de cómo funciona la resolución de nombres para las máquinas virtuales, consulte [Resolución de nombres para las máquinas virtuales e instancias de rol](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

Cuando se conecta de punto a sitio, compruebe los siguientes elementos adicionales:

- Use "ipconfig" para comprobar la dirección IPv4 asignada al adaptador de Ethernet en el equipo desde el que está intentando conectarse. Si la dirección IP está dentro del intervalo de direcciones de la red virtual a la que se va a conectar o dentro del intervalo de direcciones de su VPNClientAddressPool, esto se conoce como un espacio de direcciones superpuesto. Cuando el espacio de direcciones se superpone de esta manera, el tráfico de red no llega a Azure, sino que se mantiene en la red local.
- Compruebe que el paquete de configuración de cliente de VPN se generó después de que se especificaran las direcciones IP del servidor DNS para la red virtual. Si actualizó las direcciones IP de servidor DNS, genere un nuevo paquete de configuración de cliente de VPN e instálelo.

Para más información acerca de la solución de problemas de una conexión RDP, consulte [Solución de problemas de conexiones del Escritorio remoto a una máquina virtual de Azure](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md).
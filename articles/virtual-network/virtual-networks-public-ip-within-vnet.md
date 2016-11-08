---
title: Uso de las direcciones IP públicas en una red virtual
description: Obtenga información acerca de la configuración de una red virtual para usar direcciones IP públicas
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn

ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2016
ms.author: jdial

---
# Espacio de direcciones IP públicas en una red virtual (VNet)
Las redes virtuales pueden contener espacios de direcciones IP (bloques de direcciones RFC 1918) públicas y privadas. Cuando se agrega un intervalo de direcciones IP público, se tratará como parte del espacio de direcciones IP de redes virtuales privadas que solo es accesible dentro de la red virtual, redes virtuales interconectadas y desde su ubicación local.

La siguiente imagen muestra una red virtual que incluye espacios de direcciones IP públicas y privadas.

![Dirección IP pública conceptual](./media/virtual-networks-public-ip-within-vnet/IC775683.jpg)

## Adición de un intervalo de direcciones IP públicas
Agregue un intervalo de direcciones IP públicas del mismo modo que agregaría un intervalo de direcciones IP privadas; para ello, utilice un archivo *netcfg* o realice la adición de la configuración en el [portal de Azure](http://portal.azure.com). Puede agregar un intervalo de direcciones IP públicas al crear la red virtual, o bien puede volver atrás y agregarlo más adelante. El ejemplo siguiente muestra espacios de direcciones IP públicas y privadas configurados en la misma red virtual.

![Dirección IP pública en portal](./media/virtual-networks-public-ip-within-vnet/IC775684.png)

## ¿Hay alguna limitación de uso?
Hay algunos intervalos de direcciones IP que no están permitidos:

* 224\.0.0.0/4 (multidifusión)
* 255\.255.255.255/32 (difusión)
* 127\.0.0.0/8 (bucle invertido)
* 169\.254.0.0/16 (local de vínculo)
* 168\.63.129.16/32 (DNS interno)

## Pasos siguientes
[Administración de servidores DNS usados por una red virtual](virtual-networks-manage-dns-in-vnet.md)

<!---HONumber=AcomDC_0810_2016-->
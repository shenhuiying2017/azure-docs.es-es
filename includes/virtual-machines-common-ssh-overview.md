---
title: archivo de inclusión
description: archivo de inclusión
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 04/16/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 919582745d166cf8b3f3937f9bac4fc0dc1fe64f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/20/2018
ms.locfileid: "31613413"
---
## <a name="overview-of-ssh-and-keys"></a>Información general sobre SSH y sus claves

SSH es un protocolo de conexión cifrada que permite inicios de sesión seguros sobre conexiones no seguras. Es el protocolo de conexión predeterminado de las máquinas virtuales Linux hospedadas en Azure. Aunque el propio SSH proporciona una conexión cifrada, el uso de contraseñas con conexiones SSH deja la máquina virtual vulnerable a ataques por fuerza bruta o que se puedan averiguar las contraseñas. Un método más seguro y preferido de conectarse a una máquina virtual mediante SSH es con un par de claves públicas y privadas, a las que también se las conoce como claves SSH. 

* La *clave pública* se coloca en la máquina virtual Linux, o en cualquier otro servicio que se desee usar con una criptografía de clave pública.

* La *clave privada* es la que se introduce en la máquina virtual Linux al crear una conexión SSH para verificar la identidad. Esta clave se debe proteger, por lo que no se debe compartir.

En función de las directivas de seguridad de la organización, puede volver a usar un par de claves públicas y privadas único para obtener acceso a varias máquinas virtuales y servicios de Azure. No es necesario usar ningún par de claves independiente para cada máquina virtual o servicio al que se quiera acceder. 

La clave pública se puede compartir con cualquiera. Sin embargo, la clave privada es propiedad exclusivamente suya (o de su infraestructura de seguridad local).
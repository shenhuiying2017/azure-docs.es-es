---
title: archivo de inclusión
description: archivo de inclusión
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: cff737b8c79c44494cb0151d6a6281550401b26e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2018
ms.locfileid: "31805073"
---
## <a name="scenario"></a>Escenario

Para ilustrar cómo se crean redes virtuales y subredes, en este documento se usa el siguiente escenario:

![Escenario de red virtual](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

En este escenario se crea una red virtual denominada **TestVNet** con un bloque CIDR reservado de **192.168.0.0./16**. La red virtual contiene las subredes siguientes: 

* **FrontEnd**, con **192.168.1.0/24** como su bloque CIDR.
* **BackEnd**, con **192.168.2.0/24** como su bloque CIDR.


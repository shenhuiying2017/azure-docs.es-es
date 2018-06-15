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
ms.openlocfilehash: b91ae155761f6357e286f4742d57b97cf96d909a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2018
ms.locfileid: "31805163"
---
## <a name="scenario"></a>Escenario
Para ilustrar mejor cómo crear rutas definidas por el usuario (UDR), en este documento se usa el siguiente escenario:

![DESCRIPCIÓN DE LA IMAGEN](./media/virtual-network-create-udr-scenario-include/figure1.png)

En este escenario se crea una ruta definida por el usuario para la *subred de front-end* y otra para la *subred de back-end*, como sigue: 

* **UDR-FrontEnd**. La UDR de front-end se aplica a la subred *FrontEnd* y contiene una ruta:    
  * **RouteToBackend**. Esta ruta envía todo el tráfico a la subred de back-end y a la máquina virtual **FW1**.
* **UDR-BackEnd**. La UDR de back-end se aplica a la subred *BackEnd* y contiene una ruta:    
  * **RouteToFrontend**. Esta ruta envía todo el tráfico a la subred de front-end y a la máquina virtual **FW1**.

La combinación de estas rutas garantiza que todo el tráfico destinado de una subred a otra se enruta a la máquina virtual **FW1**, que se utiliza como aplicación virtual. También debe activar el reenvío IP para la máquina virtual **FW1**, para asegurarse de que esta recibe el tráfico destinado a otras máquinas virtuales.


---
title: "Directrices de suscripción y cuentas de Azure | Microsoft Docs"
description: "Obtenga información sobre las instrucciones de implementación y diseño clave para las suscripciones y cuentas de Azure."
documentationcenter: 
services: virtual-machines-linux
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 19343826-7eef-42a1-98be-4ec65b0f377a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: a99ab839ec9ade0049e1cc59054e333048e0208c
ms.openlocfilehash: df7291a93af5185d3bd18259a37233f7910fcabf


---
# <a name="azure-subscription-and-accounts-guidelines"></a>Directrices de suscripción y cuentas de Azure
[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

Este artículo se centra en conocer cómo enfocar la administración de cuentas y suscripciones a medida que crece su entorno y su base de usuarios.

## <a name="implementation-guidelines-for-subscriptions-and-accounts"></a>Instrucciones de implementación de suscripciones y cuentas
Decisiones:

* ¿Qué conjunto de suscripciones y cuentas necesita para hospedar su infraestructura o carga de trabajo de TI?
* ¿Cómo sería la jerarquía para adaptarse a su organización?

Tareas:

* Definir la jerarquía de organización lógica que desee administrar desde un nivel de suscripción.
* Definir las cuentas y las suscripciones necesarias de cada cuenta para que se correspondan con esta jerarquía lógica.
* Cree el conjunto de suscripciones y cuentas usando su convención de nomenclatura.

## <a name="subscriptions-and-accounts"></a>Suscripciones y cuentas 
Para trabajar con Azure, necesita una o más suscripciones a Azure. Existen recursos como redes o máquinas virtuales en esas suscripciones.

* Los clientes empresariales suelen tener una inscripción Enterprise, que es el recurso de nivel superior en la jerarquía y está asociado a una o varias cuentas.
* Para los consumidores y clientes que no tienen una inscripción Enterprise, el recurso de nivel superior es la cuenta.
* Las suscripciones están asociadas a las cuentas y puede haber una o más suscripciones por cuenta. Azure registra la información de facturación por suscripción.

Debido al límite de dos niveles de jerarquía en la relación de cuenta/suscripción, es importante adaptar la convención de nomenclatura de las cuentas y las suscripciones a las necesidades de facturación. Por ejemplo, si una empresa internacional usa Azure, puede optar por tener una cuenta por región y administrar las suscripciones a nivel regional:

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub01.png)

Por ejemplo, podría usar esta estructura:

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub02.png)

Si una región decide tener más de una suscripción asociada a un grupo determinado, la convención de nomenclatura debe incorporar un método para codificar los datos adicionales del nombre de cuenta o de suscripción. Esta organización permite manipular los datos de facturación para generar los nuevos niveles de jerarquía durante los informes de facturación:

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub03.png)

La organización podría tener un aspecto similar al del siguiente ejemplo:

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub04.png)

Microsoft proporciona una facturación detallada a través de un archivo descargable para una sola cuenta o para todas las cuentas de un contrato Enterprise.

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]




<!--HONumber=Dec16_HO3-->



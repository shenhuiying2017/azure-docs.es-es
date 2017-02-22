---
title: "Creación de FQDN para una VM de Linux en Azure Portal | Microsoft Docs"
description: "Aprenda a crear un nombre de dominio completo, o FQDN, para una máquina virtual basada en Resource Manager en Azure Portal."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2cd6c249-a737-4a0a-b5ba-e1c09e551b30
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 746ed119239d5a0a71a0663e81d9c5b259354419
ms.openlocfilehash: f687e73b4106118a89df9258f128b2ae850edd3c


---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal"></a>Crear un nombre de dominio completo en el Portal de Azure
Cuando crea una máquina virtual en el [Portal de Azure](https://portal.azure.com) mediante el modelo de implementación de Resource Manager, se crea automáticamente un recurso de IP pública para la máquina virtual. Use esta dirección IP para acceder de forma remota a la máquina virtual. Aunque el portal no crea [un nombre de dominio completo](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), o FQDN, de forma predeterminada, puede agregar uno cuando se crea la máquina virtual. Este artículo muestra los pasos para crear un nombre DNS o FQDN.

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../includes/virtual-machines-common-portal-create-fqdn.md)]

Ahora puede conectarse de manera remota a la máquina virtual con este nombre DNS, por ejemplo, con `ssh ops@mydns.westus.cloudapp.azure.com`.

## <a name="next-steps"></a>Pasos siguientes
Ahora que la máquina virtual tiene un nombre DNS y una IP pública, puede implementar marcos y servicios de aplicaciones comunes, como nginx, MongoDB, Docker, etc.

También puede leer más sobre el [uso de Resource Manager](../azure-resource-manager/resource-group-overview.md) para obtener sugerencias sobre la creación de las implementaciones de Azure.




<!--HONumber=Jan17_HO4-->



---
title: "Creación de FQDN para una VM de Windows en Azure Portal | Microsoft Docs"
description: "Aprenda a crear un nombre de dominio completo, o FQDN, para una máquina virtual basada en Resource Manager en Azure Portal."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 102/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 746ed119239d5a0a71a0663e81d9c5b259354419
ms.openlocfilehash: e89e5769701f00c6f403bd54cb2b29bcce53bf15
ms.lasthandoff: 01/26/2017


---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal"></a>Crear un nombre de dominio completo en el Portal de Azure
Cuando crea una máquina virtual en el [Portal de Azure](https://portal.azure.com) mediante el modelo de implementación de Resource Manager, se crea automáticamente un recurso de IP pública para la máquina virtual. Use esta dirección IP para acceder de forma remota a la máquina virtual. Aunque el portal no crea [un nombre de dominio completo](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), o FQDN, de forma predeterminada, puede crear uno cuando se crea la máquina virtual. Este artículo muestra los pasos para crear un nombre DNS o FQDN.

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../includes/virtual-machines-common-portal-create-fqdn.md)]

Ahora puede conectarse de forma remota a la máquina virtual con este nombre DNS, por ejemplo, para el protocolo de escritorio remoto (RDP).

## <a name="next-steps"></a>Pasos siguientes
Ahora que la máquina virtual tiene un nombre DNS y una IP pública, puede implementar marcos o servicios de aplicaciones comunes, como IIS, SQL o SharePoint.

En [Información general sobre Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) encontrará sugerencias sobre la creación de implementaciones de Azure.



---
title: "Creación de un conjunto de escalado de máquinas virtuales con Azure Portal | Microsoft Docs"
description: "Implementación de conjuntos de escalado mediante Azure Portal."
keywords: "conjuntos de escalado de máquinas virtuales"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: 9c1583f0-bcc7-4b51-9d64-84da76de1fda
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: negat
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fc52368a1a14ad094e7ab9180b90a9aa4ccdb6d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-the-azure-portal"></a>Procedimiento para crear un conjunto de escalado de máquinas virtuales con Azure Portal
En este tutorial se muestra lo fácil que resulta crear un conjunto de escalado de máquinas virtuales en unos minutos con Azure Portal. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="choose-the-vm-image-from-the-marketplace"></a>Selección de la imagen de la máquina virtual de Marketplace
En el portal, puede implementar fácilmente un conjunto de escalado con CentOS, CoreOS, Debian, Ubuntu Server, otras imágenes de Linux e imágenes de Windows Server.

En el explorador, vaya a [Azure Portal](https://portal.azure.com) . Haga clic en **Nuevo**, busque el **conjunto de escalado** y, luego, seleccione la entrada **Conjunto de escalado de máquinas virtuales**:

![conjunto de escalado de máquinas virtuales de azure portal búsqueda](./media/virtual-machine-scale-sets-portal-create/portal-search.png)

## <a name="create-the-scale-set"></a>Creación del conjunto de escalado
Ahora puede usar los valores predeterminados para crear rápidamente el conjunto de escalado.

* Escriba un nombre para el conjunto de escalado.  
Este nombre se convierte en la base del FQDN del equilibrador de carga frente al conjunto de escalado, así que asegúrese de que el nombre sea único en todo Azure.

* Seleccione el tipo de SO que desea.

* Escriba el nombre de usuario que quiera y seleccione el tipo de autenticación que desee.  
Si elige una contraseña, debe tener 12 caracteres como mínimo y reunir, al menos, tres de los cuatro requisitos de complejidad siguientes: una minúscula, una mayúscula, un número y un carácter especial. Obtenga más información acerca de los [requisitos de usuario y la contraseña](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm). Si elige **Clave pública SSH**, asegúrese de pegar solo la clave pública y *NO* la privada:

* Seleccione **Sí** o **No** para **habilitar el escalado más allá de las 100 instancias**.  
Si elige Sí, el conjunto de escalado puede abarcar varios grupos de selección de ubicación. Para obtener más información, consulte [esta documentación](./virtual-machine-scale-sets-placement-groups.md).

* Asegúrese de seleccionar un **tamaño de instancia** adecuado.  
Para más información sobre los tamaños de máquina virtual, consulte [Tamaños de las máquinas virtuales Windows](..\virtual-machines\windows\sizes.md) o [Tamaños de las máquinas virtuales Linux](..\virtual-machines\linux\sizes.md).

* Escriba el nombre del grupo de recursos y la ubicación que desee.  
Si la región y el **tamaño de la instancia** admiten las zonas de disponibilidad, el campo **Zonas de disponibilidad** aparece habilitado. Para más información sobre las zonas de disponibilidad, consulte este artículo [introductorio](../availability-zones/az-overview.md).

* Escriba la etiqueta de nombre de dominio que quiera (la base del FQDN del equilibrador de carga delante del conjunto de escalado).  
Esta etiqueta debe ser única en todo Azure.

* Elija la imagen de disco del sistema operativo que desee, el recuento de instancias y el tamaño de máquina.

* Elija el tipo de disco que desee: administrado o no administrado.  
Para obtener más información, consulte [esta documentación](./virtual-machine-scale-sets-managed-disks.md). Si quiere que el conjunto de escalado abarque varios grupos de selección de ubicación, esta opción no estará disponible porque se requiere un disco administrado para que los conjunto de escalado abarquen grupos de selección de ubicación.

* Habilite o deshabilite el escalado automático y, si lo habilita, configúrelo.

![conjunto de escalado de máquinas virtuales de azure portal creación aviso](./media/virtual-machine-scale-sets-portal-create/portal-create.png)

## <a name="connect-to-a-vm-in-the-scale-set"></a>Conexión a una máquina virtual en el conjunto de escalado
Si decide limitar su conjunto de escalado a un único grupo de selección de ubicación, el conjunto de escalado se implementará con las reglas NAT configuradas para permitirle conectarse fácilmente (si no es así, es probable que deba crear un jumpbox en la misma red virtual del conjunto de escalado para conectar con las máquinas virtuales). Para verlas, vaya a la pestaña `Inbound NAT Rules` del equilibrador de carga del conjunto de escalado:

![conjunto de escalado de máquinas virtuales de azure portal reglas nat](./media/virtual-machine-scale-sets-portal-create/portal-nat-rules.png)

Puede conectarse a las máquinas virtuales del conjunto de escalado con estas reglas NAT. Por ejemplo, para un conjunto de escalado de Windows, si hay una regla NAT en el puerto entrante 50000, se pudo conectar a esa máquina a través de RDP en `<load-balancer-ip-address>:50000`. Para un conjunto de escalado de Linux, tendría que conectarse con el comando `ssh -p 50000 <username>@<load-balancer-ip-address>`.

## <a name="next-steps"></a>Pasos siguientes
Para información sobre cómo implementar conjuntos de escalado desde la CLI, consulte [esta documentación](virtual-machine-scale-sets-cli-quick-create.md).

Para obtener información sobre cómo implementar conjuntos de escalado desde PowerShell, consulte [esta documentación](virtual-machine-scale-sets-windows-create.md).

Para información sobre cómo implementar conjuntos de escalado desde Visual Studio, consulte [esta documentación](virtual-machine-scale-sets-vs-create.md).

Para la documentación general, consulte la [página de documentación principal de los conjuntos de escalado](virtual-machine-scale-sets-overview.md).

Para información general, consulte la [página de destino principal de los conjuntos de escalado](https://azure.microsoft.com/services/virtual-machine-scale-sets/).


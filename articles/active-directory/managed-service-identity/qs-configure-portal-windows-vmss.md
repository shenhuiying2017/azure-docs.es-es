---
title: "Configuración de MSI en un conjunto de escalado de máquinas virtuales de Azure con Azure Portal"
description: "Instrucciones paso a paso para configurar una identidad de servicio administrada (MSI) en un conjunto de escalado de máquinas virtuales de Azure mediante Azure Portal."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 4d308d9cdc0405ee0041c877f5678647343631ab
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="configure-an-azure-virtual-machine-scale-set-managed-service-identity-msi-using-the-azure-portal"></a>Configuración de una identidad de servicio administrada (MSI) en un conjunto de escalado de máquinas virtuales de Azure mediante Azure Portal

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Managed Service Identity proporciona a los servicios de Azure una identidad administrada automáticamente en Azure Active Directory. Puede usar esta identidad para autenticar a cualquier servicio que admita la autenticación de Azure AD, sin necesidad de tener credenciales en el código. 

En este artículo, aprenderá a habilitar una MSI en un conjunto de escalado de máquinas virtuales de Azure, y a quitarla de ella, mediante Azure Portal.

## <a name="prerequisites"></a>requisitos previos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-azure-virtual-machine-scale-set"></a>Habilitamiento de MSI durante la creación del conjunto de escalado de máquinas virtuales de Azure

En el momento de redactar este artículo, no se admite la habilitación de MSI durante la creación de un conjunto de escalado de máquinas virtuales de Azure en Azure Portal. En su lugar, consulte la siguiente guía de inicio rápido sobre creación de un conjunto de escalado de máquinas virtuales de Azure para crear primero uno de ellos:

- [Creación de un conjunto de escalado de máquinas virtuales en Azure Portal](../../virtual-machine-scale-sets/virtual-machine-scale-sets-create-portal.md)  

A continuación, pase a la siguiente sección, donde encontrará información detallada acerca de la habilitación de MSI en el conjunto de escalado de máquinas virtuales.

## <a name="enable-msi-on-an-existing-azure-vmms"></a>Habilitación de MSI en un conjunto de escalado de máquinas virtuales de Azure existente

Si tiene un conjunto de escalado de máquinas virtuales que se aprovisionó originalmente sin una identidad MSI:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta asociada a la suscripción de Azure que contiene el conjunto de escalado de máquinas virtuales.

2. Vaya al conjunto de escalado de máquinas virtuales de Azure que desee.

3. Haga clic en la página **Configuración**; para habilitar MSI en el conjunto de escalado de máquinas virtuales, seleccione **Sí** en "Managed Service Identity" y haga clic en **Guardar**. Esta operación puede tardar 60 segundos o más en completarse:

   ![Captura de pantalla de la página de configuración](../media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)  

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>Eliminación de MSI de un conjunto de escalado de máquinas virtuales de Azure

Si tiene un conjunto de escalado de máquinas virtuales de Azure que ya no necesita una identidad de servicio administrada:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta asociada a la suscripción de Azure que contiene el conjunto de escalado de máquinas virtuales. Asegúrese también de que la cuenta pertenece a un rol que le concede permisos de escritura en el conjunto de escalado de máquinas virtuales.

2. Vaya al conjunto de escalado de máquinas virtuales de Azure que desee.

3. Haga clic en la página **Configuración**; para eliminar MSI del conjunto de escalado de máquinas virtuales, seleccione **No** en **Managed Service Identity** y haga clic en **Guardar**. Esta operación puede tardar 60 segundos o más en completarse:

   ![Captura de pantalla de la página de configuración](../media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)  

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general sobre MSI, consulte [Managed Service Identity overview](overview.md) (Introducción a Managed Service Identity).
- Mediante Azure Portal, asigne a una identidad de servicio administrada de un conjunto de escalado de máquinas virtuales de Azure [acceso a otro recurso de Azure](howto-assign-access-portal.md).

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.

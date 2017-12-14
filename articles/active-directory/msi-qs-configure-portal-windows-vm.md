---
title: "Configuración de MSI en una máquina virtual de Azure con Azure Portal"
description: "Instrucciones paso a paso para configurar una identidad de servicio administrada (MSI) en una máquina virtual de Azure, mediante Azure Portal."
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: bryanla
ms.openlocfilehash: 8decfcedec94b9d78eac73a3e8db1219fac02029
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="configure-a-vm-managed-service-identity-msi-using-the-azure-portal"></a>Configuración de Managed Service Identity (MSI) en una máquina virtual con Azure Portal

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Managed Service Identity proporciona a los servicios de Azure una identidad administrada automáticamente en Azure Active Directory. Puede usar esta identidad para autenticar a cualquier servicio que admita la autenticación de Azure AD, sin necesidad de tener credenciales en el código. 

En este artículo, aprenderá a habilitar MSI en una máquina virtual de Azure, y a quitarla de ella, mediante Azure Portal.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Habilitación de MSI durante la creación de una máquina virtual de Azure

En el momento de redactar este artículo, no se admite la habilitación de MSI durante la creación de una máquina virtual en Azure Portal. En su lugar, para crear una máquina virtual consulte uno de los siguientes artículos de la guía de inicio rápido de creación de máquinas virtuales:

- [Creación de una máquina virtual Windows desde Azure Portal](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Creación de una máquina virtuales Linux desde Azure Portal](../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

A continuación, pase a siguiente sección, donde encontrará información detallada acerca de la habilitación de MSI en la máquina virtual.

## <a name="enable-msi-on-an-existing-azure-vm"></a>Habilitación de MSI en una máquina virtual de Azure existente

Si tiene una máquina virtual que se aprovisionó originalmente sin una identidad MSI:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta asociada a la suscripción de Azure que contiene la máquina virtual. Asegúrese también de que la cuenta pertenece a un rol que le conceda permisos de escritura en la máquina virtual, como "Colaborador de la máquina virtual".

2. Vaya a la máquina virtual que desee.

2. Haga clic en la página "Configuración"; para habilitar MSI en la máquina virtual, seleccione "Sí" en "Managed Service Identity" y haga clic en **Guardar**. Esta operación puede tardar 60 segundos o más en completarse:

   ![Captura de pantalla de la página de configuración](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-msi-from-an-azure-vm"></a>Eliminación de MSI de una máquina virtual de Azure

Si tiene una máquina virtual que ya no necesita una identidad de servicio administrada:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta asociada a la suscripción de Azure que contiene la máquina virtual. Asegúrese también de que la cuenta pertenece a un rol que le conceda permisos de escritura en la máquina virtual, como "Colaborador de la máquina virtual".

2. Vaya a la máquina virtual que desee.

3. Haga clic en la página "Configuración"; para quitar MSI de la máquina virtual, seleccione "No" en "Managed Service Identity" y haga clic en **Guardar**. Esta operación puede tardar 60 segundos o más en completarse:

   ![Captura de pantalla de la página de configuración](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>Contenido relacionado

- Para obtener información general sobre MSI, consulte [Managed Service Identity overview](msi-overview.md) (Introducción a Managed Service Identity).

## <a name="next-steps"></a>Pasos siguientes

- Mediante Azure Portal, asigne a un identidad de servicio administrada de una máquina virtual de Azure [acceso a otro recurso de Azure](msi-howto-assign-access-portal.md).

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.

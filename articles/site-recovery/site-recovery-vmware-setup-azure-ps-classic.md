---
title: " Administración de un servidor de procesos que se ejecuta en Azure (clásico) | Microsoft Docs"
description: "En este artículo se describe cómo configurar un servidor de procesos (clásico) de conmutación por recuperación en Azure."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/29/2017
ms.author: anoopkv
ms.openlocfilehash: 479bbd207bcf715138c340f9e4d2634120bab85c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="manage-a-process-server-running-in-azure-classic"></a>Administración de un servidor de procesos que se ejecuta en Azure (clásico)
> [!div class="op_single_selector"]
> * [Azure clásico](./site-recovery-vmware-setup-azure-ps-classic.md)
> * [Resource Manager](./site-recovery-vmware-setup-azure-ps-resource-manager.md)

Durante la conmutación por recuperación, se recomienda implementar el servidor de procesos en Azure si hay una latencia elevada entre la red virtual de Azure y la red local. En este artículo se describe cómo instalar, configurar y administrar los servidores de procesos que se ejecutan en Azure.

> [!NOTE]
> Este artículo se utiliza si ha usado Clásico como modelo de implementación para las máquinas virtuales durante la conmutación por error. Si ha usado el modelo Resource Manager como modelo de implementación, siga los pasos de [Administración de un servidor de procesos de conmutación por recuperación (Resource Manager)](./site-recovery-vmware-setup-azure-ps-resource-manager.md).

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [site-recovery-vmware-process-server-prereq](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-on-azure"></a>Implementación de un servidor de procesos en Azure

1. En Azure Marketplace, cree una máquina virtual utilizando el **servidor de procesos de Microsoft Azure Site Recovery V2** </br>
    ![Marketplace_image_1](./media/site-recovery-vmware-setup-azure-ps-classic/marketplace-ps-image.png)
2. Asegúrese de seleccionar **Clásico** como modelo de implementación. </br>
  ![Marketplace_image_2](./media/site-recovery-vmware-setup-azure-ps-classic/marketplace-ps-image-classic.png)
3. En el Asistente para creación de máquina virtual > Configuración básica, asegúrese de activar la suscripción y ubicación en donde ha conmutado por error las máquinas virtuales.</br>
  ![create_image_1](./media/site-recovery-vmware-setup-azure-ps-classic/azureps-classic-basic-info.png)
4. Asegúrese de que la máquina virtual está conectada a la red virtual de Azure a la que está conectada máquina virtual con conmutación por error.</br>
  ![create_image_2](./media/site-recovery-vmware-setup-azure-ps-classic/azureps-classic-settings.png)
5. Cuando se aprovisiona la máquina virtual del servidor de procesos, debe iniciar sesión y registrarlo con el servidor de configuración.

> [!NOTE]
> Para poder usar este servidor de procesos para la conmutación por recuperación, hay que registrarlo con el servidor de configuración local.

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Registro del servidor de procesos (que se ejecuta en Azure) en un servidor de configuración (que se ejecuta de forma local)

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

## <a name="upgrading-the-process-server-to-latest-version"></a>Actualización del servidor de procesos a la versión más reciente.

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-upgrade-process-server.md)]

## <a name="unregistering-the-process-server-running-in-azure-from-a-configuration-server-running-on-premises"></a>Anulación del registro del servidor de procesos (que se ejecuta en Azure) desde un servidor de configuración (que se ejecuta de forma local)

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

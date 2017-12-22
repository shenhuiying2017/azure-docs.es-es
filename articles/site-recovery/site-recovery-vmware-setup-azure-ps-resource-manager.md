---
title: " Administración de un servidor de procesos que se ejecuta en Azure (Resource Manager) | Microsoft Docs"
description: "En este artículo se describe cómo configurar un servidor de procesos (Resource Manager) de conmutación por recuperación en Azure."
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
ms.date: 11/23/2017
ms.author: anoopkv
ms.openlocfilehash: 035336efa6be0d00c41baba168eaffd80939cc82
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="manage-a-process-server-running-in-azure-resource-manager"></a>Administración de un servidor de procesos que se ejecuta en Azure (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](./site-recovery-vmware-setup-azure-ps-resource-manager.md)
> * [Clásico](./site-recovery-vmware-setup-azure-ps-classic.md)

Durante la conmutación por recuperación, se recomienda implementar el servidor de procesos en Azure si hay una latencia elevada entre Azure Virtual Network y la red local. En este artículo se describe cómo instalar, configurar y administrar los servidores de procesos que se ejecutan en Azure.

> [!NOTE]
> Este artículo se utiliza si usa **Resource Manager** como modelo de implementación para las máquinas virtuales durante la conmutación por error. Si ha usado el modelo **clásico** para la implementación, siga los pasos descritos en [artículo sobre cómo instalar y configurar un servidor de procesos de conmutación por recuperación (clásico)](./site-recovery-vmware-setup-azure-ps-classic.md).

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-on-azure"></a>Implementación de un servidor de procesos en Azure
1. En Almacén > **Infraestructura de Site Recovery** (en el encabezado Administrar) > **Servidores de configuración** (en el encabezado "Para VMware y máquinas físicas"), seleccione el servidor de configuración.
2. En la página de detalles del servidor de configuración que aparece, haga clic en "+ Servidor de procesos".

  ![Adición de una galería del servidor de procesos](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps.png)

3.  En la página **Agregar servidor de procesos**, seleccione los siguientes valores:

  ![Adición de un elemento de la galería del servidor de procesos](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps-page-1.png)
|**Nombre del campo**|**Valor**|
|-|-|
|Elija dónde desea implementar el servidor de procesos.|Seleccione el valor **Deploy a failback process server in Azure** (Implementar un servidor de procesos de conmutación de recuperación en Azure). |
|La suscripción|Seleccione la suscripción de Azure donde ha conmutado por error las máquinas virtuales|
|Grupo de recursos|Puede crear un grupo de recursos para implementar este servidor de procesos u optar por implementar el servidor de procesos en un grupo de recursos existente.|
|Ubicación|Seleccione el centro de datos de Azure en donde se han conmutado por error las máquinas virtuales.|
|Red de Azure|Seleccione la instancia de Azure Virtual Network en donde se han conmutado por error las máquinas virtuales. Si ha conmutado por error máquinas virtuales en varias redes virtuales de Azure, necesita un servidor de proceso implementado por red virtual.|

4. Rellene el resto de las propiedades en el servidor de procesos.

  ![Adición de un resumen del servidor de procesos](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps-page-2.png)
|**Nombre del campo**|**Valor**|
|-|-|
|Nombre del servidor|Nombre para mostrar o nombre de host para la máquina virtual del servidor de procesos|
| User Name|Nombre de usuario que se convierte en un administrador de esa máquina virtual|
|Cuenta de almacenamiento|Nombre de la cuenta de almacenamiento donde se coloca del disco virtual de la máquina virtual|
|Subred|Subred de la red virtual de Azure donde están conectadas las máquinas virtuales|
| Dirección IP|La dirección IP que desea que asuma el servidor de procesos una vez haya iniciado.|
5. Haga clic en el botón Aceptar para iniciar la implementación de la máquina virtual del servidor de procesos.

> [!NOTE]
> Para poder usar este servidor de procesos para la conmutación por recuperación, hay que registrarlo con el servidor de configuración local.

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Registro del servidor de procesos (que se ejecuta en Azure) en un servidor de configuración (que se ejecuta de forma local)

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

## <a name="upgrading-the-process-server-to-latest-version"></a>Actualización del servidor de procesos a la versión más reciente.

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-upgrade-process-server.md)]

## <a name="unregistering-the-process-server-running-in-azure-from-a-configuration-server-running-on-premises"></a>Anulación del registro del servidor de procesos (que se ejecuta en Azure) desde un servidor de configuración (que se ejecuta de forma local)

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

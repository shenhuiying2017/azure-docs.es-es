---
title: "Configuración del entorno de origen (VMware a Azure) | Microsoft Docs"
description: "En este artículo se describe cómo configurar el entorno local para comenzar a replicar máquinas virtuales de VMware en Azure."
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/22/2018
ms.author: anoopkv
ms.openlocfilehash: 2b6b0e5cc95f28b5596e7e898f5f035e3647d9c5
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/24/2018
---
# <a name="set-up-the-source-environment-vmware-to-azure"></a>Configuración del entorno de origen (VMware a Azure)
> [!div class="op_single_selector"]
> * [VMware a Azure](./site-recovery-set-up-vmware-to-azure.md)
> * [Físico en Azure](./site-recovery-set-up-physical-to-azure.md)

En este artículo se describe cómo configurar el entorno local de origen para replicar máquinas virtuales que se ejecutan en VMware en Azure. Incluye los pasos para seleccionar el escenario de replicación, configurar una máquina local como el servidor de configuración de Site Recovery y detectar automáticamente las máquinas virtuales locales. 

## <a name="prerequisites"></a>requisitos previos

En este artículo se supone que ya ha seguido estos pasos:
- [Configurar los recursos](tutorial-prepare-azure.md) en [Azure Portal](http://portal.azure.com).
- [Configurar VMware local](tutorial-prepare-on-premises-vmware.md), incluida una cuenta dedicada para la detección automática.



## <a name="choose-your-protection-goals"></a>Selección de los objetivos de protección

1. En Azure Portal, vaya a la hoja de los almacenes de **Recovery Services** y seleccione su almacén.
2. En el menú de recursos del almacén, haga clic en **Introducción** > **Site Recovery** > **Paso 1: Preparar la infraestructura** > **Objetivo de protección**.

    ![Elegir objetivos](./media/site-recovery-set-up-vmware-to-azure/choose-goals.png)
3. En **Objetivo de protección**, seleccione **To Azure** (En Azure) y **Yes, with VMware vSphere Hypervisor** (Sí, con VMware vSphere Hypervisor). A continuación, haga clic en **Aceptar**.

    ![Elegir objetivos](./media/site-recovery-set-up-vmware-to-azure/choose-goals2.png)

## <a name="set-up-the-configuration-server"></a>Configuración del servidor de configuración

Para configurar el servidor de configuración como una máquina virtual VMware local, use una plantilla de Open Virtualization Format (OVF). [Más información](concepts-vmware-to-azure-architecture.md) sobre los componentes que se instalarán en la máquina virtual VMware. 

1. Obtenga más información sobre los [requisitos previos](how-to-deploy-configuration-server.md#prerequisites) para la implementación del servidor de configuración. [Compruebe los números que representan la capacidad](how-to-deploy-configuration-server.md#capacity-planning) para la implementación.
2. [Descargue](how-to-deploy-configuration-server.md#download-the-template) e [importe](how-to-deploy-configuration-server.md#import-the-template-in-vmware) la plantilla de OVF (how-to-deploy-configuration-server.md) para configurar una máquina virtual VMware local que se ejecute en el servidor de configuración.
3. Encienda la máquina virtual VMware y [regístrela](how-to-deploy-configuration-server.md#register-the-configuration-server) en el almacén de Recovery Services.


## <a name="add-the-vmware-account-for-automatic-discovery"></a>Incorporación de la cuenta de VMware para detección automática

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

## <a name="connect-to-the-vmware-server"></a>Conexión al servidor de VMware

Para permitir que Azure Site Recovery detecte las máquinas virtuales que se ejecutan en el entorno local, debe conectar los hosts ESXi de vSphere o servidor vCenter de VMware con Site Recovery.

Seleccione **+vCenter** para comenzar a conectar un servidor vCenter de VMware o un host ESXi de vSphere de VMware.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>Problemas comunes
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>pasos siguientes
[Configure el entorno de destino](./site-recovery-prepare-target-vmware-to-azure.md) en Azure.

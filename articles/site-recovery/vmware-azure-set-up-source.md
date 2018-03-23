---
title: Configuración del entorno de origen para la replicación de VMware en Azure con Azure Site Recovery | Microsoft Docs
description: En este artículo se describe cómo configurar el entorno local para replicar máquinas virtuales de VMware en Azure con Azure Site Recovery.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: b2c564e8d49e39d9cdc09d3fe168388d579de70e
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Configuración del entorno de origen para la replicación de VMware en Azure

En este artículo se describe cómo configurar el entorno local de origen para replicar máquinas virtuales de VMware en Azure. Incluye los pasos para seleccionar el escenario de replicación, configurar una máquina local como el servidor de configuración de Site Recovery y detectar automáticamente las máquinas virtuales locales. 

## <a name="prerequisites"></a>requisitos previos

En este artículo se supone que ya ha seguido estos pasos:
- [Configurar los recursos](tutorial-prepare-azure.md) en [Azure Portal](http://portal.azure.com).
- [Configurar VMware local](vmware-azure-tutorial-prepare-on-premises.md), incluida una cuenta dedicada para la detección automática.



## <a name="choose-your-protection-goals"></a>Selección de los objetivos de protección

1. En Azure Portal, vaya a la hoja de los almacenes de **Recovery Services** y seleccione su almacén.
2. En el menú de recursos del almacén, haga clic en **Introducción** > **Site Recovery** > **Paso 1: Preparar la infraestructura** > **Objetivo de protección**.

    ![Elegir objetivos](./media/vmware-azure-set-up-source/choose-goals.png)
3. En **Objetivo de protección**, seleccione **To Azure** (En Azure) y **Yes, with VMware vSphere Hypervisor** (Sí, con VMware vSphere Hypervisor). A continuación, haga clic en **Aceptar**.

    ![Elegir objetivos](./media/vmware-azure-set-up-source/choose-goals2.png)

## <a name="set-up-the-configuration-server"></a>Configuración del servidor de configuración

Para configurar el servidor de configuración como una máquina virtual VMware local, use una plantilla de Open Virtualization Format (OVF). [Más información](concepts-vmware-to-azure-architecture.md) sobre los componentes que se instalarán en la máquina virtual VMware. 

1. Obtenga más información sobre los [requisitos previos](vmware-azure-deploy-configuration-server.md#prerequisites) para la implementación del servidor de configuración.
2. [Compruebe los números que representan la capacidad](vmware-azure-deploy-configuration-server.md#capacity-planning) para la implementación.
3. [Descargue](vmware-azure-deploy-configuration-server.md#download-the-template) e [importe](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) la plantilla de OVF (how-to-deploy-configuration-server.md) para configurar una máquina virtual VMware local que se ejecute en el servidor de configuración.
4. Encienda la máquina virtual VMware y [regístrela](vmware-azure-deploy-configuration-server.md#register-the-configuration-server) en el almacén de Recovery Services.


## <a name="add-the-vmware-account-for-automatic-discovery"></a>Incorporación de la cuenta de VMware para detección automática

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

## <a name="connect-to-the-vmware-server"></a>Conexión al servidor de VMware

Para permitir que Azure Site Recovery detecte las máquinas virtuales que se ejecutan en el entorno local, debe conectar los hosts ESXi de vSphere o servidor vCenter de VMware con Site Recovery.

Seleccione **+vCenter** para comenzar a conectar un servidor vCenter de VMware o un host ESXi de vSphere de VMware.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>Problemas comunes
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Pasos siguientes
[Configure el entorno de destino](./vmware-azure-set-up-target.md) en Azure.

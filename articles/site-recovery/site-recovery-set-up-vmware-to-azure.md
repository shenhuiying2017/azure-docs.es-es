---
title: "Configuración del entorno de origen (VMware a Azure) | Microsoft Docs"
description: "En este artículo se describe cómo configurar el entorno local para comenzar a replicar máquinas virtuales de VMware en Azure."
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
ms.openlocfilehash: 32a3f7498d3c8891178818436e33221f91ae2f8f
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="set-up-the-source-environment-vmware-to-azure"></a>Configuración del entorno de origen (VMware a Azure)
> [!div class="op_single_selector"]
> * [VMware a Azure](./site-recovery-set-up-vmware-to-azure.md)
> * [Físico en Azure](./site-recovery-set-up-physical-to-azure.md)

En este artículo se describe cómo configurar el entorno local para comenzar a replicar máquinas virtuales que se ejecutan en VMware en Azure.

## <a name="prerequisites"></a>Requisitos previos

En este artículo se supone que ya creó:
- Un almacén de Recovery Services en [Azure Portal](http://portal.azure.com "Azure Portal").
- Una cuenta dedicada en el vCenter de VMware que se puede usar para [detección automática](./site-recovery-vmware-to-azure.md).
- Una máquina virtual en donde instalar el servidor de configuración.

## <a name="configuration-server-minimum-requirements"></a>Requisitos mínimos del servidor de configuración
En la siguiente tabla se muestran los requisitos mínimos de hardware, software y red para un servidor de configuración.

> [!IMPORTANT]
> Al implementar un servidor de configuración para la protección de máquinas virtuales de VMware, se recomienda implementarlo como una máquina virtual con **alta disponibilidad (HA)**.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> El servidor de configuración no admite los servidores proxy basados en HTTPS.

## <a name="choose-your-protection-goals"></a>Selección de los objetivos de protección

1. En Azure Portal, vaya a la hoja de los almacenes de **Recovery Services** y seleccione su almacén.
2. En el menú de recursos del almacén, haga clic en **Introducción** > **Site Recovery** > **Paso 1: Preparar la infraestructura** > **Objetivo de protección**.

    ![Elegir objetivos](./media/site-recovery-set-up-vmware-to-azure/choose-goals.png)
3. En **Objetivo de protección**, seleccione **To Azure** (En Azure) y **Yes, with VMware vSphere Hypervisor** (Sí, con VMware vSphere Hypervisor). y, a continuación, haga clic en **Aceptar**.

    ![Elegir objetivos](./media/site-recovery-set-up-vmware-to-azure/choose-goals2.png)

## <a name="set-up-the-source-environment"></a>Configuración del entorno de origen
Configurar el entorno de origen implica dos actividades principales:

- Instale y registre un servidor de configuración con Site Recovery.
- Detecte sus máquinas virtuales locales a través de la conexión de Site Recovery con los hosts EXSi de vSphere o vCenter de VMware locales.

### <a name="step-1-install-and-register-a-configuration-server"></a>Paso 1: Instalar y registrar un servidor de configuración

1. Haga clic en **Paso 1: Preparar la infraestructura** > **Origen**. En **Preparar origen**, si no tiene un servidor de configuración, haga clic en **+Servidor de configuración** para agregar uno.

    ![Configurar origen](./media/site-recovery-set-up-vmware-to-azure/set-source1.png)
2. En la hoja **Agregar servidor**, compruebe que **Servidor de configuración** aparezca en **Tipo de servidor**.
4. Descargue el archivo de instalación unificada de Site Recovery.
5. Descargue la clave de registro del almacén. Necesita la clave de registro cuando ejecuta la instalación unificada. La clave será válida durante cinco días a partir del momento en que se genera.

    ![Configurar origen](./media/site-recovery-set-up-vmware-to-azure/set-source2.png)
6. En la máquina que usa como el servidor de configuración, ejecute la **instalación unificada de Azure Site Recovery** para instalar el servidor de configuración, el servidor de procesos y el servidor de destino maestro.

#### <a name="run-azure-site-recovery-unified-setup"></a>Ejecución de la instalación unificada de Azure Site Recovery

> [!TIP]
> El registro del servidor de configuración presenta errores si la hora del reloj del sistema de su equipo difiere más de cinco minutos con respecto a la hora local. Sincronice el reloj del sistema con un [servidor horario](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) antes de comenzar la instalación.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> El servidor de configuración se puede instalar a través de la línea de comandos. Para más información, consulte [Instalación del servidor de configuración con herramientas de línea de comandos](http://aka.ms/installconfigsrv).

#### <a name="add-the-vmware-account-for-automatic-discovery"></a>Incorporación de la cuenta de VMware para detección automática

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

### <a name="step-2-add-a-vcenter"></a>Paso 2: Agregar un vCenter
Para permitir que Azure Site Recovery detecte las máquinas virtuales que se ejecutan en el entorno local, debe conectar los hosts ESXi de vSphere o servidor vCenter de VMware con Site Recovery.

Seleccione **+vCenter** para comenzar a conectar un servidor vCenter de VMware o un host ESXi de vSphere de VMware.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>Problemas comunes
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Pasos siguientes
[Configure el entorno de destino](./site-recovery-prepare-target-vmware-to-azure.md) en Azure.

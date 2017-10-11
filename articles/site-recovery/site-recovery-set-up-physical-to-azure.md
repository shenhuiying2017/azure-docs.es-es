---
title: "Configuración del entorno de origen (servidores físicos a Azure) | Microsoft Docs"
description: "En este artículo se describe cómo configurar el entorno local para comenzar a replicar servidores físicos que ejecutan Windows o Linux en Azure."
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
ms.openlocfilehash: 49b9d2e21dbcb612828a25f21ed4382327d6f64c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="set-up-the-source-environment-physical-server-to-azure"></a>Configuración del entorno de origen (servidores físicos a Azure)
> [!div class="op_single_selector"]
> * [VMware a Azure](./site-recovery-set-up-vmware-to-azure.md)
> * [Físico a Azure](./site-recovery-set-up-physical-to-azure.md)

En este artículo se describe cómo configurar el entorno local para comenzar a replicar servidores físicos que ejecutan Windows o Linux en Azure.

## <a name="prerequisites"></a>Requisitos previos

En este artículo se supone que ya cuenta con lo siguiente:
1. Un almacén de Recovery Services en [Azure Portal](http://portal.azure.com "Azure Portal").
3. Un equipo físico para instalar el servidor de configuración.

### <a name="configuration-server-minimum-requirements"></a>Requisitos mínimos del servidor de configuración
En la siguiente tabla se muestran los requisitos mínimos de hardware, software y red para un servidor de configuración.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> El servidor de configuración no admite los servidores proxy basados en HTTPS.

## <a name="choose-your-protection-goals"></a>Selección de los objetivos de protección

1. En Azure Portal, vaya a la hoja de los almacenes de **Recovery Services** y seleccione su almacén.
2. En el menú de **Recurso** del almacén, haga clic en **Introducción** > **Site Recovery** > **Paso 1: Preparar la infraestructura** > **Objetivo de protección**.

    ![Elegir objetivos](./media/site-recovery-set-up-physical-to-azure/choose-goals.png)
3. En **Objetivo de protección**, seleccione **To Azure** (En Azure) y, luego, **No virtualizado/otro**; después haga clic en **Aceptar**.

    ![Elegir objetivos](./media/site-recovery-set-up-physical-to-azure/physical-protection-goal.PNG)

## <a name="set-up-the-source-environment"></a>Configuración del entorno de origen

1. En **Preparar origen**, si no tiene un servidor de configuración, haga clic en **+Servidor de configuración** para agregar uno.

  ![Configurar origen](./media/site-recovery-set-up-physical-to-azure/plus-config-srv.png)
2. En la hoja **Agregar servidor**, compruebe que **Servidor de configuración** aparezca en **Tipo de servidor**.
4. Descargue el archivo de instalación unificada de Site Recovery.
5. Descargue la clave de registro del almacén. Necesita la clave de registro cuando ejecuta la instalación unificada. La clave será válida durante cinco días a partir del momento en que se genera.

    ![Configurar origen](./media/site-recovery-set-up-physical-to-azure/set-source2.png)
6. En la máquina que usa como el servidor de configuración, ejecute la **instalación unificada de Azure Site Recovery** para instalar el servidor de configuración, el servidor de procesos y el servidor de destino maestro.

#### <a name="run-azure-site-recovery-unified-setup"></a>Ejecución de la instalación unificada de Azure Site Recovery

> [!TIP]
> El registro del servidor de configuración presenta errores si la hora del reloj del sistema de su equipo tiene más de cinco minutos de diferencia con respecto a la hora local. Sincronice el reloj del sistema con un [servidor horario](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) antes de comenzar la instalación.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> El servidor de configuración se puede instalar a través de una línea de comandos. Para obtener más información, consulte [Instalación del servidor de configuración con herramientas de línea de comandos](http://aka.ms/installconfigsrv).


## <a name="common-issues"></a>Problemas comunes

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Pasos siguientes

El paso siguiente implica [configurar el entorno de destino](./site-recovery-prepare-target-physical-to-azure.md) en Azure.

---
title: "Configuración del origen y el destino para la replicación del servidor físico en Azure con Azure Site Recovery | Microsoft Docs"
description: "Se resumen los pasos para configurar los valores de origen y de destino para la replicación de servidores físicos en Azure Storage con el servicio Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 2e3d03bc-4e53-4590-8a01-f702d3cc9500
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: e89bbf5a2c1d71852e49da43d3106a05ebfc28a8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="step-7-set-up-the-source-and-target-for-physical-server-replication-to-azure"></a>Paso 7: Configuración del origen y el destino para la replicación del servidor físico en Azure

En este artículo se describe cómo configurar las opciones de origen y de destino al replicar servidores físicos locales en Azure mediante el servicio [Azure Site Recovery](site-recovery-overview.md) en Azure Portal.

Publique cualquier comentario o pregunta en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="set-up-the-source-environment"></a>Configuración del entorno de origen

Configure el servidor de configuración, regístrelo en el almacén y detecte las máquinas virtuales.

1. Haga clic en **Site Recovery** > **Paso 1: Preparar la infraestructura** > **Origen**.
2. Si no tiene un servidor de configuración, haga clic en **+Servidor de configuración**.
3. En **Agregar servidor**, compruebe que aparezca **Servidor de configuración** en **Tipo de servidor**.
4. Descargue el archivo de instalación unificada de Site Recovery.
5. Descargue la clave de registro del almacén. Se le solicitará cuando ejecute la instalación unificada. La clave será válida durante cinco días a partir del momento en que se genera.

   ![Configurar origen](./media/vmware-walkthrough-source-target/set-source2.png)


## <a name="register-the-configuration-server-in-the-vault"></a>Registro del servidor de configuración en el almacén

Haga lo siguiente antes de empezar y, después, ejecute la instalación unificada para instalar el servidor de configuración, el servidor de procesos y el servidor de destino maestro. En el vídeo se describe cómo configurar los componentes de la máquina virtual de VMware para la replicación de Azure. Pero este proceso también es válido para la replicación de Azure de un servidor físico.

- En la máquina virtual del servidor de configuración, asegúrese de que el reloj del sistema está sincronizado con un [servidor horario](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Deben ser iguales. Si hay una diferencia de 15 minutos, antes o después, se podría producir un error en la instalación.
- Ejecute la instalación como Administrador local en la máquina del servidor de configuración.
- Asegúrese de que TLS 1.0 está habilitada en la máquina virtual.


[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> El servidor de configuración también se puede instalar [desde la línea de comandos](http://aka.ms/installconfigsrv).




## <a name="set-up-the-target-environment"></a>Configuración del entorno de destino

Antes de configurar el entorno de destino, asegúrese de tener una cuenta de Azure Storage y una red virtual configurada.

1. Haga clic en **Preparar infraestructura** > **Destino** y seleccione la suscripción de Azure que quiere usar.
2. Especifique si el modelo de implementación de destino está basado en Resource Manager o si es el clásico.
3. Site Recovery comprueba que tiene una o más cuentas de almacenamiento y redes compatibles.

   ![Destino](./media/physical-walkthrough-source-target/gs-target.png)

4. Si no ha creado una cuenta de almacenamiento ni una red, haga clic en **+Cuenta de almacenamiento** o **+Red** para crear una cuenta o red de Resource Manager en línea.

## <a name="next-steps"></a>Pasos siguientes

Vaya al [Paso 8: Configuración de una directiva de replicación](physical-walkthrough-replication.md)

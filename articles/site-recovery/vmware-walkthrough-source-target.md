---
title: "Configuración del origen y el destino de la replicación de VMware en Azure con Azure Site Recovery | Microsoft Docs"
description: "Se resumen los pasos para configurar los valores de origen y de destino para la replicación de máquinas virtuales VMware en Azure Storage con Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d99e422e-daf7-4fa8-af3c-af2340340136
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 94b629a62c3a54eee69ee397b2f27e3f20b753d5
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="step-8-set-up-the-source-and-target-for-vmware-replication-to-azure"></a>Paso 8: Configuración del origen y el destino de la replicación de VMware en Azure

En este artículo se describe cómo configurar opciones de origen y destino al replicar máquinas virtuales de VMware locales en Azure mediante el servicio [Azure Site Recovery](site-recovery-overview.md) en Azure Portal.

Publique cualquier comentario o pregunta en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="set-up-the-source-environment"></a>Configuración del entorno de origen

Configure el servidor de configuración, regístrelo en el almacén y detecte máquinas virtuales.

1. Haga clic en **Site Recovery** > **Paso 1: Preparar la infraestructura** > **Origen**.
2. Si no tiene un servidor de configuración, haga clic en **+Servidor de configuración**.
3. En **Agregar servidor**, compruebe que aparezca **Servidor de configuración** en **Tipo de servidor**.
4. Descargue el archivo de instalación unificada de Site Recovery.
5. Descargue la clave de registro del almacén. Se le solicitará cuando ejecute la instalación unificada. La clave será válida durante cinco días a partir del momento en que se genera.

   ![Configurar origen](./media/vmware-walkthrough-source-target/set-source2.png)


## <a name="register-the-configuration-server-in-the-vault"></a>Registro del servidor de configuración en el almacén

Haga lo siguiente antes de empezar y, después, ejecute la instalación unificada para instalar el servidor de configuración, el servidor de procesos y el servidor de destino maestro.
    - Vea un vídeo introductorio rápido

        > [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video1-Source-Infrastructure-Setup/player]

    - En la máquina virtual del servidor de configuración, asegúrese de que el reloj del sistema está sincronizado con un [servidor horario](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Deben ser iguales. Si hay una diferencia de 15 minutos, antes o después, se podría producir un error en la instalación.
    - Ejecute la instalación como administrador Local en la máquina virtual del servidor de configuración.
    - Asegúrese de que TLS 1.0 está habilitada en la máquina virtual.


[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> El servidor de configuración también se puede instalar [desde la línea de comandos](http://aka.ms/installconfigsrv).



## <a name="connect-to-vmware-servers"></a>Conexión a servidores de VMware

Para permitir que Azure Site Recovery detecte las máquinas virtuales que se ejecutan en el entorno local, debe conectar los hosts ESXi de vSphere o servidor vCenter de VMware con Site Recovery. Antes de empezar, tenga en cuenta lo siguiente:

- Si agrega el servidor vCenter o los hosts de vSphere a Site Recovery con una cuenta sin privilegios de administrador en el servidor, la cuenta necesita que se habiliten estos privilegios:
    - Centro de datos, Almacén de datos, Carpeta, Host, Red, Recurso, Máquina virtual, vSphere Distributed Switch.
    - El servidor vCenter necesita el permiso Vistas de almacenamiento.
- Cuando se agregan servidores VMware a Site Recovery, pueden tardar 15 minutos o más en aparecer en el portal.

### <a name="add-the-account-for-automatic-discovery"></a>Incorporación de la cuenta para detección automática

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

### <a name="set-up-a-connection"></a>Configuración de una conexión

Conéctese a los servidores de la manera siguiente:

1. Seleccione **+vCenter** para comenzar a conectar un servidor vCenter de VMware o un host ESXi de vSphere de VMware.
2. En **Add vCenter** (Agregar vCenter), especifique un nombre descriptivo para el host de vSphere o el servidor vCenter y especifique la dirección IP o el FQDN del servidor.
3. Deje el puerto 443 a menos que los servidores de VMware estén configurados para escuchar las solicitudes en un puerto diferente. Seleccione la cuenta que va a conectar al servidor de VMware vCenter o vSphere ESXi. Haga clic en **Aceptar**.
4. Site Recovery se conecta a los servidores de VMware con la configuración especificada y detecta máquinas virtuales.

> [!NOTE]
> Si agrega un servidor o host con una cuenta que no tiene privilegios de administrador en el servidor vCenter o el servidor host, asegúrese de que la cuenta tiene habilitados los siguientes privilegios: Centro de datos, Almacén de datos, Carpeta, Host, Red, Recurso, Máquina virtual y Conmutador distribuido de vSphere. Además, el servidor VMware vCenter necesita el privilegio Vistas de almacenamiento habilitado.


## <a name="set-up-the-target-environment"></a>Configuración del entorno de destino

Antes de configurar el entorno de destino, asegúrese de tener una cuenta de Azure Storage y una red virtual configurada.

1. Haga clic en **Preparar infraestructura** > **Destino** y seleccione la suscripción de Azure que quiere usar.
2. Especifique si el modelo de implementación de destino está basado en Resource Manager o si es el clásico.
3. Site Recovery comprueba que tiene una o más cuentas de almacenamiento y redes compatibles.

   ![Destino](./media/vmware-walkthrough-source-target/gs-target.png)
4. Si no ha creado una cuenta de almacenamiento ni una red, haga clic en **+Cuenta de almacenamiento** o **+Red** para crear una cuenta o red de Resource Manager en línea.

## <a name="next-steps"></a>Pasos siguientes

Vaya al [paso 9: configuración de una directiva de replicación](vmware-walkthrough-replication.md)

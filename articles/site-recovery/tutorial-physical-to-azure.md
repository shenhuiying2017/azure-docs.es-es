---
title: "Configurar la recuperación ante desastres para servidores físicos locales en Azure con Azure Site Recovery | Microsoft Docs"
description: "Aprenda a configurar la recuperación ante desastres para servidores locales de Windows y Linux en Azure con el servicio Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 805f6946-c6da-491f-980e-bf724bebdf0b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2017
ms.author: raynew
ms.openlocfilehash: ceb4b13e326b24360799c1a7a25fe48f213fabd7
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2017
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-physical-servers"></a>Configurar la recuperación ante desastres para servidores físicos locales en Azure

El servicio [Azure Site Recovery](site-recovery-overview.md) contribuye a su estrategia de recuperación ante desastres mediante la administración y la coordinación de la replicación, la conmutación por error y la conmutación por recuperación de máquinas locales y máquinas virtuales (VM) de Azure.

Este tutorial muestra cómo configurar la recuperación ante desastres de servidores físicos locales de Windows y Linux en Azure. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configurar Azure y los requisitos previos locales
> * Crear un almacén de Recovery Services para Site Recovery 
> * Configurar los entornos de replicación de origen y destino
> * Creación de una directiva de replicación
> * Habilitar la replicación para un servidor

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial:

- Asegúrese de entender la [arquitectura y los componentes del escenario](concepts-physical-to-azure-architecture.md).
- Revise los [requisitos de compatibilidad](site-recovery-support-matrix-to-azure.md) de todos los componentes.
- Asegúrese de que los servidores que quiere replicar cumplen los [requisitos de VM de Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
- Prepare Azure. Necesita una suscripción de Azure, una red virtual de Azure y una cuenta de almacenamiento.
- Prepare una cuenta para la instalación automática de Mobility Service en cada servidor que quiera replicar.



### <a name="set-up-an-azure-account"></a>Configuración de una cuenta de Azure

Obtenga una [cuenta de Microsoft Azure](http://azure.microsoft.com/).

- Puede comenzar con una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Obtenga información sobre los [precios de Site Recovery](site-recovery-faq.md#pricing) y conozca los [detalles de los precios](https://azure.microsoft.com/pricing/details/site-recovery/).
- Averigüe qué [regiones se admiten](https://azure.microsoft.com/pricing/details/site-recovery/) en Site Recovery.

### <a name="verify-azure-account-permissions"></a>Comprobación de los permisos de cuenta de Azure

Asegúrese de que la cuenta de Azure tiene permisos para la replicación de máquinas virtuales en Azure.

- Revise los [permisos](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) que necesita para replicar máquinas en Azure.
- Compruebe y modifique los permisos de [acceso basado en roles](../active-directory/role-based-access-control-configure.md). 



### <a name="set-up-an-azure-network"></a>Configurar una red de Azure

Configure una [red de Azure](../virtual-network/virtual-network-get-started-vnet-subnet.md).

- Las VM de Azure se colocarán en esta red cuando se creen después de la conmutación por error.
- La red debe estar en la misma región que el almacén de Recovery Services.


## <a name="set-up-an-azure-storage-account"></a>Configurar una cuenta de almacenamiento de Azure

Configure una [cuenta de almacenamiento de Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account).

- Site Recovery replica máquinas virtuales locales en Azure Storage. Las máquinas virtuales de Azure se crean en el almacenamiento después de producirse la conmutación por error.
- La cuenta de almacenamiento debe estar en la misma región que el almacén de Recovery Services.
- La cuenta de almacenamiento puede ser estándar o [premium](../virtual-machines/windows/premium-storage.md).
- Si configura una cuenta de premium, también necesitará una cuenta estándar adicional para los datos de registro.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparación de una cuenta para la instalación de Mobility Service

Tiene que instalar el Servicio de movilidad en cada servidor que quiera replicar. Site Recovery instala este servicio automáticamente cuando se habilita la replicación del servidor. Para instalar automáticamente, debe preparar una cuenta que Site Recovery vaya a usar para acceder al servidor.

- Puede usar una cuenta local o de dominio.
- En el caso de máquinas virtuales de Windows, si no usa una cuenta de dominio, deshabilite el control de acceso de usuarios remotos en el equipo local. Para hacerlo, en el Registro, en **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, agregue la entrada DWORD **LocalAccountTokenFilterPolicy** con un valor de 1.
- Para agregar la entrada del Registro para deshabilitar el valor desde una CLI, escriba: ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Para Linux, la cuenta debe ser una raíz en el servidor Linux de origen.


## <a name="create-a-vault"></a>Creación de un almacén

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Selección de un objetivo de protección

Seleccione qué quiere replicar y en dónde hacerlo.

1. Haga clic en **Almacenes de Recovery Services** > almacén.
2. En el menú de recursos, haga clic en **Site Recovery** > **Preparar la infraestructura** > **Objetivo de protección**.
3. En **Objetivo de protección**, seleccione **En Azure** > **No virtualizado/Otro**.

## <a name="set-up-the-source-environment"></a>Configuración del entorno de origen

Configure el servidor de configuración, regístrelo en el almacén y detecte máquinas virtuales.

1. Haga clic en **Site Recovery** > **Preparar la infraestructura** > **Origen**.
2. Si no tiene un servidor de configuración, haga clic en **+Servidor de configuración**.
3. En **Agregar servidor**, compruebe que aparezca **Servidor de configuración** en **Tipo de servidor**.
4. Descargue el archivo de instalación unificada de Site Recovery.
5. Descargue la clave de registro del almacén. Se le solicitará cuando ejecute la instalación unificada. La clave será válida durante cinco días a partir del momento en que se genera.

   ![Configurar origen](./media/tutorial-physical-to-azure/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Registro del servidor de configuración en el almacén

Antes de empezar, haga lo siguiente: 

- En la máquina del servidor de configuración, asegúrese de que el reloj del sistema está sincronizado con un [servidor horario](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Deben ser iguales. Si hay una diferencia de 15 minutos, antes o después, se podría producir un error en la instalación.
- Asegúrese de que el equipo puede acceder a estas direcciones URL: [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]

- Las reglas de firewall basadas en direcciones IP deben permitir la comunicación con Azure.
- Permita los [intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) y el puerto HTTPS (443).
- Permita los intervalos de direcciones IP correspondientes a la región de Azure de su suscripción y del oeste de EE. UU. (se usan para Access Control y para Identity Management).

Ejecute la instalación unificada como administrador Local para instalar el servidor de configuración. El servidor de procesos y el servidor de destino maestro también se instalan de forma predeterminada en el servidor de configuración.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

Una vez terminado el registro, el servidor de configuración se muestra en la página **Configuración** > **Servidores** del almacén.


## <a name="set-up-the-target-environment"></a>Configuración del entorno de destino

Seleccione y compruebe los recursos de destino.

1. Haga clic en **Preparar infraestructura** > **Destino** y seleccione la suscripción de Azure que quiere usar.
2. Especifique el modelo de implementación de destino.
3. Site Recovery comprueba que tiene una o más redes y cuentas de Azure Storage compatibles.

   ![Destino](./media/tutorial-physical-to-azure/network-storage.png)


## <a name="create-a-replication-policy"></a>Creación de una directiva de replicación

1. Para crear una directiva de replicación, haga clic en **Site Recovery Infrastructure (Infraestructura de Site Recovery)** > **Directivas de replicación** > **+Directiva de replicación**.
2. En **Crear directiva de replicación**, especifique un nombre de directiva.
3. En **Umbral de RPO**, especifique el límite del objetivo de punto de recuperación (RPO). Este valor especifica la frecuencia con que se crean puntos de recuperación de datos. Se genera una alerta cuando la replicación continua supera este límite.
4. En **Retención de punto de recuperación**, especifique la duración (en horas) del período de retención de cada punto de recuperación. Las máquinas virtuales replicadas se pueden recuperar a cualquier momento de un período. Se admite una retención de hasta 24 horas para máquinas replicadas en Premium Storage y 72 horas para almacenamiento estándar.
5. En **Frecuencia de instantánea coherente con la aplicación**especifique la frecuencia (en minutos) con la que se crearán puntos de recuperación que contengan las instantáneas coherentes con la aplicación. Haga clic en **Aceptar** para crear la directiva.

    ![Directiva de replicación](./media/tutorial-physical-to-azure/replication-policy.png)


La directiva se asocia automáticamente al servidor de configuración. De forma predeterminada, se crea automáticamente una directiva correspondiente para la conmutación por recuperación. Por ejemplo, si la directiva de replicación es **rep-policy**, se crea una directiva de conmutación por recuperación **rep-policy-failback**. Esta directiva no se usa hasta que se inicie una conmutación por recuperación desde Azure.

## <a name="enable-replication"></a>Habilitar replicación

Habilite la replicación para cada servidor.

- Site Recovery instala Mobility Service cuando se habilita la replicación.
- Cuando se habilita la replicación para un servidor, los cambios pueden tardar 15 minutos o más en aplicarse y aparecer en el portal.

1. Haga clic en **Replicar la aplicación** > **Origen**.
2. En **Origen**, seleccione el servidor de configuración.
3. En **Tipo de máquina**, seleccione **Máquinas físicas**.
4. Seleccione el servidor de procesos (el servidor de configuración). y, a continuación, haga clic en **Aceptar**.
5. En **Destino**, seleccione la suscripción y el grupo de recursos donde quiere crear las máquinas virtuales de Azure después de la conmutación por error. Elija el modelo de implementación que quiere usar en Azure (clásico o administración de recursos).
6. Seleccione la cuenta de Azure Storage que desea usar para los datos de replicación. 
7. Seleccione la red y la subred de Azure a la que se conectarán las máquinas virtuales de Azure cuando se creen después de la conmutación por error.
8. Seleccione la opción **Configurar ahora para las máquinas seleccionadas** con el fin de aplicar la configuración de red a todas las máquinas que seleccione para su protección. Seleccione **Configurar más tarde** para seleccionar la red de Azure por máquina. 
9. En **Máquinas físicas**, haga clic en **+ Máquina física**. Especifique el nombre y la dirección IP. Seleccione el sistema operativo del equipo que quiere replicar. Los servidores tardan unos minutos en detectarse y mostrarse. 
10. En **Propiedades** > **Configurar propiedades**, seleccione la cuenta que utilizará el servidor de procesos para instalar automáticamente Mobility Service en la máquina.
11. En **Configuración de la replicación** > **Establecer configuración de replicación**, compruebe que se haya seleccionado la directiva de replicación correcta. 
12. Haga clic en **Enable Replication**. Puede hacer un seguimiento del progreso del trabajo **Habilitar protección** en **Configuración** > **Trabajos** > **Trabajos de Site Recovery**. La máquina estará preparada para la conmutación por error después de que finalice el trabajo **Finalizar la protección** .


Para supervisar los servidores que agregue, puede comprobar la última hora de detección en **Servidores de configuración** > **Último contacto**. Para agregar equipos sin esperar a una hora de detección programada, resalte el servidor de configuración (sin hacer clic en él) y haga clic en **Actualizar**.

## <a name="next-steps"></a>Pasos siguientes

[Obtención de detalles de recuperación ante desastres](tutorial-dr-drill-azure.md)

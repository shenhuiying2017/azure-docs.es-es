---
title: "Creación de recursos para usar con Azure Site Recovery | Microsoft Docs"
description: "Aprenda a preparar Azure para la replicación de máquinas locales mediante el servicio Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 321e304f-b29e-49e4-aa64-453878490ea7
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: raynew
ms.openlocfilehash: 506b625905abf52963230a787af66f956bc292b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="prepare-azure-resources-for-replication-of-on-premises-machines"></a>Preparar recursos de Azure para la replicación de máquinas locales

El servicio [Azure Site Recovery](site-recovery-overview.md) contribuye a la estrategia de recuperación ante desastres y continuidad empresarial (BCDR) al mantener sus aplicaciones empresariales al día y disponibles durante interrupciones planeadas y no planeadas. Azure Site Recovery administra y coordina la recuperación ante desastres de máquinas locales y máquinas virtuales de Azure, incluso la replicación, conmutación por error y recuperación.

En este tutorial se muestra cómo preparar los componentes de Azure cuando se desean replicar servidores físicos y máquinas virtuales locales en Azure. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Comprobar si su cuenta tiene permisos de replicación
> * Cree una cuenta de Azure Storage.
> * Establezca una red de Azure. Cuando se crean máquinas virtuales de Azure después de la conmutación por error, se unen a esta red de Azure.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de empezar.

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en Azure Portal: http://portal.azure.com/.

## <a name="verify-account-permissions"></a>Comprobar los permisos de la cuenta

Si acaba de crear su cuenta de Azure gratuita, ya es administrador de su suscripción. Si no es administrador de la suscripción, solicite al administrador que le asigne los permisos que necesita. Para habilitar la replicación de una nueva máquina virtual, necesita lo siguiente:

- Permiso para crear una máquina virtual en el grupo de recursos seleccionado
- Permiso para crear una máquina virtual en la red virtual seleccionada
- Permiso para escribir en la cuenta de Storage seleccionada

El rol integrado "Colaborador de la máquina virtual" tiene estos permisos. También necesita permiso para administrar las operaciones de Azure Site Recovery. El rol "Colaborador de Site Recovery" tiene todos los permisos necesarios para administrar las operaciones de Site Recovery en un almacén de Recovery Services.

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

Las imágenes de máquinas replicadas se conservan en Azure Storage. Las máquinas virtuales de Azure se crean desde el almacenamiento cuando se realiza la conmutación por error desde el entorno local en Azure.

1. En el menú [Azure Portal](https://portal.azure.com), haga clic en **Nuevo** -> **Storage** -> **Cuenta de Storage**.
2. Escriba un nombre para la cuenta de almacenamiento. En estos tutoriales, se usará el nombre **contosovmsacct1910171607**. El nombre debe ser único en Azure, tener entre 3 y 24 caracteres, y contener solo números y letras minúsculas.
3. Use el modelo de implementación de **Resource Manager**.
4. Seleccione **Propósito general** > **Estándar**.
5. Seleccione el valor predeterminado **RA-GRS** para la redundancia del almacenamiento.
6. Seleccione la suscripción en la que desea crear la nueva cuenta de almacenamiento.
7. Especifique un nuevo grupo de recursos. Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. En estos tutoriales, se usa el nombre **ContosoRG**.
8. Seleccione la ubicación geográfica para la cuenta de almacenamiento. La cuenta de almacenamiento debe estar en la misma región que el almacén de Recovery Services. Para estos tutoriales, se usa la ubicación **Europa Occidental**.

   ![create-storageacct](media/tutorial-prepare-azure/create-storageacct.png)

9. Haga clic en **Crear** para crear la cuenta de almacenamiento.

## <a name="create-a-vault"></a>Creación de un almacén

1. En el menú de Azure Portal, haga clic en **Nuevo** > **Supervisión y administración** >
    **Backup and Site Recovery**.
2. En **Nombre**, especifique un nombre descriptivo para identificar el almacén. En este tutorial, se usa **ContosoVMVault**.
3. Seleccione el grupo de recursos existente denominado **contosoRG**.
4. Especifique la región de Azure **Europa Occidental**.
5. Si quiere acceder rápidamente al almacén desde el panel, haga clic en **Anclar al panel** > **Crear**.

   ![Almacén nuevo](./media/tutorial-prepare-azure/new-vault-settings.png)

   El nuevo almacén aparecerá en **Panel** > **Todos los recursos** y en la página principal de **Almacenes de Recovery Services**.

## <a name="set-up-an-azure-network"></a>Configurar una red de Azure

Cuando se crean máquinas virtuales de Azure desde el almacenamiento después de la conmutación por error, se unen a esta red.

1. En el menú [Azure Portal](https://portal.azure.com), haga clic en **Nuevo** > **Redes** >
   **Red virtual**
2. Deje **Resource Manager** seleccionado como modelo de implementación. Resource Manager es el modelo de implementación recomendado.
   - Especifique un nombre de red. El nombre debe ser único dentro del grupo de recursos de Azure. Se usará el nombre **ContosoASRnet**.
   - Seleccione el grupo de recursos existente **contosoRG**.
   - Especifique el intervalo de direcciones de red 10.0.0.0/24.
   - Para este tutorial no se necesita ninguna subred.
   - Seleccione la suscripción en la que se creará la red.
   - Seleccione la ubicación **Europa Occidental**. La red virtual de Azure debe estar en la misma región que el almacén de Recovery Services.
3. Haga clic en **Crear**.

   ![create-network](media/tutorial-prepare-azure/create-network.png)

   La red virtual tarda unos segundos en crearse. Una vez creada, se ve en el panel de Azure Portal.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Preparar la infraestructura de VMware local para la recuperación ante desastres en Azure](tutorial-prepare-on-premises-vmware.md)

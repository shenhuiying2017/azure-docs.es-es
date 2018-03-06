---
title: "Creación de recursos para usar con Azure Site Recovery | Microsoft Docs"
description: "Aprenda a preparar Azure para la replicación de máquinas locales mediante Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/16/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 2f6ff1d30eef1fe34e55457d9bdd4295804ec16a
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/23/2018
---
# <a name="prepare-azure-resources-for-replication-of-on-premises-machines"></a>Preparar recursos de Azure para la replicación de máquinas locales

 [Azure Site Recovery](site-recovery-overview.md) contribuye a la estrategia de recuperación ante desastres y continuidad empresarial (BCDR) al mantener sus aplicaciones empresariales al día y disponibles durante interrupciones planeadas y no planeadas. Azure Site Recovery administra y coordina la recuperación ante desastres de máquinas locales y máquinas virtuales de Azure, lo que incluye la replicación, la conmutación por error y la recuperación.

En este tutorial se muestra cómo preparar los componentes de Azure cuando se desean replicar máquinas virtuales locales (Hyper-V o VMware) o servidores físicos de Windows/Linux en Azure. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Comprobar si su cuenta tiene permisos de replicación.
> * Cree una cuenta de Azure Storage.
> * Establezca una red de Azure. Cuando se crean máquinas virtuales de Azure después de la conmutación por error, se unen a esta red de Azure.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de empezar.

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en el [Azure Portal](http://portal.azure.com).

## <a name="verify-account-permissions"></a>Comprobar los permisos de la cuenta

Si acaba de crear su cuenta de Azure gratis, ya es el administrador de la suscripción. Si no es el administrador de la suscripción, solicite al administrador que le asigne los permisos que necesita. Para habilitar la replicación de una nueva máquina virtual, debe tener permiso para:

- Crear una máquina virtual en el grupo de recursos seleccionado.
- Crear una máquina virtual en la red virtual seleccionada.
- Escribir en la cuenta de almacenamiento seleccionada.

El rol integrado "Colaborador de la máquina virtual" tiene estos permisos. También necesita permiso para administrar las operaciones de Site Recovery. El rol "Colaborador de Site Recovery" tiene todos los permisos necesarios para administrar las operaciones de Site Recovery en un almacén de Recovery Services.

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

Las imágenes de máquinas replicadas se conservan en Azure Storage. Las máquinas virtuales de Azure se crean desde el almacenamiento cuando se realiza la conmutación por error desde el entorno local en Azure.

1. En el menú [Azure Portal](https://portal.azure.com), seleccione **Nuevo** > **Almacenamiento** > **Cuentas de almacenamiento**.
2. En **Crear cuenta de almacenamiento**, escriba un nombre para la cuenta. En estos tutoriales, se usará el nombre **contosovmsacct1910171607**. El nombre debe ser único en Azure, tener entre 3 y 24 caracteres, y contener solo números y letras minúsculas.
3. En **Modelo de implementación**, seleccione **Resource Manager**.
4. En **Tipo de cuenta** seleccione **Uso general**. En **Rendimiento**, seleccione **Estándar**. No seleccione Blob Storage.
5. En **Replicación**, seleccione el valor predeterminado **Almacenamiento con redundancia geográfica con acceso de lectura** como redundancia de almacenamiento.
6. En **Suscripción**, seleccione la suscripción en la que desea crear la nueva cuenta de almacenamiento.
7. En **Grupo de recursos**, especifique un nuevo grupo de recursos. Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. En estos tutoriales, se usa el nombre **ContosoRG**.
8. En **Ubicación**, seleccione la ubicación geográfica de la cuenta de almacenamiento. La cuenta de almacenamiento debe estar en la misma región que el almacén de Recovery Services. Para estos tutoriales, se usa la región **Europa Occidental**.

   ![Crear una cuenta de almacenamiento](media/tutorial-prepare-azure/create-storageacct.png)

9. Seleccione **Crear** para crear la cuenta de almacenamiento.

## <a name="create-a-vault"></a>Creación de un almacén

1. En Azure Portal, seleccione **Crear un recurso** > **Supervisión y administración** > **Backup y Site Recovery**.
2. En **Nombre**, escriba un nombre descriptivo para identificar el almacén. En este tutorial, se usa **ContosoVMVault**.
3. En **Grupo de recursos**, seleccione el grupo de recursos existente denominado **contosoRG**.
4. En **Ubicación**, especifique la región de Azure **Europa Occidental**, que se está usando en este conjunto de tutoriales.
5. Para acceder rápidamente al almacén desde el panel, seleccione **Anclar al panel** > **Crear**.

   ![Crear un nuevo almacén](./media/tutorial-prepare-azure/new-vault-settings.png)

   El nuevo almacén aparecerá en **Panel** > **Todos los recursos** y en la página principal de **Almacenes de Recovery Services**.

## <a name="set-up-an-azure-network"></a>Configurar una red de Azure

Cuando se crean máquinas virtuales de Azure desde el almacenamiento después de la conmutación por error, se unen a esta red.

1. En [Azure Portal](https://portal.azure.com), seleccione **Crear un recurso** > **Redes** > **Red virtual**.
2. Deje **Resource Manager** seleccionado como modelo de implementación. Resource Manager es el modelo de implementación recomendado. A continuación, siga estos pasos:

   a. En **Nombre**, escriba un nombre de red. El nombre debe ser único dentro del grupo de recursos de Azure. Use el nombre **ContosoASRnet**.

   b. En **Grupo de recursos**, seleccione el grupo de recursos existente **contosoRG**.

   c. En **Intervalo de direcciones**, escriba el intervalo de direcciones de red **10.0.0.0/24**.

   d. Para este tutorial no necesita ninguna subred.

   e. En **Suscripción**, seleccione la suscripción en la que se creará la red.

   f. En **Ubicación**, seleccione **Europa Occidental**. La red virtual de Azure debe estar en la misma región que el almacén de Recovery Services.

3. Seleccione **Crear**.

   ![Crear una red virtual](media/tutorial-prepare-azure/create-network.png)

   La red virtual tarda unos segundos en crearse. Una vez creada, se ve en el panel de Azure Portal.

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Preparar la infraestructura de VMware local para la recuperación ante desastres en Azure](tutorial-prepare-on-premises-vmware.md)

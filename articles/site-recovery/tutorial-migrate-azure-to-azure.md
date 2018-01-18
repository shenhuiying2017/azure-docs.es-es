---
title: "Migrar máquinas virtuales de Azure entre regiones de Azure mediante Azure Site Recovery | Microsoft Docs"
description: "Use Azure Site Recovery para migrar máquinas virtuales de IaaS de Azure de una región de Azure a otra."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/07/2018
ms.author: raynew
ms.openlocfilehash: e7b925d2daed11ee4e070cda6bcbd4a3511d9c17
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="migrate-azure-vms-to-another-region"></a>Migración de máquinas virtuales de Azure a otra región

Además de utilizar el servicio [Azure Site Recovery](site-recovery-overview.md) para administrar y coordinar la recuperación ante desastres de máquinas locales y máquinas virtuales de Azure para garantizar la continuidad empresarial y la recuperación ante desastres (BCDR), también puede usar Site Recovery para administrar la migración de máquinas virtuales de Azure a una región secundaria. Para migrar máquinas virtuales de Azure, debe habilitar su replicación y realizar una conmutación por error desde una región principal a la región secundaria que usted quiera.

En este tutorial se muestra cómo migrar máquinas virtuales de Azure a otra región. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear un almacén de Recovery Services
> * Habilitación de la replicación para una máquina virtual
> * Ejecutar una conmutación por error para migrar la máquina virtual

En este tutorial se asume que ya tiene una suscripción de Azure. Si no la tiene, cree una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de empezar.

>[!NOTE]
>
> La replicación de Site Recovery en máquinas virtuales de Azure está actualmente en su versión preliminar.



## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesita que las máquinas virtuales de Azure estén en la región de Azure desde la que se va a hacer la migración. Además, hay una serie de valores que debe comprobar antes de empezar.


### <a name="verify-target-resources"></a>Comprobación de los recursos de destino

1. Compruebe que su suscripción de Azure permite crear máquinas virtuales en la región de destino que se usa para la recuperación ante desastres. Para habilitar la cuota necesaria, póngase en contacto con el soporte técnico.

2. Asegúrese de que su suscripción tiene suficientes recursos para admitir máquinas virtuales con tamaños que se correspondan con las máquinas virtuales de origen. Site Recovery elige el mismo tamaño para la máquina virtual de destino o el más cercano posible.


### <a name="verify-account-permissions"></a>Comprobar los permisos de la cuenta

Si acaba de crear su cuenta de Azure gratuita, ya es administrador de su suscripción. Si no es administrador de la suscripción, solicite al administrador que le asigne los permisos que necesita. Para habilitar la replicación de una nueva máquina virtual, necesita lo siguiente:

1. Permisos para crear una máquina virtual en recursos de Azure. El rol integrado "Colaborador de la máquina virtual" tiene estos permisos, que incluyen:
    - Permiso para crear una máquina virtual en el grupo de recursos seleccionado.
    - Permiso para crear una máquina virtual en la red virtual seleccionada.
    - Permiso para escribir en la cuenta de almacenamiento seleccionada.

2. También necesita permiso para administrar las operaciones de Azure Site Recovery. El rol "Colaborador de Site Recovery" tiene todos los permisos necesarios para administrar las operaciones de Site Recovery en un almacén de Recovery Services.


### <a name="verify-vm-outbound-access"></a>Comprobar el acceso saliente de las máquinas virtuales

1. Asegúrese de que no utiliza un proxy de autenticación para controlar la conectividad de red de las máquinas virtuales que vaya a migrar. 
2. Para este tutorial asumiremos que las máquinas virtuales que quiere migrar pueden tener acceso a Internet y no están utilizando un proxy de firewall para controlar el acceso saliente. Si es así, compruebe los requisitos [aquí](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).

### <a name="verify-vm-certificates"></a>Comprobar los certificados de las máquinas virtuales

Asegúrese de que todos los certificados raíz más recientes estén presentes en las máquinas virtuales de Azure que quiera migrar. Si los certificados raíz más recientes no están presentes, la máquina virtual no se puede registrar en Site Recovery debido a restricciones de seguridad.

- Para las máquinas virtuales de Windows, instale las actualizaciones de Windows más recientes en la máquina virtual, de modo que todos los certificados raíz de confianza estén en ella. En un entorno desconectado, siga los procesos estándar de actualización de certificados y de Windows Update en su organización.
- En las máquinas virtuales Linux, para obtener los certificados raíz de confianza y la lista de revocación de certificados en la máquina virtual, siga las instrucciones proporcionadas por su distribuidor de Linux.



## <a name="create-a-vault"></a>Creación de un almacén

Cree el almacén en cualquier región, excepto en la de origen.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) > **Recovery Services**.
2. Haga clic en **Nuevo** > **Supervisión y administración** > **Backup y Site Recovery**.
3. En el apartado **Nombre**, especifique el nombre descriptivo **ContosoVMVault**. Si tiene más de una suscripción, seleccione la apropiada.
4. Cree un grupo de recursos denominado **ContosoRG**.
5. Especifique una región de Azure. Para comprobar las regiones admitidas, consulte la disponibilidad geográfica en [Detalles de precios de Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Para acceder rápidamente al almacén desde el panel, haga clic en **Anclar al panel** y, después, en **Crear**.

   ![Almacén nuevo](./media/tutorial-migrate-azure-to-azure/azure-to-azure-vault.png)

El nuevo almacén se agrega al **Panel**, en **Todos los recursos**, y en la página principal **Almacenes de Recovery Services**.






## <a name="select-the-source"></a>Seleccione el origen

1. En los almacenes de Recovery Services, haga clic en **ConsotoVMVault** > **+Replicar**.
2. En **Origen**, seleccione **Azure - VERSIÓN PRELIMINAR**.
3. En **Ubicación de origen**, seleccione la región de Azure de origen donde se ejecutan actualmente sus máquinas virtuales.
4. Seleccione el modelo de implementación de Resource Manager. A continuación, seleccione el **grupo de recursos de origen**.
5. Haga clic en **Aceptar** para guardar la configuración.


## <a name="enable-replication-for-azure-vms"></a>Habilitar la replicación para máquinas virtuales de Azure

Site Recovery recupera una lista de las máquinas virtuales asociadas a la suscripción y el grupo de recursos.


1. En Azure Portal, haga clic en **Máquinas virtuales**.
2. Seleccione la máquina virtual que quiera migrar. A continuación, haga clic en **Aceptar**.
3. En **Configuración**, haga clic en **Recuperación ante desastres (versión preliminar)**.
4. En **Configurar recuperación ante desastres** > **Región de destino**, seleccione la región de destino en la que quiere realizar la replicación.
5. Para este tutorial, acepte los valores predeterminados.
6. Haga clic en **Habilitar replicación**. Esto inicia un trabajo para habilitar la replicación de la máquina virtual.

    ![habilitar replicación](media/tutorial-migrate-azure-to-azure/settings.png)

>[!NOTE]
  >
  > Actualmente, no se admite la replicación de máquinas virtuales de Azure mediante discos administrados. 

## <a name="run-a-failover"></a>Ejecución de la conmutación por error

1. En **Configuración** > **Elementos replicados**, haga clic en la máquina y en **Conmutación por error**.
2. En **Conmutación por error**, seleccione **Más reciente**. La configuración de la clave de cifrado no es importante para este escenario.
3. Seleccione **Apague la máquina antes de comenzar con la conmutación por error**. A continuación, Site Recovery intentará apagar la máquina virtual de origen antes de desencadenar la conmutación por error. La conmutación por error continúa aunque se produzca un error de cierre. Puede seguir el progreso de la conmutación por error en la página **Trabajos**.
4. Compruebe que la máquina virtual de Azure aparece en Azure según lo previsto.
5. En **Elementos replicados**, haga clic con el botón derecho en la máquina virtual > **Completar migración**. Esto finaliza el proceso de migración y detiene la replicación de la máquina virtual.



## <a name="next-steps"></a>pasos siguientes

Siguiendo este tutorial migró una máquina virtual de Azure a otra región de Azure. Ya puede configurar la opción de recuperación ante desastres para las máquinas virtuales que haya migrado.

> [!div class="nextstepaction"]
> [Configurar la recuperación ante desastres después de la migración](azure-to-azure-quickstart.md)


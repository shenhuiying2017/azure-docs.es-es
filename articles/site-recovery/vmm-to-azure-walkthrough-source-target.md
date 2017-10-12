---
title: "Configurar el origen y destino para la replicación de Hyper-V en Azure (sin System Center VMM) con Azure Site Recovery | Microsoft Docs"
description: "Se resumen los pasos para establecer la configuración de origen y de destino para la replicación de máquinas virtuales de Hyper-V de nubes de VMM en Azure Storage con Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5edb6d87-25a5-40fe-b6f1-ddf7b55a6b31
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/25/2017
ms.author: raynew
ms.openlocfilehash: c72f839d0a1288dccb7deb3e44fc2b20d64818f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="step-8-set-up-the-source-and-target-for-hyper-v-with-vmm-replication-to-azure"></a>Paso 8: Configuración del origen y destino para la replicación de Hyper-V (con VMM) en Azure

Tras [crear un almacén](vmm-to-azure-walkthrough-create-vault.md) y especificar lo que desea replicar, use este artículo para establecer la configuración de origen y destino al replicar máquinas virtuales de Hyper-V locales de nubes de System Center Virtual Machine Manager (VMM) en Azure mediante el servicio [Azure Site Recovery](site-recovery-overview.md) en Azure Portal.

Publique cualquier comentario o pregunta en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="set-up-the-source-environment"></a>Configuración del entorno de origen

S1. Haga clic en **Preparar infraestructura** > **Origen**.

    ![Set up source](./media/vmm-to-azure-walkthrough-source-target/set-source1.png)

2. En **Preparar origen**, haga clic en **+ VMM** para agregar un servidor VMM.

    ![Configurar origen](./media/vmm-to-azure-walkthrough-source-target/set-source2.png)

3. En **Agregar servidor**, compruebe que aparece el **servidor de System Center VMM** en **Tipo de servidor** y que este cumple los [requisitos previos y los requisitos de direcciones URL](#prerequisites).
4. Descargue el archivo de instalación del proveedor de Azure Site Recovery.
5. Descargue la clave de registro. Se le solicitará cuando ejecute el programa de instalación. La clave será válida durante cinco días a partir del momento en que se genera.

    ![Configurar origen](./media/vmm-to-azure-walkthrough-source-target/set-source3.png)

## <a name="install-the-provider-on-the-vmm-server"></a>Instalación del proveedor en el servidor VMM

1. Ejecute el archivo de instalación del proveedor en el servidor VMM.
2. En **Microsoft Update** puede optar por recibir actualizaciones para que las actualizaciones del proveedor se realicen según las directivas de Microsoft Update.
3. En **Instalación** acepte o modifique la ubicación predeterminada de instalación del proveedor y haga clic en **Instalar**.

    ![Ubicación de instalación](./media/vmm-to-azure-walkthrough-source-target/provider2.png)
4. Una vez finalizada la instalación, haga clic en **Registrar** para registrar el servidor VMM en el almacén.
5. En la página **Configuración de almacén**, haga clic en **Examinar** para seleccionar el archivo de clave del almacén. Especifique la suscripción de Azure Site Recovery y el nombre del almacén.

    ![Registro de servidor](./media/vmm-to-azure-walkthrough-source-target/provider10.png)
6. En **Conexión a Internet**, especifique cómo se conecta a Site Recovery el proveedor que se ejecuta en el servidor VMM mediante Internet.

   * Si quiere que el proveedor se conecte directamente, seleccione **Conectar directamente con Azure Site Recovery sin un servidor proxy**.
   * Si el proxy existente requiere autenticación, o si quiere utilizar un proxy personalizado, seleccione **Conectar con Azure Site Recovery con un servidor proxy**.
   * Si utiliza un proxy personalizado, especifique la dirección, el puerto y las credenciales.
   * Si utiliza un servidor proxy, se deberían haber permitido ya las direcciones URL descritas en los [requisitos previos](#on-premises-prerequisites).
   * Si utiliza un proxy personalizado, se creará una cuenta de ejecución de VMM (DRAProxyAccount) mediante el uso automático de las credenciales de proxy especificadas. Configure el servidor proxy para que esta cuenta pueda autenticarse correctamente. La configuración de la cuenta de ejecución de VMM puede modificarse en la consola VMM. En **Configuración**, expanda **Seguridad** > **Cuentas de ejecución** y, luego, modifique la contraseña de DRAProxyAccount. Deberá reiniciar el servicio VMM para que esta configuración surta efecto.

     ![Internet](./media/vmm-to-azure-walkthrough-source-target/provider13.png)
7. Puede especificar o modificar la ubicación de un certificado SSL que se genera automáticamente para el cifrado de datos. Este certificado se usa si habilita el cifrado de datos para una nube protegida por Azure en el portal de Azure Site Recovery. Mantenga el certificado en un lugar seguro. Cuando ejecute una conmutación por error en Azure lo necesitará para descifrar si está habilitado el cifrado de datos.
8. En **Nombre del servidor**, especifique un nombre descriptivo para identificar el servidor VMM en el almacén. En una configuración de clúster, especifique el nombre del rol de clúster VMM.
9. Habilite la **sincronización de metadatos de la nube** si quiere sincronizar los metadatos de todas las nubes del servidor VMM con el almacén. Esta acción solo se debe ejecutar una vez en cada servidor. Si no desea sincronizar todas las nubes, puede dejar este parámetro sin marcar y sincronizar cada nube individualmente en las propiedades de la nube de la consola de VMM. Haga clic en **Register** para finalizar el proceso.

    ![Registro de servidor](./media/vmm-to-azure-walkthrough-source-target/provider16.png)
10. Con ello, se iniciará el registro. Después de finalizar el registro, el servidor aparece en **Infraestructura de Site Recovery** > **Servidores VMM**.


## <a name="install-the-azure-recovery-services-agent-on-hyper-v-hosts"></a>Instale el agente de Azure Recovery Services en servidores host de Hyper-V

1. Después de configurar el proveedor, debe descargar el archivo de instalación del agente de Azure Recovery Services. Ejecute el programa de instalación en cada servidor de Hyper-V en la nube de VMM.

    ![Sitios de Hyper-V](./media/vmm-to-azure-walkthrough-source-target/hyperv-agent1.png)
2. En **Comprobación de requisitos previos**, haga clic en **Siguiente**. Todos los requisitos previos que falten se instalarán automáticamente.

    ![Requisitos previos del agente de Recovery Services](./media/vmm-to-azure-walkthrough-source-target/hyperv-agent2.png)
3. En **Configuración de la instalación**, acepte o modifique la ubicación de instalación y la ubicación de la memoria caché. Puede configurar la memoria caché en una unidad que tenga al menos 5 GB de almacenamiento disponible, pero se recomienda usar una unidad de caché con 600 GB o más de espacio libre. Luego haga clic en **Instalar**.
4. Una vez completada la instalación, haga clic en **Cerrar** para terminar.

    ![Registro del agente MARS](./media/vmm-to-azure-walkthrough-source-target/hyperv-agent3.png)

### <a name="command-line-installation"></a>Instalación de la línea de comandos
Puede instalar el agente de Microsoft Azure Recovery Services desde la línea de comandos mediante el comando siguiente:

     marsagentinstaller.exe /q /nu

### <a name="set-up-internet-proxy-access-to-site-recovery-from-hyper-v-hosts"></a>Configuración del acceso de proxy a Internet para Site Recovery desde hosts de Hyper-V

El agente de Recovery Services que se ejecuta en los hosts de Hyper-V necesita acceso a Internet para conectarse a Azure para la replicación de máquinas virtuales. Si va a acceder a Internet a través de un proxy, configúrelo como sigue:

1. Abra el complemento Microsoft Azure Backup de MMC en el host de Hyper-V. De manera predeterminada, hay disponible un acceso directo para Microsoft Azure Backup en el escritorio o en la siguiente ruta de acceso: C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. En el complemento, haga clic en **Cambiar propiedades**.
3. En la pestaña **Configuración de proxy** especifique la información del servidor proxy.

    ![Registro del agente MARS](./media/vmm-to-azure-walkthrough-source-target/mars-proxy.png)
4. Compruebe que el agente puede llegar a las direcciones URL descritas en los [requisitos previos](#on-premises-prerequisites).

## <a name="set-up-the-target-environment"></a>Configuración del entorno de destino
Especifique la cuenta de Azure Storage que se utilizará para la replicación y la red de Azure a la que se conectarán las máquinas virtuales de Azure después de la conmutación por error.

1. Haga clic en **Preparar infraestructura** > **Destino** y seleccione la suscripción y el grupo de recursos donde desee crear las máquinas virtuales conmutadas por error. Elija el modelo de implementación que desee usar en Azure (clásico o de Resource Manager) para las máquinas virtuales conmutadas por error.

    ![Storage](./media/vmm-to-azure-walkthrough-source-target/enablerep3.png)

2. Site Recovery comprueba que tiene una o más redes y cuentas de Azure Storage compatibles.

    ![Storage](./media/vmm-to-azure-walkthrough-source-target/compatible-storage.png)

3. Si no ha creado una cuenta de almacenamiento y desea crear una con Resource Manager, haga clic en **+Cuenta de almacenamiento** para hacerlo directamente.  En la hoja **Crear cuenta de almacenamiento**, especifique el nombre, el tipo, la suscripción y la ubicación de la cuenta. La cuenta debe estar en la misma ubicación que el almacén de Recovery Services.

   ![Storage](./media/vmm-to-azure-walkthrough-source-target/gs-createstorage.png)


   * Si desea crear una cuenta de almacenamiento mediante el modelo clásico, lo hará en Azure Portal. [Más información](../storage/common/storage-create-storage-account.md)
   * Si utiliza una cuenta de almacenamiento Premium para los datos replicados, configure una cuenta de almacenamiento Estándar adicional para los registros de replicación del almacén que capturan los cambios continuos de los datos locales.
5. Si no ha creado una red de Azure y desea crear una con Resource Manager, haga clic en **+Red** para hacerlo directamente. En la hoja **Crear red virtual**, especifique el nombre, el intervalo de direcciones, los detalles de subred, la suscripción y la ubicación de la red. La red debe estar en la misma ubicación que el almacén de Recovery Services.

   ![Red](./media/vmm-to-azure-walkthrough-source-target/gs-createnetwork.png)

   Si desea crear una red mediante el modelo clásico, lo hará en Azure Portal. [Más información](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).





## <a name="next-steps"></a>Pasos siguientes

Ir a [Step 9: Configure network mapping](vmm-to-azure-walkthrough-network-mapping.md) (Paso 9: Configuración de la asignación de red)

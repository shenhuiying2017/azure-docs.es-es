---
title: "Preparación del origen y el destino para la replicación de Hyper-V en un sitio secundario con Azure Site Recovery | Microsoft Docs"
description: "Describe cómo configurar el origen y el destino al replicar máquinas virtuales de Hyper-V en un sitio de VMM secundario con Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: fa7809f1-7633-425f-b25d-d10d004e8d0b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 07135e9b5e619971a59cc22ec68a0a4e8bcaabe1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="step-6-set-up-the-replication-source-and-target"></a>Paso 6: Configuración de los valores de origen y de destino de la replicación


Después de crear un almacén de Recovery Services para la replicación de Hyper-V en un sitio de VMM secundario con [Azure Site Recovery](site-recovery-overview.md), use este artículo para configurar las ubicaciones de replicación de origen y de destino. 

Publique cualquier comentario que tenga en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).




## <a name="set-up-the-source-environment"></a>Configuración del entorno de origen

Instale el proveedor de Azure Site Recovery en servidores VMM, y detecte y registre servidores en el almacén.

1. Haga clic en **Paso 1: Preparar la infraestructura** > **Origen**.

    ![Configurar origen](./media/vmm-to-vmm-walkthrough-source-target/goals-source.png)
2. En **Preparar origen**, haga clic en **+ VMM** para agregar un servidor VMM.

    ![Configurar origen](./media/vmm-to-vmm-walkthrough-source-target/set-source1.png)
3. En **Agregar servidor**, compruebe que aparezca el **servidor VMM de System Center** en **Tipo de servidor** y que dicho servidor cumpla los [requisitos previos](#prerequisites).
4. Descargue el archivo de instalación del proveedor de Azure Site Recovery.
5. Descargue la clave de registro. Se le solicitará cuando ejecute el programa de instalación. La clave será válida durante cinco días a partir del momento en que se genera.

    ![Configurar origen](./media/vmm-to-vmm-walkthrough-source-target/set-source3.png)
6. Instale el proveedor de Azure Site Recovery en el servidor VMM. No es necesario instalar explícitamente nada en los servidores host de Hyper-V.


## <a name="install-the-azure-site-recovery-provider"></a>Instalación del proveedor de Azure Site Recovery

1. Ejecute el archivo de instalación del proveedor en cada servidor VMM. Si VMM está implementado en un clúster, siga este procedimiento la primera vez que instale:
    -  Instale el proveedor en un nodo activo y finalice la instalación para registrar el servidor VMM en el almacén.
    - A continuación, instale el proveedor en los demás nodos. Los nodos del clúster deben ejecutar la misma versión del proveedor.
2. El programa de instalación ejecuta algunas comprobaciones de requisitos previos y solicita permiso para detener el servicio VMM. El servicio VMM se reiniciará automáticamente cuando finalice la instalación. Si lo instala en un clúster VMM, se le pide que detenga el rol de clúster.
3. En **Microsoft Update**, puede participar para especificar que las actualizaciones del proveedor se instalen según las directivas de Microsoft Update.
4. En **Instalación**, acepte o modifique la ubicación predeterminada de instalación y haga clic en **Instalar**.

    ![Ubicación de instalación](./media/vmm-to-vmm-walkthrough-source-target/provider-location.png)
5. Cuando finalice la instalación, haga clic en **Registrar** para registrar el servidor en el almacén.

    ![Ubicación de instalación](./media/vmm-to-vmm-walkthrough-source-target/provider-register.png)
6. En **Nombre del almacén**, compruebe el nombre del almacén en el que se registrará el servidor. Haga clic en *Siguiente*.

    ![Registro de servidor](./media/vmm-to-vmm-walkthrough-source-target/vaultcred.png)
7. En **Conexión a Internet**, especifique cómo se conecta a Azure el proveedor que se ejecuta en el servidor VMM.

    ![Configuración de Internet](./media/vmm-to-vmm-walkthrough-source-target/proxydetails.png)

   - Puede especificar que el proveedor debe conectarse a Internet directamente o a través de un proxy.
   - Especifique la configuración de proxy si es necesario.
   - Si utiliza un proxy, se crea automáticamente una cuenta de ejecución de VMM (DRAProxyAccount) que usa las credenciales de proxy especificadas. Configure el servidor proxy para que esta cuenta pueda autenticarse correctamente. Se puede modificar la configuración de la cuenta de ejecución en la consola VMM > **Configuración** > **Seguridad** > **Cuentas de ejecución**. Reinicie el servicio VMM para actualizar los cambios.
8. En **Clave de registro**, seleccione la clave que ha descargado de Azure Site Recovery y copiado en el servidor VMM.
9. La configuración de cifrado solo se usa cuando se está replicando VM de Hyper-V en nubes de VMM en Azure. Si se está replicando en un sitio secundario, no se usa.
10. En **Nombre del servidor**, especifique un nombre descriptivo para identificar el servidor VMM en el almacén. En una configuración de clúster, especifique el nombre del rol de clúster VMM.
11. En **Sincronizar metadatos en la nube** seleccione si quiere sincronizar los metadatos de todas las nubes del servidor VMM con el almacén. Esta acción solo se debe ejecutar una vez en cada servidor. Si no desea sincronizar todas las nubes, puede dejar este parámetro sin marcar y sincronizar cada nube individualmente en las propiedades de la nube de la consola de VMM.
12. Haga clic en **Next** para finalizar el proceso. Después del registro, la Recuperación del sitio de Azure recupera los metadatos del servidor VMM. El servidor se muestra en la pestaña **Servidores VMM** de la página **Servidores** del almacén.

    ![Server](./media/vmm-to-vmm-walkthrough-source-target/provider13.png)
13. Después de que el servidor esté disponible en la consola de Site Recovery, en **Origen** > **Preparar origen** , seleccione el servidor VMM y la nube en la que se encuentra el host Hyper-V. y, a continuación, haga clic en **Aceptar**.

También puede instalar el proveedor desde la línea de comandos:

[!INCLUDE [site-recovery-rw-provider-command-line](../../includes/site-recovery-rw-provider-command-line.md)]


## <a name="set-up-the-target-environment"></a>Configuración del entorno de destino

Seleccione el servidor VMM y la nube de destino:

1. Haga clic en **Preparar infraestructura** > **Destino** y seleccione el servidor VMM de destino que quiere usar.
2. Se mostrarán las nubes en el servidor que están sincronizadas con Site Recovery. Seleccione la nube de destino.

   ![Destino](./media/vmm-to-vmm-walkthrough-source-target/target-vmm.png)



## <a name="next-steps"></a>Pasos siguientes

Vaya al [Paso 7: Configuración de la asignación de red](vmm-to-vmm-walkthrough-network-mapping.md).

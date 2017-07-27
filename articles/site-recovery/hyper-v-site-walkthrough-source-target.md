---
title: "Configurar el origen y destino para la replicación de Hyper-V en Azure (sin System Center VMM) con Azure Site Recovery | Microsoft Docs"
description: "Se resumen los pasos para configurar los valores de origen y de destino para la replicación de máquinas virtuales de Hyper-V en Azure Storage con Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d2010d85-77fd-4dea-84f3-1c960ed4c63f
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: b38eb3a011d46f2239891ea1d1bcac2a4059a866
ms.contentlocale: es-es
ms.lasthandoff: 06/23/2017

---

# <a name="step-8-set-up-the-source-and-target-for-hyper-v-replication-to-azure"></a>Paso 8: configuración del origen y destino para la replicación de Hyper-V en Azure

En este artículo se describe cómo configurar las opciones de origen y destino al replicar máquinas virtuales de Hyper-V locales (sin System Center VMM) en Azure mediante el servicio [Azure Site Recovery](site-recovery-overview.md) en Azure Portal.

Publique cualquier comentario o pregunta en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="set-up-the-source-environment"></a>Configuración del entorno de origen

Configure el sitio Hyper-V, instale el Proveedor de Azure Site Recovery y el agente de Azure Recovery Services en hosts de Hyper-V y registre el sitio en el almacén.

1. En **Preparar infraestructura**, haga clic en **Origen**. Para agregar un nuevo sitio de Hyper-V como contenedor para los hosts o clústeres de Hyper-V, haga clic en **+Sitio Hyper-V**.

    ![Configurar origen](./media/hyper-v-site-walkthrough-source-target/set-source1.png)
2. En **Crear sitio Hyper-V**, especifique un nombre para el sitio. A continuación, haga clic en **Aceptar**. Ahora, seleccione el sitio que creó y, después, haga clic en **+Hyper-V Server** para agregar un servidor al sitio.

    ![Configurar origen](./media/hyper-v-site-walkthrough-source-target/set-source2.png)

3. En **Agregar servidor** > **Tipo de servidor**, compruebe que se muestra **Hyper-V Server**.

    - Asegúrese de que el servidor de Hyper-V que se va a agregar satisface los [requisitos previos](#on-premises-prerequisites) y puede acceder a las direcciones URL especificadas.
    - Descargue el archivo de instalación del proveedor de Azure Site Recovery. Ejecute este archivo para instalar el proveedor y el agente de Recovery Services en cada host de Hyper-V.

    ![Configurar origen](./media/hyper-v-site-walkthrough-source-target/set-source3.png)


## <a name="install-the-provider-and-agent"></a>Instalación del proveedor y el agente

1. Ejecute el archivo de instalación del proveedor en cada host que agregue al sitio Hyper-V. Si va a realizar la instalación en un clúster de Hyper-V, ejecute el programa de instalación en cada nodo de clúster. Instalar y registrar los nodos de cada clúster de Hyper-V garantiza que las máquinas virtuales permanezcan protegidas incluso si se migran entre nodos.
2. En **Microsoft Update** puede optar por recibir actualizaciones para que las actualizaciones del proveedor se realicen según las directivas de Microsoft Update.
3. En **Instalación** acepte o modifique la ubicación predeterminada de instalación del proveedor y haga clic en **Instalar**.
4. En **Configuración de almacén**, haga clic en **Examinar** para seleccionar el archivo de clave del almacén que ha descargado. Especifique la suscripción de Azure Site Recovery, el nombre del almacén y el sitio de Hyper-V al que pertenece el servidor Hyper-V.

    ![Registro de servidor](./media/hyper-v-site-walkthrough-source-target/provider3.png)

5. En **Configuración del proxy**, especifique cómo se conecta el proveedor que se ejecuta en los hosts de Hyper-V a Azure Site Recovery a través de Internet.

    * Si quiere que el proveedor se conecte directamente, seleccione **Conectar directamente con Azure Site Recovery sin un servidor proxy**.
    * Si el proxy existente requiere autenticación, o si quiere utilizar un proxy personalizado para la conexión del proveedor, seleccione **Conectar con Azure Site Recovery con un servidor proxy**.
    * Si utiliza un proxy:
        - Especifique la dirección, el puerto y las credenciales.
        - Asegúrese de que las direcciones URL descritas en los [requisitos previos](#prerequisites) se permiten a través del proxy.

    ![Internet](./media/hyper-v-site-walkthrough-source-target/provider7.png)

6. Una vez finalizada la instalación, haga clic en **Registrar** para registrar el servidor en el almacén.

    ![Ubicación de instalación](./media/hyper-v-site-walkthrough-source-target/provider2.png)

7. Después de que finalice el registro, los metadatos del servidor de Hyper-V se recuperan mediante Azure Site Recovery y el servidor se muestra en **Infraestructura de Site Recovery** > **Hosts de Hyper-V**.


## <a name="set-up-the-target-environment"></a>Configuración del entorno de destino

Especifique la cuenta de Azure Storage para la replicación y la red de Azure a la que se conectarán las máquinas virtuales de Azure después de la conmutación por error.

1. Haga clic en **Preparar infraestructura** > **Destino**.
2. Seleccione la suscripción y el grupo de recursos donde desee crear las máquinas virtuales de Azure después de la conmutación por error. Elija el modelo de implementación que desee usar en Azure (clásico o de Resource Manager) para las máquinas virtuales.

3. Site Recovery comprueba que tiene una o más cuentas de almacenamiento y redes compatibles.

    - Si no tiene una cuenta de almacenamiento, haga clic en **+Storage** para crear una cuenta basada en Resource Manager insertada. 
    - Si no tiene una red de Azure, haga clic en **+Red** para crear una red basada en Resource Manager insertada.






## <a name="next-steps"></a>Pasos siguientes

Vaya al [paso 9: configuración de una directiva de replicación](hyper-v-site-walkthrough-replication.md)


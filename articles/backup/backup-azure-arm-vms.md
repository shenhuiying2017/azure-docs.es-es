---
title: "Copia de seguridad de máquinas virtuales de Azure en un almacén de Recovery Services | Microsoft Docs"
description: "Detecte y registre máquinas virtuales de Azure y haga copias de seguridad de las mismas en un almacén de Recovery Services."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "copia de seguridad de máquinas virtuales; hacer copia de seguridad de máquinas virtuales; copia de seguridad y recuperación ante desastres; copia de seguridad de arm"
ms.assetid: 5c68481d-7be3-4e68-b87c-0961c267053e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/30/2017
ms.author: trinadhk;jimpark;markgal;
translationtype: Human Translation
ms.sourcegitcommit: 39147f2db1e660a21d6ed622206787ea0c569056
ms.openlocfilehash: 28a5014f7ee73b30f879d249811e7fc303b13ac6


---
# <a name="back-up-azure-vms-to-a-recovery-services-vault"></a>Copia de seguridad de máquinas virtuales de Azure en un almacén de Servicios de recuperación
> [!div class="op_single_selector"]
> * [Copia de seguridad de VM en el almacén de Servicios de recuperación](backup-azure-arm-vms.md)
> * [Copia de seguridad de VM en el almacén de Copia de seguridad](backup-azure-vms.md)
>
>

En este artículo se proporcionan detalles para realizar una copia de seguridad de VM de Azure (con el modelo de implementación de Resource Manager y el modelo clásico) en un almacén de Recovery Services. La mayoría del trabajo de la copia de seguridad de máquinas virtuales se basa en la preparación. Antes de poder realizar una copia de seguridad de una máquina virtual, o protegerla, es necesario realizar una serie de tareas que son [requisito previo](backup-azure-arm-vms-prepare.md) con el fin de preparar el entorno para la protección de las máquinas virtuales. Cuando haya realizado estas tareas, puede iniciar la operación de copia de seguridad para tomar instantáneas de la máquina virtual.


[!INCLUDE [learn about backup deployment models](../../includes/backup-deployment-models.md)]

Para más información, vea los artículos sobre cómo [planear la infraestructura de copia de seguridad de máquinas virtuales en Azure](backup-azure-vms-introduction.md) y sobre [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/).

## <a name="triggering-the-backup-job"></a>Desencadenamiento del trabajo de copia de seguridad
La directiva asociada con el almacén de Recovery Services define con qué frecuencia y cuándo se ejecuta la operación de copia de seguridad. De forma predeterminada, la primera copia de seguridad programada es la copia de seguridad inicial. Hasta que se realice la copia de seguridad inicial, el estado de la última copia aparecerá como **Advertencia (copia de seguridad inicial pendiente)** en la hoja **Trabajos de copia de seguridad**.

![Copia de seguridad pendiente](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

A menos que la copia de seguridad inicial vaya a comenzar pronto, se recomienda ejecutar **Hacer ahora una copia de seguridad**. El procedimiento siguiente se inicia desde el panel del almacén. Este procedimiento sirve para ejecutar el trabajo de copia de seguridad inicial después de haber completado todos los requisitos previos. Si ya se ha ejecutado el trabajo de copia de seguridad inicial, este procedimiento no está disponible. La directiva de copia de seguridad asociada determina el siguiente trabajo de copia de seguridad.  

Para ejecutar el trabajo de copia de seguridad inicial:

1. En el panel del almacén, en el icono **Copia de seguridad**, haga clic en **Azure Virtual Machines**. <br/>
    ![Icono Configuración](./media/backup-azure-vms-first-look-arm/rs-vault-in-dashboard-backup-vms.png)

    Se abrirá la hoja **Elementos de copia de seguridad** .
2. En la hoja **Elementos de copia de seguridad**, haga clic con el botón derecho en el almacén del que desea realizar una copia de seguridad y haga clic en **Realizar copia de seguridad ahora**.

    ![Icono Configuración](./media/backup-azure-vms-first-look-arm/back-up-now.png)

    Se desencadena el trabajo de copia de seguridad. <br/>

    ![Trabajo de copia de seguridad desencadenado](./media/backup-azure-vms-first-look-arm/backup-triggered.png)
3. Para ver si la copia de seguridad inicial se ha completado, en el panel del almacén, en el icono **Trabajos de copia de seguridad**, haga clic en **Azure Virtual Machines**.

    ![Icono de Trabajos de copia de seguridad](./media/backup-azure-vms-first-look-arm/open-backup-jobs.png)

    Se abrirá la hoja Trabajos de copia de seguridad.
4. En la hoja **Trabajos de copia de seguridad** , puede ver el estado de todos los trabajos.

    ![Icono de Trabajos de copia de seguridad](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view.png)

   > [!NOTE]
   > Durante la operación de copia de seguridad, la extensión de copia de seguridad de cada máquina virtual vacía todas las escrituras y toma una instantánea coherente.
   >
   >

    Cuando finalice el trabajo de copia de seguridad, el estado será *Completado*.

## <a name="troubleshooting-errors"></a>Solución de errores
Si se encuentra con problemas mientras realiza la copia de seguridad de la máquina virtual, vea el [artículo sobre cómo solucionar problemas de máquinas virtuales](backup-azure-vms-troubleshoot.md) para obtener ayuda.

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha protegido su máquina virtual, vea los siguientes artículos para obtener información sobre las tareas de administración de las máquinas virtuales y cómo restaurarlas.

* [Administración y supervisión de las máquinas virtuales](backup-azure-manage-vms.md)
* [Restauración de máquinas virtuales](backup-azure-arm-restore-vms.md)



<!--HONumber=Jan17_HO5-->



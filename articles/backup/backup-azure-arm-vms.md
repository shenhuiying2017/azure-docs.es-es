<properties
	pageTitle="Copia de seguridad de máquinas virtuales de Azure Resource Manager | Microsoft Azure"
	description="Detecte, registre y realice copias de seguridad de máquinas virtuales de ARM en un almacén de Servicios de recuperación con estos procedimientos de copia de seguridad de máquinas virtuales de Azure."
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="jwhit"
	editor=""
	keywords="copia de seguridad de máquinas virtuales; hacer copia de seguridad de máquinas virtuales; copia de seguridad y recuperación ante desastres; copia de seguridad de arm"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="trinadhk; jimpark; markgal;"/>


# Copia de seguridad de máquinas virtuales de Azure Resource Manager (ARM)

> [AZURE.SELECTOR]
- [Back up ARM VMs to Azure](backup-azure-arm-vms.md) (Copia de seguridad de máquinas virtuales de ARM en Azure)
- [Copia de seguridad de máquinas virtuales de Azure](backup-azure-vms.md)

En este artículo se proporciona el procedimiento para crear una copia de seguridad de máquinas virtuales (VM) de Azure Resource Manager en un almacén de Servicios de recuperación. La mayor parte del trabajo de copia de seguridad de una máquina virtual de ARM pasa por una fase de preparación. Antes de poder realizar una copia de seguridad de una máquina virtual de Azure, o protegerla, es necesario realizar una serie de tareas que son [requisito previo](backup-azure-arm-vms-prepare.md) con el fin de preparar el entorno para la protección de las máquinas virtuales. Cuando haya realizado estas tareas, puede iniciar la operación de copia de seguridad para tomar instantáneas de la máquina virtual.

>[AZURE.NOTE] Azure cuenta con dos modelos de implementación para crear recursos y trabajar con ellos: [Resource Manager y el modelo clásico](../resource-manager-deployment-model.md). Este artículo es para su uso con Resource Manager y máquinas virtuales basadas en ARM. Consulte [Copia de seguridad de máquinas virtuales de Azure](backup-azure-vms.md) para más información sobre cómo trabajar con máquinas virtuales del modelo de implementación clásica.

Para obtener más información, vea los artículos sobre cómo [planear la infraestructura de copia de seguridad de máquinas virtuales en Azure](backup-azure-vms-introduction.md) y sobre las [máquinas virtuales de Azure](https://azure.microsoft.com/documentation/services/virtual-machines/).

## Desencadenamiento del trabajo de copia de seguridad

La directiva asociada con el almacén de Servicios de recuperación define con qué frecuencia y cuándo se ejecuta la operación de copia de seguridad. De forma predeterminada, la primera copia de seguridad programada es la copia de seguridad inicial. Hasta que se realice la copia de seguridad inicial, el estado de la última copia de seguridad en la hoja **Trabajos de copia de seguridad** se muestra como **Advertencia (copia de seguridad inicial pendiente)**.

![Copia de seguridad pendiente](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

A menos que la copia de seguridad inicial vaya a empezar en breve, se recomienda que ejecute **Backup now** (Iniciar copia de seguridad). El procedimiento siguiente se inicia desde el panel del almacén. Este procedimiento sirve para ejecutar el trabajo de copia de seguridad inicial después de haber completado todos los requisitos previos. Si ya se ha ejecutado el trabajo de copia de seguridad inicial, este procedimiento no está disponible. La directiva de copia de seguridad asociada determina el siguiente trabajo de copia de seguridad.

Para ejecutar el trabajo de copia de seguridad inicial:

1. En el panel del almacén, en el icono **Copia de seguridad**, haga clic en **Máquinas virtuales de Azure**. <br/> ![Icono Configuración](./media/backup-azure-vms-first-look-arm/rs-vault-in-dashboard-backup-vms.png)

    Se abrirá la hoja **Elementos de copia de seguridad**.

2. En la hoja **Elementos de copia de seguridad**, haga clic con el botón derecho en el almacén del que quiera realizar la copia de seguridad, y haga clic en **Backup now** (Iniciar copia de seguridad).

    ![Icono Configuración](./media/backup-azure-vms-first-look-arm/back-up-now.png)

    Se desencadena el trabajo de copia de seguridad. <br/>

    ![Trabajo de copia de seguridad desencadenado](./media/backup-azure-vms-first-look-arm/backup-triggered.png)

3. Para ver si la copia de seguridad inicial se ha completado, en el panel del almacén, en el icono **Trabajos de copia de seguridad**, haga clic en **Máquinas virtuales de Azure**.

    ![Icono de Trabajos de copia de seguridad](./media/backup-azure-vms-first-look-arm/open-backup-jobs.png)

    Se abrirá la hoja Trabajos de copia de seguridad.

4. En la hoja **Trabajos de copia de seguridad**, puede ver el estado de todos los trabajos.

    ![Icono de Trabajos de copia de seguridad](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view.png)

    >[AZURE.NOTE] Como parte de la operación de copia de seguridad, el servicio Copia de seguridad de Azure emite un comando a la extensión de copia de seguridad en cada máquina virtual para vaciar toda la escritura y tomar una instantánea coherente.

    Cuando finalice el trabajo de copia de seguridad, el estado será *Completado*.


## Solución de errores
Si se encuentra con problemas mientras realiza la copia de seguridad de la máquina virtual, consulte el [artículo sobre cómo solucionar problemas de máquinas virtuales](backup-azure-vms-troubleshoot.md) para obtener ayuda.

## Pasos siguientes

Ahora que ha protegido su máquina virtual, consulte los siguientes artículos para conocer las tareas de administración adicionales que puede hacer con las máquinas virtuales y cómo restaurarlas.

- [Administración y supervisión de las máquinas virtuales](backup-azure-manage-vms.md)
- [Restauración de máquinas virtuales](backup-azure-arm-restore-vms.md)

<!---HONumber=AcomDC_0518_2016-->
---
title: "Habilitación de la copia de seguridad de máquinas virtuales de Azure durante el proceso de creación | Microsoft Docs"
description: "Vea los pasos para habilitar la copia de seguridad de máquinas virtuales de Azure durante el proceso de creación."
services: backup, virtual-machines
documentationcenter: 
author: markgalioto
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: 
ms.service: backup, virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 01/08/2018
ms.author: trinadhk
ms.openlocfilehash: 4041fc555fe4b61d10f84236dcae5156c6282fd3
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/17/2018
---
# <a name="enable-backup-during-azure-virtual-machine-creation"></a>Habilitación de la copia de seguridad durante la creación de máquinas virtuales de Azure 

El servicio Azure Backup proporciona una interfaz para crear y configurar copias de seguridad en la nube. Proteja sus datos realizando copias de seguridad (también llamadas "puntos de recuperación") a intervalos regulares. Azure Backup crea puntos de recuperación que se guardan en almacenes de recuperación con redundancia geográfica. En este artículo se detalla cómo habilitar la copia de seguridad al crear una máquina virtual (VM) en Azure Portal.  

## <a name="log-in-to-azure"></a>Inicie sesión en Azure. 

Si aún no ha iniciado sesión en su cuenta, hágalo en [Azure Portal](http://portal.azure.com).
 
## <a name="create-virtual-machine-with-backup-configured"></a>Creación de máquinas virtuales con la copia de seguridad configurada 

1. Haga clic en **Nuevo**, en la esquina superior izquierda de Azure Portal. 

2. Seleccione **Proceso** y, luego, seleccione una imagen de la máquina virtual.   

3. Escriba la información de la máquina virtual. El nombre de usuario y la contraseña que especifique se usarán para iniciar sesión en la máquina virtual. Cuando haya terminado, haga clic en **Aceptar**. 

4. Seleccione un tamaño para la máquina virtual.  

5. En **Configuración > Backup**, haga clic en **Habilitado** para abrir la configuración de copia de seguridad. Puede aceptar los valores predeterminados y hacer clic en **Aceptar** en la página de configuración para pasar a la página Resumen con el fin de crear la máquina virtual. Si desea cambiar los valores, siga estos pasos.  

6. Cree o seleccione un almacén de Recovery Services, que contiene las copias de seguridad de la máquina virtual. Si va a crear un almacén de Recovery Services, puede elegir un grupo de recursos del almacén.  

    ![Configuración de Backup en la página de creación de máquinas virtuales](./media/backup-during-vm-creation/create-vm-backup-config.png) 

    > [!NOTE] 
    > El grupo de recursos del almacén de Recovery Services puede ser diferente al de la máquina virtual.  
    > 
    > 

7. De forma predeterminada, se selecciona una directiva de copia de seguridad para proteger rápidamente la máquina virtual. Una directiva de copia de seguridad especifica la frecuencia con que tomar instantáneas de copia de seguridad y el tiempo que desea conservar esas copias de seguridad. Puede aceptar la directiva predeterminada, o bien crear o seleccionar una directiva de copia de seguridad diferente. Para editar la directiva de copia de seguridad, seleccione **Directiva de Backup** y cambiar los valores de la directiva.  

8. Cuando se encuentre satisfecho con los valores de configuración de copia de seguridad, en la página de configuración, haga clic en **Aceptar**.  

9. En la página Resumen, una vez que haya pasado la validación, haga clic en **Crear** para crear una máquina virtual que use la configuración de copia de seguridad. 

## <a name="initiate-a-backup-after-creating-the-vm"></a>Inicio de una copia de seguridad después de crear la máquina virtual 

Aunque se haya creado la directiva de Backup, es recomendable crear una copia de seguridad inicial. Para ver los detalles de la copia de seguridad de la máquina virtual una vez que finalice la plantilla de creación de máquinas virtuales, en la configuración de **Operaciones** del menú izquierdo, haga clic en **Backup**. Puede utilizar esta opción para desencadenar una copia de seguridad a petición, restaurar una máquina virtual completa o todos los discos, restaurar archivos desde una copia de seguridad de máquina virtual o cambiar la directiva de copia de seguridad asociada con la máquina virtual.  

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes 

### <a name="which-vm-images-enable-backup-at-the-time-of-vm-creation"></a>¿Qué imágenes de máquina virtual habilitan la copia de seguridad en el momento de creación de las máquinas virtuales? 

La siguiente lista de imágenes de núcleo publicada por Microsoft es compatible con la habilitación de la copia de seguridad durante la creación de máquinas virtuales. Para otras máquinas virtuales, puede habilitar la copia de seguridad una vez creada la máquina virtual. Más información en el artículo sobre cómo [habilitar la copia de seguridad una vez creada la máquina virtual](quick-backup-vm-portal.md) 

- **Windows**: Windows Server 2016 Datacenter, Windows Server 2016 Datacenter (instalación básica), Windows Server 2012 Datacenter, Windows Server 2012 R2 Datacenter y Windows Server 2008 R2 SP1 
- **Ubuntu**: Ubuntu Server 1710, Ubuntu Server 1704, Ubuntu Server 1604(LTS) y Ubuntu Server 1404(LTS) 
- **Redhat**: RHEL 6.7, 6.8, 6.9, 7.2, 7.3 y 7.4 
- **SUSE**: SUSE Linux Enterprise Server 11 SP4, 12 SP2 y 12 SP3 
- **Debian**: Debian 8 y Debian 9 
- **CentOS**: CentOS 6.9, CentOS 7.3 
- **Oracle Linux**: Oracle Linux 6.7, 6.8, 6.9, 7.2 y 7.3 
 
### <a name="is-backup-cost-included-in-the-vm-cost"></a>¿Están los costos de copia de seguridad incluidos en el costo de la máquina virtual? 

No, los costos de copia de seguridad son independientes o distintos de los de máquinas virtuales. Para obtener más información sobre los precios de copia de seguridad, consulte el [sitio Precios de Backup](https://azure.microsoft.com/pricing/details/backup/).
 
### <a name="which-permissions-are-required-to-enable-backup-on-a-vm"></a>¿Qué permisos son necesarios para habilitar la copia de seguridad en una máquina virtual? 

Si es un colaborador de la máquina virtual, puede habilitar la copia de seguridad en la máquina virtual. Si utiliza un rol personalizado, necesita los permisos siguientes para habilitar correctamente la copia de seguridad en la máquina virtual. 

- Microsoft.RecoveryServices/Vaults/write 
- Microsoft.RecoveryServices/Vaults/read 
- Microsoft.RecoveryServices/locations/* 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write 
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write 
- Microsoft.RecoveryServices/Vaults/backupPolicies/read 
- Microsoft.RecoveryServices/Vaults/backupPolicies/write 
 
Si el almacén de Recovery Services y la máquina virtual tienen distintos grupos de recursos, asegúrese de que tiene permisos de escritura en el grupo de recursos del almacén de Recovery Services.  

## <a name="next-steps"></a>pasos siguientes 

Ahora que ha protegido su máquina virtual, vea los siguientes artículos para obtener información sobre las tareas de administración de las máquinas virtuales y cómo restaurarlas. 

- [Administración y supervisión de las máquinas virtuales](backup-azure-manage-vms.md) 
- [Restauración de máquinas virtuales](backup-azure-arm-restore-vms.md) 

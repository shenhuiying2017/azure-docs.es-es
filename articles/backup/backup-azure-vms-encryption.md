---
title: "Copia de seguridad y restauración de máquinas virtuales cifradas mediante Azure Backup"
description: "Este artículo trata sobre la experiencia de copia de seguridad y restauración de máquinas virtuales cifradas mediante Azure Disk Encryption."
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 8387f186-7d7b-400a-8fc3-88a85403ea63
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 04/24/2017
ms.author: pajosh;markgal;trinadhk
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: fc27849f3309f8a780925e3ceec12f318971872c
ms.openlocfilehash: b6aed1f34a899f4e182f0cf48426ec2da88b51c2
ms.contentlocale: es-es
ms.lasthandoff: 06/14/2017


---
# <a name="how-to-back-up-and-restore-encrypted-virtual-machines-with-azure-backup"></a>Procedimiento de realización de copias de seguridad y restauración de máquinas virtuales cifradas con Azure Backup
Este artículo trata sobre los pasos para realizar la copia de seguridad y restauración de máquinas virtuales mediante Azure Backup. También proporciona detalles acerca de los escenarios admitidos, requisitos previos y pasos para solucionar problemas en los casos de error.

## <a name="supported-scenarios"></a>Escenarios admitidos
> [!NOTE]
> 1. La copia de seguridad y restauración de máquinas virtuales cifradas solo se admite para máquinas virtuales implementadas con el modelo de Resource Manager. No se admite para máquinas virtuales implementadas con el modelo clásico. <br>
> 2. Se puede usar en máquinas virtuales con Windows y Linux mediante Azure Disk Encryption, que, para proporcionar el cifrado de discos, utiliza la característica BitLocker estándar en el caso de Windows y la característica DM-Crypt en el caso de Linux. <br>
> 3. Solo se admite para máquinas virtuales cifradas mediante la clave de cifrado de BitLocker y la clave de cifrado de clave. No se admite para máquinas virtuales cifradas solo mediante la clave de cifrado de BitLocker. <br>
>
>

## <a name="pre-requisites"></a>Requisitos previos
1. Máquina virtual cifrada mediante [Azure Disk Encryption](../security/azure-security-disk-encryption.md). Se deben cifrar las máquinas virtuales mediante la clave de cifrado de BitLocker y la clave de cifrado de clave (ambas).
2. Se ha creado el almacén de Recovery Services y se ha establecido la replicación de almacenamiento mediante los pasos mencionados en el artículo [Preparación del entorno para la copia de seguridad de máquinas virtuales implementadas según el modelo de Resource Manager](backup-azure-arm-vms-prepare.md).

## <a name="backup-encrypted-vm"></a>Copia de seguridad cifrada de máquina virtual
Utilice los pasos siguientes para establecer el objetivo de copia de seguridad, definir directivas, configurar elementos y desencadenar la copia de seguridad.

### <a name="configure-backup"></a>Configuración de la copia de seguridad
1. Si ya tiene abierto un almacén de Recovery Services, vaya al siguiente paso. Si no tiene abierto un almacén de Recovery Services, pero está en Azure Portal, en el menú del concentrador, haga clic en **Examinar**.

   * En la lista de recursos, escriba **Recovery Services**.
   * Cuando comience a escribir, la lista se filtrará en función de la entrada. Haga clic en **Almacenes de Recovery Services**cuando lo vea.

      ![Creación del almacén de Recovery Services, paso 1](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

     Aparece la lista de almacenes de Recovery Services. En la lista de almacenes de Recovery Services, seleccione un almacén.

     Se abre el panel del almacén seleccionado.
2. En la lista de elementos que aparece en el almacén, haga clic en **Backup** para abrir la hoja Backup.

      ![Hoja Backup abierta](./media/backup-azure-vms-encryption/select-backup.png)
3. En la hoja Backup, haga clic en **Objetivo de Backup** para abrir la hoja del mismo nombre.

      ![Hoja Escenario abierta](./media/backup-azure-vms-encryption/select-backup-goal-one.png)
4. En la hoja Objetivo de Backup, establezca **¿Dónde se ejecuta su carga de trabajo?** en Azure y **What do you want to backup** (¿De qué desea realizar copias de seguridad?) en Máquina virtual. A continuación, haga clic en **Aceptar**.

   La hoja Objetivo de Backup se cierra y se abre la hoja Directiva de Backup.

   ![Hoja Escenario abierta](./media/backup-azure-vms-encryption/select-backup-goal-two.png)
5. En la hoja Directiva de Backup, seleccione la que quiera aplicar al almacén y haga clic en **Aceptar**.

      ![Seleccionar directiva de copia de seguridad](./media/backup-azure-vms-encryption/setting-rs-backup-policy-new.png)

    En los detalles se muestran los datos de la directiva predeterminada. Si desea crear una directiva, seleccione **Crear nuevo** en el menú desplegable. Al hacer clic en **Aceptar**, la directiva de copia de seguridad se asocia con el almacén.

    A continuación, elija las máquinas virtuales que se asociarán con el almacén.
6. Elija las máquinas virtuales cifradas que se asociarán con la directiva especificada y haga clic en **Aceptar**.

      ![Selección de las máquinas virtuales cifradas](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)
7. Esta página muestra un mensaje sobre el almacén de claves asociado a las máquinas virtuales cifradas seleccionadas. El servicio de Backup requiere acceso de solo lectura a las claves y secretos del almacén de claves. Este utiliza estos permisos para la copia de seguridad de la clave y el secreto, junto con la de las máquinas virtuales asociadas.

      ![Mensaje de máquinas virtuales cifradas](./media/backup-azure-vms-encryption/encrypted-vm-message.png)

      Ahora que ha definido toda la configuración del almacén, en la hoja Backup, haga clic en Habilitar Backup en la parte inferior de la página. Habilitar Backup permite implementar la directiva en el almacén y en las máquinas virtuales.
8. La siguiente fase de la preparación es instalar el agente de máquina virtual o comprobar que esté instalado. Para ello, utilice los pasos mencionados en el artículo [Preparación del entorno para la copia de seguridad de máquinas virtuales implementadas según el modelo de Resource Manager](backup-azure-arm-vms-prepare.md).

### <a name="triggering-backup-job"></a>Desencadenamiento del trabajo de copia de seguridad
Utilice los pasos mencionados en el artículo [Copia de seguridad de máquinas virtuales de Azure en un almacén de Recovery Services](backup-azure-arm-vms.md) para desencadenar el trabajo de copia de seguridad.

### <a name="continue-backups-of-already-backed-up-vms-with-encryption-enabled"></a>Continuar copias de seguridad de máquinas virtuales ya copiadas con el cifrado habilitado  
Si tiene máquinas virtuales cuya copia de seguridad ya se ha realizado en el almacén de Recovery Services y en las que se ha habilitado el cifrado en un momento posterior, debe conceder permisos al servicio de copia de seguridad para acceder al almacén de claves para que las copias de seguridad continúen. Puede proporcionar estos permisos en PowerShell mediante los pasos de la sección **Habilitar Backup** de la [documentación de PowerShell](backup-azure-vms-automation.md). 

## <a name="restore-encrypted-vm"></a>Restauración de máquinas virtuales cifradas
Para restaurar una máquina virtual cifrada, en primer lugar es preciso restaurar los discos, para lo que hay que seguir los pasos que se indican en la sección **Restauración de discos de copia de seguridad** de [Elección de una configuración de restauración para una máquina virtual](backup-azure-arm-restore-vms.md#choosing-a-vm-restore-configuration). Después de eso, puede usar una de las siguientes opciones:
* Use los pasos de PowerShell que se indican en [Creación de una máquina virtual a partir de discos restaurados](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) para crear una máquina virtual completa a partir de discos restaurados.
* O bien, [usar una plantilla generada como parte de los discos de restauración](backup-azure-arm-restore-vms.md#use-templates-to-customize-restore-vm) para crear máquinas virtuales a partir de discos restaurados. Las plantillas pueden usarse únicamente para los puntos de recuperación creados después del 26 de abril de 2017.

## <a name="troubleshooting-errors"></a>Solución de errores
| Operación | Detalles del error | Resolución |
| --- | --- | --- |
| Backup |La validación produjo un error debido a que la máquina virtual se ha cifrado solo con BEK. Las copias de seguridad se pueden habilitar únicamente para las máquinas virtuales cifradas con BEK y KEK. |Las máquinas virtuales deben cifrarse mediante BEK y KEK. En primer lugar, descifre la VM y cífrela mediante BEK y KEK. Habilite la copia de seguridad una vez que la VM esté cifrada mediante BEK y KEK. Conozca más sobre cómo [descifrar y cifrar la VM](../security/azure-security-disk-encryption.md).  |
| Restauración |No se puede restaurar esta máquina virtual cifrada porque no existe el almacén de claves asociado con esta máquina virtual. |Cree el almacén de claves mediante los pasos descritos en [Introducción a Azure Key Vault](../key-vault/key-vault-get-started.md). Consulte el artículo [Restore key vault key and secret using Azure Backup](backup-azure-restore-key-secret.md) (Restauración de la clave y secreto del almacén de claves mediante Azure Backup) para restaurar la clave y el secreto si estos no existen. |
| Restauración |No se puede restaurar esta máquina virtual cifrada porque no existe la clave y el secreto asociados con esta máquina virtual. |Consulte el artículo [Restore key vault key and secret using Azure Backup](backup-azure-restore-key-secret.md) (Restauración de la clave y secreto del almacén de claves mediante Azure Backup) para restaurar la clave y el secreto si estos no existen. |
| Restauración |El servicio Backup no tiene autorización para acceder a los recursos de su suscripción. |Como ya se ha indicado, en primer lugar es preciso restaurar los discos, para lo que hay que seguir los pasos especificados en la sección **Restauración de discos de copia de seguridad** de [Elección de una configuración de restauración para una máquina virtual](backup-azure-arm-restore-vms.md#choosing-a-vm-restore-configuration). Después, debe usar PowerShell para [crear una máquina virtual a partir de discos restaurados](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). |
|Copia de seguridad | El servicio Azure Backup no tiene los permisos suficientes en Key Vault para realizar copias de seguridad de máquinas virtuales cifradas | La máquina virtual debe cifrarse con la clave de cifrado BitLocker y la clave de cifrado de claves. Después de eso, debe habilitarse la copia de seguridad.  Se debe proporcionar estos permisos al servicio Backup en PowerShell mediante los pasos que se indican en la sección **Habilitar protección** de la documentación de PowerShell en [Uso de los cmdlets AzureRM.RecoveryServices.Backup para realizar copias de seguridad de máquinas virtuales](backup-azure-vms-automation.md). |  


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
ms.date: 10/25/2016
ms.author: markgal; jimpark; trinadhk
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: fd65e7acc10b3e750025279820bddbdef5de5498


---
# <a name="backup-and-restore-encrypted-vms-using-azure-backup"></a>Copia de seguridad y restauración de máquinas virtuales cifradas mediante Azure Backup
Este artículo trata sobre los pasos para realizar la copia de seguridad y restauración de máquinas virtuales mediante Azure Backup. También proporciona detalles acerca de los escenarios admitidos, requisitos previos y pasos para solucionar problemas en los casos de error.

## <a name="supported-scenarios"></a>Escenarios admitidos
> [!NOTE]
> 1. La copia de seguridad y restauración de máquinas virtuales cifradas solo se admite para máquinas virtuales implementadas con el modelo de Resource Manager. No se admite para máquinas virtuales implementadas con el modelo clásico. <br>
> 2. Solo se admite para máquinas virtuales cifradas mediante la clave de cifrado de BitLocker y la clave de cifrado de clave. No se admite para máquinas virtuales cifradas solo mediante la clave de cifrado de BitLocker. <br>
> 
> 

## <a name="pre-requisites"></a>Requisitos previos
1. Máquina virtual cifrada mediante [Azure Disk Encryption](../security/azure-security-disk-encryption.md). Se deben cifrar las máquinas virtuales mediante la clave de cifrado de BitLocker y la clave de cifrado de clave (ambas).
2. Se ha creado el almacén de Recovery Services y se ha establecido la replicación de almacenamiento mediante los pasos mencionados en el artículo [Preparación del entorno para la copia de seguridad de máquinas virtuales implementadas según el modelo de Resource Manager](backup-azure-arm-vms-prepare.md).

## <a name="backup-encrypted-vm"></a>Copia de seguridad cifrada de máquina virtual
Utilice los pasos siguientes para establecer el objetivo de copia de seguridad, definir directivas, configurar elementos y desencadenar la copia de seguridad.

### <a name="configure-backup"></a>Configuración de la copia de seguridad
1. Si ya tiene abierto un almacén de Recovery Services, vaya al siguiente paso. Si no tiene abierto un almacén de Servicios de recuperación, pero está en el Portal de Azure, en el menú del concentrador, haga clic en **Examinar**.
   
   * En la lista de recursos, escriba **Servicios de recuperación**.
   * Cuando comience a escribir, la lista se filtrará en función de la entrada. Haga clic en **Almacenes de servicios de recuperación**cuando lo vea.
     
      ![Creación del almacén de Servicios de recuperación, paso 1](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>
     
     Aparece la lista de almacenes de Servicios de recuperación. En la lista de almacenes de Servicios de recuperación, seleccione un almacén.
     
     Se abre el panel del almacén seleccionado.
2. En la lista de elementos que aparece en el almacén, haga clic en **Copia de seguridad** para abrir la hoja Copia de seguridad.
   
      ![Hoja Copia de seguridad abierta](./media/backup-azure-vms-encryption/select-backup.png) 
3. En la hoja Copia de seguridad, haga clic en **Objetivo de copia de seguridad** para abrir la hoja del mismo nombre.
   
      ![Hoja Escenario abierta](./media/backup-azure-vms-encryption/select-backup-goal-one.png) 
4. En la hoja Objetivo de copia de seguridad, establezca **¿Dónde se ejecuta su carga de trabajo?** en Azure y **What do you want to backup** (¿De qué desea realizar copias de seguridad?) en Máquina virtual. A continuación, haga clic en **Aceptar**.
   
   La hoja Backup Goal (Objetivo de la copia de seguridad) se cierra y se abre la hoja Directiva de copia de seguridad.
   
   ![Hoja Escenario abierta](./media/backup-azure-vms-encryption/select-backup-goal-two.png) 
5. En la hoja Directiva de copia de seguridad, seleccione la que quiera aplicar al almacén y haga clic en **Aceptar**.
   
      ![Seleccionar directiva de copia de seguridad](./media/backup-azure-vms-encryption/setting-rs-backup-policy-new.png) 
   
    En los detalles se muestran los datos de la directiva predeterminada. Si desea crear una directiva, seleccione **Crear nuevo** en el menú desplegable. Al hacer clic en **Aceptar**, la directiva de copia de seguridad se asocia con el almacén.
   
    A continuación, elija las máquinas virtuales que se asociarán con el almacén.
6. Elija las máquinas virtuales cifradas que se asociarán con la directiva especificada y haga clic en **Aceptar**.
   
      ![Selección de las máquinas virtuales cifradas](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)
7. Esta página muestra un mensaje sobre el almacén de claves asociado a las máquinas virtuales cifradas seleccionadas. El servicio de copia de seguridad requiere acceso de solo lectura a las claves y secretos del almacén de claves. Este utiliza estos permisos para la copia de seguridad de la clave y el secreto, junto con la de las máquinas virtuales asociadas. 
   
      ![Mensaje de máquinas virtuales cifradas](./media/backup-azure-vms-encryption/encrypted-vm-message.png)
   
      Ahora que ha definido toda la configuración del almacén, en la hoja Copia de seguridad, haga clic en Habilitar copia de seguridad en la parte inferior de la página. Habilitar copia de seguridad permite implementar la directiva en el almacén y en las máquinas virtuales.
8. La siguiente fase de la preparación es instalar el agente de máquina virtual o comprobar que esté instalado. Para ello, utilice los pasos mencionados en el artículo [Preparación del entorno para la copia de seguridad de máquinas virtuales implementadas según el modelo de Resource Manager](backup-azure-arm-vms-prepare.md). 

### <a name="triggering-backup-job"></a>Desencadenamiento del trabajo de copia de seguridad
Utilice los pasos mencionados en el artículo [Copia de seguridad de máquinas virtuales de Azure en un almacén de servicios de recuperación](backup-azure-arm-vms.md) para desencadenar el trabajo de copia de seguridad.

## <a name="restore-encrypted-vm"></a>Restauración de máquinas virtuales cifradas
La experiencia de restauración para máquinas virtuales cifradas y sin cifrar es la misma. Utilice los pasos mencionados en [Uso de Azure Portal para restaurar máquinas virtuales](backup-azure-arm-restore-vms.md) para restaurar la máquina virtual cifrada. En caso de que necesite restaurar las claves y secretos, debe asegurarse de que el almacén de claves para restaurarlos ya existe.

## <a name="troubleshooting-errors"></a>Solución de errores
| Operación | Detalles del error | Resolución |
| --- | --- | --- |
| Copia de seguridad |La validación produjo un error debido a que la máquina virtual se ha cifrado solo con BEK. Las copias de seguridad se pueden habilitar únicamente para las máquinas virtuales cifradas con BEK y KEK. |Las máquinas virtuales deben cifrarse mediante BEK y KEK. Después de eso, debe habilitarse la copia de seguridad. |
| Restauración |No se puede restaurar esta máquina virtual cifrada porque no existe el almacén de claves asociado con esta máquina virtual. |Cree el almacén de claves mediante los pasos descritos en [Introducción a Azure Key Vault](../key-vault/key-vault-get-started.md). Consulte el artículo [Restore key vault key and secret using Azure Backup](backup-azure-restore-key-secret.md) (Restauración de la clave y secreto del almacén de claves mediante Azure Backup) para restaurar la clave y el secreto si estos no existen. |
| Restauración |No se puede restaurar esta máquina virtual cifrada porque no existe la clave y el secreto asociados con esta máquina virtual. |Consulte el artículo [Restore key vault key and secret using Azure Backup](backup-azure-restore-key-secret.md) (Restauración de la clave y secreto del almacén de claves mediante Azure Backup) para restaurar la clave y el secreto si estos no existen. |




<!--HONumber=Nov16_HO3-->



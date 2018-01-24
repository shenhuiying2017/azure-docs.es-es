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
ms.date: 10/13/2017
ms.author: pajosh;markgal;trinadhk; sogup
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 509e891207d1469ed244eab4512ec66420284fd5
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="back-up-and-restore-encrypted-virtual-machines-with-azure-backup"></a>Copia de seguridad y restauración de máquinas virtuales cifradas con Azure Backup
Este artículo trata sobre los pasos para realizar la copia de seguridad y restauración de máquinas virtuales (VM) mediante Azure Backup. También se proporcionan detalles sobre los escenarios admitidos, requisitos previos y pasos para solucionar problemas en los casos de error.

## <a name="supported-scenarios"></a>Escenarios admitidos

 * La copia de seguridad y restauración de máquinas virtuales cifradas solo se admite para máquinas virtuales que usan el modelo de implementación de Azure Resource Manager. No se admite en las que usan el modelo de implementación clásica. <br>
 * La copia de seguridad y restauración de máquinas virtuales cifradas se admite en máquinas virtuales Windows y Linux que usen Azure Disk Encryption. Disk Encryption emplea BitLocker, la característica estándar del sector de Windows y la característica dm-crypt de Linux para proporcionar el cifrado de los discos. <br>
 
 En la tabla siguiente se muestran los escenarios admitidos para máquinas virtuales cifradas solo con clave de cifrado de BitLocker (BEK) y las cifradas con clave de cifrado de claves (KEK).
 
 
   |  | BEK + máquinas virtuales con KEK | Máquinas virtuales solo con BEK |
   | --- | --- | --- |
   | **Máquinas virtuales no administradas**  | Sí | Sí  |
   | **Máquinas virtuales administradas**  | Sí | Sí  |

## <a name="prerequisites"></a>requisitos previos
* Las máquinas virtuales se cifran mediante [Azure Disk Encryption](../security/azure-security-disk-encryption.md).

* Se ha creado un almacén de Recovery Services y la replicación del almacenamiento se ha establecido siguiendo los pasos que se indican en la [preparación del entorno para la copia de seguridad](backup-azure-arm-vms-prepare.md).

* Se han otorgado a Azure Backup [permisos de acceso a un almacén de claves](#provide-permissions-to-azure-backup) que contiene claves y secretos para las máquinas virtuales cifradas.

## <a name="backup-encrypted-vm"></a>Copia de seguridad de máquina virtual cifrada
Utilice los pasos siguientes para establecer un objetivo de copia de seguridad, definir directivas, configurar elementos y desencadenar una copia de seguridad.

### <a name="configure-backup"></a>Configuración de la copia de seguridad
1. Si ya tiene abierto un almacén de Recovery Services, vaya al siguiente paso. Si no tiene abierto un almacén de Recovery Services, pero está en Azure Portal, en el **menú del concentrador**, haga clic en **Examinar**.

   a. En la lista de recursos, escriba **Recovery Services**.

   b. Cuando comience a escribir, la lista se filtrará en función de la entrada. Cuando vea la opción **Almacenes de Recovery Services**, haga clic en ella.

      ![Almacén de Recovery Services](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

    c. Aparece la lista de almacenes de Recovery Services. Seleccione un almacén de la lista.

     Se abre el panel del almacén seleccionado.
2. En la lista de elementos que aparece en el almacén, seleccione **Copia de seguridad** para iniciar la copia de seguridad de la máquina virtual cifrada.

      ![Hoja Copia de seguridad](./media/backup-azure-vms-encryption/select-backup.png)
3. En el icono **Copia de seguridad**, seleccione **Objetivo de Backup**.

      ![Hoja Escenario](./media/backup-azure-vms-encryption/select-backup-goal-one.png)
4. En **¿Dónde se ejecuta su carga de trabajo?**, seleccione **Azure**. En **¿De qué quiere realizar una copia de seguridad?**, seleccione **Máquina virtual**. Después seleccione **Aceptar**.

   ![Hoja Escenario abierta](./media/backup-azure-vms-encryption/select-backup-goal-two.png)
5. En **Elegir directiva de copia de seguridad**, seleccione la directiva de copia de seguridad que quiere aplicar al almacén. Después seleccione **Aceptar**.

      ![Seleccionar directiva de copia de seguridad](./media/backup-azure-vms-encryption/setting-rs-backup-policy-new.png)

    Se muestran los detalles de la directiva predeterminada. Si quiere crear una directiva, seleccione **Crear nuevo** en la lista desplegable. Al seleccionar **Aceptar**, la directiva de copia de seguridad se asocia con el almacén.

6. Elija las máquinas virtuales cifradas que se asociarán con la directiva especificada y seleccione **Aceptar**.

      ![Selección de las máquinas virtuales cifradas](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)
7. Esta página muestra un mensaje sobre los almacenes de claves asociados a las máquinas virtuales cifradas seleccionadas. El servicio Backup necesita acceso de solo lectura a las claves y los secretos del almacén de claves. Estos permisos los usa para hacer copia de seguridad de ellos, junto con la de las máquinas virtuales asociadas.<br>
Si es un **usuario miembro**, el proceso Habilitar copia de seguridad no tendrá problema acceder al almacén de claves y hacer copias de seguridad de las máquinas virtuales cifradas sin que se requiera la intervención del usuario.

   ![Mensaje de máquinas virtuales cifradas](./media/backup-azure-vms-encryption/member-user-encrypted-vm-warning-message.png)

   En el caso de los **usuarios invitados**, es necesario conceder permisos al servicio Backup para que pueda acceder al almacén de claves y puedan realizarse copias de seguridad. Para proporcionar estos permisos, siga los [pasos que se indican en la siguiente sección](#provide-permissions-to-backup).

   ![Mensaje de máquinas virtuales cifradas](./media/backup-azure-vms-encryption/guest-user-encrypted-vm-warning-message.png)
 
    Ahora que ha definido toda la configuración del almacén, haga clic en **Habilitar Backup** en la parte inferior de la página. **Habilitar Backup** permite implementar la directiva en el almacén y en las máquinas virtuales.
  
8. La siguiente fase de la preparación es instalar el agente de máquina virtual o comprobar que esté instalado. Para ello, siga los pasos que se indican en la [preparación de su entorno para la copia de seguridad](backup-azure-arm-vms-prepare.md).

### <a name="trigger-a-backup-job"></a>Desencadenamiento de un trabajo de copia de seguridad
Siga los pasos mencionados en el artículo [Copia de seguridad de máquinas virtuales de Azure en un almacén de Recovery Services](backup-azure-arm-vms.md) para desencadenar el trabajo de copia de seguridad.

### <a name="continue-backups-of-already-backed-up-vms-with-encryption-enabled"></a>Continuación de las copias de seguridad de máquinas virtuales ya copiadas con el cifrado habilitado  
Si tiene máquinas virtuales cuya copia de seguridad ya se ha realizado en el almacén de Recovery Services y están habilitadas para el cifrado en un momento posterior, debe conceder al servicio Backup permisos de acceso al almacén de claves para que las copias de seguridad puedan continuar. Para proporcionar estos servicios, puede seguir los [pasos de la sección siguiente](#provide-permissions-to-azure-backup). O bien, puede seguir los pasos de PowerShell en la sección "Habilitar Backup" de la [documentación de PowerShell](backup-azure-vms-automation.md). 

## <a name="provide-permissions-to-backup"></a>Otorgamiento de permisos a Backup
Use los pasos siguientes para proporcionar a Backup los permisos de acceso oportunos al almacén de claves y realizar copias de seguridad de máquinas virtuales cifradas.
1. Seleccione **Más servicios** y busque **Almacenes de claves**.

    ![Almacenes de claves](./media/backup-azure-vms-encryption/search-key-vault.png)
    
2. En la lista de almacenes de claves, seleccione el almacén de claves asociado a la máquina virtual cifrada de la que es necesario hacer una copia de seguridad.

     ![Selección del almacén de claves](./media/backup-azure-vms-encryption/select-key-vault.png)
     
3. Seleccione **Directivas de acceso** y luego seleccione **Agregar nueva**.

    ![Agregar nueva](./media/backup-azure-vms-encryption/select-key-vault-access-policy.png)
    
4. Haga clic en **Seleccionar la entidad de seguridad** y escriba **servicio de administración de copias de seguridad** en el cuadro de búsqueda. 

    ![Búsqueda del servicio Backup](./media/backup-azure-vms-encryption/search-backup-service.png)
    
5. Seleccione **Backup Management Service** (Servicio de administración de copias de seguridad) y haga clic en el botón **Seleccionar**.

    ![Selección del servicio Backup](./media/backup-azure-vms-encryption/select-backup-service.png)
    
6. En **Configurar a partir de una plantilla (opcional)**, seleccione **Azure Backup**. Los permisos necesarios se rellenan previamente en **Permisos clave** y **Permisos de secretos**. Si la máquina virtual se cifra con **solo BEK**, únicamente se necesitan permisos para los secretos, por lo que debe eliminar la selección de los **permisos de clave**.

    ![Selección de Azure Backup](./media/backup-azure-vms-encryption/select-backup-template.png)
    
7. Seleccione **Aceptar**. Tenga en cuenta que **Backup Management Service** se agrega en **Directivas de acceso**. 

    ![Directivas de acceso](./media/backup-azure-vms-encryption/backup-service-access-policy.png)
    
8. Seleccione **Guardar** para conceder los permisos necesarios a Backup.

    ![Directiva de acceso de Backup](./media/backup-azure-vms-encryption/save-access-policy.png)

Una vez que los permisos se han proporcionado correctamente, puede continuar con la habilitación de la copia de seguridad para máquinas virtuales cifradas.

## <a name="restore-an-encrypted-vm"></a>Restauración de una máquina virtual cifrada
Para restaurar una máquina virtual cifrada, primero restaure los discos; para ello, siga los pasos de la sección "Restauración de discos de copia de seguridad" de [Elección de una configuración de restauración para una máquina virtual](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Después de eso, puede usar una de las siguientes opciones:

* Siga los pasos de PowerShell que se indican en [Creación de una máquina virtual a partir de discos restaurados](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) para crear una máquina virtual completa a partir de discos restaurados.
* O bien, [use plantillas para personalizar una máquina virtual restaurada](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm) para crear máquinas virtuales a partir de discos restaurados. Solo se pueden usar plantillas para los puntos de recuperación creados después del 26 de abril de 2017.

## <a name="troubleshooting-errors"></a>Solución de errores
| Operación | Detalles del error | Resolución |
| --- | --- | --- |
|Backup | Backup no tiene suficientes permisos para el almacén de claves y no se puede realizar la copia de seguridad de las máquinas virtuales cifradas. | Es necesario proporcionar estos permisos a Backup mediante los [pasos de la sección anterior](#provide-permissions-to-azure-backup). También puede seguir los pasos de PowerShell que se describen en la sección "Habilitar protección" de la documentación de PowerShell en [Uso de los cmdlets AzureRM.RecoveryServices.Backup para realizar copias de seguridad de máquinas virtuales](backup-azure-vms-automation.md#back-up-azure-vms). |  
| Restore |No se puede restaurar esta máquina virtual cifrada porque no existe el almacén de claves asociado con esta máquina virtual. |Cree un almacén de claves mediante los pasos descritos en [Introducción a Azure Key Vault](../key-vault/key-vault-get-started.md). Consulte [Restauración de la clave y el secreto de Key Vault para máquinas virtuales cifradas mediante Azure Backup](backup-azure-restore-key-secret.md) para restaurar la clave y el secreto si estos no existen. |
| Restore |No se puede restaurar esta máquina virtual cifrada porque la clave y el secreto asociados con ella no existen. |Consulte [Restauración de la clave y el secreto de Key Vault para máquinas virtuales cifradas mediante Azure Backup](backup-azure-restore-key-secret.md) para restaurar la clave y el secreto si estos no existen. |
| Restore |El servicio Backup no tiene autorización para acceder a los recursos de su suscripción. |Como se ha mencionado anteriormente, restaure primero los discos según los pasos descritos en la sección "Restauración de discos de copia de seguridad" de [Elección de una configuración de restauración para una máquina virtual](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Después, use PowerShell para [crear una máquina virtual a partir de discos restaurados](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). |

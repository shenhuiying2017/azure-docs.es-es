---
title: "Preguntas más frecuentes sobre la migración en Azure Storage | Microsoft Docs"
description: "Respuestas a preguntas habituales sobre la migración en Azure Storage"
services: storage
documentationcenter: na
author: genlin
manager: timlt
editor: tysonn
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 11/16/2017
ms.author: genli
ms.openlocfilehash: 516a0487afe11ef6915a002375661a23eaf13edc
ms.sourcegitcommit: 21a58a43ceceaefb4cd46c29180a629429bfcf76
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/27/2017
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Preguntas más frecuentes sobre la migración en Azure Storage

En este artículo se responden a preguntas habituales sobre la migración en Azure Storage. 

## <a name="faq"></a>P+F

**¿Cómo puedo crear un script para copiar archivos de un contenedor a otro?**

Para copiar archivos entre contenedores, puede utilizar AzCopy. Consulte el ejemplo siguiente:

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

AzCopy utiliza [API Copy Blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) para realizar la copia de cada archivo del contenedor.  
  
Puede usar cualquier máquina virtual o local que tenga acceso a Internet para ejecutar AzCopy. También puede utilizar el servicio Programación por lotes de Azure para hacerlo automáticamente, pero es más complicado.  
  
El script de Automation se ha diseñado para la implementación de Azure Resource Manager, en lugar de para la manipulación de contenido de almacenamiento. Para más información, consulte [Implementación de recursos con plantillas de Resource Manager y Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**¿Se aplica algún cargo por copiar datos entre dos recursos compartidos de archivos distintos en la misma cuenta de almacenamiento de la misma región?**

No. No se aplica ningún cargo por este proceso.

**¿Cómo puedo realizar una copia de seguridad de toda mi cuenta de almacenamiento en otra diferente?**

No hay ninguna opción para hacer directamente una copia de seguridad de toda una cuenta de almacenamiento. Sin embargo, puede mover manualmente el contenedor de esa cuenta de almacenamiento a otra con AzCopy o el Explorador de Storage. Los pasos siguientes muestran cómo utilizar AzCopy para mover el contenedor:  
 

1.  Instale la herramienta de línea de comandos [AzCopy](storage-use-azcopy.md). Esta herramienta lo ayuda a mover el archivo VHD entre cuentas de almacenamiento.

2.  Después de instalar AzCopy en Windows mediante el programa de instalación, abra una ventana del símbolo del sistema y, luego, navegue hasta la carpeta de instalación de AzCopy en el equipo. De forma predeterminada, AzCopy se instala en **%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy** o **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy**.

3.   Ejecute el siguiente comando para mover el contenedor. Debe reemplazar el texto con el valor real.   
     
            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

        - /source: proporcione el URI de la cuenta de almacenamiento de origen (hasta el contenedor).  
        - /dest: proporcione el URI de la cuenta de almacenamiento de destino (hasta el contenedor).  
        - /sourcekey: proporcione la clave principal de la cuenta de almacenamiento de origen; puede copiarla desde el portal seleccionando la cuenta de almacenamiento.  
        - /destkey: proporcione la clave principal de la cuenta de almacenamiento de destino; puede copiarla desde el portal seleccionando la cuenta de almacenamiento.

Después de ejecutar este comando, los archivos del contenedor se mueven a la cuenta de almacenamiento de destino.

**La CLI de AzCopy no funciona con el modificador de patrón (Pattern) al copiar desde un blob de Azure a otro.**

Puede copiar y editar directamente el comando de AzCopy, así como realizar una comprobación para asegurarse de que el patrón coincide con el origen. Asegúrese también de que se apliquen los caracteres comodín **/S**. Para obtener más información, consulte el artículo sobre los [parámetros de AzCopy](storage-use-azcopy.md).

**¿Cómo puedo mover datos de un contenedor de almacenamiento a otro?**

Para ello, siga estos pasos.

1.  Cree el contenedor (carpeta) en el blob de destino.

2.  Use [AzCopy](https://azure.microsoft.com/en-us/blog/azcopy-5-1-release/) para copiar el contenido del contenedor de blobs original a otro.

**¿Cómo puedo crear un script de PowerShell para mover datos de un recurso compartido de Azure Files a otro almacenamiento de Azure?**

Use AzCopy para mover los datos de un recurso compartido de Azure Files a otro almacenamiento de Azure. Para obtener más información, consulte [Transferencia de datos con AzCopy en Windows](storage-use-azcopy.md) y [Transferencia de datos con AzCopy en Linux](storage-use-azcopy-linux.md).

**¿Cómo puedo cargar archivos .csv grandes en Azure Storage?**

Use AzCopy para cargar archivos .csv grandes en Azure Storage. Para obtener más información, consulte [Transferencia de datos con AzCopy en Windows](storage-use-azcopy.md) y [Transferencia de datos con AzCopy en Linux](storage-use-azcopy-linux.md).

**Tengo que mover registros de la unidad D a mi cuenta de almacenamiento de Azure todos los días. ¿Cómo automatizo este proceso?**

Puede utilizar AzCopy y crear una tarea en el Programador de tareas. Cargue los archivos en una cuenta de almacenamiento de Azure mediante un script por lotes de AzCopy. Para más información, consulte [Configuración y ejecución de tareas de inicio para un servicio en la nube](../../cloud-services/cloud-services-startup-tasks.md).

**¿Cómo puedo mover mi cuenta de almacenamiento entre suscripciones?**

Use AzCopy para mover una cuenta de almacenamiento entre suscripciones. Para obtener más información, consulte [Transferencia de datos con AzCopy en Windows](storage-use-azcopy.md) y [Transferencia de datos con AzCopy en Linux](storage-use-azcopy-linux.md).

**¿Cómo puedo mover unos 10 TB de datos en el almacenamiento de otra región?**

Use AzCopy para mover los datos. Para obtener más información, consulte [Transferencia de datos con AzCopy en Windows](storage-use-azcopy.md) y [Transferencia de datos con AzCopy en Linux](storage-use-azcopy-linux.md).

**¿Cómo puedo copiar datos de un entorno local en Azure Storage?**

Use AzCopy para copiar los datos. Para obtener más información, consulte [Transferencia de datos con AzCopy en Windows](storage-use-azcopy.md) y [Transferencia de datos con AzCopy en Linux](storage-use-azcopy-linux.md).

**¿Cómo puedo mover datos de un entorno local al servicio Azure Files?**

Use AzCopy para mover los datos. Para obtener más información, consulte [Transferencia de datos con AzCopy en Windows](storage-use-azcopy.md) y [Transferencia de datos con AzCopy en Linux](storage-use-azcopy-linux.md).

**¿Cómo puedo asignar una carpeta de contenedor en una máquina virtual?**

Use un recurso compartido de Azure Files.

**¿Cómo puedo realizar una copia de seguridad de mi almacenamiento de Azure Files?**

No hay ninguna solución de copia de seguridad. Sin embargo, Azure Files también admite la copia asincrónica. Por lo tanto, puede copiar archivos de un recurso compartido a otro (en una cuenta de almacenamiento o en una cuenta de almacenamiento diferente) o en un contenedor de blobs (en una cuenta de almacenamiento o en una cuenta de almacenamiento diferente).
Para obtener más información, consulte [Transferencia de datos con AzCopy en Windows](storage-use-azcopy.md).

**¿Cómo puedo mover discos administrados a otra cuenta de almacenamiento?**

Para ello, siga estos pasos.

1.  Detenga la máquina virtual a la que está conectado el disco administrado.

2.  Copie el disco duro virtual del disco administrado de un área a otra ejecutando el siguiente script de Azure PowerShell:

    ```
    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionId <ID>

    $sas = Grant-AzureRmDiskAccess -ResourceGroupName <RG name> -DiskName <Disk name> -DurationInSecond 3600 -Access Read

    $destContext = New-AzureStorageContext –StorageAccountName contosostorageav1 -StorageAccountKey <your account key>

    Start-AzureStorageBlobCopy -AbsoluteUri $sas.AccessSAS -DestContainer 'vhds' -DestContext $destContext -DestBlob 'MyDestinationBlobName.vhd'
    ```

3.  Cree un disco administrado mediante el archivo VHD en otra región en la que copió el VHD. Puede hacerlo ejecutando el siguiente script de Azure PowerShell:  

    ```
    $resourceGroupName = 'MDDemo'

    $diskName = 'contoso\_os\_disk1'

    $vhdUri = 'https://contoso.storageaccou.com.vhd

    $storageId = '/subscriptions/<ID>/resourceGroups/<RG name>/providers/Microsoft.Storage/storageAccounts/contosostorageaccount1'

    $location = 'westus'

    $storageType = 'StandardLRS'

    $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location $location -CreateOption Import -SourceUri $vhdUri -StorageAccountId $storageId -DiskSizeGB 128

    $osDisk = New-AzureRmDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
    ``` 

Para obtener más información sobre cómo implementar una máquina virtual de un disco administrado, consulte [CreateVmFromManagedOsDisk.ps1](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/blob/master/CreateVmFromManagedOsDisk.ps1).

**¿Cómo puedo descargar entre 1 y 2 TB aprox. de datos desde Azure Portal?**

Use AzCopy para descargar los datos. Para obtener más información, consulte [Transferencia de datos con AzCopy en Windows](storage-use-azcopy.md) y [Transferencia de datos con AzCopy en Linux](storage-use-azcopy-linux.md).

**¿Cómo puedo cambiar la ubicación secundaria a la región Europa en una cuenta de almacenamiento?**

Cuando crea una cuenta de almacenamiento, selecciona la región principal de la cuenta. La selección de la región secundaria se determina según la región primaria y no es posible cambiarla. Consulte el artículo sobre [replicación de Azure Storage](storage-redundancy.md).

**¿Dónde puedo obtener más información sobre el cifrado del servicio Azure Storage (SSE)?**  
  
Consulte los artículos siguientes:

-  [Guía de seguridad de Azure Storage](storage-security-guide.md)

-   [Cifrado del servicio Azure Storage para datos en reposo (versión preliminar)](storage-service-encryption.md)

**¿Cómo puedo mover o descargar datos de una cuenta de almacenamiento?**

Use AzCopy para descargar los datos. Para obtener más información, consulte [Transferencia de datos con AzCopy en Windows](storage-use-azcopy.md) y [Transferencia de datos con AzCopy en Linux](storage-use-azcopy-linux.md).


**¿Cómo puedo cifrar datos en una cuenta de almacenamiento?**

Después de habilitar el cifrado en la cuenta de almacenamiento, no se cifran los datos existentes. Para cifrar los datos existentes, debe volver a cargar los datos en la cuenta de almacenamiento.  Para ello, siga estos pasos.

Use AzCopy para copiar los datos en una cuenta de almacenamiento diferente y, luego, vuelva a una cuenta de almacenamiento. También puede usar [Cifrado en reposo](storage-service-encryption.md).

**¿Cómo puedo descargar un disco duro virtual en una máquina local sin utilizar la opción de descarga del portal?**

Puede usar el [Explorador de Storage](https://azure.microsoft.com/features/storage-explorer/) para descargar un disco duro virtual.

**¿Hay requisitos previos para cambiar la replicación de una cuenta de almacenamiento de GRS a LRS?**

No. 

**¿Cómo puedo acceder al almacenamiento redundante de Azure Files?**

El almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS) es necesario para acceder al almacenamiento redundante. Sin embargo, Azure Files solo admite LRS y GRS estándar, que no permite el acceso de solo lectura. 

**¿Cómo puedo mover archivos de Premium Storage a Standard Storage?**

Para ello, siga estos pasos.

1.  Puede crear una nueva cuenta de Standard Storage (o usar una cuenta de Standard Storage de su suscripción).

2.  Descargue AzCopy. Ejecute uno de los siguientes comandos de AzCopy.
      
    Para copiar discos completos en la cuenta de almacenamiento, siga estos pasos:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S 

    Para copiar un solo disco, proporcione el nombre del disco después de Pattern:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

   
El proceso puede durar varias horas.

Para asegurarse de que la transferencia se completó correctamente, examine el contenedor de la cuenta de almacenamiento de destino en Azure Portal. Después de que los discos se copien en la cuenta de almacenamiento estándar, puede conectarlos a la máquina virtual como un disco existente. Para obtener más información, consulte [Cómo conectar un disco de datos administrado a una VM con Windows en Azure Portal](../../virtual-machines/windows/attach-managed-disk-portal.md).  
  
**¿Cómo puedo convertir un recurso compartido de archivos para usarse en Premium Storage?**

Premium Storage no se permite en un recurso compartido de Azure Files.

**¿Cómo puedo actualizar de una cuenta de Standard Storage a una de Premium Storage? ¿Cómo puedo cambiar de una cuenta de Premium Storage a una de Standard Storage?**

- Debe crear la cuenta de almacenamiento de destino, copiar los datos de la cuenta de origen en la de destino y, luego, eliminar la cuenta de origen.

- Puede usar una herramienta como AzCopy para realizar la copia de datos.

- Si también tiene máquinas virtuales, hay que realizar varios pasos adicionales antes de migrar los datos de la cuenta de almacenamiento. Para obtener más información, consulte [Migración a Azure Premium Storage (discos no administrados)](storage-migration-to-premium-storage.md).

**¿Cómo puedo pasar de una cuenta de almacenamiento clásica a una de almacenamiento de Azure Resource Manager?**

1.  Puede usar el cmdlet Move-AzureStorageAccount.

2.  Este cmdlet tiene varios pasos (validación, preparación y confirmación) y puede validar el cambio antes de efectuarlo.

3.  Si también tiene máquinas virtuales, hay que realizar varios pasos más antes de migrar los datos de la cuenta de almacenamiento. Para obtener más información, consulte [Migración de recursos de IaaS de la implementación clásica a la de Resource Manager con Azure PowerShell](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md).

**¿Cómo puedo descargar datos en un equipo Linux desde una cuenta de almacenamiento de Azure o cargar datos de una máquina Linux?**

Puede usar la CLI de Azure.

-   Descarga de un solo blob

        azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

-   Carga de un solo blob: 

        azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**¿Cómo puedo permitir que otras personas accedan a mis recursos de almacenamiento?**

Para permitir que otras personas accedan a los recursos de almacenamiento, siga estos pasos:

-   Use un token de firma de acceso compartido (SAS) para proporcionar acceso a un recurso. 

-   Proporcione a un usuario la clave principal o secundaria de la cuenta de almacenamiento. Para obtener más información, consulte la sección [Administración de la cuenta de almacenamiento](storage-create-storage-account.md#manage-your-storage-account).

-   Cambie la directiva de acceso para permitir el acceso anónimo. Para obtener más información, consulte [Concesión de permisos a usuarios anónimos a contenedores y blobs](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs).

**¿Dónde se instala AzCopy?**

-   Si accede a AzCopy desde la línea de comandos de Microsoft Azure Storage, escriba "AzCopy". La línea de comandos se instala junto a AzCopy.

-   Si ha instalado la versión de 32 bits, estará aquí: **%ProgramFiles(x86)%\\Microsoft SDKs\\Azure\\AzCopy**.

-   Si ha instalado la versión de 64 bits, estará aquí: **%ProgramFiles%\\Microsoft SDKs\\Azure\\AzCopy**.

**Con una cuenta de almacenamiento replicado (por ejemplo, ZRS, GRS o RA-GRS), ¿cómo puedo acceder a los datos que se almacenan en la región secundaria?**

-   Si usa el almacenamiento con redundancia de zona (ZRS) o el almacenamiento con redundancia geográfica (GRS), no puede acceder a datos de la región secundaria, salvo que se produzca una conmutación por error. Para obtener más información sobre el proceso de conmutación por error, consulte [Qué esperar si se produce una conmutación por error de almacenamiento](storage-disaster-recovery-guidance.md#what-to-expect-if-a-storage-failover-occurs).

-   Si usa el almacenamiento con redundancia geográfica con acceso de lectura (**RA-GRS**), puede acceder a datos de la región secundaria en cualquier momento. Para ello, use uno de los siguientes métodos:  
      
    AzCopy: anexe "-secondary" al nombre de la cuenta de almacenamiento en la dirección URL para tener acceso al punto de conexión secundario. Por ejemplo:  
     
    https://nombrecuentaalmacenamiento-secondary.blob.core.windows.net/vhds/nombreDelBlob.vhd

    Token de SAS: use un token de SAS para acceder a los datos desde el punto de conexión. Para obtener más información, consulte [Uso de firmas de acceso compartido (SAS)](storage-dotnet-shared-access-signature-part-1.md).

**¿Cómo puedo usar un dominio personalizado HTTPS con mi cuenta de almacenamiento? Por ejemplo, ¿cómo puedo conseguir que "https://nombremicuentaalmacenamiento.blob.core.windows.net/images/image.gif" se muestre como "https://www.contoso.com/images/image.gif"?**

SSL no se admite actualmente en las cuentas de almacenamiento con dominios personalizados.
Sin embargo, puede usar dominios personalizados que no sean HTTPS. Para obtener más información, consulte [Configuración de un nombre de dominio personalizado para el punto de conexión de Blob Storage](../blobs/storage-custom-domain-name.md).

**¿Cómo puedo usar FTP para acceder a los datos de una cuenta de almacenamiento?**

No hay ninguna forma de acceder a una cuenta de almacenamiento directamente mediante FTP. Sin embargo, puede configurar una máquina virtual de Azure y, luego, instalar un servidor FTP en la máquina virtual. Puede hacer que el servidor FTP almacene los archivos en un recurso compartido de Azure Files o en un disco de datos disponible de la máquina virtual.
Si solo quiere que se descargan los datos sin tener que usar el Explorador de Storage o una aplicación similar, estos pueden usar un token de SAS. Para obtener más información, consulte [Uso de firmas de acceso compartido (SAS)](storage-dotnet-shared-access-signature-part-1.md).

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.

Si sigue necesitando ayuda, [póngase en contacto con el servicio de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.
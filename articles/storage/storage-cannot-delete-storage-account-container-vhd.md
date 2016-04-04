<properties
	pageTitle="Solución de problemas de eliminación de cuentas de Almacenamiento de Azure, contenedores o discos duros virtuales | Microsoft Azure"
	description="Solución de problemas de eliminación de cuentas de Almacenamiento de Azure, contenedores o discos duros virtuales"
	services="storage"
	documentationCenter=""
	authors="genlin"
	manager="felixwu"
	editor=""
	tags="storage"/>

<tags
	ms.service="storage"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/20/2016"
	ms.author="genli"/>

# Solución de problemas de eliminación de cuentas de Almacenamiento de Azure, contenedores o discos duros virtuales

## Resumen
Puede recibir errores al intentar eliminar la cuenta de Almacenamiento de Azure, el contenedor o el disco duro virtual en el [Portal de Azure](https://portal.azure.com/) o en el [Portal de Azure clásico](https://manage.windowsazure.com/). Estos problemas pueden deberse a:

-	Cuando se elimina una máquina virtual, el disco y el disco duro virtual no se eliminan automáticamente, lo que puede ser el motivo del error en la eliminación de la cuenta de almacenamiento. No eliminamos el disco, por tanto puede usarlo para montar otra máquina virtual.

-	Todavía hay una concesión en un disco o en el blob asociado en el disco.

Si su problema con Azure no se trata en este artículo, visite los foros de Azure en [MSDN y Stack Overflow](https://azure.microsoft.com/support/forums/). Puede registrar su problema en estos foros o en @AzureSupport en Twitter. También puede presentar una solicitud de soporte técnico de Azure, para ello seleccione **Obtener soporte técnico** en el sitio de [Soporte técnico de Azure](https://azure.microsoft.com/support/options/).

## Resolución
Para resolver los problemas más comunes, pruebe el siguiente método:

1. Cambie al [Portal de Azure clásico](https://manage.windowsazure.com/).
2. Seleccione **MÁQUINA VIRTUAL**>**DISCOS**.

	![disk.png](./media/storage-cannot-delete-storage-account-container-vhd/VMUI.png)

3. Busque los discos asociados a la cuenta de almacenamiento, contenedor o disco duro virtual que desea eliminar. Compruebe la ubicación del disco, encontrará la cuenta de almacenamiento asociada, el contenedor y el disco duro virtual.

	![location](./media/storage-cannot-delete-storage-account-container-vhd/DiskLocation.png)

4. Confirme que no existe ninguna máquina virtual en el campo **Conectado a** de los discos y, después, elimine los discos.

 	> [AZURE.NOTE] Si un disco está conectado a una máquina virtual, no podrá eliminarlo. Los discos se desasocian en una máquina virtual eliminada de forma asincrónica, por lo que borrar este campo puede tardar unos minutos después de eliminar la máquina virtual.

5. Seleccione **MÁQUINA VIRTUAL**>**IMÁGENES** y, después, elimine las imágenes que están asociadas a la cuenta de almacenamiento, contenedor o disco duro virtual+.

Después, pruebe a eliminar otra vez la cuenta de almacenamiento, contenedor o disco duro virtual.

> [AZURE.WARNING] Asegúrese de hacer una copia de seguridad de cualquier contenido que desee guardar antes de eliminar la cuenta. No es posible restaurar una cuenta de almacenamiento eliminada ni recuperar el contenido que contenía antes de la eliminación. Esto también se aplica a los recursos de la cuenta: cuando se elimina un disco duro virtual, un blob, una tabla, una cola o un archivo, este se eliminará definitivamente. Asegúrese de que el recurso no está en uso.

## Síntoma

La sección siguiente muestra los errores comunes que puede recibir al intentar eliminar la cuentas de Almacenamiento de Azure, los contenedores o los discos duros virtuales, consulte:

### Escenario 1: No se puede eliminar una cuenta de almacenamiento

Al intentar eliminar una cuenta de almacenamiento que ya no se está utilizando navegando a la cuenta de almacenamiento en el [Portal de Azure](https://portal.azure.com/) o [Portal de Azure clásico](https://manage.windowsazure.com/) y selecciona Eliminar, verá el mensaje de error siguiente:

**En el Portal de Azure**:

*No se puede eliminar la cuenta de almacenamiento <vm-storage-account-name>. No se puede eliminar la cuenta de almacenamiento <vm-storage-account-name>: 'cuenta de almacenamiento <vm-storage-account-name> tiene imágenes o discos activos. Controle que se quiten las imágenes y los discos antes de eliminar esta cuenta de almacenamiento.'.*

**En el Portal de Azure clásico**:

La *cuenta de almacenamiento <vm-storage-account-name> tiene imágenes o discos activos, por ejemplo, xxxxxxxxx-xxxxxxxxx-O-209490240936090599. Controle que se quiten las imágenes y los discos antes de eliminar esta cuenta de almacenamiento.*

También puede ver este error:

**En el Portal de Azure**:

La *cuenta de almacenamiento <vm-storage-account-name> tiene 1 contenedores que tienen una imagen activa y/o artefactos de disco. Compruebe que estos artefactos se quitan del repositorio de imágenes antes de eliminar esta cuenta de almacenamiento*.

**En el Portal de Azure clásico**:

La *cuenta de almacenamiento que no se pudo enviar <vm-storage-account-name> tiene 1 contenedores que tienen una imagen activa y/o artefactos de disco. Compruebe que estos artefactos se quitan del repositorio de imágenes antes de eliminar esta cuenta de almacenamiento. Cuando se intenta eliminar una cuenta de almacenamiento y todavía hay discos activos asociados a ella, verá un mensaje que le indica que hay discos activos que deben eliminarse*.

### Escenario 2: No se puede eliminar un contenedor

Al intentar eliminar el contenedor de almacenamiento, puede ver el error siguiente:

*No se pudo eliminar el contenedor de almacenamiento <container name>. Error: 'Actualmente hay una concesión en el contenedor y no se especificó ningún identificador de concesión en la solicitud*.

### Escenario 3: No se puede eliminar un VHD

Después de eliminar una máquina virtual, intenta eliminar los blobs para los discos duros virtuales asociados y recibe el mensaje siguiente:

*No se pudo eliminar el blob 'path/XXXXXX-XXXXXX-os-1447379084699.vhd'. Error: 'Actualmente hay una concesión en el blob y no se especificó ningún identificador de concesión en la solicitud.*

## Más información

Las máquinas virtuales V1 que se han conservado se mostrarán en el estado desasignado detenido en el [Portal de Azure](https://portal.azure.com/) o en el [Portal de Azure clásico](https://manage.windowsazure.com/).

**Portal de Azure clásico**:

![screenshot1](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo1.png)

**Portal de Azure**:

![screenshot2](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo2.png)

Un estado "Detenido (desasignado)" libera los recursos de proceso como la CPU, memoria y red, pero se conservarán los discos para que el usuario pueda volver a crear rápidamente la máquina virtual si fuera necesario. Estos discos se crean encima de los discos duros virtuales que están respaldados por Almacenamiento de Azure. La cuenta de almacenamiento tiene estos discos duros virtuales y los discos tienen concesiones sobre esos discos duros virtuales.

## Referencias

- [Eliminar una cuenta de almacenamiento](storage-create-storage-account.md#delete-a-storage-account)
- [How to break the locked lease of blob storage in Microsoft Azure (PowerShell)](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492) (Cómo interrumpir la concesión bloqueada de Almacenamiento de blobs en Microsoft Azure (PowerShell))

<!---HONumber=AcomDC_0323_2016-->
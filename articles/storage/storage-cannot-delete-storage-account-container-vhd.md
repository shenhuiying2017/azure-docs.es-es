<properties
	pageTitle="Solución de problemas de eliminación de cuentas de almacenamiento de Azure, contenedores o discos duros virtuales | Microsoft Azure"
	description="Solución de problemas de eliminación de cuentas de Almacenamiento de Azure, contenedores o discos duros virtuales"
	services="storage"
	documentationCenter=""
	authors="genlin"
	manager="felixwu"
	editor="tysonn"
	tags="storage"/>

<tags
	ms.service="storage"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="minet;genli;robinsh"/>

# Solución de problemas de eliminación de cuentas de Almacenamiento de Azure, contenedores o discos duros virtuales

## Resumen
Podría recibir errores al intentar eliminar la cuenta de almacenamiento de Azure, el contenedor o el disco duro virtual en el [Portal de Azure](https://portal.azure.com/) o en el [Portal de Azure clásico](https://manage.windowsazure.com/). Los problemas pueden deberse a las siguientes circunstancias:

-	Al eliminar una VM, el disco y el disco duro virtual no se eliminan automáticamente. Este podría ser el motivo del error en la eliminación de la cuenta de almacenamiento. No eliminamos el disco, por tanto puede usarlo para montar otra máquina virtual.

-	Todavía hay una concesión en un disco o en el blob asociado con el disco.

Si su problema con Azure no se trata en este artículo, visite los foros de Azure en [MSDN y Stack Overflow](https://azure.microsoft.com/support/forums/). Puede registrar su problema en estos foros o en @AzureSupport en Twitter. También puede presentar una solicitud de soporte técnico de Azure, para ello seleccione **Obtener soporte técnico** en el sitio de [Soporte técnico de Azure](https://azure.microsoft.com/support/options/).

## Resolución
Para resolver los problemas más comunes, pruebe el siguiente método:

1. Cambie al [Portal de Azure clásico](https://manage.windowsazure.com/).
2. Seleccione **MÁQUINA VIRTUAL** > **DISCOS**.

	![Imagen de discos en máquinas virtuales en el Portal de Azure clásico.](./media/storage-cannot-delete-storage-account-container-vhd/VMUI.png)

3. Busque los discos asociados a la cuenta de almacenamiento, contenedor o disco duro virtual que quiere eliminar. Cuando compruebe la ubicación del disco, encontrará la cuenta de almacenamiento asociada, el contenedor y el disco duro virtual.

	![Imagen que muestra la información de ubicación de los discos en el Portal de Azure clásico](./media/storage-cannot-delete-storage-account-container-vhd/DiskLocation.png)

4. Confirme que no existe ninguna VM en el campo **Conectado a** de los discos y, después, elimine los discos.

 	> [AZURE.NOTE] Si un disco está conectado a una VM, no podrá eliminarlo. Los discos se desconectan de una máquina virtual eliminada de forma asincrónica. Este campo puede tardar unos minutos en borrarse después de eliminar la VM.

5. Seleccione **MÁQUINA VIRTUAL** > **IMÁGENES** y, después, elimine las imágenes que están asociadas a la cuenta de almacenamiento, contenedor o disco duro virtual.

	Después, trate de eliminar otra vez la cuenta de almacenamiento, contenedor o disco duro virtual.

> [AZURE.WARNING] Asegúrese de hacer una copia de seguridad de cualquier contenido que desee guardar antes de eliminar la cuenta. No es posible restaurar una cuenta de almacenamiento eliminada ni recuperar el contenido que contenía antes de la eliminación. Esto también se aplica a los recursos de la cuenta: cuando se elimina un disco duro virtual, un blob, una tabla, una cola o un archivo, este se eliminará definitivamente. Asegúrese de que el recurso no está en uso.

## Problemas comunes

La sección siguiente muestra los errores comunes que puede recibir al intentar eliminar las cuentas de almacenamiento de Azure, los contenedores o los discos duros virtuales.

### Escenario 1: No se puede eliminar una cuenta de almacenamiento

Cuando navegue hasta la cuenta de almacenamiento en el [Portal de Azure](https://portal.azure.com/) o el [Portal de Azure clásico](https://manage.windowsazure.com/) y seleccione **Eliminar**, verá el mensaje de error siguiente:

**En el Portal de Azure**:

*Error al eliminar la cuenta de almacenamiento <nombre-de-cuenta-de-almacenamiento-de-vm>. No se puede eliminar la cuenta de almacenamiento <nombre-de-cuenta-de-almacenamiento-de-vm>: 'La cuenta de almacenamiento <nombre-de-cuenta-de-almacenamiento-de-vm> tiene algunas imágenes o discos activos. Controle que se quiten las imágenes y los discos antes de eliminar esta cuenta de almacenamiento.'.*

**En el Portal de Azure clásico**:

*La cuenta de almacenamiento <nombre-de-cuenta-de-almacenamiento-de-vm> tiene algunas imágenes o discos activos; por ejemplo, xxxxxxxxx- xxxxxxxxx-O-209490240936090599. Controle que se quiten las imágenes y los discos antes de eliminar esta cuenta de almacenamiento.*

También puede ver este error:

**En el Portal de Azure**:

*La cuenta de almacenamiento <nombre-de-cuenta-de-almacenamiento> tiene 1 contenedor que tiene artefactos de imagen o disco activos. Compruebe que estos artefactos se quitan del repositorio de imágenes antes de eliminar esta cuenta de almacenamiento*.

**En el Portal de Azure clásico**:

*Error en el envío. La cuenta de almacenamiento <nombre-de-cuenta-de-almacenamiento-de-vm> tiene 1 contenedor que tiene artefactos de imagen o disco activos. Compruebe que estos artefactos se quitan del repositorio de imágenes antes de eliminar esta cuenta de almacenamiento. Cuando se intenta eliminar una cuenta de almacenamiento y todavía hay discos activos asociados a ella, verá un mensaje que le indica que hay discos activos que deben eliminarse*.

### Escenario 2: No se puede eliminar un contenedor

Al intentar eliminar el contenedor de almacenamiento, podría ver el error siguiente:

*Error al eliminar el contenedor de almacenamiento <nombre de contenedor>. Error: 'Actualmente hay una concesión en el contenedor y no se especificó ningún identificador de concesión en la solicitud*.

### Escenario 3: No se puede eliminar un VHD

Después de eliminar una VM e intentar eliminar después los blobs de los discos duros virtuales asociados, podría recibir el mensaje siguiente:

*Error al eliminar el blob 'path/XXXXXX-XXXXXX-os-1447379084699.vhd'. Error: 'Actualmente hay una concesión en el blob y no se especificó ningún identificador de concesión en la solicitud.*

## Acerca del estado Detenido (desasignado)

Las VM que se crearon en el modelo de implementación clásica y que se han conservado tendrán el estado **Detenido (desasignado)** tanto en el [Portal de Azure](https://portal.azure.com/) como en el [Portal de Azure clásico](https://manage.windowsazure.com/).

**Portal de Azure clásico**:

![Estado Detenido (desasignado) de VM en el Portal de Azure.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo2.png)


**Portal de Azure**:

![Estado Detenido (desasignado) de VM en el Portal de Azure clásico.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo1.png)

Un estado "Detenido (desasignado)" libera los recursos del equipo, como la CPU, la memoria y la red. Sin embargo, los discos se siguen conservando para que el usuario pueda volver a crear la VM si fuera necesario. Estos discos se crean encima de los discos duros virtuales que están respaldados por Almacenamiento de Azure. La cuenta de almacenamiento tiene estos discos duros virtuales y los discos tienen concesiones sobre esos discos duros virtuales.

## Pasos siguientes

- [Eliminar una cuenta de almacenamiento](storage-create-storage-account.md#delete-a-storage-account)
- [How to break the locked lease of blob storage in Microsoft Azure (PowerShell) (Cómo interrumpir la concesión bloqueada de Almacenamiento de blobs en Microsoft Azure (PowerShell))](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)

<!---HONumber=AcomDC_0921_2016-->
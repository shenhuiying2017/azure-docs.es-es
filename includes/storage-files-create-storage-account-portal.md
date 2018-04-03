---
title: storage-files-create-storage-account-portal
description: Procedimiento para crear una cuenta de almacenamiento de Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 03/28/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 82e141eb4941dbd58be95e19dae186acd18caf94
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/29/2018
---
Una cuenta de almacenamiento es un grupo compartido de almacenamiento en el que puede implementar un recurso compartido de archivos de Azure u otros recursos de almacenamiento como blobs o colas. Una cuenta de almacenamiento puede contener un número ilimitado de recursos compartidos y un recurso compartido puede almacenar un número ilimitado de archivos, hasta los límites de capacidad de la cuenta de almacenamiento.

Para crear una cuenta de almacenamiento:

1. En el menú izquierdo, haga clic en **+** para crear un recurso.
2. Escriba **storage account** en el cuadro de búsqueda (1) y seleccione **Storage account - blob, file, table, queue** (2) (Cuenta de almacenamiento: blob, archivo, tabla, cola) y, a continuación, haga clic en **Crear**.
    ![Captura de pantalla del aspecto que la entrada de la cuenta de almacenamiento debería tener en el cuadro de diálogo de búsqueda de recursos](../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png)

3. En **Nombre**, escriba *mystorageacct* seguido de algunos números aleatorios hasta llegar a la marca de verificación verde que indica que es un nombre único. Un nombre de cuenta de almacenamiento debe estar en minúsculas y ser único globalmente. Anote el nombre de cuenta de almacenamiento ya que se va a usar más adelante. 
4. En **Modelo de implementación**, deje el valor predeterminado de **Resource Manager**. Para más información sobre las diferencias entre Azure Resource Manager y la implementación clásica, consulte [La implementación de Azure Resource Manager frente a la implementación clásica: los modelos de implementación y el estado de los recursos](../articles/azure-resource-manager/resource-manager-deployment-model.md).
5. En **Tipo de cuenta**, seleccione **StorageV2**. Para más información sobre los diferentes tipos de cuentas de almacenamiento, consulte [Opciones de la cuenta de Azure Storage](../articles/storage/common/storage-account-options.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
6. En **Rendimiento**, mantenga el valor predeterminado de **Almacenamiento estándar**. Azure Files actualmente solo admite el almacenamiento estándar; incluso si selecciona el almacenamiento premium, el recurso compartido de archivos se almacena en dicho tipo de almacenamiento.
7. En **Replicación**, seleccione *Almacenamiento con redundancia local (LRS)*. 
8. En **Se requiere transferencia segura** se recomienda seleccionar siempre **Habilitado**. Para más información sobre esta opción, consulte [Requerir transferencia segura en Azure Storage](../articles/storage/common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
9. En **Suscripción**, seleccione la suscripción en la que se creará la cuenta de almacenamiento. Si solo tiene una suscripción, debe ser la predeterminada.
10. En **Grupo de recursos**, seleccione **Crear nuevo** y escriba *myResourceGroup* como nombre.
11. En **Ubicación**: seleccione **Este de EE. UU**.
12. En **Redes virtuales**, deje la opción predeterminada como *Deshabilitada*. 
13. Seleccione **Anclar al panel** para convertir la cuenta de almacenamiento más fáciles de encontrar.
14. Cuando termine, haga clic en **Crear** para iniciar la implementación.
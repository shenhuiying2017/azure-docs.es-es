---
title: storage-files-create-storage-account-portal
description: Creación de una cuenta de almacenamiento en Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 03/28/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: a09e9206b3ecd23a6208572a88c16b149033f123
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
ms.locfileid: "31433342"
---
Una cuenta de almacenamiento es un grupo compartido de almacenamiento en el que puede implementar un recurso compartido de archivos de Azure u otros recursos de almacenamiento como blobs o colas. Una cuenta de almacenamiento puede contener un número ilimitado de recursos compartidos. Un recurso compartido puede almacenar un número ilimitado de archivos, hasta los límites de capacidad de la cuenta de almacenamiento.

Para crear una cuenta de almacenamiento:

1. En el menú de la izquierda, seleccione **+** para crear un recurso.
2. En el cuadro de búsqueda, escriba **cuenta de almacenamiento**, seleccione **Storage account - blob, file, table, queue** (Cuenta de almacenamiento: blob, archivo, tabla, cola) y, luego, seleccione **Crear**.
    ![Captura de pantalla del aspecto que la entrada de la cuenta de almacenamiento debería tener en el cuadro de diálogo de búsqueda de recursos](../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png)

3. En **Nombre**, escriba *mystorageacct* seguido de algunos números aleatorios, hasta que aparezca una marca de verificación verde que indica que es un nombre único. Un nombre de cuenta de almacenamiento debe estar en minúsculas y ser único globalmente. Anote el nombre de la cuenta de almacenamiento. Lo usará más adelante. 
4. En **Modelo de implementación**, deje el valor predeterminado de **Resource Manager**. Para más información sobre las diferencias entre Azure Resource Manager y el modelo de implementación clásica, consulte [Modelos de implementación y estado de los recursos](../articles/azure-resource-manager/resource-manager-deployment-model.md).
5. En **Tipo de cuenta**, seleccione **StorageV2**. Para más información sobre los diferentes tipos de cuentas de almacenamiento, consulte [Cuentas de Azure Storage](../articles/storage/common/storage-account-options.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
6. En **Rendimiento**, mantenga el valor predeterminado de **Almacenamiento estándar**. Azure Files actualmente solo admite el almacenamiento estándar; incluso si selecciona Azure Premium Storage, el recurso compartido de archivos se almacena en dicho tipo de almacenamiento.
7. En **Replicación**, seleccione **Almacenamiento con redundancia local (LRS)**. 
8. En **Se requiere transferencia segura** se recomienda seleccionar siempre **Habilitado**. Para más información sobre esta opción, consulte [Cifrado en tránsito](../articles/storage/common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
9. En **Suscripción**, seleccione la suscripción que se usó para crear la cuenta de almacenamiento. Si solo tiene una suscripción, debe ser la predeterminada.
10. En **Grupo de recursos**, seleccione **Crear nuevo**. Como nombre, escriba *myResourceGroup*.
11. En **Ubicación**: seleccione **Este de EE. UU**.
12. En **Redes virtuales**, deje la opción predeterminada como **Deshabilitada**. 
13. Seleccione **Anclar al panel** para que la cuenta de almacenamiento sea más fácil de encontrar.
14. Cuando termine, haga clic en **Crear** para iniciar la implementación.
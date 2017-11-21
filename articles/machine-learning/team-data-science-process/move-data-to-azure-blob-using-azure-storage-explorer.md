---
title: Movimiento de datos hacia y desde Blob Storage con el Explorador de Azure Storage | Microsoft Docs
description: Mover datos hacia y desde Azure Blob Storage con el Explorador de Azure Storage
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 10bd283f-0875-4c67-af63-6492270b7656
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev
ms.openlocfilehash: 3db06eed16191ef906f2c565d78fd0063cdb2f7b
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2017
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Mover datos hacia y desde Azure Blob Storage con el Explorador de Azure Storage
El Explorador de Azure Storage es una herramienta gratuita de Microsoft que permite trabajar con los datos de Azure Storage en Windows, macOS y Linux. En este tema se describe cómo usarlo para cargar y descargar datos del Almacenamiento de blobs de Azure. La herramienta se puede descargar desde el [Explorador de Microsoft Azure Storage](http://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Si está usando la máquina virtual que se configuró con los scripts ofrecidos por [Máquinas virtuales de ciencia de datos en Azure](virtual-machines.md),el Explorador de Azure Storage ya está instalado en la máquina virtual.
> 
> [!NOTE]
> Para ver una introducción completa a Azure Blob Storage, vea los [Aspectos básicos sobre los blobs de Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) y [Azure Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx).   
> 
> 

## <a name="prerequisites"></a>Requisitos previos
En este documento se supone que tiene una suscripción de Azure y una cuenta de almacenamiento y la clave de almacenamiento correspondiente para dicha cuenta. Antes de cargar o descargar datos, debe conocer su nombre de cuenta de almacenamiento de Azure y la clave de cuenta. 

* Para configurar una suscripción a Azure, consulte [Prueba gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/).
* Para obtener instrucciones sobre la creación de una cuenta de almacenamiento y para obtener información de cuentas y claves, vea [Acerca de las cuentas de almacenamiento de Azure](../../storage/common/storage-create-storage-account.md). Anote la clave de acceso de la cuenta de almacenamiento, ya que la necesita para conectarse a la cuenta con la herramienta Explorador de Azure Storage.
* La herramienta Explorador de Azure Storage puede descargarse desde [aquí](http://storageexplorer.com/). Acepte los valores predeterminados durante la instalación.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Usar el Explorador de Azure Storage
En los pasos siguientes se describe cómo cargar y descargar datos mediante el Explorador de Azure Storage. 

1. Inicie Explorador de Microsoft Azure Storage.
2. Para que aparezca el Asistente **Iniciar sesión en una cuenta...**, seleccione el icono de **Configuración de la cuenta de Azure**, luego **Agregar una cuenta** y escriba las credenciales. ![Agregar una cuenta de Azure Storage](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Para que aparezca el Asistente **Conectar a Azure Storage**, seleccione el icono de **Conectar a Azure Storage**. ![Conectar a Azure Storage](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Escriba la clave de acceso de la cuenta de Azure Storage en el Asistente **Conectar a Azure Storage** y luego haga clic en **Siguiente**. ![Conectar a Azure Storage](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Escriba el nombre de la cuenta de almacenamiento en el cuadro **Nombre de cuenta** y, después, seleccione **Siguiente**. ![Adjuntar almacenamiento externo](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Ahora debería aparecer la cuenta de almacenamiento agregada. Para crear un contenedor de blobs en una cuenta de almacenamiento, haga clic con el botón derecho en el nodo **Contenedores de blob** de esa cuenta, seleccione **Crear contenedor de blobs** y escriba un nombre.
7. Para cargar datos en un contenedor, seleccione el contenedor de destino y haga clic en el botón **Cargar**.![Cuentas de almacenamiento](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Haga clic en **...** a la derecha del cuadro **Archivos**, seleccione uno o varios archivos para cargarlos desde el sistema de archivos y haga clic en **Cargar** para empezar a cargarlos.![Cargar archivos](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Para descargar datos, seleccione el blob en el contenedor correspondiente para descarga y haga clic en **Descargar**. ![Descargar archivos](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)


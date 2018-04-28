---
title: Administración de recursos Azure Data Lake Store en el Explorador Azure Storage
description: Más información para acceder a los datos y recursos de Azure Data Lake Store y administrarlos en el Explorador de Azure Storage
Keywords: Azure Data Lake Store, Azure Storage Explorer
services: Data Lake Store
documentationcenter: ''
author: jejiang
manager: DJ
editor: Jenny Jiang
ms.assetid: ''
ms.service: Data Lake Store
ms.custom: Azure Data Lake Store
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 02/05/2018
ms.author: jejiang
ms.openlocfilehash: 396452faf36bc43a185fd6492f952b7bfd9511cc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="manage-azure-data-lake-store-resources-by-using-storage-explorer"></a>Administración de recursos de Azure Data Lake Store en el Explorador de Storage

[Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) es un servicio de almacenamiento de grandes cantidades de datos no estructurados, como texto o datos binarios. Puede acceder a los datos desde cualquier lugar a través de HTTP o HTTPS. Data Lake Store en el Explorador de Azure Storage le permite acceder a los datos y recursos de Data Lake Storage y administrarlos junto con otras entidades de Azure, como los blobs o las colas. Ahora puede usar la misma herramienta para administrar las diferentes entidades de Azure en un solo lugar.

Otra ventaja es que, para administrar datos de Data Lake Store, no es necesario disponer de permiso de suscripción. En el Explorador de Storage, puede adjuntar la ruta de acceso de Data Lake Store al nodo de **Local and Attached** (locales y conectados) siempre que un usuario conceda el permiso.

## <a name="prerequisites"></a>requisitos previos
Para completar los pasos de este artículo, necesitará lo siguiente:

*   Una suscripción de Azure. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial).
*   Una cuenta de Azure Data Lake Store. Para obtener instrucciones sobre cómo crear una, consulte la [introducción a Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="install-storage-explorer"></a>Instalación del Explorador de Azure Storage

Instale la versión más reciente del Explorador de Azure Storage desde la [página web del producto](https://azure.microsoft.com/features/storage-explorer/). La instalación admite las versiones de Windows, Linux y Mac.

## <a name="connect-to-an-azure-subscription"></a>Conexión a una suscripción de Azure

1. En el Explorador de Storage, seleccione el icono de complemento de la izquierda.
       
   ![Icono de complemento](./media/data-lake-store-in-storage-explorer/plug-in-icon.png)
 
2. Seleccione **Add an Azure Account** (Agregar una cuenta de Azure) y seleccione **Iniciar sesión**.

   ![Cuadro de diálogo "Connect to Azure Storage"](./media/data-lake-store-in-storage-explorer/connect-to-azure-subscription.png)

2. En el cuadro de diálogo **Inicie sesión en su cuenta**, escriba sus credenciales de Azure.

    ![Cuadro de diálogo para iniciar sesión en Azure](./media/data-lake-store-in-storage-explorer/sign-in.png)

3. Seleccione la suscripción en la lista y luego seleccione **Aplicar**.

    ![Información de suscripción y el botón "Aplicar"](./media/data-lake-store-in-storage-explorer/apply-subscription.png)

    El panel del **Explorador** se actualiza y muestra las cuentas en la suscripción seleccionada.

    ![Lista de cuentas](./media/data-lake-store-in-storage-explorer/account-list.png)

Ha conectado correctamente Azure Data Lake Store a la suscripción de Azure.

## <a name="connect-to-data-lake-store"></a>Conexión al Almacén de Data Lake
Puede tener acceso a recursos que no existen en su suscripción si alguien le proporciona el URI para los recursos. Después, puede conectar a Data Lake Store con el URI una vez iniciada la sesión.
1. Abra el Explorador de Storage.
2. En el panel izquierdo, expanda **Local and Attached** (Local y asociada).
3. Haga clic con el botón derecho en **Data Lake Store** y seleccione **Connect to Data Lake Store** (Conectar a Data Lake Store).

      !["Connect to Data Lake Store" (Conectar a Data Lake Store) en el menú contextual](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Escriba el URI. La herramienta se desplaza a la ubicación de la dirección URL que acaba de escribir.

      ![Cuadro de diálogo "Connect to Data Lake Store" (Conectar a Data Lake Store), con el cuadro de texto para escribir el identificador URI](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

      ![Resultado de conectar a Data Lake Store](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="view-an-azure-data-lake-store-accounts-contents"></a>Visualización del contenido de la cuenta Azure Data Lake Store
Los recursos de una cuenta de Azure Data Lake Store contienen archivos y carpetas.

Los siguientes pasos muestran cómo ver el contenido de una cuenta de Data Lake Store en el Explorador de Storage:

1. Abra el Explorador de Storage.
2. En el panel izquierdo, expanda la suscripción que contenga la cuenta de Azure Data Lake Store que desee ver.
3. Expanda **Data Lake Store**.
4. Haga clic con el botón derecho en el nodo de la cuenta de Azure Data Lake Store que desee ver y seleccione **Open** (Abrir). Para abrir la cuenta de Data Lake Store también puede hacer doble clic en ella. 
   
   El panel principal muestra el contenido de la cuenta de Data Lake Store.

   ![Panel principal con una lista de carpetas](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-toolbar-mainpane.png) 

## <a name="manage-resources-in-azure-data-lake-store"></a>Administración de recursos en Azure Data Lake Store

A continuación se muestran las operaciones de administración de recursos de Azure Data Lake Store:
*   Examinar los recursos de Data Lake Store entre varias cuentas de Azure Data Lake.  
*   Usar una cadena de conexión para conectarse a Data Lake Store y realizar la administración directamente. 
*   Ver los recursos de Data Lake Store compartidos por otros usuarios mediante la lista de control de acceso de **Local and Attached** (Locales y conectados).
*   Realizar operaciones CRUD de archivo y carpeta: admite archivos de selección múltiple y carpetas recursivas. 
*   Arrastrar, colocar y agregar una carpeta para tener acceso rápidamente a ubicaciones recientes. Esta operación refleja la experiencia del Explorador de archivos del escritorio. 
*   Copiar y abrir el hipervínculo de Azure Data Lake en el Explorador de Storage con un solo clic. 
*   Mostrar el registro de actividad en el panel inferior derecho para ver el estado de actividad.
*   Mostrar las estadísticas de las carpetas y las propiedades de los archivos.

## <a name="manage-resources-in-azure-storage-explorer"></a>Administración de recursos en el Explorador de Azure Storage
Tras crear una cuenta de Azure Data Lake Store, puede:

* Cargar archivos y carpetas, descargarlos y abrir recursos en el equipo local.
* Anclar a **Acceso rápido**, crear una nueva carpeta, copiar una dirección URL y seleccionar todo.
* Copiar y pegar, cambiar el nombre, eliminar, obtener las estadísticas de la carpeta y actualizar.

Los elementos siguientes muestran cómo administrar los recursos de una cuenta de Azure Data Lake Store. Siga los pasos de la tarea que desea realizar.

### <a name="upload-files"></a>Carga de archivos

1. En la barra de herramientas del panel principal, seleccione **Upload** (Cargar) y **Upload Files** (Cargar archivos) en el menú desplegable.

   ![Elemento del menú "Upload Files"](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-menu.png) 

2. En el cuadro de diálogo **Select files to upload** (Seleccionar archivos para cargar), seleccione los archivos que desea cargar.

   ![Cuadro de diálogo para cargar archivos](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-dialog.png)

3. Seleccione **Open** (Abrir) para iniciar la carga.

### <a name="upload-a-folder"></a>Carga de una carpeta

1. En la barra de herramientas del panel principal, seleccione **Upload** (Cargar) y **Upload Folder** (Cargar carpeta) en el menú desplegable.

   ![Elemento del menú "Upload Folder"](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-menu.png) 
     
2. En el cuadro de diálogo **Select folder to upload** (Seleccionar carpeta para cargar), seleccione la carpeta que desea cargar. Después, haga clic en **Select Folder** (Seleccionar carpeta).

   ![Cuadro de diálogo para cargar carpetas](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-dialog.png)      

   Se inicia la carga.

   ![Cuadro de diálogo con la carga en curso](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-drag.png) 

> [!NOTE] 
> Puede arrastrar directamente las carpetas y los archivos a un equipo local para comenzar la carga. 
       
### <a name="download-folders-or-files-to-your-local-computer"></a>Descarga de archivos o carpetas en el equipo local

1. Seleccione las carpetas o archivos que desea descargar.
2. En la barra de herramientas del panel principal, seleccione **Descargar**.
3. En el cuadro de diálogo **Select a folder to save the downloaded files into** (Seleccionar una carpeta para guardar los archivos descargados), especifique la ubicación y el nombre.
4. Seleccione **Guardar**.

### <a name="open-a-folder-or-file-from-your-local-computer"></a>Apertura de un archivo o una carpeta desde el equipo local

1. Seleccione el archivo o la carpeta que desea abrir.
2. En la barra de herramientas del panel principal, seleccione **Abrir**. O haga clic con el botón derecho en la carpeta o el archivo seleccionado, y, a continuación, seleccione **Abrir** en el menú contextual.

El archivo se descargará y se abrirá mediante la aplicación asociada al tipo de archivo subyacente. O bien, se abrirá la carpeta en el panel principal.

![Archivo abierto](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-open.png) 

### <a name="copy-folders-or-files-to-the-clipboard"></a>Copia de carpetas o archivos en el Portapapeles

1. Seleccione las carpetas o archivos que desea copiar.
2. En la barra de herramientas del panel principal, seleccione **Copiar**. O haga clic con el botón derecho en las carpetas o archivos seleccionados, y, a continuación, seleccione **Copiar** en el menú contextual.
3. En el panel izquierdo, vaya a otra cuenta de Data Lake Store y haga doble clic en ella para verla en el panel principal.
4. En la barra de herramientas del panel principal, seleccione **Paste** (Pegar) para crear una copia. O bien, seleccione **Pegar** en el menú contextual del destino.

![Selecciones para copiar una carpeta](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-copy-paste.png)

> [!NOTE] 
> No se admiten las operaciones de copiar y pegar entre tipos de almacenamiento. Puede copiar los archivos o carpetas de Data Lake Store y pegarlos en otra cuenta de Data Lake Store. Pero *no puede* copiar archivos o carpetas de Data Lake Store y pegarlos en Azure Blob Storage o viceversa.
> 
> La operación de copiar y pegar consiste en descargar las carpetas o los archivos en el equipo local y después cargarlos en el destino. La herramienta *no* realiza la acción en el back-end. La operación de copiar y pegar archivos de gran tamaño es lenta. Se está llevando a cabo la optimización del movimiento o la copia de archivos de alto rendimiento.

### <a name="delete-folders-or-files"></a>Eliminación de archivos o carpetas

1. Seleccione las carpetas o archivos que desee eliminar.
2. En la barra de herramientas del panel principal, seleccione **Eliminar**. O haga clic con el botón derecho en las carpetas o archivos seleccionados, y, a continuación, seleccione **Eliminar** en el menú contextual.
3. Seleccione **Sí** en el cuadro de diálogo de confirmación.

![Selecciones para eliminar una carpeta](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-delete.png)

### <a name="pin-to-quick-access"></a>Anclar a acceso rápido

1. Seleccione la carpeta que desee anclar.
2. En la barra de herramientas del panel principal, seleccione **Pin to Quick access** (Anclar a Acceso rápido).

   En el panel izquierdo, la carpeta seleccionada se agrega al nodo **Quick Access** (Acceso rápido).

   ![Selecciones para anclar una carpeta a Quick Access"](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-quick-access.png)

Después de anclar una carpeta al nodo de **Quick Access**, puede acceder fácilmente a los recursos.

### <a name="use-deep-links"></a>Uso de vínculos profundos
Si tiene una dirección URL, puede escribirla en la ruta de acceso de dirección del Explorador de archivos o un explorador. A continuación, Explorador de Storage.exe se ejecuta automáticamente para ir a la ubicación de la dirección URL.

![Vínculo profundo en el Explorador de archivos](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-deep-link.png)


## <a name="next-steps"></a>Pasos siguientes
* Vea las [notas de la versión y los vídeos más recientes del Explorador de Storage](http://www.storageexplorer.com).
* Aprenda a [administrar Azure Cosmos DB en el Explorador de Azure Storage](https://docs.microsoft.com/azure/cosmos-db/storage-explorer).
* [Introducción al Explorador de Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).
* [Introducción a Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview).
* Vea el [vídeo de YouTube acerca de cómo usar Azure Cosmos DB en el Explorador de Azure Storage](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).

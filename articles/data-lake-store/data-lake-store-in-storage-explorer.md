---
title: "Administración de recursos Azure Data Lake Store en el Explorador Azure Storage"
description: Permite a los usuarios acceder a los datos y recursos de ADLS y administrarlos en el Explorador de Azure Storage
Keywords: Azure Data Lake Store, Azure Storage Explorer
services: Data Lake Store
documentationcenter: 
author: jejiang
manager: DJ
editor: Jenny Jiang
ms.assetid: 
ms.service: Data Lake Store
ms.custom: Azure Data Lake Store
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 02/05/2018
ms.author: jejiang
ms.openlocfilehash: 4c69acebceac6719cf3f12ff0a93879f1b5a8943
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="manage-azure-data-lake-store-resources-with-storage-explorer-preview"></a>Administración de recursos de Azure Data Lake Store con el Explorador de Storage (versión preliminar)

[Azure Data Lake Store (ADLS)](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-overview) es un servicio de almacenamiento para grandes cantidades de datos no estructurados, como texto o datos binarios. El usuario puede acceder a los datos desde cualquier lugar a través de HTTP o HTTPS. ADLS en el Explorador de Azure Storage permite a los usuarios acceder a los datos y recursos de ADLS y administrarlos junto con otras entidades de Azure, como los blobs o las colas. Ahora, los usuarios pueden usar la misma herramienta para administrar las diferentes entidades de Azure en un solo lugar.

Otra ventaja es que para administrar datos de ADLS, los usuarios no necesitan permiso de suscripción. En el Explorador de Storage, los usuarios pueden adjuntar la ruta de acceso a ADLS al nodo "Local and Attached" (Locales y conectados) siempre que otro conceda el permiso.

## <a name="prerequisites"></a>requisitos previos
Para completar los pasos de este artículo, necesitará lo siguiente:

*   Una suscripción de Azure. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial).
*   Una cuenta de Azure Data Lake Store. Para instrucciones sobre cómo crear una, consulte la [introducción a Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="installation"></a>Instalación

Instale los bits más recientes del Explorador de Azure Storage aquí: [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/). Ahora se admite la versión de Windows, Linux y MAC.

## <a name="connect-to-an-azure-subscription"></a>Conexión a una suscripción de Azure

1. Después de instalar el **Explorador de Azure Storage**, haga clic en el icono de **complemento** situado a la izquierda como se muestra en la imagen de abajo.
       
   ![Icono de complemento](./media/data-lake-store-in-storage-explorer/plug-in-icon.png)
 
2. Seleccione **Add an Azure Account** (Agregar una cuenta de Azure) y haga clic en **Iniciar sesión**.

   ![Conectarse a una suscripción de Azure](./media/data-lake-store-in-storage-explorer/connect-to-azure-subscription.png)

2. En el cuadro de diálogo **Inicio de sesión en Microsoft Azure**, seleccione **Iniciar sesión** y luego escriba las credenciales de Azure.

    ![Iniciar sesión](./media/data-lake-store-in-storage-explorer/sign-in.png)

3. Seleccione la suscripción en la lista y luego haga clic en **Aplicar**.

    ![Aplicar](./media/data-lake-store-in-storage-explorer/apply-subscription.png)

    El panel del Explorador se actualiza y muestra las cuentas en la suscripción seleccionada.

    ![Lista de cuentas](./media/data-lake-store-in-storage-explorer/account-list.png)

    Ha conectado correctamente **Azure Data Lake Store** a la suscripción de Azure.

## <a name="connect-to-data-lake-store"></a>Conexión al Almacén de Data Lake
Si desea acceder a recursos que no existen en su suscripción, a cuyos URI le conceden acceso otros usuarios. En este caso, se puede conectar Data Lake Store con el URI una vez iniciada sesión. Consulte los pasos siguientes.
1. Abra el Explorador de Storage (versión preliminar).
2. En el panel izquierdo, expanda **Local and Attached** (Locales y conectados).
3. Haga clic con el botón derecho en **Data Lake Store** y, en el menú contextual, seleccione **Connect to Data Lake Store...** (Conectar a Data Lake Store...).

      ![Menú contextual Connect to Data Lake Store... (Conectar a Data Lake Store...)](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Escriba el URI y la herramienta irá a la ubicación de la dirección URL que acaba de escribir.

      ![Cuadro de diálogo Connect to Data Lake Store... (Conectar a Data Lake Store...)](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

      ![Resultado de Connect to Data Lake Store... (Conectar a Data Lake Store...)](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="view-an-azure-data-lake-store-accounts-contents"></a>Visualización del contenido de la cuenta Azure Data Lake Store
Los recursos de una cuenta de Azure Data Lake Store contienen archivos y carpetas.

Los siguientes pasos muestran cómo ver el contenido de una cuenta de ADLS en el Explorador de Storage (versión preliminar):

1. Abra el Explorador de Storage (versión preliminar).
2. En el panel izquierdo, expanda la suscripción que contiene la cuenta de Azure Data Lake Store que desea ver.
3. Expanda **Data Lake Store**.
4. Haga clic con el botón derecho en el nodo de la cuenta de Azure Data Lake Store que desea ver y (en el menú contextual) seleccione **Open** (Abrir). Para abrir la cuenta de ADLS también puede hacer doble clic en ella. 
5. El panel principal muestra el contenido de la cuenta de ADLS.

     ![panel principal](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-toolbar-mainpane.png) 

## <a name="azure-data-lake-store-resources-management"></a>Administración de recursos de Azure Data Lake Store

A continuación se muestran las operaciones de administración de recursos de Azure Data Lake Store:
*   Ir a recursos de ADLS de varias cuentas de ADLS.  
*   Usar cadenas de conexión para conectarse a ADLS y realizar la administración directamente. 
*   Ver los recursos compartidos de ADLS de otros usuarios mediante la lista de control de acceso de Local and Attached (Locales y conectados).
*   Realizar operaciones CRUD de archivo o carpeta: admite archivos de carpeta repetitiva y multiseleccionados. 
*   Arrastrar, colocar y agregar carpetas de acceso directo y ubicaciones recientes, lo cual refleja la experiencia del explorador de archivos de escritorio. 
*   Copiar y abrir el hipervínculo de ADLS con el Explorador de Storage con un solo clic. 
*   Mostrar el registro de actividad en el panel inferior derecho para ver el estado de actividad.
*   Mostrar las estadísticas de las carpetas y las propiedades de los archivos.

## <a name="manage-resources-in-azure-storage-explorer"></a>Administración de recursos en el Explorador de Azure Storage
Una vez que haya creado una cuenta de Azure Data Lake Store, puede cargar carpetas y archivos, y descargar y abrir recursos en el equipo local. También podrá realizar las siguientes operaciones: Pin to Quick access (Anclar a Acceso rápido), New folder (nueva carpeta), Copy URL (Copiar dirección URL), Select All (Seleccionar todo). Además, también podrá realizar acciones de copia, pega, cambio de nombre, eliminación, estadística de carpeta y actualización.

Los elementos siguientes muestran cómo administrar los recursos de una cuenta de Azure Data Lake Store. Siga estos pasos según la tarea que desea realizar:
   * **Carga de archivos**

     1. En la barra de herramientas del panel principal, seleccione **Upload** (Cargar) y **Upload Files...** (Cargar archivos...) en el menú desplegable.

        ![Menú Cargar archivos](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-menu.png) 

     2. En el cuadro de diálogo **Select files to Upload** (Seleccionar archivos para cargar), seleccione los archivos que desea cargar.

        ![Cuadro de diálogo Upload folder (Cargar carpeta)](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-dialog.png)

     3. Seleccione **Open** (Abrir) para iniciar la carga.
   * **Carga de una carpeta**

     1. En la barra de herramientas del panel principal, seleccione **Upload** (Cargar) y **Upload Folder...** (Cargar carpeta...) en el menú desplegable.

        ![Menú Cargar carpeta](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-menu.png) 
     2. En el cuadro de diálogo **Select folder to Upload** (Seleccionar carpeta para cargar), seleccione la carpeta que desea cargar.

        ![Cuadro de diálogo Upload folder (Cargar carpeta)](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-dialog.png)      

     3. Seleccione **Select Folder** (Seleccionar carpeta) para empezar a cargar carpetas.

        ![Cuadro de diálogo Upload folder (Cargar carpeta)](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-drag.png) 

      > [!NOTE] 
          > Puede arrastrar directamente las carpetas y los archivos al equipo local para implementar la carga. 
       
   * **Descarga de archivos o carpetas en el equipo local**

     1. Seleccione las carpetas o los archivos que desea descargar.
     2. En la barra de herramientas del panel principal, seleccione **Descargar**.
     3. En el cuadro de diálogo **Select a folder to save the downloaded files into** (Seleccionar carpeta para guardar los archivos descargados), especifique la ubicación donde desea que se descarguen las carpetas o los archivos y especifique el nombre que desea asignarle.
     4. Seleccione **Guardar**.
   * **Apertura de un archivo o una carpeta desde el equipo local**

     1. Seleccione el archivo o la carpeta que desea abrir.
     2. En la barra de herramientas del panel principal, seleccione **Open** (Abrir) o haga clic con el botón derecho en la carpeta o el archivo seleccionados y, en el menú contextual, haga clic en **Open** (Abrir).
     3. El archivo se descargará y se abrirá mediante la aplicación asociada con el tipo de archivo subyacente. O bien, se abrirá la carpeta en el panel principal.

        ![archivo abierto](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-open.png) 

   * **Copia de carpetas o archivos en el Portapapeles**

     1. Seleccione las carpetas o los archivos que desea copiar.
     2. En la barra de herramientas del panel principal, seleccione **Copy** (Copiar) o haga clic con el botón derecho en las carpetas o los archivos seleccionados y, en el menú contextual, haga clic en **Copy** (Copiar).
     3. En el panel izquierdo, vaya a otra cuenta de ADLS y haga doble clic en ella para verla en el panel principal.
     4. En la barra de herramientas del panel principal, seleccione **Paste** (Pegar) para crear una copia. Como alternativa, haga clic en el menú contextual **Paste** (Pegar) en el destino.

        ![copiado y pegado de un archivo o una carpeta](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-copy-paste.png)

      > [!NOTE] 
          > + **No** se pueden copiar y pegar elementos de tipos de almacenamiento distintos. Se pueden copiar archivos o carpetas de ADLS y pegarlos en otra cuenta de ADLS. Pero **no** copiar archivos o carpetas de ADLS y pegarlos en Blob Storage o viceversa. 
          > + La operación de copiar y pegar consiste en descargar las carpetas o los archivos en el equipo local y después cargarlos en el destino. La herramienta **no** realiza la acción en el back-end. La operación de copiar y pegar archivos de gran tamaño es lenta. Se está llevando a cabo la optimización del movimiento de copia de archivos de alto rendimiento.
   * **Eliminación de archivos o carpetas**

     1. Seleccione las carpetas o los archivos que desea eliminar.
     2. En la barra de herramientas del panel principal, seleccione **Delete** (Eliminar) o haga clic con el botón derecho en las carpetas o los archivos seleccionados y, en el menú contextual, haga clic en **Delete** (Eliminar).
     3. Seleccione **Yes** (Sí) en el cuadro de diálogo de confirmación.

        ![copiado y pegado de un archivo o una carpeta](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-delete.png)

   * **Anclar a Acceso rápido**

     1. Seleccione la carpeta que desea anclar.
     2. En la barra de herramientas del panel principal, seleccione **Pin to Quick access** (Anclar a Acceso rápido).
     3. En el panel izquierdo, verá que la carpeta seleccionada se agrega al nodo **Quick Access** (Acceso rápido).

        ![anclar a acceso rápido](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-quick-access.png)
     4. Al crear el acceso rápido podrá acceder a los recursos rápidamente.
   * **Vínculos profundos**
     1. Si tiene una dirección URL, solo puede especificarla en la ruta de acceso de dirección del **Explorador de archivos** o el explorador.
     2. A continuación, **Storage Explorer.exe** se inicia automáticamente para ir a la ubicación de la dirección URL que acaba de escribir.

        ![vínculo profundo en el explorador de archivos](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-deep-link.png)


## <a name="next-steps"></a>pasos siguientes
* Ver las [notas de la versión y los vídeos más recientes del Explorador de Storage (versión preliminar)](http://www.storageexplorer.com).
* Más información sobre la [Administración de Azure Cosmos DB en el Explorador de Azure Storage](https://docs.microsoft.com/en-us/azure/cosmos-db/storage-explorer)
* Más información sobre el Explorador de Storage en la [Introducción al Explorador de Storage (versión preliminar)](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Introducción a Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-overview)
* Vídeo de información acerca del [uso de Azure Cosmos DB en el Explorador de Azure Storage](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be)

<properties 
	pageTitle="Mover datos hacia y desde el almacenamiento de blobs de Azure con el Explorador de almacenamiento de Azure | Microsoft Azure" 
	description="Mover datos hacia y desde el almacenamiento de blobs de Azure con el Explorador de almacenamiento de Azure" 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="bradsev" 
	manager="jhubbard" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/31/2016"
	ms.author="bradsev" />

# Mover datos hacia y desde el almacenamiento de blobs de Azure con el Explorador de almacenamiento de Azure

El Explorador de almacenamiento de Azure es una herramienta gratuita de Microsoft que permite trabajar con los datos de Almacenamiento de Azure en Windows, macOS y Linux. En este tema se describe cómo usarlo para cargar y descargar datos del Almacenamiento de blobs de Azure. La herramienta se puede descargar desde el [Explorador de almacenamiento de Microsoft Azure](http://storageexplorer.com/).

A continuación se ofrecen vínculos de orientación sobre las tecnologías que se usan para mover datos hacia o desde el almacenamiento de blobs de Azure:
 
[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]

 
> [AZURE.NOTE] Si está usando la máquina virtual que se configuró con los scripts ofrecidos por [Máquinas virtuales de ciencia de datos en Azure](machine-learning-data-science-virtual-machines.md),el Explorador de almacenamiento de Azure ya está instalado en la máquina virtual.
 
> [AZURE.NOTE] Para ver una introducción completa al almacenamiento de blobs de Azure, consulte [Aspectos básicos del blob de Azure](../storage/storage-dotnet-how-to-use-blobs.md) y [Servicio BLOB de Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).

## Requisitos previos

En este documento se supone que tiene una suscripción de Azure y una cuenta de almacenamiento y la clave de almacenamiento correspondiente para dicha cuenta. Antes de cargar o descargar datos, debe conocer su nombre de cuenta de almacenamiento de Azure y la clave de cuenta.

- Para configurar una suscripción a Azure, consulte [Prueba gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/).
- Para obtener instrucciones sobre la creación de una cuenta de almacenamiento y para obtener información de cuentas y claves, vea [Acerca de las cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md). Anote la clave de acceso de la cuenta de almacenamiento, ya que la necesita para conectarse a la cuenta con la herramienta Explorador de almacenamiento de Azure.
- La herramienta Explorador de almacenamiento de Azure puede descargarse desde [aquí](http://storageexplorer.com/). Acepte los valores predeterminados durante la instalación.


<a id="explorer"></a>
## Usar el Explorador de almacenamiento de Azure 

En los pasos siguientes se describe cómo cargar y descargar datos mediante el Explorador de almacenamiento de Azure.

1.  Inicie Explorador de almacenamiento de Microsoft Azure.
2.  Para que aparezca el Asistente **Iniciar sesión en la cuenta**, seleccione el icono de **Configuración de la cuenta de Azure**, **Agregar una cuenta** y escriba las credenciales. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3.  Para que aparezca el Asistente para **conexión con Almacenamiento de Azure**, seleccione el icono de **conexión con Almacenamiento de Azure**. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Escriba la clave de acceso de la cuenta de almacenamiento de Azure en el Asistente para **conexión con Almacenamiento de Azure** y, luego, haga clic en **Siguiente**. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Escriba el nombre de la cuenta de almacenamiento en el cuadro **Nombre de cuenta** y, después, seleccione **Siguiente**. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Ahora debería aparecer la cuenta de almacenamiento agregada. Para crear un contenedor de blobs en una cuenta de almacenamiento, haga clic con el botón derecho en el nodo **Contenedores de blobs** de esa cuenta, seleccione **Crear contenedor de blobs** y escriba un nombre.
7. Para cargar datos en un contenedor, seleccione el contenedor de destino y haga clic en el botón **Cargar**.![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Haga clic en **...** a la derecha del cuadro **Archivos**, seleccione uno o varios para cargarlos desde el sistema de archivos y haga clic en **Cargar** para empezar a cargarlos.![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
7. Para descargar datos, seleccione el blob en el contenedor correspondiente y haga clic en el botón **Descargar**. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

<!---HONumber=AcomDC_0914_2016-->
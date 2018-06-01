---
title: Descarga de elementos de Marketplace desde Azure | Microsoft Docs
description: El operador en la nube puede descargar elementos de Marketplace desde Azure a una implementación de Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/16/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 69148a0ac9a5761eeee0ab47d83862724583619a
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/17/2018
ms.locfileid: "34258800"
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Descarga de elementos de Marketplace desde Azure a Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Los operadores en la nube pueden descargar elementos de Azure Marketplace y hacer que estén disponibles en Azure Stack. Dichos elementos se pueden elegir en una lista de elementos de Azure Marketplace que se han probados con anterioridad y se sabe que son compatibles con Azure Stack. Con frecuencia se agregan nuevos elementos a esta lista, así que es aconsejable consultar periódicamente si tiene contenido nuevo. 

Hay dos escenarios para conectarse a Azure Marketplace: 

- **Un escenario conectado**: requiere que el entorno de Azure Stack esté conectado a Internet. El portal de Azure Stack se usa para buscar y descargar elementos. 
- **Un escenario sin conexión o con conexión parcial**: requiere que se acceda a internet mediante la herramienta de redifusión de Marketplace para descargar los elementos de este. Después, los elementos descargados se transfieren a la instalación desconectada de Azure Stack. Este escenario usa PowerShell.

En [Elementos de Azure Marketplace disponibles para Azure Stack](azure-stack-marketplace-azure-items.md) encontrará una lista de los elementos de Marketplace que se pueden descargar.


## <a name="connected-scenario"></a>Escenario conectado
Si Azure Stack se conecta a internet, se puede usar el Portal de administración para descargar los elementos de Marketplace.

### <a name="prerequisites"></a>Requisitos previos
La implementación de Azure Stack debe tener conectividad a Internet y estar [registrada en Azure](azure-stack-register.md).

### <a name="use-the-portal-to-download-marketplace-items"></a>Uso del portal para descargar elementos de Marketplace  
1. Inicie sesión en el Portal de administración de Azure Stack.

2.  Antes de empezar a descargar elementos de Marketplace vea el espacio de almacenamiento que tiene disponible. Después, cuando seleccione los elementos que desea descargar, puede comparar el tamaño de la descarga con la capacidad de almacenamiento disponible. Si hay un límite de capacidad, considere distintas opciones para [administrar el espacio disponible](azure-stack-manage-storage-shares.md#manage-available-space). 

    Para examinar el espacio disponible, en **Region management** (Administración de regiones), seleccione la región que desea explorar y vaya a **Resource Providers** > **Storage** (Proveedores de recursos > Almacenamiento).

    ![Examinar espacio de almacenamiento](media/azure-stack-download-azure-marketplace-item/storage.png)  

    
3. Abra Azure Stack Marketplace y conéctese a Azure. Para ello, seleccione **Marketplace management** (Administración de Marketplace) y, después, seleccione **Add from Azure** (Agregar desde Azure).

    ![Adición desde Azure](media/azure-stack-download-azure-marketplace-item/marketplace.png)

    El portal muestra la lista de elementos que se pueden descargar desde Azure Marketplace. Puede hacer clic en cada elemento para ver su descripción, así como información adicional sobre él, incluido su tamaño de descarga. 

    ![Lista de Marketplace](media/azure-stack-download-azure-marketplace-item/image03.png)

4. Seleccione el elemento que desee y, después, seleccione **Download** (Descargar). Los tiempos de descarga varían.

    ![Mensaje de descarga](media/azure-stack-download-azure-marketplace-item/image04.png)

    Una vez finalizada la descarga, el nuevo elemento de Marketplace se puede implementar como usuario u operador de Azure Stack.

5. Para implementar el elemento descargado, seleccione **+Nuevo** y busque entre las categorías el nuevo elemento de Marketplace. Luego, seleccione el elemento para comenzar el proceso de implementación. El proceso no es el mismo para todos los artículos de Marketplace. 

## <a name="disconnected-or-a-partially-connected-scenario"></a>Escenario sin conexión o con conexión parcial

Si Azure Stack está en modo sin conexión y sin conectividad a internet, use PowerShell y la *herramienta de redifusión de Marketplace* para descargar los elementos de Marketplace en un equipo con conectividad a Internet. Luego, transfiera elementos al entorno de Azure Stack. En un entorno sin conexión, no se pueden descargar elementos de Marketplace desde el portal Azure Stack. 

La herramienta de redifusión de Marketplace también se puede usar en un escenario con conexión. 

Este escenario tiene dos partes:
- **Parte 1:** descarga desde Azure Marketplace. En el equipo con acceso a Internet, configure PowerShell, descargue la herramienta de redifusión y, después, descargue los elementos desde Azure Marketplace.  
- **Parte 2:** carga y publicación en Azure Stack Marketplace. Mueva los archivos que ha descargado a su entorno de Azure Stack, impórtelos en Azure Stack y, después, publíquelos en Azure Stack Marketplace.  


### <a name="prerequisites"></a>Requisitos previos
- La implementación de Azure Stack se debe [registrar en Azure](azure-stack-register.md).  

- El equipo que tenga conectividad a Internet debe tener la **versión 1.2.11 del paquete Azure Stack PowerShell Module**, o cualquier versión superior. Si no están, [instale los módulos de PowerShell específicos de Azure Stack](azure-stack-powershell-install.md).  

- Para habilitar la importación de un elemento de Marketplace descargado, debe estar configurado el [entorno de PowerShell para el operador de Azure Stack](azure-stack-powershell-configure-admin.md).  

- Debe tener una cuenta de almacenamiento en Azure Stack que tenga un contenedor al que se pueda acceder de forma pública (que es un blob de almacenamiento). Utilice el contenedor como almacenamiento temporal para los archivos de la galería de elementos de Marketplace. Si no está familiarizado con las cuentas de almacenamiento y los contenedores, consulte [Carga, descarga y enumeración de blobs mediante Azure Portal](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) en la documentación de Azure.

- La herramienta de redifusión de Marketplace se descarga en el primer procedimiento. 

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Uso de la herramienta de redifusión de Marketplace para descargar elementos de Marketplace

1. En un equipo con conexión a Internet, abra una consola de PowerShell como administrador.

2. Agregue la cuenta de Azure que ha usado para registrar Azure Stack. Para agregar la cuenta, en PowerShell ejecute `Add-AzureRmAccount` sin parámetros. Se le solicita que escriba las credenciales de su cuenta de Azure y puede que tenga que utilizar la autenticación en dos fases en función de la configuración de la cuenta.

3. Si tiene varias suscripciones, ejecute el siguiente comando para seleccionar la que ha usado para el registro:  

   ```PowerShell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. Descargue la versión más reciente de la herramienta de redifusión de Marketplace mediante el siguiente script:  

   ```PowerShell
   # Download the tools archive.
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
   invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip `
     -OutFile master.zip

   # Expand the downloaded files.
   expand-archive master.zip `
     -DestinationPath . `
     -Force

   # Change to the tools directory.
   cd .\AzureStack-Tools-master

   ```

5. Importe el módulo de redifusión e inicie la herramienta mediante la ejecución del script siguiente. Reemplace la *ruta de acceso de la carpeta de destino* por una ubicación para almacenar los archivos que descargue desde Azure Marketplace.   

   ```PowerShell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Sync-AzSOfflineMarketplaceItem `
     -destination "Destination folder path" `
     -AzureTenantID $AzureContext.Tenant.TenantId `
     -AzureSubscriptionId $AzureContext.Subscription.Id  
   ```

6. Cuando la herramienta se ejecuta, se le solicita que escriba las credenciales de la cuenta de Azure. Inicie sesión en la cuenta de Azure que haya usado para registrar Azure Stack. Tras el inicio de sesión, debe ver una pantalla similar a la de la siguiente imagen, con la lista de elementos disponibles en Marketplace.  

   ![Elementos emergentes de Azure Marketplace](media/azure-stack-download-azure-marketplace-item/image05.png)

7. Seleccione el elemento que desea descargar y tome nota de la *versión* (para seleccionar varias imágenes, mantenga presionada la tecla *Ctrl*). En el siguiente procedimiento, cuando importe el elemento, hará referencia a la *versión*. 
   
   También puede filtrar la lista de imágenes mediante la opción **Agregar criterios**.

8. Seleccione **Aceptar**, lea los términos legales y acéptelos. 

9. El tiempo que tarda la descarga depende del tamaño del elemento. Una vez que se completa la descarga, el elemento está disponible en la carpeta que se especificó en el script. La descarga incluye un archivo VHD (para máquinas virtuales) o un archivo .ZIP (para extensiones de máquina virtual). También incluye un paquete de galería con formato *.azpkg* (un paquete *.azpkg* es un archivo *.zip*).
 

### <a name="import-the-download-and-publish-to-azure-stack-marketplace"></a>Importación de la descarga y su publicación en Azure Stack Marketplace
1. Los archivos de las imágenes de máquina virtual o plantillas de solución que haya [descargado con anterioridad](#use-the-marketplace-syndication-tool-to-download-marketplace-items) deben estar disponibles localmente para su entorno de Azure Stack.  

2. Importe los archivos .VHD en Azure Stack. Para importar correctamente una imagen de máquina virtual (VM), debe tener la siguiente información acerca de la máquina virtual:
   - La *versión*, como se indicó en el paso 7 del procedimiento anterior.
   - Los valores de las máquinas virtuales *publisher*, *offer* y *sku*. Para obtener estos valores, cambie el nombre del archivo **.azpkg** archivo para cambiar su extensión a **.zip**. Luego puede utilizar un editor de texto para abrir **DeploymentTemplates\CreateUiDefinition.json**. En el archivo .json, busque la sección *imageReference*, que contiene estos valores para el elemento de Marketplace. En el siguiente ejemplo se muestra cómo aparece esta información:

     ```json  
     "imageReference": {  
        "publisher": "MicrosoftWindowsServer",  
        "offer": "WindowsServer",  
        "sku": "2016-Datacenter-Server-Core"  
      }
     ```  

   Importe la imagen en Azure Stack mediante el cmdlet **Add-AzsPlatformimage**. Al usar este cmdlet, asegúrese de reemplazar los valores de *publisher*, *offer* y otros parámetros por los valores de la imagen que va a importar. Los valores *publisher*, *ofrecen* y *sku* de la imagen se pueden obtener del archivo de texto que se descarga junto con el archivo AZPKG y se almacenan en la ubicación de destino. 

   En el siguiente script de ejemplo, se usan los valores de la máquina virtual Windows Server 2016 Datacenter - Server Core. 

   ```PowerShell  
   Add-AzsPlatformimage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsDiskLocalPath "C:\AzureStack-Tools-master\Syndication\Windows-Server-2016-DatacenterCore-20171215-en.us-127GB.vhd" `
   ```
   **Acerca de las plantillas de solución:** algunas plantillas pueden incluir un pequeño archivo .VHD de 3 MB llamado **fixed3.vhd**. Dicho archivo no es preciso que se importe en Azure Stack. Fixed3.vhd.  Este archivo se incluye con algunas plantillas de solución para cumplir requisitos de publicación de Azure Marketplace.

   Vea la descripción de las plantillas y descargue e importe los requisitos adicionales, como los discos duros virtuales, necesarios para trabajar con la plantilla de solución.

3. Use el Portal de administración para cargar el paquete de elementos de Marketplace (el archivo .azpkg) en el almacenamiento de blobs de Azure Stack. La carga del paquete hace que esté disponible para Azure Stack, por lo que más adelante el elemento se podrá publicar en Azure Stack Marketplace.

   Para poder realizar la carga es preciso tener una cuenta de almacenamiento con un contenedor con acceso público (consulte los requisitos previos para este escenario)   
   1. En el Portal de administración de Azure Stack, vaya a **More services** > **Storage accounts** (Más servicios > Cuentas de almacenamiento).  
   
   2. Seleccione una cuenta de almacenamiento en la suscripción y, después, en **BLOB SERVICE**, seleccione **Contenedores**.  
      ![Blob service](media/azure-stack-download-azure-marketplace-item/blob-service.png)  
   
   3. Seleccione el contenedor que desee usar y, después, seleccione **Cargar**, para abrir el panel **Cargar blob**.  
      ![Contenedor](media/azure-stack-download-azure-marketplace-item/container.png)  
   
   4. En el panel Cargar blob, vaya a los archivos que desee cargar en el almacenamiento y seleccione **Cargar**.  
      ![upload](media/azure-stack-download-azure-marketplace-item/upload.png)  

   5. Los archivos que se cargan aparecen en el panel del contenedor. Seleccione un archivo y, después, copie la dirección URL del panel **Propiedades del blob**. Esta dirección URL la usará en el paso siguiente, cuando importe el elemento de Marketplace en Azure Stack.  En la siguiente imagen, el contenedor es *blob-test-storage* y el archivo es *Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg*.  La dirección URL del archivo es *https://testblobstorage1.blob.local.azurestack.external/blob-test-storage/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg*.  
      ![Propiedades del blob](media/azure-stack-download-azure-marketplace-item/blob-storage.png)  

4.  Use PowerShell para publicar el elemento de Marketplace en Azure Stack mediante el cmdlet **Add-AzsGalleryItem**. Por ejemplo:   
    ```PowerShell  
    Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg" `
     –Verbose
    ```
5. Después de publicar un elemento de la galería, puede verlo desde **More services** > **Marketplace** (Más servicios > Marketplace).  Si la descarga es una plantilla de solución, asegúrese de agregar todas las imágenes de discos duros virtuales de dicha plantilla.  
  ![Ver Marketplace](media/azure-stack-download-azure-marketplace-item/view-marketplace.png)  

> [!NOTE]
> Con el lanzamiento de Azure Stack PowerShell 1.3.0 se pueden agregar extensiones de máquina virtual.

Por ejemplo: 

````PowerShell
Add-AzsVMExtension -Publisher "Microsoft" -Type "MicroExtension" -Version "0.1.0" -ComputeRole "IaaS" -SourceBlob "https://github.com/Microsoft/PowerShell-DSC-for-Linux/archive/v1.1.1-294.zip" -SupportMultipleExtensions -VmOsType "Linux"
````

## <a name="next-steps"></a>Pasos siguientes
[Creación y publicación de un producto en Marketplace](azure-stack-create-and-publish-marketplace-item.md)
---
title: Descarga de elementos de Marketplace desde Azure | Microsoft Docs
description: "Puedo descargar elementos de Marketplace desde Azure a la implementación de Azure Stack."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/30/2017
ms.author: erikje
ms.openlocfilehash: 33b7be4a85723ab03e4c656a8dd28632ad854e29
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/02/2017
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Descarga de elementos de Marketplace desde Azure a Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Puesto que usted decide qué contenido desea incluir en su Marketplace de Azure Stack, debe tener en cuenta el contenido disponible en Azure Marketplace. Puede descargar de una lista protegida de elementos de Azure Marketplace que han sido previamente probados para su ejecución en Azure Stack. Con frecuencia se agregan nuevos elementos a esta lista, así que asegúrese de volver a consultar si hay contenido nuevo.

## <a name="download-marketplace-items-in-a-connected-scenario-with-internet-connectivity"></a>Descarga de elementos de Marketplace en un escenario conectado (con conectividad a Internet)

1. Para descargar elementos de Marketplace, primero debe [registrar Azure Stack en Azure](azure-stack-register.md). 
2. Inicie sesión en el portal de administrador de Azure Stack (https://portal.local.azurestack.external).
3. Algunos elementos de Marketplace pueden ser grandes. Para comprobar que dispone de espacio suficiente en el sistema, haga clic en **Proveedores de recursos** > **Storage**.

    ![](media/azure-stack-download-azure-marketplace-item/image01.png)

4. Haga clic en **Más servicios** > **Marketplace Management** (Administración de Marketplace).

    ![](media/azure-stack-download-azure-marketplace-item/image02.png)

4. Haga clic en **Agregar desde Azure** para ver una lista de elementos disponibles para su descarga. Puede hacer clic en cada elemento de la lista para ver su descripción y el tamaño de la descarga.

    ![](media/azure-stack-download-azure-marketplace-item/image03.png)

5. Seleccione el elemento que desee en la lista y, a continuación, haga clic en **Descargar**. Esto inicia la descarga de la imagen de máquina virtual para el elemento seleccionado. Los tiempos de descarga varían.

    ![](media/azure-stack-download-azure-marketplace-item/image04.png)

6. Una vez finalizada la descarga, puede implementar el nuevo elemento de Marketplace como un usuario o un operador de Azure Stack. Haga clic en **+Nuevo**, busque entre las categorías el nuevo elemento de Marketplace y, a continuación, seleccione el elemento.
7. Haga clic en **Crear** para abrir la experiencia de creación del elemento recién descargado. Siga las instrucciones paso a paso para implementar el elemento.

## <a name="download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>Descarga de elementos de Marketplace en un escenario sin conexión o parcialmente conectado (con conectividad a Internet limitada)

Cuando Azure Stack se implementa en un modo sin conexión (sin conectividad a Internet), no puede descargar elementos de Marketplace a través del portal de Azure Stack. Sin embargo, puede usar la herramienta de redifusión de Marketplace para descargar los elementos de Marketplace en un equipo que tenga conectividad a Internet y, después, transferirlos a su entorno de Azure Stack. 

### <a name="prerequisites"></a>Requisitos previos
Para poder usar la herramienta de redifusión de Marketplace, asegúrese de haber [registrado Azure Stack con su suscripción de Azure](azure-stack-register.md).  

En el equipo que tenga conectividad a Internet, siga estos pasos para descargar los elementos de Marketplace que desee:

1. Abra una consola de PowerShell como administrador e [instale módulos de PowerShell específicos para Azure Stack](azure-stack-powershell-install.md). Asegúrese de instalar la **versión 1.2.11 de PowerShell, o cualquiera posterior**.  

2. Agregue la cuenta de Azure que ha usado para registrar Azure Stack. Para ello, ejecute el cmdlet **Add-AzureRmAccount** sin parámetros. Se le pedirá que escriba sus credenciales de cuenta de Azure y puede que tenga que utilizar la autenticación en dos fases en función de la configuración de la cuenta.  

3. Si tiene varias suscripciones, ejecute el siguiente comando para seleccionar la que ha usado para el registro:  

   ```powershell
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. Descargue la versión más reciente de la herramienta de redifusión de Marketplace mediante el siguiente script:  

   ```PowerShell
   # Download the tools archive.
   invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/vnext.zip `
     -OutFile vnext.zip

   # Expand the downloaded files.
   expand-archive vnext.zip `
     -DestinationPath . `
     -Force

   # Change to the tools directory.
   cd \AzureStack-Tools-vnext

   ```

5. Importe el módulo de redifusión e inicie la herramienta mediante la ejecución de los siguientes comandos:  

   ```powershell
   Import-Module .\ Syndication\AzureStack.MarketplaceSyndication.psm1

   Sync-AzSOfflineMarketplaceItem `
     -destination “<Destination folder path>” `
     -AzureTenantID $AzureContext.Tenant.TenantId `
     -AzureSubscriptionId $AzureContext.Subscription.Id  
   ```

6. Cuando la herramienta se ejecuta, se le solicita que escriba las credenciales de la cuenta de Azure. Inicie sesión en la cuenta de Azure que haya usado para registrar Azure Stack. Una vez el inicio de sesión se realiza correctamente, debería ver la siguiente pantalla con la lista de elementos de Marketplace disponibles.  

   ![Elementos emergentes de Azure Marketplace](./media/azure-stack-download-azure-marketplace-item/image05.png)

7. Seleccione la imagen que desea descargar (para seleccionar varias imágenes, mantenga presionada la tecla Ctrl) y anote la versión de la imagen. Esta versión se usará para importar la imagen en la sección siguiente > haga clic en **Aceptar** > acepte las condiciones legales haciendo clic en **Sí**. También puede filtrar la lista de imágenes mediante la opción **Agregar criterios**. La descarga tarda un tiempo según el tamaño de la imagen. Una vez que la imagen se descarga, está disponible en la ruta de acceso de destino que proporcionó anteriormente. La descarga contiene el archivo VHD y los elementos de la galería en formato Azpkg.  

### <a name="import-the-image-and-publish-it-to-azure-stack-marketplace"></a>Importación de la imagen y su publicación en el Marketplace en Azure Stack

1. Después de descargar la imagen y el paquete de la galería, guárdelos, así como su contenido, en la carpeta AzureStack-Tools-vnext de una unidad de disco extraíble y cópielo en el entorno de Azure Stack (puede copiarlo localmente en cualquier ubicación, como: "C:\MarketplaceImages").   

2. Antes de importar la imagen debe conectarse al entorno del operador de Azure Stack mediante los pasos que se describen en [Configuración del entorno de PowerShell del operador de Azure Stack](azure-stack-powershell-configure-admin.md).  

3. Importe la imagen en Azure Stack mediante el cmdlet Add-AzsVMImage. Al usar este cmdlet, asegúrese de reemplazar los valores de publisher, offer y otros parámetros por los valores de la imagen que va a importar. Puede obtener los valores "publisher", "offer" y "sku" de la imagen del objeto imageReference del archivo Azpkg que descargó anteriormente y el valor "version" del paso 6 de la sección anterior.

   ```json
   "imageReference": {
      "publisher": "MicrosoftWindowsServer",
      "offer": "WindowsServer",
      "sku": "2016-Datacenter-Server-Core"
    }
   ```

   Reemplace los valores parameter y ejecute el siguiente comando:

   ```powershell
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   Add-AzsVMImage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2017.09.25" `
    -OsDiskLocalPath "C:\AzureStack-Tools-master\Syndication\Microsoft.WindowsServer2016DatacenterServerCore-ARM-Eval.2017.09.25.vhd" `
    -CreateGalleryItem $False `
    -Location Local 
   ```

4. Use el portal para cargar el elemento de Marketplace (. Azpkg) en Azure Stack Blob Storage. Puede cargar en el almacenamiento de Azure Stack local o cargar en Azure Storage. (Se trata de una ubicación temporal para el paquete.) Asegúrese de que el blob es accesible públicamente y anote el identificador URI.  

5. Publique el elemento de Marketplace en Azure Stack mediante **Add-AzureRMGalleryItem**. Por ejemplo:

   ```powershell
   Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.2.azpkg" `
     –Verbose
   ```

6. Una vez publicado el elemento de la galería, puede verlo en el panel **Nuevo** > **Marketplace**.  

   ![Marketplace](./media/azure-stack-download-azure-marketplace-item/image06.png)

## <a name="next-steps"></a>Pasos siguientes

[Creación y publicación de un producto en Marketplace](azure-stack-create-and-publish-marketplace-item.md)

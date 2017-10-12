---
title: "Agregación de una imagen de máquina virtual a Azure Stack | Microsoft Docs"
description: "Agregar la imagen personalizada de máquina virtual Windows o Linux de su organización para uso de los inquilinos"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: e5a4236b-1b32-4ee6-9aaa-fcde297a020f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: sngun
ms.openlocfilehash: de8540397b63093457382cf427a65ea0e48b93e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="make-a-custom-virtual-machine-image-available-in-azure-stack"></a>Hacer que una imagen de máquina virtual personalizada esté disponible en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Azure Stack permite a los operadores poner imágenes de máquina virtual personalizadas a disposición de los usuarios. Las plantillas de Azure Resource Manager pueden hacer referencia a estas imágenes y pueden ser agregadas a la interfaz de usuario de Azure Marketplace con la creación de un elemento de Marketplace. 

## <a name="add-a-vm-image-to-marketplace-with-powershell"></a>Agregación de una imagen de máquina virtual a Marketplace con PowerShell

Implemente los siguientes requisitos previos desde el [kit de desarrollo](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) o desde un cliente externo basado en Windows, si está [conectado a través de VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)

* [Instale PowerShell para Azure Stack](azure-stack-powershell-install.md).  

* Descargue las [herramientas necesarias para trabajar con Azure Stack](azure-stack-powershell-download.md).  

* Prepare una imagen de disco duro virtual del sistema operativo Windows o Linux en formato VHD (no VHDX).
   
   * Para imágenes de Windows, el artículo [Carga de una imagen de máquina virtual Windows en Azure para implementaciones de Resource Manager](../virtual-machines/windows/upload-generalized-managed.md) contiene instrucciones de preparación de la imagen en la sección **Preparación del disco duro virtual para la carga**.
   * Para imágenes de Linux, siga los pasos para preparar la imagen o use una imagen de Linux de Azure Stack existente como se describe en el artículo [Implementación de máquinas virtuales Linux en Azure Stack](azure-stack-linux.md).  

Ahora, ejecute los pasos siguientes para agregar la imagen en Marketplace de Azure Stack:

1. Importe los módulos Connect y ComputeAdmin:
   
   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # import the Connect and ComputeAdmin modules
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1
   ``` 

2. Inicie sesión en el entorno de Azure Stack. Ejecute el siguiente script en función de si su entorno de Azure Stack se implementó mediante el uso de AAD o AD FS (asegúrese de reemplazar los valores de tenantName, GraphAudience endpoint y ArmEndpoint de AAD según la configuración de su entorno): 

   a. **Azure Active Directory**, use el siguiente cmdlet:

   ```PowerShell
   # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
   $ArmEndpoint = "<Resource Manager endpoint for your environment>"

   # For Azure Stack development kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
   $GraphAudience = "<GraphAuidence endpoint for your environment>"

   #Create the Azure Stack operator's AzureRM environment by using the following cmdlet:
   Add-AzureRMEnvironment `
     -Name "AzureStackAdmin" `
     -ArmEndpoint $ArmEndpoint 

   Set-AzureRmEnvironment `
    -Name "AzureStackAdmin" `
    -GraphAudience $GraphAudience

   $TenantID = Get-AzsDirectoryTenantId `
     -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
     -EnvironmentName AzureStackAdmin

   Login-AzureRmAccount `
     -EnvironmentName "AzureStackAdmin" `
     -TenantId $TenantID 
   ```

   b. **Servicios de federación de Active Directory**, use el siguiente cmdlet:
    
   ```PowerShell
   # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
   $ArmEndpoint = "<Resource Manager endpoint for your environment>"

   # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
   $GraphAudience = "<GraphAuidence endpoint for your environment>"

   # Create the Azure Stack operator's AzureRM environment by using the following cmdlet:
   Add-AzureRMEnvironment `
     -Name "AzureStackAdmin" `
     -ArmEndpoint $ArmEndpoint

   Set-AzureRmEnvironment `
     -Name "AzureStackAdmin" `
     -GraphAudience $GraphAudience `
     -EnableAdfsAuthentication:$true

   $TenantID = Get-AzsDirectoryTenantId `
     -ADFS 
     -EnvironmentName AzureStackAdmin 

   Login-AzureRmAccount `
     -EnvironmentName "AzureStackAdmin" `
     -TenantId $TenantID 
   ```
    
3. Agregue la imagen de máquina virtual invocando el cmdlet `Add-AzsVMImage`. En el cmdlet Add-AzsVMImage, especifique Windows o Linux como valor de osType. Incluya el publicador, oferta, SKU y versión de la imagen de máquina virtual. Consulte la sección [Parámetros](#parameters) para obtener información acerca de los parámetros permitidos. Estos parámetros se utilizan en las plantillas de Azure Resource Manager para hacer referencia a la imagen de máquina virtual. A continuación se muestra un ejemplo de la invocación del script:
     
     ```powershell
     Add-AzsVMImage `
       -publisher "Canonical" `
       -offer "UbuntuServer" `
       -sku "14.04.3-LTS" `
       -version "1.0.0" `
       -osType Linux `
       -osDiskLocalPath 'C:\Users\AzureStackAdmin\Desktop\UbuntuServer.vhd' `
     ```

El comando hace lo siguiente:

* Se autentica en el entorno de Azure Stack
* Carga el disco duro virtual local en una cuenta de almacenamiento temporal recién creada
* Agrega la imagen de la máquina virtual en el repositorio de imágenes de máquina virtual y
* Crea un elemento de Marketplace

Para comprobar que el comando se ejecutó correctamente, vaya a Marketplace en el portal y, a continuación, compruebe que la imagen de máquina virtual está disponible en la categoría **Máquinas virtuales**.

![Imagen de máquina virtual agregada correctamente](./media/azure-stack-add-vm-image/image5.PNG) 

## <a name="remove-a-vm-image-with-powershell"></a>Eliminación de una imagen de máquina virtual con PowerShell

Cuando ya no necesite la imagen de máquina virtual que ha cargado anteriormente, puede eliminarla de Marketplace mediante el siguiente cmdlet:

```powershell
Remove-AzsVMImage `
  -publisher "Canonical" `
  -offer "UbuntuServer" `
  -sku "14.04.3-LTS" `
  -version "1.0.0" `
```

## <a name="parameters"></a>parameters

| Parámetro | Descripción |
| --- | --- |
| **publisher** |El segmento de nombre del publicador de la imagen de máquina virtual que utilizan los usuarios al implementar la imagen. Un ejemplo es 'Microsoft'. No incluya un espacio u otros caracteres especiales en este campo. |
| **offer** |El segmento de nombre de la oferta de la imagen de máquina virtual que utilizan los usuarios al implementar la imagen de máquina virtual. Un ejemplo es 'WindowsServer'. No incluya un espacio u otros caracteres especiales en este campo. |
| **sku** |El segmento de nombre de SKU de la imagen de máquina virtual que utilizan los usuarios al implementar la imagen de máquina virtual. Un ejemplo es 'Datacenter2016'. No incluya un espacio u otros caracteres especiales en este campo. |
| **version** |La versión de la imagen de máquina virtual que utilizan los usuarios al implementar la imagen de máquina virtual. Esta versión está en el formato *\#.\#.\#*. Un ejemplo es '1.0.0'. No incluya un espacio u otros caracteres especiales en este campo. |
| **osType** |El valor de osType de la imagen debe ser 'Windows' o 'Linux'. |
| **osDiskLocalPath** |La ruta de acceso local al archivo VHD del disco de sistema operativo que se va a cargar como una imagen de máquina virtual en Azure Stack. |
| **dataDiskLocalPaths** |Una matriz opcional de las rutas de acceso locales para los discos de datos que se pueden cargar como parte de la imagen de máquina virtual. |
| **CreateGalleryItem** |Una marca booleana que determina si se crea un elemento en Marketplace. De forma predeterminada, se establece en true. |
| **title** |El nombre para mostrar del elemento de Marketplace. De forma predeterminada, se establece en Publicador-Oferta-SKU de la imagen de máquina virtual. |
| **descripción** |La descripción del elemento de Marketplace. |
| **ubicación** |La ubicación en la que debe publicarse la imagen de máquina virtual. De forma predeterminada, este valor se establece en local.|
| **osDiskBlobURI** |Opcionalmente, este script también acepta un identificador URI de Blob Storage para disco de sistema operativo. |
| **dataDiskBlobURIs** |Opcionalmente, este script también acepta una matriz de identificadores URI de Blob Storage para agregar discos de datos a la imagen. |

## <a name="add-a-vm-image-through-the-portal"></a>Agregación de una imagen de máquina virtual a través del portal

> [!NOTE]
> Este método requiere la creación del elemento de Marketplace por separado.

Un requisito de las imágenes es que se pueda hacer referencia a ellas con un identificador URI de Blob Storage. Prepare una imagen de sistema operativo Windows o Linux en formato VHD (no VHDX) y, a continuación, cargue la imagen en una cuenta de almacenamiento en Azure o Azure Stack. Si la imagen ya se ha cargado en Blob Storage en Azure o Azure Stack, puede omitir el paso 1.

1. [Carga de una imagen de máquina virtual Windows en Azure para implementaciones de Resource Manager](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) o, para una imagen de Linux, siga las instrucciones descritas en el artículo [Implementación de máquinas virtuales Linux en Azure Stack](azure-stack-linux.md). Debe comprender las siguientes consideraciones antes de cargar la imagen:

   * Es más eficaz cargar una imagen en Blob Storage de Azure Stack que en Azure Blob Storage porque se tarda menos tiempo en insertar la imagen en el repositorio de imágenes de Azure Stack. 
   
   * Al cargar la [imagen de máquina virtual Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/), asegúrese de sustituir el paso **Inicio de sesión en Azure** por el paso [Configuración del entorno de PowerShell del operador de Azure Stack](azure-stack-powershell-configure-admin.md).  

   * Tome nota del identificador URI de Blob Storage donde se carga la imagen, que se encuentra en el siguiente formato:  *&lt;storageAccount&gt;/&lt;blobContainer&gt; / &lt;targetVHDName&gt;*.vhd

   * Para hacer el blob accesible de forma anónima, vaya al contenedor de blob de la cuenta de almacenamiento donde cargó el disco duro virtual de la imagen de máquina virtual en **Blob,** y, a continuación, seleccione **Directiva de acceso**. Si lo desea, puede generar una firma de acceso compartido para el contenedor e incluirla como parte del identificador URI del blob.

   ![Navegación a blobs de la cuenta de almacenamiento](./media/azure-stack-add-vm-image/image1.png)

   ![Establecimiento del acceso del blob en público](./media/azure-stack-add-vm-image/image2.png)

2. Inicie sesión en Azure Stack como operador > en el menú, haga clic en **Más servicios** > **Proveedores de recursos** > seleccione **Compute** >  **Imágenes de máquina virtual** > **Agregar**

3. En la hoja **Agregar una imagen de máquina virtual**, escriba el publicador, oferta, SKU y versión de la imagen de máquina virtual. Estos segmentos del nombre hacen referencia a la imagen de máquina virtual en las plantillas de Resource Manager. Asegúrese de seleccionar el valor de **osType** correctamente. En **Identificador URI del blob del disco de sistema operativo**, escriba el identificador URI del blob donde se cargó la imagen y haga clic en **Crear** para empezar a crear la imagen de máquina virtual.
   
   ![Inicio de la creación de la imagen](./media/azure-stack-add-vm-image/image4.png)

   Cuando la imagen se crea correctamente, el estado de la imagen de máquina virtual cambia a 'Correcto'.

4. Para que la imagen de máquina virtual esté disponible con más facilidad para su consumo por el usuario en la interfaz de usuario, es mejor [crear un elemento de Marketplace](azure-stack-create-and-publish-marketplace-item.md).

## <a name="next-steps"></a>Pasos siguientes

[Aprovisionamiento de una máquina virtual](azure-stack-provision-vm.md)
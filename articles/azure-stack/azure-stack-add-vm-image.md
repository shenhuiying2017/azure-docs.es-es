---
title: "Adición de una imagen de máquina virtual a Azure Stack | Microsoft Docs"
description: "Agregue la imagen de máquina virtual Windows o Linux personalizada de su organización para que la usen los inquilinos."
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
ms.openlocfilehash: 520e4dfaadf1d476447a600ef2b3d092b6955a89
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2017
---
# <a name="make-a-custom-virtual-machine-image-available-in-azure-stack"></a>Hacer que una imagen de máquina virtual personalizada esté disponible en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

En Azure Stack, los operadores pueden poner imágenes de máquina virtual personalizadas a disposición de los usuarios. Luego, las plantillas de Azure Resource Manager pueden hacer referencia a estas imágenes o se pueden agregar a la interfaz de usuario de Azure Marketplace como un elemento de Marketplace. 

## <a name="add-a-vm-image-to-marketplace-by-using-powershell"></a>Adición de una imagen de máquina virtual a Marketplace con PowerShell

Implemente los siguientes requisitos previos desde el [kit de desarrollo](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) o desde un cliente externo basado en Windows, si está [conectado a través de VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

1. [Instale PowerShell para Azure Stack](azure-stack-powershell-install.md).  

2. Descargue las [herramientas necesarias para trabajar con Azure Stack](azure-stack-powershell-download.md).  

3. Prepare una imagen de disco duro virtual del sistema operativo Windows o Linux en formato VHD (no use el formato VHDX).
   
   * Para obtener instrucciones sobre cómo preparar una imagen de Windows, consulte [Carga de una imagen de máquina virtual Windows en Azure para implementaciones de Resource Manager](../virtual-machines/windows/upload-generalized-managed.md).
   * En el caso de imágenes de Linux, consulte [Implementación de máquinas virtuales Linux en Azure Stack](azure-stack-linux.md). Realice los pasos para preparar la imagen o use una imagen de Linux de Azure Stack existente como se describe en el artículo.  

Para agregar la imagen a Azure Stack Marketplace, realice los pasos siguientes:

1. Importe los módulos Connect y ComputeAdmin:
   
   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # Import the Connect and ComputeAdmin modules.
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1
   ``` 

2. Inicie sesión en el entorno de Azure Stack. Ejecute uno de los siguientes scripts, según si implementó su entorno de Azure Stack mediante Azure Active Directory (Azure AD) o Servicios de federación de Active Directory (AD FS). (Reemplace el valor de `tenantName` de Azure AD, el punto de conexión `GraphAudience` y el valor de `ArmEndpoint` para que reflejen la configuración de su entorno).

    * **Azure Active Directory**. Use el siguiente cmdlet:

      ```PowerShell
      # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
      $ArmEndpoint = "<Resource Manager endpoint for your environment>"

      # For Azure Stack Development Kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
      $GraphAudience = "<GraphAuidence endpoint for your environment>"
      
      # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
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

   * **Servicios de federación de Active Directory**. Use el siguiente cmdlet:
    
        ```PowerShell
        # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
        $ArmEndpoint = "<Resource Manager endpoint for your environment>"

        # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
        $GraphAudience = "<GraphAuidence endpoint for your environment>"

        # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
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
    
3. Agregue la imagen de máquina virtual invocando el cmdlet `Add-AzsVMImage`. En el cmdlet `Add-AzsVMImage`, especifique Windows o Linux para `osType`. Incluya el publicador, oferta, SKU y versión de la imagen de máquina virtual. Para obtener información sobre los parámetros permitidos, consulte [Parámetros](#parameters). Estos parámetros se usan en las plantillas de Azure Resource Manager para hacer referencia a la imagen de máquina virtual. En el ejemplo siguiente se invoca el script:
     
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

* Realiza la autenticación en el entorno de Azure Stack.
* Carga el disco duro virtual local en una cuenta de almacenamiento temporal recién creada.
* Agrega la imagen de máquina virtual al repositorio de imágenes de máquina virtual.
* Crea un elemento de Marketplace.

Para comprobar que el comando se ejecutó correctamente, vaya a Marketplace en el portal. Compruebe que la imagen de máquina virtual está disponible en la categoría **Máquinas virtuales**.

![Imagen de máquina virtual agregada correctamente](./media/azure-stack-add-vm-image/image5.PNG) 

## <a name="remove-a-vm-image-by-using-powershell"></a>Eliminación de una imagen de máquina virtual mediante PowerShell

Cuando ya no necesite la imagen de máquina virtual que ha cargado, puede eliminarla de Marketplace mediante el siguiente cmdlet:

```powershell
Remove-AzsVMImage `
  -publisher "Canonical" `
  -offer "UbuntuServer" `
  -sku "14.04.3-LTS" `
  -version "1.0.0" `
```

## <a name="parameters"></a>Parámetros

| Parámetro | Descripción |
| --- | --- |
| **publisher** |El segmento del nombre del publicador de la imagen de máquina virtual que usan los usuarios al implementar la imagen. Un ejemplo es **Microsoft**. No incluya un espacio u otros caracteres especiales en este campo. |
| **offer** |El segmento del nombre de la oferta de la imagen de máquina virtual que usan los usuarios al implementar la imagen de máquina virtual. Un ejemplo es **WindowsServer**. No incluya un espacio u otros caracteres especiales en este campo. |
| **sku** |El segmento del nombre de la SKU de la imagen de máquina virtual que usan los usuarios al implementar la imagen de máquina virtual. Un ejemplo es **Datacenter2016**. No incluya un espacio u otros caracteres especiales en este campo. |
| **version** |La versión de la imagen de máquina virtual que usan los usuarios al implementar la imagen de máquina virtual. Esta versión está en el formato *\#.\#.\#*. Un ejemplo es **1.0.0**. No incluya un espacio u otros caracteres especiales en este campo. |
| **osType** |El valor de osType de la imagen debe ser **Windows** o **Linux**. |
| **osDiskLocalPath** |La ruta de acceso local al archivo VHD del disco de sistema operativo que se va a cargar como una imagen de máquina virtual en Azure Stack. |
| **dataDiskLocalPaths** |Una matriz opcional de las rutas de acceso locales para los discos de datos que se pueden cargar como parte de la imagen de máquina virtual. |
| **CreateGalleryItem** |Una marca booleana que determina si se crea un elemento en Marketplace. De forma predeterminada, se establece en **true**. |
| **title** |El nombre para mostrar del elemento de Marketplace. De forma predeterminada, se establece en el valor `Publisher-Offer-Sku` de la imagen de máquina virtual. |
| **descripción** |La descripción del elemento de Marketplace. |
| **ubicación** |La ubicación en la que debe publicarse la imagen de máquina virtual. De forma predeterminada, este valor se establece en **local**.|
| **osDiskBlobURI** |Opcionalmente, este script también acepta un identificador URI de Blob Storage para `osDisk`. |
| **dataDiskBlobURIs** |Opcionalmente, este script también acepta una matriz de identificadores URI de Blob Storage para agregar discos de datos a la imagen. |

## <a name="add-a-vm-image-through-the-portal"></a>Agregación de una imagen de máquina virtual a través del portal

> [!NOTE]
> Con este método, debe crear el elemento de Marketplace por separado.

Debe poderse hacer referencia a las imágenes mediante un identificador URI de Blob Storage. Prepare una imagen de sistema operativo Windows o Linux en formato VHD (no VHDX) y, a continuación, cargue la imagen en una cuenta de almacenamiento en Azure o Azure Stack. Si la imagen ya se ha cargado en Blob Storage en Azure o Azure Stack, puede omitir el paso 1.

1. [Carga de una imagen de máquina virtual Windows en Azure para implementaciones de Resource Manager](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) o, para una imagen de Linux, siga las instrucciones descritas en el artículo [Implementación de máquinas virtuales Linux en Azure Stack](azure-stack-linux.md). Antes de cargar la imagen, es importante tener en cuenta los siguientes factores:

   * Es más eficaz cargar una imagen en Blob Storage de Azure Stack que en Azure Blob Storage porque se tarda menos tiempo en insertar la imagen en el repositorio de imágenes de Azure Stack. 
   
   * Al cargar la [imagen de máquina virtual Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/), asegúrese de sustituir el paso **Inicio de sesión en Azure** por el paso [Configuración del entorno de PowerShell del operador de Azure Stack](azure-stack-powershell-configure-admin.md).  

   * Anote el identificador URI donde cargará la imagen de Blob Storage. El identificador URI de Blob Storage tiene el siguiente formato: *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;*.vhd.

   * Para que sea posible acceder al blob de manera anónima, vaya al contenedor de blobs de la cuenta de almacenamiento donde se cargó el disco duro virtual de la imagen de máquina virtual. Seleccione **Blob** y, a continuación, seleccione **Directiva de acceso**. Opcionalmente, puede generar una firma de acceso compartido para el contenedor e incluirla como parte del identificador URI del blob.

   ![Vaya a los blobs de la cuenta de almacenamiento.](./media/azure-stack-add-vm-image/image1.png)

   ![Establecimiento del acceso del blob en público](./media/azure-stack-add-vm-image/image2.png)

2. Inicie sesión como operador en Azure Stack. En el menú, seleccione **Más servicios** > **Proveedores de recursos**. A continuación, seleccione **Proceso** > **Imágenes de máquina virtual** > **Agregar**.

3. En la hoja **Add a VM Image** (Agregar una imagen de máquina virtual), escriba el publicador, la oferta, la SKU y la versión de la imagen de máquina virtual. Estos segmentos de nombre hacen referencia a la imagen de máquina virtual en las plantillas de Resource Manager. Asegúrese de seleccionar el valor de **osType** correctamente. En **OD Disk Blob URI** (URI de blob de disco de OD), escriba el URI de blob donde se cargó la imagen. A continuación, seleccione **Crear** para empezar a crear la imagen de máquina virtual.
   
   ![Inicio de la creación de la imagen](./media/azure-stack-add-vm-image/image4.png)

   Cuando la imagen se crea correctamente, el estado de la imagen de máquina virtual cambia a **Correcto**.

4. Para que la imagen de máquina virtual esté disponible con más facilidad para su consumo por el usuario en la interfaz de usuario, es una buena idea [crear un elemento de Marketplace](azure-stack-create-and-publish-marketplace-item.md).

## <a name="next-steps"></a>Pasos siguientes

[Aprovisionamiento de una máquina virtual](azure-stack-provision-vm.md)
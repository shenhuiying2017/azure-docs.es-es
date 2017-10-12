---
title: "Agregación de la imagen de máquina virtual predeterminada en Marketplace de Azure Stack | Microsoft Docs"
description: "Agregue la imagen predeterminada de máquina virtual Windows Server 2016 en Marketplace de Azure Stack."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/10/2017
ms.author: sngun
ms.openlocfilehash: 43781cb025865df1d228376f57412f3d482d3ad0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="add-the-windows-server-2016-vm-image-to-the-azure-stack-marketplace"></a>Agregación de la imagen de máquina virtual Windows Server 2016 en Marketplace de Azure Stack

De forma predeterminada, no hay ninguna imagen de máquina virtual disponible en Marketplace de Azure Stack. El operador de Azure Stack debe agregar una imagen en Marketplace antes de que los usuarios puedan usarla. Puede agregar la imagen de Windows Server 2016 en Marketplace de Azure Stack mediante uno de los dos métodos siguientes:

* [Agregar la imagen mediante la descarga desde Azure Marketplace](#add-the-image-by-downloading-it-from-the-Azure-marketplace): utilice esta opción si está trabajando en un escenario conectado y ha registrado la instancia de Azure Stack en Azure.

* [Agregar la imagen mediante el uso de PowerShell](#add-the-image-by-using-powershell): utilice esta opción si ha implementado Azure Stack en un escenario sin conexión o en escenarios con conectividad limitada.

## <a name="add-the-image-by-downloading-it-from-the-azure-marketplace"></a>Agregación de la imagen mediante la descarga de Azure Marketplace

1. Después de implementar Azure Stack, inicie sesión en Azure Stack Development Kit.

2. Haga clic en **Más servicios** > **Marketplace Management** (Administración de Marketplace)  > **Add from Azure** (Agregar desde Azure). 

3. Busque la imagen **Windows Server 2016 Datacenter – Eval** > haga clic en **Descargar**

   ![Descarga de la imagen desde Azure](media/azure-stack-add-default-image/download-image.png)

Una vez finalizada la descarga, la imagen se agrega a la hoja **Marketplace Management** y se pone a disposición desde la hoja **Máquinas virtuales**.

## <a name="add-the-image-by-using-powershell"></a>Agregación de la imagen mediante el uso de PowerShell

### <a name="prerequisites"></a>Requisitos previos 

Implemente los siguientes requisitos previos desde el [kit de desarrollo](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) o desde un cliente externo basado en Windows, si está [conectado a través de VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Instale los [módulos de Azure PowerShell compatibles con Azure Stack](azure-stack-powershell-install.md).  

* Descargue las [herramientas necesarias para trabajar con Azure Stack](azure-stack-powershell-download.md).  

* Vaya a https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016 y descargue la evaluación de Windows Server 2016. Cuando se le solicite, seleccione la versión **ISO** de la descarga. Tome nota de la ruta de acceso a la ubicación de la descarga, que se utilizará más adelante en estos pasos. Este paso requiere conectividad a Internet.  

Ahora, ejecute los pasos siguientes para agregar la imagen en Marketplace de Azure Stack:
   
1. Importe los módulos Connect y ComputeAdmin de Azure Stack mediante los comandos siguientes:

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
   
   # Create the Azure Stack operator's AzureRM environment by using the following cmdlet:
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
   
3. Agregar la imagen de Windows Server 2016 en Marketplace de Azure Stack (asegúrese de reemplazar la *Ruta_de_acceso_a_ISO* por la ruta de acceso al archivo ISO WS2016 que descargó):

   ```PowerShell
   $ISOPath = "<Fully_Qualified_Path_to_ISO>"

   # Add a Windows Server 2016 Evaluation VM Image.
   New-AzsServer2016VMImage `
     -ISOPath $ISOPath

   ```

Para asegurarse de que la imagen de máquina virtual Windows Server 2016 tiene la actualización acumulativa más reciente, incluya el parámetro `IncludeLatestCU` al ejecutar el cmdlet `New-AzsServer2016VMImage`. Consulte la sección [Parámetros](#parameters) para obtener información acerca de los parámetros permitidos para el cmdlet `New-AzsServer2016VMImage`. Se tarda aproximadamente una hora en publicar la imagen en Marketplace de Azure Stack. 

## <a name="parameters"></a>parameters

|Parámetros de New-AzsServer2016VMImage|¿Necesario?|Descripción|
|-----|-----|------|
|ISOPath|Sí|La ruta de acceso completa a la imagen ISO de Windows Server 2016 descargada.|
|Net35|No|Este parámetro permite instalar el runtime de .NET 3.5 en la imagen de Windows Server 2016. De forma predeterminada, este valor se establece en true.|
|Versión|No|Este parámetro le permite elegir si desea agregar una imagen de Windows Server 2016 **Básica** o **Completa** o **Ambas**. De forma predeterminada, este valor se establece en "Full".|
|VHDSizeInMB|No|Establece el tamaño (en MB) de la imagen de disco duro virtual que se va a agregar a su entorno de Azure Stack. De forma predeterminada, este valor se establece en 40.960 MB.|
|CreateGalleryItem|No|Especifica si se debe crear un elemento de Marketplace para la imagen de Windows Server 2016. De forma predeterminada, este valor se establece en true.|
|location |No |Especifica la ubicación en la que se debe publicar la imagen de Windows Server 2016.|
|IncludeLatestCU|No|Establezca este modificador para aplicar la actualización acumulativa más reciente de Windows Server 2016 en el nuevo disco duro virtual.|
|CUUri |No |Establezca este valor para elegir la actualización acumulativa de Windows Server 2016 desde un identificador URI concreto. |
|CUPath |No |Establezca este valor para elegir la actualización acumulativa de Windows Server 2016 desde una ruta de acceso local. Esta opción es útil si ha implementado la instancia de Azure Stack en un entorno desconectado.|

## <a name="next-steps"></a>Pasos siguientes

[Aprovisionamiento de una máquina virtual](azure-stack-provision-vm.md)

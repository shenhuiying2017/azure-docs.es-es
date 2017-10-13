---
title: Paso de credenciales a Azure con DSC | Microsoft Docs
description: "Información general sobre cómo pasar credenciales de forma segura a máquinas virtuales de Azure con la configuración de estado deseado de PowerShell"
services: virtual-machines-windows
documentationcenter: 
author: zjalexander
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
keywords: 
ms.assetid: ea76b7e8-b576-445a-8107-88ea2f3876b9
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 09/15/2016
ms.author: zachal
ms.openlocfilehash: acd768c0219ec23c0453a65c575faf5213d9c616
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="passing-credentials-to-the-azure-dsc-extension-handler"></a>Cómo pasar las credenciales al controlador de extensiones de la DSC de Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

En este artículo se trata la extensión de la configuración de estado deseado de Azure. En [Introducción al controlador de extensiones de configuración de estado deseado de Azure](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) encontrará información general sobre el controlador de extensiones de la DSC. 

## <a name="passing-in-credentials"></a>Transmisión de credenciales
Como parte del proceso de configuración, es posible que deba configurar las cuentas de usuario, obtener acceso a los servicios o instalar un programa en un contexto de usuario. Para llevar a cabo estas acciones, debe proporcionar las credenciales. 

La DSC permite las configuraciones parametrizadas en las que las credenciales se pasan a la configuración y se almacenan de forma segura en archivos MOF. El controlador de extensiones de Azure simplifica la administración de credenciales, ya que administra los certificados de forma automática. 

Observe el siguiente script de configuración de DSC, que crea una cuenta de usuario local con la contraseña especificada:

*user_configuration.ps1*

```
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )    
    Node localhost {       
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        } 
    }  
} 
```

Es importante incluir *node localhost* como parte de la configuración. Si falta esta instrucción, lo que sigue no funcionará ya que el controlador de extensiones busca específicamente la instrucción node localhost. También es importante incluir la conversión de tipo *[PsCredential]*, ya que este tipo específico desencadena la extensión para cifrar la credencial. 

Publique este script en el almacenamiento de blobs:

`Publish-AzureVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Establezca la extensión de DSC de Azure y proporcione la credencial:

```
$configurationName = "Main"
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = "user_configuration.ps1.zip"
$vm = Get-AzureVM "example-1"

$vm = Set-AzureVMDSCExtension -VM $vm -ConfigurationArchive $configurationArchive 
-ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzureVM
```
## <a name="how-credentials-are-secured"></a>¿Cómo se protegen las credenciales?
Al ejecutar este código, se solicita una credencial. Una vez proporcionada, se almacena brevemente en la memoria. Cuando se publica con el cmdlet `Set-AzureVmDscExtension` , se transmite a través de HTTPS a la máquina virtual, donde Azure la almacena cifrada en disco mediante el certificado de máquina virtual local. Se descifra brevemente en la memoria y se vuelve a cifrar para pasarla a DSC.

Este comportamiento no es el mismo que cuando se [usan configuraciones seguras sin el controlador de extensiones](https://msdn.microsoft.com/powershell/dsc/securemof). El entorno de Azure ofrece una manera de transmitir datos de configuración de forma segura mediante certificados. Cuando se utiliza el controlador de extensiones DSC, no es necesario proporcionar $CertificatePath o una entrada $CertificateID / $Thumbprint en ConfigurationData.

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre el controlador de extensiones DSC de Azure, consulte [Introducción al controlador de extensiones de configuración de estado deseado de Azure](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Examine la [plantilla de Azure Resource Manager para la extensión de DSC](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Para más información sobre DSC de PowerShell, [visite el centro de documentación de PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

Para buscar otras funcionalidades que se puedan administrar con DSC de PowerShell, [examine la Galería de PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) para encontrar más recursos de DSC.


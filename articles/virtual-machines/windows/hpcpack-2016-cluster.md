---
title: Clúster de HPC Pack 2016 en Azure | Microsoft Docs
description: Aprenda a implementar un clúster de HPC Pack 2016 en Azure.
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 3dde6a68-e4a6-4054-8b67-d6a90fdc5e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 03/09/2018
ms.author: danlep
ms.openlocfilehash: 91f067de33d1ff4bc272773e3db49de47fac2feb
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32190019"
---
# <a name="deploy-an-hpc-pack-2016-cluster-in-azure"></a>Implementación de un clúster de HPC Pack 2016 en Azure

Siga los pasos de este artículo para implementar un clúster de [Microsoft HPC Pack 2016 Update 1](https://technet.microsoft.com/library/cc514029) en máquinas virtuales de Azure. HPC Pack es la solución HPC gratuita de Microsoft que se basa en las tecnologías de Microsoft Azure y Windows Server, y admite cargas de trabajo HPC.

Use una de las [plantillas de Azure Resource Manager](https://github.com/MsHpcPack/HPCPack2016) para implementar el clúster de HPC Pack 2016. Existen varias opciones de topología de clúster con distintos números y tipos de nodos principales de clúster y con nodos de ejecución.

## <a name="prerequisites"></a>requisitos previos

### <a name="pfx-certificate"></a>Certificado PFX

Un clúster de Microsoft HPC Pack 2016 requiere un certificado de intercambio de información Personal (PFX) para proteger la comunicación entre los nodos HPC. El certificado debe cumplir los siguientes requisitos:

* Debe tener una clave privada con funcionalidad de intercambio de claves.
* El uso de la clave incluye firma digital y cifrado de claves.
* La clave mejorada incluye autenticación de cliente y autenticación de servidor.

Si aún no cuenta con un certificado que cumpla estos requisitos, puede solicitarlo a una entidad de certificación. Como alternativa, use los siguientes comandos para generar el certificado autofirmado en función del sistema operativo en el que se ejecuta el comando. Luego, exporte el certificado como un archivo con formato PFX protegido por contraseña con una clave privada.

* **Para Windows 10 o Windows Server 2016**, ejecute el cmdlet de PowerShell integrado **New-SelfSignedCertificate** como sigue:

  ```PowerShell
  New-SelfSignedCertificate -Subject "CN=HPC Pack 2016 Communication" -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2") -CertStoreLocation cert:\CurrentUser\My -KeyExportPolicy Exportable -NotAfter (Get-Date).AddYears(5)
  ```
* **En el caso de sistemas operativos previos a Windows 10 o Windows Server 2016**, descargue el [generador de certificados autofirmados](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) del Centro de scripts de Microsoft. Extraiga su contenido y ejecute los siguientes comandos en un símbolo del sistema de PowerShell:

    ```PowerShell 
    Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
  
    New-SelfSignedCertificateEx -Subject "CN=HPC Pack 2016 Communication" -KeySpec Exchange -KeyUsage "DigitalSignature,KeyEncipherment" -EnhancedKeyUsage "Server Authentication","Client Authentication" -StoreLocation CurrentUser -Exportable -NotAfter (Get-Date).AddYears(5)
    ```

Una vez que el certificado se crea en el almacén de usuario actual, use el complemento de certificados para exportar el certificado como un archivo PFX protegido por contraseña con una clave privada. También puede exportar el certificado con el cmdlet [Export-Pfxcertificate](/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps) de PowerShell.

### <a name="upload-certificate-to-an-azure-key-vault"></a>Carga del certificado en una instancia de Azure Key Vault

Antes de implementar el clúster HPC, cargue el certificado PFX en un [almacén de claves de Azure](../../key-vault/index.yml) como un secreto y registre la siguiente información para usarla durante la implementación: **nombre del almacén**, **grupo de recursos del almacén**, **dirección URL de certificado** y **huella digital del certificado**.

A continuación, aparece un script de PowerShell de ejemplo para cargar el certificado, crear el almacén de claves y generar la información requerida. Para más información sobre cómo cargar un certificado en un almacén de claves de Azure, consulte [Introducción a Azure Key Vault](../../key-vault/key-vault-get-started.md).

```powershell
#Give the following values
$VaultName = "mytestvault"
$SecretName = "hpcpfxcert"
$VaultRG = "myresourcegroup"
$location = "westus"
$PfxFile = "c:\Temp\mytest.pfx"
$Password = "yourpfxkeyprotectionpassword"
#Validate the pfx file
try {
    $pfxCert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $PfxFile, $Password
}
catch [System.Management.Automation.MethodInvocationException]
{
    throw $_.Exception.InnerException
}
$thumbprint = $pfxCert.Thumbprint
$pfxCert.Dispose()
# Create and encode the JSON object
$pfxContentBytes = Get-Content $PfxFile -Encoding Byte
$pfxContentEncoded = [System.Convert]::ToBase64String($pfxContentBytes)
$jsonObject = @"
{
"data": "$pfxContentEncoded",
"dataType": "pfx",
"password": "$Password"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
#Create an Azure key vault and upload the certificate as a secret
$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
$rg = Get-AzureRmResourceGroup -Name $VaultRG -Location $location -ErrorAction SilentlyContinue
if($null -eq $rg)
{
    $rg = New-AzureRmResourceGroup -Name $VaultRG -Location $location
}
$hpcKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $VaultRG -Location $location -EnabledForDeployment -EnabledForTemplateDeployment
$hpcSecret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secret
"The following Information will be used in the deployment template"
"Vault Name             :   $VaultName"
"Vault Resource Group   :   $VaultRG"
"Certificate URL        :   $($hpcSecret.Id)"
"Certificate Thumbprint :   $thumbprint"

```


## <a name="supported-topologies"></a>Topologías admitidas

Elija una de las [plantillas de Azure Resource Manager](https://github.com/MsHpcPack/HPCPack2016) para implementar el clúster de HPC Pack 2016. Las siguientes son arquitecturas de alto nivel de tres topologías de clúster de ejemplo. Las topologías de alta disponibilidad incluyen varios nodos principales de clúster.

1. Clúster de alta disponibilidad con dominio de Active Directory

    ![Clúster de alta disponibilidad en dominio de AD](./media/hpcpack-2016-cluster/haad.png)


2. Clúster de alta disponibilidad sin dominio de Active Directory

    ![Clúster de alta disponibilidad sin dominio de AD](./media/hpcpack-2016-cluster/hanoad.png)

3. Clúster con un único nodo principal

   ![Clúster con un único nodo principal](./media/hpcpack-2016-cluster/singlehn.png)


## <a name="deploy-a-cluster"></a>Implementación de un clúster

Para crear el clúster, elija una plantilla y haga clic en **Implementar en Azure**. En el [portal de Azure](https://portal.azure.com), especifique para la plantilla los parámetros que se describen en los pasos siguientes. Cada plantilla crea todos los recursos de Azure necesarios para la infraestructura de clúster HPC. Los recursos incluyen una red virtual de Azure, una dirección IP pública, un equilibrador de carga (solo para un clúster de alta disponibilidad), interfaces de red, conjuntos de disponibilidad, cuentas de almacenamiento y máquinas virtuales.

### <a name="step-1-select-the-subscription-location-and-resource-group"></a>Paso 1: Selección de la suscripción, la ubicación y el grupo de recursos

La **suscripción** y la **ubicación** deben ser las mismas que ha especificado al cargar el certificado PFX (consulte los requisitos previos). Le recomendamos que cree un **grupo de recursos** distinto para la implementación.

### <a name="step-2-specify-the-parameter-settings"></a>Paso 2: Especificación de la configuración de parámetros

Escriba o modifique los valores de los parámetros de plantilla. Haga clic en el icono junto a cada parámetro para obtener información de ayuda. Consulte también la guía sobre [tamaños de máquina virtual disponibles](sizes.md).

Especifique los valores que registró en los requisitos previos para los siguientes parámetros: **Nombre de almacén**, **Vault resource group** (Grupo de recursos de almacén), **Dirección URL del certificado**, y **Huella digital del certificado**.

### <a name="step-3-review-terms-and-create"></a>Paso 3. Revisión de los términos legales y creación
Revise los términos y condiciones asociados con la plantilla. Si está de acuerdo, haga clic en **Comprar** para iniciar la implementación.

En función de la topología de clúster, la implementación puede tardar 30 minutos o más en completarse.

## <a name="connect-to-the-cluster"></a>Conexión al clúster
1. Después de implementar el clúster de HPC Pack, vaya al [portal de Azure](https://portal.azure.com). Haga clic en **Grupos de recursos** y busque el grupo de recursos en el que se implementó el clúster. Puede encontrar las máquinas virtuales de nodo principal.

    ![Nodos principales del clúster en el portal](./media/hpcpack-2016-cluster/clusterhns.png)

2. Haga clic en un nodo principal (en un clúster de alta disponibilidad, haga clic en cualquiera de los nodos principales). En **Overview** (Información general), puede encontrar la dirección IP pública o el nombre DNS completo del clúster.

    ![Configuración de la conexión del clúster](./media/hpcpack-2016-cluster/clusterconnect.png)

3. Haga clic en **Conectar** para iniciar sesión en cualquiera de los nodos principales con el nombre de usuario de administrador especificado mediante Escritorio remoto . Si el clúster implementado está en un dominio de Active Directory, el nombre de usuario tiene el formato \<NombreDominioPrivado>\\\<NombredeusuariodeAdministrador> (por ejemplo, hpc.local\hpcadmin).

## <a name="next-steps"></a>Pasos siguientes
* Envíe los trabajos al clúster. Consulte [Envío de trabajos a un clúster de HPC Pack en Azure](hpcpack-cluster-submit-jobs.md) y [Manage an HPC Pack 2016 cluster in Azure using Azure Active Directory](hpcpack-cluster-active-directory.md) (Administración de un clúster de HPC Pack 2016 en Azure mediante Azure Active Directory).


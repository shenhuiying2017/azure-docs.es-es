---
title: Validación de certificados de infraestructura de clave pública de Azure Stack para la implementación de sistemas integrados de Azure Stack | Microsoft Docs
description: Describe cómo validar certificados PKI de Azure Stack para sistemas integrados de Azure Stack. Incluye el uso de la herramienta Azure Stack Certificate Checker.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: cd917165804314f6ee4ee006e3f29263d8d4b4c5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="validate-azure-stack-pki-certificates"></a>Validación de certificados PKI de Azure Stack

La herramienta Azure Stack Readiness Checker que se describe en este artículo está disponible [en la Galería de PowerShell](https://aka.ms/AzsReadinessChecker). Puede usar esta herramienta para comprobar si los [certificados PKI generados](azure-stack-get-pki-certs.md) son válidos para la implementación previa. Cuando valide los certificados, debe dejar el tiempo suficiente para que se prueben los certificados y, en caso necesario, vuelvan a emitirse.

La herramienta Readiness Checker realiza las siguientes validaciones de certificados:

- **Lectura de PFX**  
    Comprueba si el archivo PFX es válido y la contraseña es correcta, y advierte si la información pública no está protegida con contraseña. 
- **Algoritmo de firma**  
    Comprueba que el algoritmo de firma no sea SHA1.
- **Clave privada**  
    Comprueba si la clave privada está presente y se exporta con el atributo de máquina local. 
- **Cadena de certificados**  
    Comprueba si la cadena de certificados está intacta junto con los certificados autofirmados.
- **Nombres DNS**  
    Comprueba que el SAN contiene nombres DNS correspondientes a cada punto de conexión o si hay un comodín compatible.
- **Uso de la clave**  
    Comprueba si el uso de la clave contiene la firma digital y el cifrado de la clave, y si el uso mejorado de clave contiene la autenticación del servidor y del cliente.
- **Tamaño de la clave**  
    Comprueba si tamaño de la clave es 2048 o más.
- **Orden de la cadena**  
    Comprueba que el orden de los demás certificados que conforman la cadena es correcto.
- **Otros certificados**  
    Garantiza que ningún otro certificado se haya empaquetado en PFX, solo el certificado de hoja pertinente y su cadena.

> [!IMPORTANT]  
> El certificado PKI es un archivo PFX y una contraseña que deben tratarse como información confidencial.

## <a name="prerequisites"></a>requisitos previos

El sistema debe cumplir los siguientes requisitos previos para poder validar los certificados PKI de una implementación de Azure Stack:

- Microsoft Azure Stack Readiness Checker
- Certificados SSL exportados siguiendo las [instrucciones de preparación](azure-stack-prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 o Windows Server 2016

## <a name="perform-certificate-validation"></a>Realización de la validación de certificados

Siga estos pasos para preparar y validar los certificados PKI de Azure Stack:

1. Instale AzsReadinessChecker desde un símbolo del sistema de PowerShell (5.1 o superior) mediante la ejecución del siguiente cmdlet:

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker 
    ````

2. Cree la estructura de directorios del paquete. En el ejemplo siguiente, puede cambiar `<c:\certificates>` por la ruta de un nuevo directorio de su elección.

    ````PowerShell  
    New-Item C:\Certificates -ItemType Directory

    $directories = 'ACSBlob','ACSQueue','ACSTable','ADFS','Admin Portal','ARM Admin','ARM Public','Graph','KeyVault','KeyVaultInternal','Public Portal' 

    $destination = 'c:\certificates' 

    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}  
    ````

 - Sitúe los certificados en los directorios apropiados que creó en el paso anterior. Por ejemplo:   
    - c:\certificates\ACSBlob\CustomerCertificate.pfx 
    - c:\certificates\Certs\Admin Portal\CustomerCertificate.pfx 
    - c:\certificates\Certs\ARM Admin\CustomerCertificate.pfx 
    - etcétera. 

3. En la ventana de PowerShell, ejecute:

    ````PowerShell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
    ````

4. Revise la salida para verificar que todos los certificados pasan las pruebas. Por ejemplo: 

    ````PowerShell
    AzsReadinessChecker v1.1803.405.3 started
    Starting Certificate Validation

    Starting Azure Stack Certificate Validation 1.1803.405.3
    Testing: ARM Public\ssl.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: ACSBlob\ssl.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Finished Certificate Validation

    AzsReadinessChecker Log location: C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Report location (for OEM): C:\AzsReadinessChecker\AzsReadinessReport.json
    AzsReadinessChecker Completed
    ````

### <a name="known-issues"></a>Problemas conocidos

**Síntoma**: se omiten las pruebas.

**Causa**: AzsReadinessChecker omite varias pruebas si no se cumple una dependencia:

 - Otros certificados se omiten si se produce un error en la cadena de certificados.

    ````PowerShell  
    Testing: ACSBlob\singlewildcard.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: Fail
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: Skipped
    Details:
    The certificate records '*.east.azurestack.contoso.com' do not contain a record that is valid for '*.blob.east.azurestack.contoso.com'. Please refer to the documentation for how to create the required certificate file.
    The Other Certificates check was skipped because Cert Chain and/or DNS Names failed. Follow the guidance to remediate those issues and recheck. 
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Finished Certificate Validation

    AzsReadinessChecker Log location: C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Report location (for OEM): C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Completed
    ````

**Resolución**: siga las instrucciones de la herramienta que se describen en la sección de detalles de los conjuntos de pruebas de cada certificado.

## <a name="using-validated-certificates"></a>Uso de certificados validados

Una vez que AzsReadinessChecker valide los certificados, podrá utilizarlos en la implementación de Azure Stack o en la rotación de secretos de Azure Stack. 

 - En el caso de la implementación, transfiera de forma segura los certificados al ingeniero de implementación para que pueda copiarlos en el host de la implementación tal y como se especifica en la [documentación de los requisitos de infraestructura de clave pública (PKI) de Azure Stack](azure-stack-pki-certs.md).
 - En el caso de la rotación de secretos, puede utilizar los certificados para actualizar los certificados antiguos de los puntos de conexión de la infraestructura pública del entorno de Azure Stack tal y como se indica en la [documentación de rotación de secretos de Azure Stack](azure-stack-rotate-secrets.md).

## <a name="next-steps"></a>Pasos siguientes

[Integración de identidades de centros de datos](azure-stack-integrate-identity.md)

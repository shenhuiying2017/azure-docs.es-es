---
title: "Validación de certificados de infraestructura de clave pública de Azure Stack para la implementación de sistemas integrados de Azure Stack | Microsoft Docs"
description: "Describe cómo validar certificados PKI de Azure Stack para sistemas integrados de Azure Stack."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: jeffgilb
ms.reviewer: ppacent
ms.openlocfilehash: 86f1b889d83905abfb5ddab2e82f32922409ff5f
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/24/2018
---
# <a name="validate-azure-stack-pki-certificates"></a>Validación de certificados PKI de Azure Stack
La herramienta de comprobación de certificados de Azure Stack que se describe en este artículo la proporciona el OEM incluido con el archivo deploymentdata.json para validar que los [certificados PKI generados](azure-stack-get-pki-certs.md) son adecuados para la implementación previa. Los certificados se deben validar con tiempo suficiente para probarlos y volverlos a emitir si es necesario. 

La herramienta de comprobación de certificados (Certchecker) realiza las siguientes comprobaciones:

- **Lectura de PFX**. Comprueba si el archivo PFX es válido, la contraseña correcta y advierte si información pública no está protegida con contraseña. 
- **Algoritmo de firma**. Comprueba que el algoritmo de firma no sea SHA1. 
- **Clave privada**. Comprueba que la clave privada está presente y se exporta con el atributo de máquina Local. 
- **Cadena de certificados**. Comprueba que la cadena de certificados está intacta y que incluye los certificados autofirmados. 
- **Nombres DNS**. Comprueba que el SAN contiene nombres DNS correspondientes a cada punto de conexión o si hay un comodín compatible. 
- **Uso de la clave**. Comprueba que el uso de clave contiene la firma digital y el cifrado de la clave, y que el uso mejorado de clave contiene la autenticación de servidor y de cliente. 
- **Tamaño de la clave**. Comprueba que tamaño de la clave sea de 2048 o más. 
- **Orden de la cadena**. Comprueba que el orden de los otros certificados que conforman la cadena es correcto. 
- **Otros certificados**. Garantiza que ningún otro certificado se haya empaquetado en PFX, solo el certificado de hoja pertinente y su cadena. 
- **Sin perfil**. Comprueba que un nuevo usuario puede cargar los datos PFX sin un perfil de usuario cargado, lo que imita el comportamiento de las cuentas de gMSA durante el servicio de certificado.   

> [!IMPORTANT]
> El archivo PFX y la contraseña del certificado PKI deben tratarse como información confidencial.

## <a name="prerequisites"></a>requisitos previos
El sistema debe cumplir los siguientes requisitos previos antes de validar los certificados PKI para la implementación de Azure Stack:
- CertChecker (en PartnerToolKit, en \utils\certchecker)
- Certificados SSL exportados siguiendo las [instrucciones de preparación](prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 o Windows Server 2016

## <a name="perform-certificate-validation"></a>Realización de la validación de certificados
Siga estos pasos para preparar y validar los certificados PKI de Azure Stack: 

1. Extraiga el contenido del <partnerToolkit>\utils\certchecker en un nuevo directorio, por ejemplo, **c:\certchecker**.

2. Abra PowerShell como administrador y cambie el directorio a la carpeta certchecker:

  ```powershell
  cd c:\certchecker
  ```
 
3. Cree una estructura de directorios para los certificados ejecutando los siguientes comandos de PowerShell:

  ```powershell 
  $directories = "ACS","ADFS","Admin Portal","ARM Admin","ARM Public","Graph","KeyVault","KeyVaultInternal","Public Portal" 
  $destination = '.\certs' 
  $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}  
  ```

  >  [!NOTE]
  >  Si el proveedor de identidades para la implementación de Azure Stack es Azure AD, quite los directorios **ADFS** y **Graph**. 

4. Coloque los certificados en los directorios adecuados creados en el paso anterior, por ejemplo: 
  - c:\certchecker\Certs\ACS\CustomerCertificate.pfx  
  - c:\certchecker\Certs\Admin Portal\CustomerCertificate.pfx  
  - c:\certchecker\Certs\ARM Admin\CustomerCertificate.pfx  
  - etcétera. 

5. Copie **deploymentdata.json** en el directorio **c:\certchecker**.

6. Ejecute los siguientes comandos en la consola de PowerShell: 

  ```powershell
  $password = Read-Host -Prompt "Enter PFX Password" -AsSecureString 
  .\CertChecker.ps1 -CertificatePath .\Certs\ -pfxPassword $password -deploymentDataJSONPath .\DeploymentData.json  
  ```

7. El resultado debe contener Aceptar en todos los certificados y todos los atributos comprobados: 

  ```powershell
  Starting Azure Stack Certificate Validation 1.1802.221.1
  Testing: ADFS\ContosoSSL.pfx
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Testing: KeyVaultInternal\ContosoSSL.pfx
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Testing: ACS\ContosoSSL.pfx
  WARNING: Pre-1803 certificate structure. The folder structure for Azure Stack 1803 and above is: ACSBlob, ACSQueue, ACSTable instead of ACS folder. Refer to deployment documentation for further informat
  ion.
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Detailed log can be found C:\CertChecker\CertChecker.log 
  ```

### <a name="known-issues"></a>Problemas conocidos 
**Síntoma**: Certchecker se cierra prematuramente y recibe el error siguiente: 
> Con error

> Detalles: Este comando no se puede ejecutar debido al error: El nombre del directorio no es válido. 

**Causa**: ejecutar certchecker.ps1 desde una carpeta restrictiva, por ejemplo, c:\temp o %temp%. 

**Resolución**: mueva la herramienta certchecker al nuevo directorio, por ejemplo, C:\CertChecker. 


**Síntoma**: Certchecker emite una advertencia sobre el uso de Pre-1803 (como en el ejemplo anterior del paso 7):

> [!WARNING]
> Estructura del certificado Pre-1803. La carpeta de la estructura de Azure Stack 1803 y superior es: ACSBlob, ACSQueue, ACSTable en lugar de ACS folder. Para obtener más información, consulte la documentación de implementación.

**Causa**: CertChecker ha detectado el uso de una sola carpeta ACS; esto es correcto para las implementaciones antes de 1803. Para la versión 1803 de Azure Stack e implementaciones posteriores, la estructura de carpetas cambia a ACSTable, ACSQueue, ACSBlob. Certchecker ya se ha actualizado para admitir esta funcionalidad.

**Resolución**: si se implementa la versión 1802, se requiere ninguna acción. Si implementa la 1803 y posterior, reemplace ACS con ACSTable, ACSQueue, ACSBlob, y copie los certificados ACS en esas carpetas.

**Síntoma**: se omiten las pruebas.

**Causa**: CertChecker omite determinadas pruebas si no se cumple una dependencia:
- Otros certificados se omiten si se produce un error en la cadena de certificados.
- No se omite ningún perfil si:
  - Hay una directiva de seguridad que restringe la capacidad de crear un usuario temporal y ejecutar PowerShell como ese usuario.
  - Se produce un error en la comprobación de la clave privada.

**Resolución**: siga las instrucciones de las herramientas en la sección de detalles en cada conjunto de pruebas de certificados.


## <a name="prepare-deployment-script-certificates"></a>Preparación de los certificados de script de implementación 
Como paso final, todos los certificados que ha preparado deben ubicarse en los directorios adecuados del host de implementación. En el host de implementación, cree una carpeta denominada Certificates** y coloque los archivos de certificado exportados en las subcarpetas correspondientes especificadas en la sección de [certificados obligatorios](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates):

```
\Certificates
\ACS\ssl.pfx
\Admin Portal\ssl.pfx
\ARM Admin\ssl.pfx
\ARM Public\ssl.pfx
\KeyVault\ssl.pfx
\KeyVaultInternal\ssl.pfx
\Public Portal\ssl.pfx
\ADFS\ssl.pfx*
\Graph\ssl.pfx*
```

<sup>*</sup> Los certificados se marcan con un asterisco * solo son necesarios cuando se utiliza AD FS como almacén de identidades.


## <a name="next-steps"></a>pasos siguientes
[Integración de identidades de centros de datos](azure-stack-integrate-identity.md)
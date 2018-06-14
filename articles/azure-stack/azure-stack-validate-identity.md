---
title: Validación de la identidad de Azure en Azure Stack | Microsoft Docs
description: Use Azure Stack Readiness Checker para validar la identidad de Azure.
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
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: fe5e7281cbe01ad11f667729df344f91ef1327d2
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937563"
---
# <a name="validate-azure-identity"></a>Validación de la identidad de Azure 
Use la herramienta Azure Stack Readiness Checker (AzsReadinessChecker) para validar que su instancia de Azure Active Directory (Azure AD) está lista para usarse con Azure Stack. Valide la solución de identidad de Azure antes de empezar la implementación de Azure Stack.  

La herramienta Readiness Checker valida:
 - Azure Active Directory (Azure AD) como proveedor de identidades de Azure Stack.
 - La cuenta de Azure AD que planea usar puede iniciar sesión como administrador global de Azure Active Directory. 

La validación garantiza que el entorno está listo para que Azure Stack almacene información sobre los usuarios, las aplicaciones, los grupos y las entidades de servicio de Azure Stack en Azure AD.

## <a name="get-the-readiness-checker-tool"></a>Obtención de la herramienta Readiness Checker
Descargue la versión más reciente de Azure Stack Readiness Checker (AzsReadinessChecker) desde [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>requisitos previos
Deben cumplirse los siguientes requisitos previos.

**Equipo donde se ejecuta la herramienta:**
 - Windows 10 o Windows Server 2016, con conectividad a internet.
 - PowerShell 5.1 o posterior. Para comprobar la versión, ejecute el siguiente cmd de PowerShell y luego revise la versión *principal* y las versiones *secundarias*:  

   > `$PSVersionTable.PSVersion`
 - Configure [PowerShell para Azure Stack](azure-stack-powershell-install.md). 
 - Versión más reciente de la herramienta [Microsoft Azure Stack Readiness Checker](https://aka.ms/AzsReadinessChecker).

**Entorno de Azure Active Directory:**
 - Identifique la cuenta de Azure AD que va a usar con Azure Stack y compruebe que es administrador global de Azure Active Directory.
 - Identifique el nombre del inquilino de Azure AD. El nombre del inquilino debe ser el nombre de dominio *principal* de Azure Active Directory. Por ejemplo, *contoso.onmicrosoft.com*. 
 - Identifique el valor de AzureEnvironment que va a usar: *AzureCloud*, *AzureGermanCloud* o *AzureChinaCloud*.

## <a name="validate-azure-identity"></a>Validación de la identidad de Azure 
1. En un equipo que cumpla los requisitos previos, abra un símbolo del sistema administrativo de PowerShell y ejecute el siguiente comando para instalar AzsReadinessChecker:  

   > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. Desde el símbolo del sistema de PowerShell, ejecute el siguiente código para establecer *$serviceAdminCredential* como administrador de servicios del inquilino de Azure AD.  Reemplace *serviceadmin@contoso.onmicrosoft.com* por la cuenta y el inquilino. 
   > `$serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant"` 

3. Desde el símbolo del sistema de PowerShell, ejecute el siguiente código para iniciar la validación de Azure AD. 
   - Especifique el valor de AzureEnvironment como *AzureCloud*, *AzureGermanCloud* o *AzureChinaCloud*.  
   - Especifique el nombre del inquilino de Azure Active Directory que quiere reemplazar *contoso.onmicrosoft.com*. 

   > `Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment AzureCloud -AADDirectoryTenantName contoso.onmicrosoft.com`
4. Después de ejecutar la herramienta, revise el resultado. Confirme que el estado del inicio de sesión y los requisitos de instalación es **Correcto**. Una validación correcta tiene un aspecto similar al de la siguiente imagen: 
 
![ejecución de la validación](./media/azure-stack-validate-identity/validation.png)


## <a name="report-and-log-file"></a>Informe y archivo de registro
Cada vez que se ejecuta la validación, los resultados se registran en **AzsReadinessChecker.log** y **AzsReadinessCheckerReport.json**. La ubicación de estos archivos se muestra con los resultados de validación de PowerShell.

Estos archivos pueden ayudar a compartir el estado de validación antes de implementar Azure Stack o investigar problemas de validación.  Ambos archivos conservan los resultados de cada comprobación de validación posterior. El informe ofrece la confirmación del equipo de implementación de la configuración de identidad. El archivo de registro puede ayudar al equipo de implementación o de soporte técnico a investigar los problemas de validación. 

De forma predeterminada, los dos archivos se escriben en *C:\Users\<nombre de usuario>\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
 - Use el parámetro **-OutputPath** ***&lt;ruta de acceso&gt;***  al final de la línea de comandos de ejecución para especificar otra ubicación para el informe.   
 - Use el parámetro **-CleanReport** al final del comando de ejecución para borrar la información de *AzsReadinessCheckerReport.json*.  sobre ejecuciones anteriores de la herramienta. 

Para obtener más información, vea [Informe de validación de Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Errores de validación
Si se producen errores en una comprobación de validación, los detalles sobre los errores se muestran en la ventana de PowerShell. La herramienta también registra información en el AzsReadinessChecker.log.

Los ejemplos siguientes ofrecen instrucciones sobre errores de validación comunes.

### <a name="expired-or-temporary-password"></a>Contraseña temporal o expirada 
 
![contraseña expirada](./media/azure-stack-validate-identity/expired-password.png)
**Causa**: no se pudo iniciar sesión en la cuenta porque la contraseña es temporal o ha expirado.     

**Resolución**: en PowerShell, ejecute el siguiente comando y siga las indicaciones para restablecer la contraseña.  
> `Login-AzureRMAccount`

O bien, inicie sesión en https://portal.azure.com con la cuenta y el usuario se verá obligados a cambiar la contraseña.
### <a name="unknown-user-type"></a>Tipo de usuario desconocido 
 
![usuario desconocido](./media/azure-stack-validate-identity/unknown-user.png)
**Causa**: la cuenta no puede iniciar sesión en el entorno de Azure Active Directory especificado (AADDirectoryTenantName). En este ejemplo, *AzureChinaCloud* se especifica como valor de *AzureEnvironment*.

**Resolución**: confirme que la cuenta es válida para el entorno de Azure especificado. En PowerShell, ejecute el siguiente comando para comprobar que la cuenta es válida para un entorno concreto: Login-AzureRmAccount – EnvironmentName AzureChinaCloud 
### <a name="account-is-not-an-administrator"></a>La cuenta no es de administrador 
 
![no de administrador](./media/azure-stack-validate-identity/not-admin.png)

**Causa**: aunque la cuenta puede iniciar sesión correctamente, la cuenta no es de administrador de Azure Active Directory (AADDirectoryTenantName).  

**Resolución**: inicie sesión en https://portal.azure.com con la cuenta, vaya a **Azure Active Directory** > **Usuarios** > *Seleccione el usuario* > **Rol del directorio** y asegúrese de que el usuario es **Administrador global**.  Si la cuenta es de usuario, vaya a **Azure Active Directory** >  **Nombres de dominio personalizado** y confirme que el nombre que especificó para *AADDirectoryTenantName* está marcado como nombre de dominio principal del directorio.  En este ejemplo, se trata de *contoso.onmicrosoft.com*. 

Azure Stack requiere que el nombre de dominio sea el nombre de dominio principal.

## <a name="next-steps"></a>Pasos siguientes
[Validación del registro de Azure](azure-stack-validate-registration.md)  
[Vista del informe de preparación](azure-stack-validation-report.md)  
[Consideraciones generales sobre la integración de Azure Stack](azure-stack-datacenter-integration.md)  


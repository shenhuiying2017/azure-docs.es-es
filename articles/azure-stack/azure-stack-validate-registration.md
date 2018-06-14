---
title: Validación del registro de Azure en Azure Stack | Microsoft Docs
description: Use Azure Stack Readiness Checker para validar el registro de Azure.
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
ms.openlocfilehash: 84eb1c08cc3f9ef104e2eb0b96ed397315c3f374
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937569"
---
# <a name="validate-azure-registration"></a>Validación del registro de Azure 
Use la herramienta Azure Stack Readiness Checker (AzsReadinessChecker) para validar que su suscripción de Azure está lista para usarse con Azure Stack. Valide el registro antes de empezar la implementación de Azure Stack. La herramienta Readiness Checker valida:
- La suscripción de Azure que usa es de tipo compatible. Las suscripciones deben ser un proveedor de servicios en la nube (CSP) o el contrato Enterprise (EA). 
- La cuenta que se usa para registrar la suscripción con Azure puede iniciar sesión en Azure y es propietario de la suscripción. 

Para obtener más información sobre el registro de Azure Stack, vea [Registro de Azure Stack en Azure](azure-stack-registration.md). 

## <a name="get-the-readiness-checker-tool"></a>Obtención de la herramienta Readiness Checker
Descargue la versión más reciente de Azure Stack Readiness Checker (AzsReadinessChecker) disponible en [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>requisitos previos
Deben cumplirse los siguientes requisitos previos.

**Equipo donde se ejecuta la herramienta:**
 - Windows 10 o Windows Server 2016, con conectividad a internet.
 - Azure PowerShell 5.1 o posterior. Para comprobar la versión, ejecute el siguiente cmd de PowerShell y luego revise la versión *principal* y las versiones *secundarias*:  

    >`$PSVersionTable.PSVersion` 
 - Configure [PowerShell para Azure Stack](azure-stack-powershell-install.md). 
 - Descargue la versión más reciente de la herramienta [Microsoft Azure Stack Readiness Checker](https://aka.ms/AzsReadinessChecker).  

**Entorno de Azure Active Directory:**
 - Identifique el nombre de usuario y la contraseña de una cuenta que sea propietario de la suscripción de Azure que va a usar con Azure Stack.  
 - Indique el identificador de suscripción de la suscripción de Azure que va a usar. 
 - Identifique el valor de AzureEnvironment que va a usar: *AzureCloud*, *AzureGermanCloud* o *AzureChinaCloud*.

## <a name="validate-azure-registration"></a>Validación del registro de Azure
1. En un equipo que cumpla los requisitos previos, abra un símbolo del sistema administrativo de PowerShell y ejecute el siguiente comando para instalar AzsReadinessChecker.
    > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. Desde el símbolo del sistema de PowerShell, ejecute el siguiente código para establecer *$registrationCredential* como cuenta propietaria de la suscripción.   Reemplace *subscriptionowner@contoso.onmicrosoft.com* por la cuenta y el inquilino. 
    > `$registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"`

3. Desde el símbolo del sistema de PowerShell, ejecute el siguiente código para establecer *$subscriptionID* como suscripción de Azure que se va a usar. Reemplace *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx* por su propio identificador de suscripción.  
     > `$subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"` 

4. Desde el símbolo del sistema de PowerShell, ejecute el siguiente código para iniciar la validación de la suscripción. 
   - Especifique el valor de AzureEnvironment como *AzureCloud*, *AzureGermanCloud* o *AzureChinaCloud*.  
   - Indique el administrador de Azure Active Directory y el nombre del inquilino de Azure Active Directory. 

   > `Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID`

5. Después de ejecutar la herramienta, revise el resultado. Confirme que el estado del inicio de sesión y los requisitos de instalación es Correcto. Una validación correcta tiene un aspecto similar al de la siguiente imagen:  
![ejecución de la validación](./media/azure-stack-validate-registration/registration-validation.png)


## <a name="report-and-log-file"></a>Informe y archivo de registro
Cada vez que se ejecuta la validación, los resultados se registran en **AzsReadinessChecker.log** y **AzsReadinessCheckerReport.json**. La ubicación de estos archivos se muestra con los resultados de validación de PowerShell. 

Estos archivos pueden ayudar a compartir el estado de validación antes de implementar Azure Stack o investigar problemas de validación. Ambos archivos conservan los resultados de cada comprobación de validación posterior. El informe ofrece la confirmación del equipo de implementación de la configuración de identidad. El archivo de registro puede ayudar al equipo de implementación o de soporte técnico a investigar los problemas de validación. 

De forma predeterminada, los dos archivos se escriben en *C:\Users\<nombre de usuario>\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
 - Use el parámetro **-OutputPath** ***&lt;ruta de acceso&gt;***  al final de la línea de comandos de ejecución para especificar otra ubicación para el informe.   
 - Use el parámetro **-CleanReport** al final del comando de ejecución para borrar la información de *AzsReadinessCheckerReport.json*.  sobre ejecuciones anteriores de la herramienta. Para obtener más información, vea [Informe de validación de Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Errores de validación
Si se producen errores en una comprobación de validación, los detalles sobre los errores se muestran en la ventana de PowerShell. La herramienta también registra información en el AzsReadinessChecker.log.

Los ejemplos siguientes ofrecen instrucciones sobre errores de validación comunes.

### <a name="user-must-be-an-owner-of-the-subscription"></a>El usuario que llama debe ser propietario de la suscripción.   
![propietario de la suscripción](./media/azure-stack-validate-registration/subscription-owner.png)
**Causa**: la cuenta no es de administrador de la suscripción de Azure.   

**Resolución**: use una cuenta que sea de administrador de la suscripción de Azure que se facture por uso de la implementación de Azure Stack.


### <a name="expired-or-temporary-password"></a>Contraseña temporal o expirada 
![contraseña expirada](./media/azure-stack-validate-registration/expired-password.png)
**Causa**: no se pudo iniciar sesión en la cuenta porque la contraseña es temporal o ha expirado.     

**Resolución**: en PowerShell, ejecute el siguiente comando y siga las indicaciones para restablecer la contraseña. 
  > `Login-AzureRMAccount` 

O bien, inicie sesión en https://portal.azure.com con la cuenta y el usuario se verá obligados a cambiar la contraseña.


### <a name="microsoft-accounts-are-not-supported-for-registration"></a>No se admiten cuentas de Microsoft en el registro.  
![cuenta no admitida](./media/azure-stack-validate-registration/unsupported-account.png)
**Causa**: se especificó una cuenta de Microsoft (por ejemplo, Outlook.com o Hotmail.com).  No se admiten estas cuentas.

**Resolución**: use una cuenta y una suscripción de un proveedor de servicios en la nube (CSP) o un contrato Enterprise (EA). 


### <a name="unknown-user-type"></a>Tipo de usuario desconocido  
![usuario desconocido](./media/azure-stack-validate-registration/unknown-user.png)
**Causa**: la cuenta no puede iniciar sesión en el entorno de Azure Active Directory especificado. En este ejemplo, *AzureChinaCloud* se especifica como valor de *AzureEnvironment*.  

**Resolución**: confirme que la cuenta es válida para el entorno de Azure especificado. En PowerShell, ejecute el siguiente comando para comprobar que la cuenta es válida para un entorno concreto.     
  > `Login-AzureRmAccount -EnvironmentName AzureChinaCloud`


## <a name="next-steps"></a>Pasos siguientes
[Validación de la identidad de Azure](azure-stack-validate-identity.md)
[Vista del informe de preparación](azure-stack-validation-report.md)
[Consideraciones generales sobre la integración de Azure Stack](azure-stack-datacenter-integration.md)


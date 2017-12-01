---
title: Registro de Azure Stack | Microsoft Docs
description: "Registre Azure Stack con su suscripción de Azure."
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
ms.topic: article
ms.date: 11/15/2017
ms.author: erikje
ms.openlocfilehash: 977630741b8424c4c6bd5f5d492e33b9981b9cb5
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2017
---
# <a name="register-azure-stack-with-your-azure-subscription"></a>Registro de Azure Stack con una suscripción de Azure

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Puede registrar [Azure Stack](azure-stack-poc.md) con Azure para descargar los elementos de marketplace de Azure y configurar informes de datos comerciales para Microsoft. 

> [!NOTE]
>El registro se recomienda porque le permite probar cierta funcionalidad importante de Azure Stack, como son los informes de uso y distribución de marketplace. Después de registrar Azure Stack, se informa del uso para el comercio de Azure. Puede verlo en la suscripción que usó para el registro. No se cobra a los usuarios de Azure Stack Development Kit por ningún uso que comuniquen.
>


## <a name="get-azure-subscription"></a>Obtención de la suscripción a Azure

Antes de registrar Azure Stack con Azure, debe tener:

- El identificador de suscripción de una suscripción de Azure. Para obtener el identificador, inicie sesión en Azure, haga clic en **Más servicios** > **Suscripciones**, haga clic en la suscripción que desee usar, y en **Essentials** puede encontrar el **Identificador de suscripción**. Las suscripciones en la nube en los gobiernos de China, Alemania y EE. UU. no se admiten actualmente.
- El nombre de usuario y la contraseña de una cuenta que sea propietaria de la suscripción (se admiten las cuentas MSA/2FA).
- Azure Active Directory para la suscripción de Azure. Puede encontrar este directorio en Azure manteniendo el mouse sobre su avatar en la esquina superior derecha de Azure Portal. 

Si no tiene una suscripción de Azure que cumpla estos requisitos, puede [crear una cuenta gratuita de Azure aquí](https://azure.microsoft.com/en-us/free/?b=17.06). El registro de Azure Stack no supone ningún costo en su suscripción de Azure.



## <a name="register-azure-stack-resource-provider-in-azure"></a>Registro del proveedor de recursos de Azure Stack en Azure
> [!NOTE] 
> Este paso solo debe realizarse una vez en un entorno de Azure Stack.
>

1. Inicie una sesión de PowerShell como administrador.
2. Inicie sesión en una cuenta de Azure que tenga una suscripción de Azure (puede usar el cmdlet Login-AzureRmAccount para iniciar sesión y asegurarse de que estableció el parámetro -EnvironmentName en "AzureCloud").
3. Registre el proveedor de recursos de Azure "Microsoft.AzureStack".

**Ejemplo:** 
```Powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
```

## <a name="register-azure-stack-with-azure"></a>Registro de Azure Stack con Azure

> [!NOTE]
> Todos estos pasos se deben ejecutar desde un equipo que tenga acceso al punto de conexión con privilegios. En cuanto al kit de desarrollo de Azure Stack, deberá ser el equipo host. Si usa un sistema integrado, póngase en contacto con su operador de Azure Stack.
>

1. Abra una consola de PowerShell como administrador e [instale PowerShell para Azure Stack](azure-stack-powershell-install.md).  

2. Agregue la cuenta de Azure que usará para registrar Azure Stack. Para ello, ejecute el cmdlet `Add-AzureRmAccount` sin parámetros. Se le pedirá que escriba sus credenciales de cuenta de Azure y puede que tenga que utilizar la autenticación en dos fases en función de la configuración de la cuenta.  

3. Si tiene varias suscripciones, ejecute el siguiente comando para seleccionar la suscripción con la que quiera trabajar:  

   ```powershell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Elimine las versiones existentes de los módulos de PowerShell que corresponden al registro y [descargue su versión más reciente desde GitHub](azure-stack-powershell-download.md).  

5. En el directorio "AzureStack-Tools-master" que se creó en el paso anterior, vaya a la carpeta "Registration" e importe el módulo ".\RegisterWithAzure.psm1":  

   ```powershell 
   Import-Module .\RegisterWithAzure.psm1 
   ```

6. En la misma sesión de PowerShell, ejecute el siguiente script. Cuando se le pidan las credenciales, especifique `azurestack\cloudadmin` como el usuario; la contraseña es la misma que usó como administrador local durante la implementación.  

   ```powershell
   $AzureContext = Get-AzureRmContext
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
   Add-AzsRegistration `
       -CloudAdminCredential $CloudAdminCred `
       -AzureSubscriptionId $AzureContext.Subscription.Id `
       -AzureDirectoryTenantName $AzureContext.Tenant.TenantId `
       -PrivilegedEndpoint AzS-ERCS01 `
       -BillingModel Development 
   ```

   | Parámetro | Descripción |
   | -------- | ------------- |
   | CloudAdminCredential | Son las credenciales de dominio en la nube que se utilizan para [obtener acceso al punto de conexión con privilegios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). El nombre de usuario tiene el formato "\<dominio de Azure Stack\>\cloudadmin". Para el kit de desarrollo, se define el nombre de usuario "azurestack\cloudadmin". Si usa un sistema integrado, póngase en contacto con su operador de Azure Stack para obtener este valor.|
   | AzureSubscriptionId | Es la suscripción de Azure que va a usar para registrar Azure Stack.|
   | AzureDirectoryTenantName | Es el nombre de directorio del inquilino de Azure que está asociado con su suscripción de Azure. El recurso de registro se creará en este inquilino de directorio. |
   | PrivilegedEndpoint | Es una consola remota de PowerShell previamente configurada que proporciona funcionalidades tales como la recopilación de registros y otras tareas posteriores a la implementación. En el kit de desarrollo, el punto de conexión con privilegios se hospeda en la máquina virtual de "AzS ERCS01". Si usa un sistema integrado, póngase en contacto con su operador de Azure Stack para obtener este valor. Para obtener más información, consulte el tema [Uso del punto de conexión con privilegios](azure-stack-privileged-endpoint.md).|
   | BillingModel | Es el modelo de facturación que usa la suscripción. Los valores permitidos de este parámetro son: "Capacity","PayAsYouUse" y "Development". En el kit de desarrollo, este valor se establece en "Development". Si usa un sistema integrado, póngase en contacto con su operador de Azure Stack para obtener este valor. |

7. Cuando se complete el script, verá el mensaje "Activando Azure Stack (este paso puede tardar hasta 10 minutos en completarse)". 

## <a name="verify-the-registration"></a>Comprobación del registro

1. Inicie sesión el portal del administrador (https://adminportal.local.azurestack.external).
2. Haga clic en **Más servicios** > **Marketplace Management** (Administración de Marketplace)  > **Add from Azure** (Agregar desde Azure).
3. Si aparece una lista de elementos disponibles de Azure (como WordPress), la activación fue correcta.

> [!NOTE]
> Una vez completado el registro, ya no aparecerá la advertencia activa para no registrar.

## <a name="next-steps"></a>Pasos siguientes

[Conexión a Azure Stack](azure-stack-connect-azure-stack.md)


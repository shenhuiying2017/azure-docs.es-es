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
ms.date: 09/25/2017
ms.author: erikje
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 4a2ab892df6e18e87ba9a33b8b67a3b433ab8a56
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---
# <a name="register-azure-stack-with-your-azure-subscription"></a>Registro de Azure Stack con una suscripción de Azure

*Se aplica a: Azure Stack Development Kit*

En las implementaciones de Azure Active Directory, puede registrar [Azure Stack](azure-stack-poc.md) con Azure para descargar los elementos de marketplace de Azure y configurar informes de datos comerciales para Microsoft. 

> [!NOTE]
>El registro se recomienda porque le permite probar cierta funcionalidad importante de Azure Stack, como son los informes de uso y distribución de marketplace. Después de registrar Azure Stack, se informa del uso para el comercio de Azure. Puede verlo en la suscripción que usó para el registro. No se cobrará a los usuarios de Azure Stack Development Kit por ningún uso que comuniquen.
>


## <a name="get-azure-subscription"></a>Obtención de la suscripción a Azure

Antes de registrar Azure Stack con Azure, debe tener:

- El identificador de suscripción de una suscripción de Azure. Para obtener el identificador, inicie sesión en Azure, haga clic en **Más servicios** > **Suscripciones**, haga clic en la suscripción que desee usar, y en **Essentials** puede encontrar el **Identificador de suscripción**. Las suscripciones en la nube en los gobiernos de China, Alemania y EE. UU. no se admiten actualmente.
- El nombre de usuario y la contraseña de una cuenta que sea propietaria de la suscripción (se admiten las cuentas MSA/2FA)
- Azure Active Directory para la suscripción de Azure. Puede encontrar este directorio en Azure manteniendo el mouse sobre su avatar en la esquina superior derecha de Azure Portal. 
- Haber registrado el proveedor de recursos de Azure Stack (consulte la sección **Registro de un proveedor de recursos de Azure Stack**, para más información)

Si no tiene una suscripción de Azure que cumpla estos requisitos, puede [crear una cuenta gratuita de Azure aquí](https://azure.microsoft.com/en-us/free/?b=17.06). El registro de Azure Stack no supone ningún costo en su suscripción de Azure.



## <a name="register-azure-stack-resource-provider-in-azure"></a>Registro del proveedor de recursos de Azure Stack en Azure
> [!NOTE] 
> Este paso solo debe realizarse una vez en un entorno de Azure Stack.
>

1. Inicie PowerShell ISE como administrador.
2. Inicie sesión en la cuenta de Azure que sea propietaria de la suscripción de Azure con el parámetro -EnvironmentName establecido en "AzureCloud".
3. Registre el proveedor de recursos de Azure "Microsoft.AzureStack".

Ejemplo: 
```Powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack -Force
```


## <a name="register-azure-stack-with-azure"></a>Registro de Azure Stack con Azure

> [!NOTE]
>Todos estos pasos deben realizarse en el equipo host.
>

1. [Instale PowerShell para Azure Stack](azure-stack-powershell-install.md). 
2. Copie el [script RegisterWithAzure.ps1](https://go.microsoft.com/fwlink/?linkid=842959) en una carpeta (como C:\Temp).
3. Inicie PowerShell ISE como administrador.    
4. Ejecute el script RegisterWithAzure.ps1, reemplazando los marcadores de posición siguientes:
    - *YourAccountName* es el propietario de la suscripción de Azure
    - *YourID* es el identificador de la suscripción de Azure que desea usar para registrar Azure Stack
    - *YourDirectory* es el nombre del inquilino de Azure Active Directory del que su suscripción de Azure forma parte.

    ```powershell
    RegisterWithAzure.ps1 -azureSubscriptionId YourID -azureDirectoryTenantName YourDirectory -azureAccountId YourAccountName
    ```
    
    Por ejemplo:
    
    ```powershell
    C:\temp\RegisterWithAzure.ps1 -azureSubscriptionId "5e0ae55d-0b7a-47a3-afbc-8b372650abd3" `
    -azureDirectoryTenantName "contoso.onmicrosoft.com" `
    -azureAccountId serviceadmin@contoso.onmicrosoft.com
    ```
    
5. En los dos mensajes, presione ENTRAR.
6. En la ventana emergente de inicio de sesión, escriba sus credenciales de suscripción de Azure.

## <a name="verify-the-registration"></a>Comprobación del registro

1. Inicie sesión el portal del administrador (https://adminportal.local.azurestack.external).
2. Haga clic en **Más servicios** > **Marketplace Management** (Administración de Marketplace)  > **Add from Azure** (Agregar desde Azure).
3. Si aparece una lista de elementos disponibles de Azure (como WordPress), la activación fue correcta.

## <a name="next-steps"></a>Pasos siguientes

[Conexión a Azure Stack](azure-stack-connect-azure-stack.md)



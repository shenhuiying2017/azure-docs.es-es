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
ms.date: 10/10/2017
ms.author: erikje
ms.openlocfilehash: 3282b9d4cdf67035d966cf934a7d8574eae6ae34
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2017
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
- [Registrado el proveedor de recursos de Azure Stack](#register-azure-stack-resource-provider-in-azure).

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
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
```


## <a name="register-azure-stack-with-azure"></a>Registro de Azure Stack con Azure

> [!NOTE]
>Todos estos pasos deben realizarse en el equipo host.
>

1. [Instale PowerShell para Azure Stack](azure-stack-powershell-install.md). 
2. Copie el [script RegisterWithAzure.psm1](https://go.microsoft.com/fwlink/?linkid=842959) en una carpeta (como C:\Temp).
3. Inicie PowerShell ISE como administrador e importe el módulo RegisterWithAzure.    
4. Desde el script RegisterWithAzure.psm1, ejecute el módulo Add-AzsRegistration. Reemplace los marcadores de posición siguientes: 
    - *YourCloudAdminCredential* es un objeto de PowerShell que contiene las credenciales de dominio local para domain\cloudadmin (para el kit de desarrollo, es azurestack\cloudadmin).
    - *YourAzureSubscriptionID* es el identificador de la suscripción de Azure que desea usar para registrar Azure Stack.
    - *YourAzureDirectoryTenantName* es el nombre del directorio del inquilino de Azure en el que desea crear el recurso de registro.
    - *YourPrivilegedEndpoint* es el nombre del [punto final con privilegios](azure-stack-privileged-endpoint.md).

    ```powershell
    Add-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -AzureDirectoryTenantName $YourAzureDirectoryTenantName  -AzureSubscriptionId $YourAzureSubscriptionId -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Development 
    ```
5. En la ventana emergente de inicio de sesión, escriba sus credenciales de suscripción de Azure.

## <a name="verify-the-registration"></a>Comprobación del registro

1. Inicie sesión el portal del administrador (https://adminportal.local.azurestack.external).
2. Haga clic en **Más servicios** > **Marketplace Management** (Administración de Marketplace)  > **Add from Azure** (Agregar desde Azure).
3. Si aparece una lista de elementos disponibles de Azure (como WordPress), la activación fue correcta.

> [!NOTE]
> Una vez completado el registro, ya no aparecerá la advertencia activa para no registrar.

## <a name="next-steps"></a>Pasos siguientes

[Conexión a Azure Stack](azure-stack-connect-azure-stack.md)


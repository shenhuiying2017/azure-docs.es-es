---
title: Registro de Azure Stack | Microsoft Docs
description: "Registre Azure Stack con su suscripción de Azure."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/8/2018
ms.author: jeffgilb
ms.openlocfilehash: b58b3fc538d2237c12a860d268d550c4223155ba
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2018
---
# <a name="register-azure-stack-with-your-azure-subscription"></a>Registro de Azure Stack con una suscripción de Azure

*Se aplica a: sistemas integrados de Azure Stack y kit de desarrollo de Azure Stack*

Puede registrar [Azure Stack](azure-stack-poc.md) con Azure para descargar los elementos de marketplace de Azure y configurar informes de datos comerciales para Microsoft.

> [!NOTE]
>El registro se recomienda porque le permite probar cierta funcionalidad importante de Azure Stack, como son los informes de uso y distribución de marketplace. Después de registrar Azure Stack, se informa del uso para el comercio de Azure. Puede verlo en la suscripción que usó para el registro. No se cobra a los usuarios de Azure Stack Development Kit por ningún uso que comuniquen.


## <a name="get-azure-subscription"></a>Obtención de la suscripción a Azure

Antes de registrar Azure Stack con Azure, debe tener:

- El identificador de suscripción de una suscripción de Azure. Para obtener el identificador, inicie sesión en Azure, haga clic en **Más servicios** > **Suscripciones**, haga clic en la suscripción que desee usar, y en **Essentials** puede encontrar el **Identificador de suscripción**. Las suscripciones en la nube en los gobiernos de China, Alemania y EE. UU. no se admiten actualmente.
- El nombre de usuario y la contraseña de una cuenta que sea propietaria de la suscripción (se admiten las cuentas MSA/2FA).
- *No es necesario a partir de la versión de actualización 1712 de Azure Stack (180106.1)*: Azure Active Directory para la suscripción de Azure. Puede encontrar este directorio en Azure manteniendo el mouse sobre su avatar en la esquina superior derecha de Azure Portal.

Si no tiene una suscripción de Azure que cumpla estos requisitos, puede [crear una cuenta gratuita de Azure aquí](https://azure.microsoft.com/en-us/free/?b=17.06). El registro de Azure Stack no supone ningún costo en su suscripción de Azure.

## <a name="register-azure-stack-with-azure"></a>Registro de Azure Stack con Azure  
> [!NOTE]
> Todos estos pasos se deben ejecutar desde un equipo que tenga acceso al punto de conexión con privilegios. En cuanto al kit de desarrollo de Azure Stack, deberá ser el equipo host. Si usa un sistema integrado, póngase en contacto con su operador de Azure Stack.
>

1. Abra una consola de PowerShell como administrador e [instale PowerShell para Azure Stack](azure-stack-powershell-install.md).  

2. Agregue la cuenta de Azure que usará para registrar Azure Stack. Para agregar la cuenta, ejecute el cmdlet `Add-AzureRmAccount` con el parámetro EnvironmentName establecido en **AzureCloud**. Se le pedirá que escriba sus credenciales de cuenta de Azure y puede que tenga que utilizar la autenticación en dos fases en función de la configuración de la cuenta.

   ```Powershell
   Add-AzureRmAccount -EnvironmentName "AzureCloud"
   ```

3. Si tiene varias suscripciones, ejecute el siguiente comando para seleccionar la suscripción con la que quiera trabajar:  

   ```powershell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Ejecute el siguiente comando para registrar el proveedor de recursos de Azure Stack en su suscripción de Azure:

   ```Powershell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Elimine las versiones existentes de los módulos de PowerShell que corresponden al registro y [descargue su versión más reciente desde GitHub](azure-stack-powershell-download.md).  

6. En el directorio "AzureStack-Tools-master" que se creó en el paso anterior, vaya a la carpeta "Registration" e importe el módulo ".\RegisterWithAzure.psm1":  

   ```powershell
   Import-Module .\RegisterWithAzure.psm1
   ```

7. En la misma sesión de PowerShell, ejecute el siguiente script: cuando se le pidan las credenciales, especifique `azurestack\cloudadmin` como el usuario; la contraseña es la misma que usó como administrador local durante la implementación.  

   ```powershell
   $AzureContext = Get-AzureRmContext
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
   Set-AzsRegistration `
       -CloudAdminCredential $CloudAdminCred `
       -PrivilegedEndpoint AzS-ERCS01 `
       -BillingModel Development
   ```

   | . | DESCRIPCIÓN |  
   |--------|-------------|
   | CloudAdminCredential | Son las credenciales de dominio en la nube que se utilizan para [obtener acceso al punto de conexión con privilegios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). El nombre de usuario tiene el formato **\<dominio de Azure Stack\>\cloudadmin**. Para el kit de desarrollo, se define el nombre de usuario **azurestack\cloudadmin**. Si usa un sistema integrado, póngase en contacto con su operador de Azure Stack para obtener este valor.|  
   | PrivilegedEndpoint | Es una consola remota de PowerShell previamente configurada que proporciona funcionalidades tales como la recopilación de registros y otras tareas posteriores a la implementación. En el kit de desarrollo, el punto de conexión con privilegios se hospeda en la máquina virtual de "AzS ERCS01". Si usa un sistema integrado, póngase en contacto con su operador de Azure Stack para obtener este valor. Para más información, consulte el artículo acerca del [uso del punto de conexión con privilegios](azure-stack-privileged-endpoint.md).|  
   | BillingModel | Es el modelo de facturación que usa la suscripción. Los valores permitidos de este parámetro son: **Capacity**, **PayAsYouUse** y **Development**. En el kit de desarrollo, este valor se establece en **Development**. Si usa un sistema integrado, póngase en contacto con su operador de Azure Stack para obtener este valor. |

8. Cuando se complete el script, verá el mensaje "Activando Azure Stack (este paso puede tardar hasta 10 minutos en completarse)".




## <a name="verify-the-registration"></a>Comprobación del registro  

1. Inicie sesión el portal del administrador (https://adminportal.local.azurestack.external).

2. Haga clic en **Más servicios** > **Marketplace Management** (Administración de Marketplace)  > **Add from Azure** (Agregar desde Azure).

3. Si aparece una lista de elementos disponibles de Azure (como WordPress), la activación fue correcta.

> [!NOTE]
> Una vez completado el registro, ya no aparecerá la advertencia activa para no registrar.


## <a name="modify-the-registration"></a>Modificación del registro

### <a name="change-the-subscription-you-use"></a>Cambio de la suscripción que se usa
Si desea cambiar la suscripción que utiliza, primero debe ejecutar Remove-AzsRegistration, después, asegurarse de que inicia una sesión en el contexto correcto de Azure PowerShell y, finalmente, ejecutar Set-AzsRegistration con cualquier parámetro cambiado.

  ```Powershell   
  Remove-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```
### <a name="change-the-billing-model-or-syndication-features"></a>Cambio del modelo de facturación o de las características de redifusión
Si desea cambiar el modelo de facturación o las características de redifusión de la instalación, puede llamar a la función de registro para establecer los valores nuevos. No es preciso eliminar antes la configuración actual.  

  ```Powershell
     Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```


## <a name="disconnected-registration"></a>Registro sin conexión
*La información de esta sección aplica a partir de la versión de actualización 1712 de Azure Stack (180106.1) y no es compatible con las versiones anteriores.*

Si va a registrar Azure Stack en un entorno desconectado, tendrá que obtener un token de registro del entorno de Azure Stack y, después, utilizarlo en una máquina que pueda conectarse a Azure para su registro.  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Obtención de un token de registro del entorno de Azure Stack
  1. Para obtener un token de registro, ejecute lo siguiente:  

     ```Powershell
        $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
        $RegistrationToken = Get-AzsRegistrationToken -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<your agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
      ```
      > [!TIP]  
      > El token de registro se guarda en el archivo especificado para *$env:SystemDrive\RegistrationToken.txt*.

  2. Guarde este token de registro para usarlo en la máquina conectada a Azure.


### <a name="connect-to-azure-and-register"></a>Conexión a Azure y posterior registro
Ejecute los pasos de este procedimiento en una máquina que se pueda conectar a Azure.

  1. [Descargue los módulos de PowerShell más recientes relacionados con el registro de GitHub](azure-stack-powershell-download.md).  

  2. En el directorio "AzureStack-Tools-master" que se creó en el paso anterior, vaya a la carpeta "Registration" e importe el módulo ".\RegisterWithAzure.psm1":  

     ```powershell
     Import-Module .\RegisterWithAzure.psm1
     ```

  3. Copie [RegisterWithAzure.psm1](https://go.microsoft.com/fwlink/?linkid=842959) en una carpeta, como *C:\Temp*.

  4. Inicie PowerShell ISE como administrador e importe el módulo RegisterWithAzure.  

  5. Asegúrese de que ha iniciado sesión en el contexto correcto de Azure PowerShell que desea usar para registrar el entorno de Azure Stack:  

     ```Powershell
        Set-AzureRmContext -SubscriptionId $YourAzureSubscriptionId   
     ```
  6. Especifique el token de registro que desea registrar con Azure:

     ```Powershell  
       $registrationToken = "*Your Registration Token*"
       Register-AzsEnvironment -RegistrationToken $registrationToken  
     ```
    Si lo desea, puede usar el cmdlet Get-Content para señalar a un archivo que contenga el token de registro:

    ```Powershell  
       $registrationToken = Get-Content -Path 'C:\Temp\<Registration Token File>'
       Register-AzsEnvironment -RegistrationToken $registrationToken  
    ```
> [!NOTE]  
> Guarde el nombre de recurso de registro o el token de registro para futuras referencias.

### <a name="remove-a-registered-resource"></a>Eliminación de un recurso registrado
Si desea eliminar el recurso de registro, debe usar UnRegister-AzsEnvironment y pasar el nombre del recurso de registro o el token de registro que usó para Register-AzsEnvironment.
- **Nombre del recurso de registro:**

  ```Powershell    
     UnRegister-AzsEnvironment -RegistrationName "*Name of the registration resource*"
  ```
- **Token del registro:**    

  ```Powershell
     $registrationToken = "*Your copied registration token*"
     UnRegister-AzsEnvironment -RegistrationToken $registrationToken
   ```


## <a name="next-steps"></a>pasos siguientes
[Conexión a Azure Stack](azure-stack-connect-azure-stack.md)

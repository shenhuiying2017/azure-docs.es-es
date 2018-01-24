---
title: Registro de Azure para sistemas integrados en Azure Stack | Microsoft Docs
description: "Describe el proceso de registro de Azure para implementaciones de varios nodos con conexión a Azure en Azure Stack."
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
ms.date: 01/16/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 03a6ce77eed16cbc5d2fe46094b0b6ac7fbc022e
ms.sourcegitcommit: 5108f637c457a276fffcf2b8b332a67774b05981
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/17/2018
---
# <a name="register-azure-stack-with-azure"></a>Registro de Azure Stack en Azure
Puede registrar Azure Stack en Azure para descargar elementos de Marketplace de Azure y configurar informes de datos comerciales para Microsoft. Después de registrar Azure Stack, se informa del uso para el comercio de Azure. Puede verlo en la suscripción que usó para el registro.

> [!IMPORTANT]
> Si elige el modelo de facturación de pago por uso, el registro es obligatorio. De lo contrario, infringirá los términos de la licencia de la implementación de Azure Stack, ya que no hay otra forma de informar acerca del uso.

## <a name="before-you-register-azure-stack-with-azure"></a>Antes de registrar Azure Stack en Azure
Antes de registrar Azure Stack en Azure, debe tener:

- El identificador de suscripción de una suscripción de Azure. Para obtener el identificador, inicie sesión en Azure, haga clic en **Más servicios** > **Suscripciones**, haga clic en la suscripción que desee usar y, en **Essentials**, encontrará el identificador de la suscripción. 

  > [!NOTE]
  > Las suscripciones en la nube en los gobiernos de China, Alemania y EE. UU. no se admiten actualmente. 

- El nombre de usuario y la contraseña de una cuenta que sea propietaria de la suscripción (se admiten las cuentas MSA/2FA)
- *No es necesario a partir de la versión de actualización 1712 de Azure Stack (180106.1)*: Azure AD para la suscripción de Azure. Puede encontrar este directorio en Azure manteniendo el mouse sobre su avatar en la esquina superior derecha de Azure Portal. 
- Haber registrado el proveedor de recursos de Azure Stack (para más información, consulte la sección Registro de un proveedor de recursos de Azure Stack)

Si no tiene una suscripción de Azure que cumpla estos requisitos, puede [crear una cuenta gratuita de Azure aquí](https://azure.microsoft.com/free/?b=17.06). El registro de Azure Stack no supone ningún costo en su suscripción de Azure.

### <a name="bkmk_powershell"></a>Instalación de PowerShell para Azure Stack
Para registrar el sistema en Azure, es preciso usar la versión más reciente de PowerShell para Azure Stack.

Si no está instalado aún, [instale PowerShell para Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install). 

### <a name="bkmk_tools"></a>Descarga de las herramientas de Azure Stack
El repositorio de GitHub de las herramientas de Azure Stack contiene módulos de PowerShell que admiten la funcionalidad de Azure Stack; lo que incluye la funcionalidad de registro. Durante el proceso de registro tendrá que importar y usar el módulo de PowerShell RegisterWithAzure.psm1, que se encuentra en el repositorio de herramientas de Azure Stack, para registrar la instancia de Azure Stack en Azure. 

```powershell
# Change directory to the root directory. 
cd \

# Download the tools archive.
  invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
  expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
  cd AzureStack-Tools-master
```

## <a name="register-azure-stack-in-connected-environments"></a>Registro de Azure Stack en entornos conectados
Los entornos conectados pueden acceder a Internet y a Azure. Para estos entornos, es preciso registrar el proveedor de recursos de Azure Stack en Azure y, después, configurar el modelo de facturación.

### <a name="register-the-azure-stack-resource-provider"></a>Registro del proveedor de recursos de Azure Stack
Para registrar el proveedor de recursos de Azure Stack en Azure, inicie Powershell ISE como administrador y utilice los siguientes comandos de PowerShell. Estos comandos:
- Le solicitarán que inicie sesión como propietario de la suscripción de Azure que se va a usar y que establezca el parámetro `EnvironmentName` en **AzureCloud**.
- Registrarán el proveedor de recursos de Azure **Microsoft.AzureStack**.

PowerShell que se ejecuta:

```powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack 
```

### <a name="register-azure-stack-with-azure-using-the-pay-as-you-use-billing-model"></a>Registro de Azure Stack en Azure mediante el modelo de facturación de pago por uso
Use estos pasos para registrar Azure Stack en Azure mediante el modelo de facturación de pago por uso.

Inicie PowerShell ISE como administrador y vaya a la carpeta **Registration**del directorio **AzureStack-Tools-master** que creó cuando [descargó las herramientas de Azure Stack](#bkmk_tools). Importe el módulo **RegisterWithAzure.psm1** mediante PowerShell: 

PowerShell que se ejecuta:

```powershell
Import-Module .\RegisterWithAzure.psm1
```
A continuación, en la misma sesión de PowerShell, ejecute el cmdlet **Set-AzsRegistration**. Cuando se le pidan las credenciales, especifique el propietario de la suscripción de Azure.  

PowerShell que se ejecuta:

```powershell
$AzureContext = Get-AzureRmContext
$CloudAdminCred = Get-Credential -UserName <Azure subscription owner>  -Message "Enter the cloud domain credentials to access the privileged endpoint"
Set-AzsRegistration `
    -CloudAdminCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -BillingModel PayAsYouUse
```

|.|DESCRIPCIÓN|
|-----|-----|
|CloudAdminCredential|Objeto de PowerShell que contiene información de las credenciales (nombre de usuario y contraseña) del propietario de la suscripción de Azure.|
|PrivilegedEndpoint|Es una consola remota de PowerShell previamente configurada que proporciona funcionalidades tales como la recopilación de registros y otras tareas posteriores a la implementación. Para más información, consulte el artículo acerca del [uso del punto de conexión con privilegios](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint#access-the-privileged-endpoint).|
|BillingModel|Es el modelo de facturación que usa la suscripción. Los valores que se permiten en este parámetro son: Capacity, PayAsYouUse y Development.|

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>Registro de Azure Stack en Azure mediante el modelo de facturación de capacidad
Siga las mismas instrucciones que usó para realizar el registro mediante el modelo de facturación de pago por uso, pero agregue el número de contrato con la que se adquirió la capacidad y cambiar el parámetro `BillingModel` a **Capacity**. Los restantes parámetros son opcionales.

PowerShell que se ejecuta:
```powershell
$AzureContext = Get-AzureRmContext
$CloudAdminCred = Get-Credential -UserName <Azure subscription owner>  -Message "Enter the cloud domain credentials to access the privileged endpoint"
Set-AzsRegistration `
    -CloudAdminCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -AgreementNumber <EA agreement number> `
    -BillingModel Capacity
```

## <a name="register-azure-stack-in-disconnected-environments"></a>Registro de Azure Stack en entornos sin conexión 
*La información de esta sección aplica a partir de la versión de actualización 1712 de Azure Stack (180106.1) y no es compatible con las versiones anteriores.*

Si va a registrar Azure Stack en un entorno desconectado (sin conexión a Internet), tendrá que obtener un token de registro del entorno de Azure Stack y, después, utilizarlo en un equipo que puede conectarse a Azure y que tenga [PowerShell para Azure Stack instalado](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Obtención de un token de registro del entorno de Azure Stack
  1. Para obtener un token de registro, ejecute los siguientes comandos de PowerShell:  

     ```Powershell
        $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
        $RegistrationToken = Get-AzsRegistrationToken -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<your agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
      ```
      > [!TIP]  
      > El token de registro se guarda en el archivo especificado para *$env:SystemDrive\RegistrationToken.txt*.

  2. Guarde este token de registro para usarlo en la máquina conectada a Azure.


### <a name="connect-to-azure-and-register"></a>Conexión a Azure y posterior registro
Inicie PowerShell ISE como administrador y vaya a la carpeta **Registration**del directorio **AzureStack-Tools-master** que creó cuando [descargó las herramientas de Azure Stack](#bkmk_tools). Importe el módulo **RegisterWithAzure.psm1**: 

PowerShell que se ejecuta:
```powershell
Import-Module .\RegisterWithAzure.psm1
```
A continuación, en la misma sesión de PowerShell, especifique el token para registrarse en Azure:

```Powershell  
$registrationToken = "<Your Registration Token>"
Register-AzsEnvironment -RegistrationToken $registrationToken  
```
Si lo desea, puede usar el cmdlet Get-Content para señalar a un archivo que contenga el token de registro:

 ```Powershell  
 $registrationToken = Get-Content -Path '<Path>\<Registration Token File>'
 Register-AzsEnvironment -RegistrationToken $registrationToken  
 ```
> [!NOTE]  
> Guarde el nombre de recurso de registro o el token de registro para futuras referencias.

## <a name="verify-azure-stack-registration"></a>Comprobación del registro de Azure Stack
Siga estos pasos para comprobar que Azure Stack se ha registrado correctamente en Azure.
1. Inicie sesión en el [portal de administrador](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal) de Azure Stack: https&#58;//adminportal.*&lt;región>.&lt;fqdn>*.
2. Haga clic en **Más servicios** > **Marketplace Management** (Administración de Marketplace)  > **Add from Azure** (Agregar desde Azure).

Si aparece una lista de elementos disponibles de Azure (como WordPress), la activación fue correcta.

> [!NOTE]
> Una vez completado el registro, ya no aparecerá la advertencia activa para no registrar.

## <a name="renew-or-change-registration"></a>Renovación o cambio de registro
El registro se tendrá que actualizar o renovar en las siguientes circunstancias:
- Después de renovar su suscripción anual basada en capacidad.
- Al cambiar el modelo de facturación.
- Cuando se escalan cambios (se agregan o quitan nodos) para la facturación por capacidad.

### <a name="change-the-subscription-you-use"></a>Cambio de la suscripción que se usa
Si desea cambiar la suscripción que utiliza, primero debe ejecutar el cmdlet **Remove-AzsRegistration**, después, asegurarse de que inicia una sesión en el contexto correcto de Azure PowerShell y, finalmente, ejecutar **Set-AzsRegistration** con cualquier parámetro c cambiado:

```powershell
Remove-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
Set-AzureRmContext -SubscriptionId $NewSubscriptionId
Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
```

### <a name="change-the-billing-model-or-syndication-features"></a>Cambio del modelo de facturación o de las características de redifusión
Si desea cambiar el modelo de facturación o las características de redifusión de la instalación, puede llamar a la función de registro para establecer los valores nuevos. No es preciso eliminar antes la configuración actual: 

```powershell
Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
```

## <a name="remove-a-registered-resource"></a>Eliminación de un recurso registrado
Si desea eliminar un registro, debe usar el cmdlet **UnRegister-AzsEnvironment** y pasar el nombre del recurso de registro o el token de registro que usó para **Register-AzsEnvironment**.

Para quitar un registro que usa un nombre de recurso:

```Powershell    
UnRegister-AzsEnvironment -RegistrationName "*Name of the registration resource*"
```
Para quitar un registro que usa un token de registro:

```Powershell
$registrationToken = "*Your copied registration token*"
UnRegister-AzsEnvironment -RegistrationToken $registrationToken
```


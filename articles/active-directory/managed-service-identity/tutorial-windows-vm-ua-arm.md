---
title: Uso de MSI asignada por el usuario en una máquina virtual Windows para acceder a Azure Resource Manager
description: Tutorial que le guía a través del proceso para usar una Managed Service Identity (MSI) asignada por el usuario con una máquina virtual Windows para acceder a Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2018
ms.author: arluca
ms.openlocfilehash: a2225409e4cb50d91c09207ee70b76df12925192
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301218"
---
# <a name="tutorial-use-a-user-assigned-managed-service-identity-msi-on-a-windows-vm-to-access-azure-resource-manager"></a>Tutorial: Uso de Managed Service Identity (MSI) asignada por el usuario en una máquina virtual Windows para acceder a Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

En este tutorial, se explica cómo crear una identidad asignada por el usuario, asignarla a una máquina virtual Windows y después usar esa identidad para acceder a la API de Azure Resource Manager. Azure administra automáticamente las identidades de servicio administradas. Estas identidades permiten autenticarse en servicios que admiten la autenticación de Azure AD, sin necesidad de incluir credenciales en el código. 

Aprenderá a:

> [!div class="checklist"]
> * Creación de una máquina virtual Windows 
> * Creación de una identidad asignada por el usuario
> * Asignación de su identidad asignada por el usuario a la máquina virtual Windows
> * Conceder a la identidad asignada por el usuario acceso a un grupo de recursos en Azure Resource Manager 
> * Obtener un token de acceso mediante la identidad asignada por el usuario y usarla para llamar a Azure Resource Manager 
> * Leer las propiedades de un grupo de recursos

## <a name="prerequisites"></a>requisitos previos

- Si no está familiarizado con Managed Service Identity, consulte la sección de [introducción](overview.md). **No olvide revisar las [diferencias entre una identidad asignada por el sistema y una asignada por el usuario](overview.md#how-does-it-work)**.
- Si aún no tiene una cuenta de Azure, [regístrese para una cuenta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para cumplir con los pasos necesarios de creación de recursos y administración de roles de este tutorial, la cuenta debe tener permisos de "Propietario" en el ámbito adecuado (en su suscripción o grupo de recursos). Si necesita ayuda con la asignación de roles, consulte [Uso del control de acceso basado en rol para administrar el acceso a los recursos de la suscripción de Azure](/azure/role-based-access-control/role-assignments-portal).

Si decide instalar y usar PowerShell de forma local, en este tutorial necesitará la versión 5.7 del módulo de Azure PowerShell o cualquier versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Login-AzureRmAccount` para crear una conexión con Azure.

## <a name="create-resource-group"></a>Creación de un grupo de recursos

En el siguiente ejemplo, se crea un grupo de recursos denominado *myResourceGroupVM* en la región *EastUS*.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroupVM" -Location "EastUS"
```

## <a name="create-virtual-machine"></a>Crear máquina virtual

Una vez creado el grupo de recursos, cree una máquina virtual Windows.

Establezca el nombre de usuario y la contraseña que se necesitan para la cuenta de administrador en la máquina virtual con [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```
Cree la máquina virtual con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="create-a-user-assigned-identity"></a>Creación de una identidad asignada por el usuario

Se crea una identidad asignada por el usuario como recurso de Azure independiente. Mediante [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity), Azure crea una identidad en el inquilino de Azure AD que puede asignarse a una o varias instancias de servicio de Azure.

> [!IMPORTANT]
> La creación de identidades asignadas por el usuario solo admite caracteres alfanuméricos y guiones (0-9, a-z, A-z, -). Además, el nombre debe limitarse a una longitud de 24 caracteres para que la asignación a VM/VMSS funcione correctamente. Compruebe si hay actualizaciones. Para obtener más información, consulte [Preguntas más frecuentes y problemas conocidos](known-issues.md).

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1
```

La respuesta contiene detalles de la identidad asignada por el usuario que se ha creado, de forma similar al ejemplo siguiente. Anote el valor `Id` para la identidad asignada por el usuario, ya que se usará en el paso siguiente:

```azurepowershell
{
Id: /subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1
ResourceGroupName : myResourceGroupVM
Name: ID1
Location: westus
TenantId: 733a8f0e-ec41-4e69-8ad8-971fc4b533f8
PrincipalId: e591178e-b785-43c8-95d2-1397559b2fb9
ClientId: af825a31-b0e0-471f-baea-96de555632f9
ClientSecretUrl: https://control-westus.identity.azure.net/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1/credentials?tid=733a8f0e-ec41-4e69-8ad8-971fc4b533f8&oid=e591178e-b785-43c8-95d2-1397559b2fb9&aid=af825a31-b0e0-471f-baea-96de555632f9
Type: Microsoft.ManagedIdentity/userAssignedIdentities
}
```

## <a name="assign-the-user-assigned-identity-to-a-windows-vm"></a>Asignar una identidad asignada por el usuario a una máquina virtual Windows

Los clientes pueden usar una identidad asignada por el usuario en varios recursos de Azure. Use los siguientes comandos para asignar la identidad asignada por el usuario a una única máquina virtual. Use la propiedad `Id` devuelta en el paso anterior para el parámetro `-IdentityID`.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzureRmVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-your-user-assigned-msi-access-to-a-resource-group-in-azure-resource-manager"></a>Conceder a la MSI asignada por el usuario acceso a un grupo de recursos en Azure Resource Manager 

Managed Service Identity (MSI) proporciona las identidades que el código puede usar a fin de solicitar tokens de acceso para autenticarse en API de recursos que admiten la autenticación de Azure AD. En este tutorial, el código accederá a la API de Azure Resource Manager. 

Antes de que el código pueda acceder a la API, debe conceder a la identidad acceso a un recurso en Azure Resource Manager. En este caso, se trata del grupo de recursos que contiene la máquina virtual. Actualice el valor de `<SUBSCRIPTION ID>` según corresponda en su entorno.

```azurepowershell-interactive
$spID = (Get-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1).principalid
New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/"
```

La respuesta contiene detalles de la asignación de roles que se ha creado, de forma similar al ejemplo siguiente:

```azurepowershell
RoleAssignmentId: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM/providers/Microsoft.Authorization/roleAssignments/f9cc753d-265e-4434-ae19-0c3e2ead62ac
Scope: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM
DisplayName: ID1
SignInName:
RoleDefinitionName: Reader
RoleDefinitionId: acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId: e591178e-b785-43c8-95d2-1397559b2fb9
ObjectType: ServicePrincipal
CanDelegate: False
```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Obtenga un token de acceso con la identidad de la máquina virtual y úselo para llamar a Resource Manager 

En el resto del tutorial, trabajará desde la máquina virtual que ha creado anteriormente.

1. Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com).

2. En el portal, vaya a **Virtual Machines** y diríjase a la máquina virtual Windows. A continuación, en **Introducción**, haga clic en **Conectar**.

3. Escriba el **nombre de usuario** y la **contraseña** que usó al crear la máquina virtual Windows.

4. Ahora que ha creado una **conexión a Escritorio remoto** con la máquina virtual, abra **PowerShell** en la sesión remota.

5. Mediante el comando `Invoke-WebRequest` de PowerShell, realice una solicitud al punto de conexión de MSI local para obtener un token de acceso para Azure Resource Manager.

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=73444643-8088-4d70-9532-c3a0fdc190fz&resource=https://management.azure.com' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

## <a name="read-the-properties-of-a-resource-group"></a>Leer las propiedades de un grupo de recursos

Use el token de acceso que ha recuperado en el paso anterior para acceder a Azure Resource Manager y lea las propiedades del grupo de recursos al que ha concedido acceso a la identidad asignada por el usuario. Reemplace <SUBSCRIPTION ID> con el identificador de la suscripción de su entorno.

```azurepowershell
(Invoke-WebRequest -Uri https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/myResourceGroupVM?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{Authorization ="Bearer $ArmToken"}).content
```
La respuesta contiene la información específica del grupo de recursos, de forma similar al ejemplo siguiente:

```json
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/TestRG","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general sobre MSI, consulte [Managed Service Identity overview](overview.md) (Introducción a Managed Service Identity).

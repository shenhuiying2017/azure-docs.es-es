---
title: Vinculación de una cuenta de Azure a un Id. de partner | Microsoft Docs
description: Controle las interacciones con clientes de Azure vinculando un Id. de partner a la cuenta de usuario que utiliza para administrar los recursos del cliente.
services: billing
author: dhirajgandhi
ms.author: dhgandhi
ms.date: 03/12/2018
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: d1f6515c63e789643c285218aa121f1182902e0f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/19/2018
---
# <a name="link-partner-id-to-your-azure-accounts"></a>Vinculación de un Id. de partner a cuentas de Azure 
Como partner, puede realizar un seguimiento de su impacto en las interacciones con sus clientes mediante la vinculación de su Id. de partner con las cuentas utilizadas para administrar recursos del cliente.

Esta característica está disponible en versión preliminar pública. 

## <a name="get-access-from-your-customer"></a>Obtención de acceso del cliente 
Antes de vincular su Id. de partner, el cliente debe concederle acceso a sus recursos de Azure mediante una de las siguientes opciones:

- **Usuario invitado:** el cliente puede añadirle como usuario invitado y asignar roles RBAC. Para más información, consulte [Adición de usuarios invitados de otro directorio](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Cuenta de directorio:** el cliente puede crear un nuevo usuario de su organización en el directorio del cliente y asignarle cualquier rol RBAC. 

- **Entidad de servicio:** el cliente puede añadir una aplicación o un script de su organización en el directorio del cliente y asignarle cualquier rol RBAC. La identidad de la aplicación o el script se conoce como entidad de servicio.

## <a name="link-partner-id"></a>Vinculación de Id. de partner
Cuando obtenga acceso a los recursos del cliente, use PowerShell o la CLI para vincular su identificador de Microsoft Partner Network (Id. de MPN) a su identificador de usuario o entidad de servicio. Debe vincular el Id. de partner para cada inquilino de cliente. 

### <a name="use-powershell-to-link-new-partner-id"></a>Uso de PowerShell para vincular un nuevo Id. de partner

1. Instale el módulo de PowerShell [AzurePartnerRP](https://www.powershellgallery.com/packages/AzureRM.ManagementPartner/0.1.0-preview).

2. Inicie sesión en el inquilino de cliente con la cuenta de usuario o la entidad de servicio. Para más información, consulte [Inicio de sesión con Powershell](https://docs.microsoft.com/en-us/powershell/azure/authenticate-azureps?view=azurermps-5.2.0).
 
   ```azurepowershell-interactive
    C:\> Connect-AzureRmAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
   ```


3. Vincule el nuevo Id. de partner. El Id. de partner es el identificador de [Microsoft Partner Network (MPN)](https://partner.microsoft.com/) de su organización.

    ```azurepowershell-interactive
    C:\> new-AzureRmManagementPartner -PartnerId 12345 
    ```

#### <a name="get-the-linked-partner-id"></a>Obtención del Id. de partner vinculado
```azurepowershell-interactive
C:\> get-AzureRmManagementPartner 
```

#### <a name="update-the-linked-partner-id"></a>Actualización del Id. de partner vinculado
```azurepowershell-interactive
C:\> Update-AzureRmManagementPartner -PartnerId 12345 
```
#### <a name="delete-the-linked-partner-id"></a>Eliminación del Id. de partner vinculado
```azurepowershell-interactive
C:\> remove-AzureRmManagementPartner -PartnerId 12345 
```

### <a name="use-cli-to-link-new-partner-id"></a>Uso de la CLI para vincular un nuevo Id. de partner
1.  Instale la extensión de la CLI.

    ```azure-cli
    C:\ az extension add --name managementpartner
    ``` 

2.  Inicie sesión en el inquilino de cliente con la cuenta de usuario o la entidad de servicio. Para más información, consulte [Inicio de sesión con la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azure-cli
    C:\ az login --tenant <tenant>
    ``` 


3.  Vincule el nuevo Id. de partner. El Id. de partner es el identificador de [Microsoft Partner Network (MPN)](https://partner.microsoft.com/) de su organización.

     ```azure-cli
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Obtención del Id. de partner vinculado
```azure-cli
C:\ az managementpartner show
``` 

#### <a name="update-the-linked-partner-id"></a>Actualización del Id. de partner vinculado
```azure-cli
C:\ az managementpartner update --partner-id 12345
``` 

#### <a name="delete-the-linked-partner-id"></a>Eliminación del Id. de partner vinculado
```azure-cli
C:\ az managementpartner delete --partner-id 12345
``` 


## <a name="frequently-asked-questions"></a>Preguntas frecuentes

**¿Quién puede vincular el Id. de partner?**

Cualquier usuario de la organización asociada que vaya a administrar los recursos del cliente puede vincular el Id. de partner a la cuenta.
  

**Una vez vinculado un Id. de partner, ¿se puede cambiar?**

Sí, el Id. de partner vinculado se puede cambiar, añadir o quitar.

**¿Qué ocurre si un usuario tiene una cuenta en varios inquilinos de cliente?**

El vínculo entre el Id. de partner y la cuenta se realiza para cada inquilino de cliente.  Debe vincular el Id. de partner en cada inquilino de cliente.

**¿Puede otro partner o cliente editar o quitar el vínculo con el Id. de partner?**

El vínculo está asociado a nivel de cuenta. Solo usted puede editar o quitar el vínculo con el Id. de partner. Ni el cliente ni ningún otro partner podrán modificar el vínculo con el Id. de partner. 



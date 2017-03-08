---
title: "Creación de una máquina virtual Windows desde una plantilla de Azure | Microsoft Docs"
description: "Use una plantilla de Resource Manager y PowerShell para crear fácilmente una máquina virtual de Windows."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 19129d61-8c04-4aa9-a01f-361a09466805
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: davidmu
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 32e30b44c2f7cfa9c1069190fdc53dbe6e9f4cd5
ms.openlocfilehash: c1c301b5687cf35e93698a66fac7103b0cc90aed
ms.lasthandoff: 03/01/2017


---

# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Creación de una máquina virtual Windows con una plantilla de Resource Manager

En este artículo se muestra cómo implementar una plantilla de Azure Resource Manager con PowerShell. La [plantilla](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json) implementa una sola máquina virtual que ejecuta Windows Server en una nueva red virtual con una sola subred.

Para obtener una descripción detallada del recurso de máquina virtual, consulte [Virtual machines in an Azure Resource Manager template](virtual-machines-windows-template-description.md) (Máquinas virtuales en una plantilla de Azure Resource Manager). Para obtener más información acerca de todos los recursos de una plantilla, consulte [Tutorial de la plantilla de Resource Manager](../resource-manager-template-walkthrough.md).

Tardará unos cinco minutos en realizar los pasos de este artículo.

## <a name="step-1-install-azure-powershell"></a>Paso 1: Instalación de Azure PowerShell
Consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para obtener más información sobre cómo instalar la versión más reciente de Azure PowerShell, seleccionar la suscripción que quiere usar e iniciar sesión en su cuenta.

## <a name="step-2-create-a-resource-group"></a>Paso 2: Creación de un grupo de recursos
Todos los recursos se deben implementar en un [grupo de recursos](../azure-resource-manager/resource-group-overview.md).

1. Obtenga una lista de las ubicaciones donde se pueden crear los recursos.
   
  ```powershell   
  Get-AzureRmLocation | sort DisplayName | Select DisplayName
  ```

2. Cree el grupo de recursos en la ubicación que seleccione. En este ejemplo se muestra la creación de un grupo de recursos denominado **myResourceGroup** en la ubicación **Centro de EE. UU.**:

  ```powershell   
  New-AzureRmResourceGroup -Name "myResourceGroup" -Location "Central US"
  ```
   
  Puede ver algo parecido a este ejemplo:

  ```
  ResourceGroupName : myResourceGroup
  Location          : centralus
  ProvisioningState : Succeeded
  Tags              :
  ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myResourceGroup
  ```

## <a name="step-3-create-the-resources"></a>Paso 3: Creación de los recursos
Implemente la plantilla y proporcione valores de parámetro cuando se le solicite. En este ejemplo se implementa la plantilla 101-vm-simple-windows en el grupo de recursos que ha creado:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json" 
```
Se le pide que proporcione el nombre de la cuenta de administrador en la máquina virtual, la contraseña de la cuenta y el prefijo de DNS.

Puede ver algo parecido a este ejemplo:

```
DeploymentName    : azuredeploy
ResourceGroupName : myResourceGroup
ProvisioningState : Succeeded
Timestamp         : 12/29/2016 8:11:37 PM
Mode              : Incremental
TemplateLink      :
                    Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/
                    101-vm-simple-windows/azuredeploy.json
                    ContentVersion : 1.0.0.0
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    adminUsername    String                     myAdminUser
                    adminPassword    SecureString
                    dnsLabelPrefix   String                     myDomain
                    windowsOSVersion String                     2016-Datacenter
Outputs           :
                    Name             Type                       Value
                    ===============  =========================  ===========
                    hostname         String                     myDomain.centralus.cloudapp.azure.com
DeploymentDebugLogLevel :
```

> [!NOTE]
> También puede implementar las plantillas y los parámetros desde archivos locales. Para obtener más información, consulte [Usar Azure PowerShell con Azure Storage](../storage/storage-powershell-guide-full.md).

## <a name="next-steps"></a>Pasos siguientes
* Si se produjeron problemas durante la implementación, el paso siguiente sería consultar [Solución de errores comunes de implementación de Azure con Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)
* Aprenda a administrar la máquina virtual que ha creado. Para ello, consulte [Administración de máquinas virtuales con Azure Resource Manager y PowerShell](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



---
title: Creación y administración de máquinas virtuales en DevTest Labs con la CLI de Azure | Microsoft Docs
description: Aprenda a usar Azure DevTest Labs para crear y administrar máquinas virtuales con la CLI de Azure 2.0
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 0f6713b9b8704e813ab1fd77ab1cf4e71e7f6670
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33783354"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Creación y administración de máquinas virtuales con DevTest Labs mediante la CLI de Azure
Este inicio rápido le ayudará a crear, iniciar, conectarse, actualizar y limpiar una máquina de desarrollo en el laboratorio. 

Antes de empezar:

* Si no se ha creado un laboratorio, encontrará instrucciones [aquí](devtest-lab-create-lab.md).

* [Instale CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). Para empezar, ejecute az login para crear una conexión con Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Creación y comprobación de la máquina virtual 
Cree una máquina virtual desde una imagen de Marketplace mediante autenticación ssh.
```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```
> [!NOTE]
> Coloque el nombre del **grupo de recursos del laboratorio** en el parámetro --resource-group.
>

Si desea crear una máquina virtual mediante una fórmula, utilice el parámetro --formula en [az lab vm create](https://docs.microsoft.com/cli/azure/lab/vm#az_lab_vm_create).


Compruebe que la máquina virtual esté disponible.
```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>Inicio y conexión a la máquina virtual
Inicie una máquina virtual.
```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```
> [!NOTE]
> Coloque el nombre del **grupo de recursos del laboratorio** en el parámetro --resource-group.
>

Conéctese a una máquina virtual: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) o [Escritorio remoto](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Actualización de la máquina virtual
Aplique artefactos a una máquina virtual.
```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

Muestre los artefactos disponibles en el laboratorio.
```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand "properties(\$expand=artifacts)" --query 'artifacts[].{artifactId: artifactId, status: status}'
```
```json
{
  "artifactId": "/subscriptions/abcdeftgh1213123/resourceGroups/lisalab123RG822645/providers/Microsoft.DevTestLab/labs/lisalab123/artifactSources/public repo/artifacts/linux-install-nodejs",
  "status": "Succeeded"
}
```

## <a name="stop-and-delete-the-virtual-machine"></a>Detención y eliminación de la máquina virtual    
Detenga una máquina virtual.
```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Elimine una máquina virtual.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
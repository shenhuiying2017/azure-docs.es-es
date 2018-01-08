---
title: "Creación de un conjunto de escalado de máquinas virtuales con Linux con una plantilla de Azure | Microsoft Docs"
description: "Aprenda a crear rápidamente un conjunto de escalado de máquinas virtuales con Linux con una plantilla de Azure Resource Manager que implementa una aplicación de ejemplo y configura reglas de escalado automático."
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/19/2017
ms.author: iainfou
ms.openlocfilehash: b07bdd0739dabb05ef7012051b7ac28af3aaddaf
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-linux-virtual-machine-scale-set-with-an-azure-template"></a>Creación de un conjunto de escalado de máquinas virtuales con Linux con una plantilla de Azure
El conjunto de escalado de máquinas virtuales le permite implementar y administrar un conjunto de máquinas virtuales de escalado automático idénticas. Puede escalar el número de máquinas virtuales del conjunto de escalado manualmente o definir reglas de escalado automático basado en el uso de recursos tales como la CPU, la demanda de memoria o el tráfico de red. En este artículo introductorio, debe crear un conjunto de escalado de máquinas virtuales con Linux con una plantilla de Azure Resource Manager. También puede crear un conjunto de escalado mediante la [CLI de Azure 2.0](virtual-machine-scale-sets-create-cli.md), [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md) o [Azure Portal](virtual-machine-scale-sets-create-portal.md).

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este tutorial es preciso que ejecute la CLI de Azure de la versión 2.0.20 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 


## <a name="define-a-scale-set-in-a-template"></a>Definición de un conjunto de escalado en una plantilla
Las plantillas de Azure Resource Manager le permiten implementar grupos de recursos relacionados. Las plantillas se escriben en notación de objetos JavaScript (JSON) y definen el entorno de infraestructura de Azure completo para la aplicación. En una única plantilla, puede crear el conjunto de escalado de máquinas virtuales, instalar aplicaciones y configurar reglas de escalado automático. Con el uso de variables y parámetros, esta plantilla se puede reutilizar para actualizar los conjuntos de escalado existentes o crear más. Puede implementar plantillas mediante Azure Portal, la CLI de Azure 2.0 o Azure PowerShell, o bien desde las canalizaciones de integración continua o entrega continua (CI/CD).

Para obtener más información sobre las plantillas, consulte [Información general de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment).

Para crear una escala con una plantilla, debe definir los recursos adecuados. Las partes principales del tipo de recurso del conjunto de escalado de máquinas virtuales son:

| Propiedad                     | Descripción de la propiedad                                  | Valor de la plantilla de ejemplo                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| Tipo                         | Tipo de recurso de Azure que se creará                            | Microsoft.Compute/virtualMachineScaleSets |
| Nombre                         | Nombre del conjunto de escalado                                       | myScaleSet                                |
| location                     | Ubicación donde se creará el conjunto de escalado                     | Este de EE. UU                                   |
| sku.name                     | Tamaño de VM para cada instancia de conjunto de escalado                  | Standard_A1                               |
| sku.capacity                 | Número de instancias de VM que se crearán inicialmente           | 2                                         |
| upgradePolicy.mode           | Modo de actualización de la instancia de VM cuando se producen cambios              | Automático                                 |
| imageReference               | Imagen personalizada o plataforma que se usará para las instancias de VM | Canonical Ubuntu Server 16.04-LTS         |
| osProfile.computerNamePrefix | Prefijo de nombre para cada instancia de VM                     | myvmss                                    |
| osProfile.adminUsername      | Nombre de usuario para cada instancia de VM                        | azureuser                                 |
| osProfile.adminPassword      | Contraseña para cada instancia de VM                        | P@ssw0rd!                                 |

 En el siguiente ejemplo se muestra la definición del recurso del conjunto de escalado principal. Para personalizar una plantilla de conjunto de escalado, puede cambiar el tamaño de la VM o la capacidad inicial, o usar otra plataforma o una imagen personalizada.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US",
  "apiVersion": "2017-12-01",
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

 Para proporcionarle un ejemplo corto, no se muestra la configuración de la tarjeta adaptadora de red virtual (NIC). Tampoco se muestran los componentes adicionales, como un equilibrador de carga. [Al final de este artículo](#deploy-the-template), se muestra una plantilla de conjunto de escalado completa.


## <a name="install-an-application"></a>Instalación de una aplicación
Cuando se implementa un conjunto de escalado, las extensiones de VM pueden proporcionar tareas de configuración y automatización posteriores a la implementación, como la instalación de una aplicación. Los scripts se pueden descargar desde Azure Storage o GitHub, o se pueden proporcionar a Azure Portal en el tiempo de ejecución de la extensión. Para aplicar una extensión al conjunto de escalado, agregue la sección *extensionProfile* en el ejemplo anterior del recurso. Normalmente, el perfil de extensión define las propiedades siguientes:

- Tipo de extensión
- Editor de la extensión
- Versión de la extensión
- Ubicación de los scripts de configuración o instalación
- Comandos que se ejecutarán en las instancias de VM

La plantilla [Servidor HTTP de Python en Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) usa la extensión de script personalizada para instalar [Bottle](http://bottlepy.org/docs/dev/), un marco web de Python y un servidor HTTP sencillo. 

Se definen dos scripts en **fileUris** - *installserver.sh* y *workserver.py*. Estos archivos se descargan desde GitHub y, a continuación, *commandToExecute* ejecuta `bash installserver.sh` para instalar y configurar la aplicación:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
            "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
          ],
          "commandToExecute": "bash installserver.sh"
        }
      }
    }
  ]
}
```


## <a name="deploy-the-template"></a>Implementación de la plantilla
Puede implementar la plantilla [Servidor HTTP de Python en Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) con el botón **Implementar en Azure**. Este botón abre Azure Portal, carga la plantilla completa y pide algunos parámetros, como un nombre para el conjunto de escalado, el recuento de instancias y las credenciales de administrador.

[![Implementación de plantillas en Azure](media/virtual-machine-scale-sets-create-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-bottle-autoscale%2Fazuredeploy.json)

También puede utilizar la CLI de Azure 2.0 para instalar el servidor HTTP de Python en Linux con [az group deployment create](/cli/azure/group/deployment#create) como se indica a continuación:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location EastUS

# Deploy template into resource group
az group deployment create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/azuredeploy.json
```

Responda a los avisos para proporcionar un nombre para el conjunto de escalado, el recuento de instancias y las credenciales de administrador de las instancias de VM. El proceso de creación del conjunto de escalado y de los recursos compatibles tarda unos minutos.


## <a name="test-your-sample-application"></a>Prueba de la aplicación de ejemplo
Para ver la aplicación en acción, obtenga la dirección IP pública del equilibrador de carga con [az network public-ip list](/cli/azure/network/public-ip#show) según se indica a continuación:

```azurecli-interactive
az network public-ip list \
    --resource-group myResourceGroup \
    --query [*].ipAddress -o tsv
```

Escriba la dirección IP pública del equilibrador de carga en un explorador web con el formato *http://publicIpAddress:9000/do_work*. El equilibrador de carga distribuye el tráfico a una de las instancias de VM, como se muestra en el ejemplo siguiente:

![Página web predeterminada de NGINX](media/virtual-machine-scale-sets-create-template/running-python-app.png)


## <a name="clean-up-resources"></a>Limpieza de recursos
Cuando ya no lo necesite, puede usar el comando [az group delete](/cli/azure/group#delete) para quitar el grupo de recursos, el conjunto de escalado y todos los recursos relacionados como se indica a continuación:

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>pasos siguientes
En este artículo introductorio, creó un conjunto de escalado de Linux con una plantilla de Azure y usó la extensión de script personalizada para instalar un servidor web de Python básico en las instancias de VM. Para obtener mayor escalabilidad y automatización, expanda el conjunto de escalado con los siguientes artículos de procedimientos:

- [Implementación de la aplicación en conjuntos de escalado de máquinas virtuales](virtual-machine-scale-sets-deploy-app.md)
- Escalado automático con la [CLI de Azure](virtual-machine-scale-sets-autoscale-cli.md), [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md) o [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Uso de las actualizaciones automáticas del sistema operativo para las instancias de VM del conjunto de escalado](virtual-machine-scale-sets-automatic-upgrade.md)

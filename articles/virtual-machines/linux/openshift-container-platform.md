---
title: "Implementación de OpenShift Container Platform en Azure | Microsoft Docs"
description: Implemente OpenShift Container Platform en Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 81d1e2a92a24d43c6324b4fe026680c379e656da
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Implementación de OpenShift Container Platform en Azure

Puede usar uno de los diversos métodos para implementar OpenShift Container Platform en Azure:

- Puede implementar manualmente los componentes necesarios de la infraestructura Azure y, a continuación, seguir la [documentación](https://docs.openshift.com/container-platform/3.6/welcome/index.html) de OpenShift Container Platform.
- También puede usar una plantilla existente de [Resource Manager](https://github.com/Microsoft/openshift-container-platform/) que simplifica la implementación del clúster de OpenShift Container Platform.
- Otra opción consiste en usar la [oferta de Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

En todos los casos, se requiere una suscripción a Red Hat. Durante la implementación, la instancia de Red Hat Enterprise Linux está registrada en la suscripción de Red Hat y asociada al identificador de grupo que contiene los derechos para OpenShift Container Platform.
Asegúrese de que tiene un nombre de usuario de Red Hat Subscription Manager (RHSM), una contraseña y un identificador de grupo válidos. Puede comprobar la información de registro iniciando sesión en https://access.redhat.com.

## <a name="deploy-by-using-the-openshift-container-platform-resource-manager-template"></a>Implementación con la plantilla de Resource Manager para OpenShift Container Platform

Para realizar la implementación mediante la plantilla de Resource Manager, se usa un archivo de parámetros para proporcionar los parámetros de entrada. Para personalizar algún elemento de implementación que no sea posible con los parámetros de entrada, bifurque el repositorio de GitHub y cambie los elementos adecuados.

Algunas opciones de personalización comunes incluyen, entre otras:

- CIDR de red virtual (variable en azuredeploy.json)
- Tamaño de máquina virtual de bastión (variable en azuredeploy.json)
- Convenciones de nomenclatura (variables en azuredeploy.json)
- Especificaciones del clúster de OpenShift, modificadas mediante el archivo de hosts (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Configuración del archivo de parámetros

Use el valor `appId` de la entidad de servicio que creó anteriormente para el parámetro `aadClientId`. 

En el ejemplo siguiente se crea un archivo de parámetros llamado azuredeploy.parameters.json con todas las entradas necesarias.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "true"
        },
        "enableCockpit": {
            "value": "false"
        },
        "rhsmUsernamePasswordOrActivationKey": {
            "value": "usernamepassword"
        },
        "rhsmUsernameOrOrgId": {
            "value": "{RHSM Username}"
        },
        "rhsmPasswordOrActivationKey": {
            "value": "{RHSM Password}"
        },
        "rhsmPoolId": {
            "value": "{Pool ID}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

Reemplace los elementos entre corchetes con su información específica.

### <a name="deploy-by-using-azure-cli"></a>Implementación con la CLI de Azure

> [!NOTE] 
> El comando siguiente requiere la CLI de Azure 2.0.8 o una versión posterior. Puede comprobar la versión de la CLI con el comando `az --version`. Para actualizar la versión de la CLI, consulte [Instalación de la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

En el ejemplo siguiente se implementa el clúster de OpenShift y todos los recursos relacionados en un grupo de recursos llamado myResourceGroup, con el nombre de implementación myOpenShiftCluster. Se hace referencia a la plantilla directamente desde el repositorio de GitHub y se usa un archivo de parámetros local denominado azuredeploy.parameters.json.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

La implementación tarda al menos 30 minutos en completarse, según el número total de nodos implementados. La dirección URL de la consola de OpenShift y el nombre DNS del maestro OpenShift se imprimen en el terminal cuando concluye la implementación.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="deploy-by-using-the-openshift-container-platform-azure-marketplace-offer"></a>Implementación con la oferta de Azure Marketplace de OpenShift Container Platform

La manera más sencilla de implementar OpenShift Container Platform en Azure es usar la [oferta de Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Se trata de la opción más sencilla, pero también tiene funcionalidades de personalización limitadas. La oferta incluye tres opciones de configuración:

- **Pequeña**: implementa un clúster sin alta disponibilidad con un nodo maestro, un nodo de infraestructura, dos nodos de aplicación y un nodo bastión. Todos los nodos tienen tamaños de máquina virtual DS2v2 estándar. Este clúster requiere 10 núcleos en total y es adecuado para pruebas a pequeña escala.
- **Media**: implementa un clúster de alta disponibilidad con tres nodos maestros, dos nodos de infraestructura, cuatro nodos de aplicación y un nodo bastión. Todos los nodos excepto el bastión tienen tamaños de máquina virtual DS3v2 estándar. El nodo bastión es DS2v2 estándar. Este clúster requiere 38 núcleos.
- **Grande**: implementa un clúster de alta disponibilidad con tres nodos maestros, dos nodos de infraestructura, seis nodos de aplicación y un nodo bastión. Los nodos de infraestructura y maestro tienen tamaños de máquina virtual DS3v2 estándar. Los nodos de aplicación tienen tamaños de máquina virtual DS4v2 estándar y el nodo bastión tiene el tamaño DS2v2 estándar. Este clúster requiere 70 núcleos.

La configuración del Proveedor de soluciones en la nube de Azure es opcional para los tamaños de clúster mediano y grande. El tamaño de clúster pequeño no ofrece una opción para configurar el Proveedor de soluciones en la nube de Azure.

## <a name="connect-to-the-openshift-cluster"></a>Conexión con el clúster de OpenShift

Cuando concluya la implementación, conéctese a la consola de OpenShift con el explorador mediante `OpenShift Console Uri`. Como alternativa, puede conectarse al maestro de OpenShift con el siguiente comando:

```bash
$ ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no se necesiten, puede usar el comando [az group delete](/cli/azure/group#delete) para quitar el grupo de recursos, el clúster de OpenShift y todos los recursos relacionados.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

- [Tareas posteriores a la implementación](./openshift-post-deployment.md)
- [Solución de problemas de implementación de OpenShift en Azure](./openshift-troubleshooting.md)
- [Introducción a OpenShift Container Platform](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)

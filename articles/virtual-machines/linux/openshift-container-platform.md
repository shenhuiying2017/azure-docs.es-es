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
ms.openlocfilehash: c91b7232b2f87e0b4b5e659126b96a6ef8b4202c
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2017
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Implementación de OpenShift Container Platform en Azure

Existen varias maneras de implementar la plataforma de contenedores OpenShift en Azure. Puede implementar manualmente todos los componentes necesarios de la infraestructura Azure y, a continuación, seguir la [documentación](https://docs.openshift.com/container-platform/3.6/welcome/index.html) de OpenShift Container Platform.
También puede usar una plantilla existente de Resource Manager que simplifica la implementación del clúster de OpenShift Container Platform. Dicha plantilla se puede encontrar [aquí](https://github.com/Microsoft/openshift-container-platform/).

Otra opción consiste en usar la [oferta de Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

En ambos casos, se requiere una suscripción a Red Hat. Durante la implementación, la instancia RHEL está registrada en la suscripción de Red Hat y asociada al identificador de grupo que contiene los derechos para OpenShift Container Platform.
Asegúrese de que tiene un nombre de usuario, una contraseña y un identificador de grupo válidos para el administrador de suscripciones de Red Hat (nombre de usuario de RHSM, contraseña de RHSM e identificador de grupo). Puede comprobar la información de registro en https://access.redhat.com.

## <a name="deploy-using-the-openshift-container-platform-resource-manager-template"></a>Implementación de la plantilla de Resource Manager para OpenShift Container Platform

Para realizar la implementación mediante la plantilla de Resource Manager, se usa un archivo de parámetros para proporcionar todos los parámetros de entrada. Si desea personalizar algún elemento de implementación que no sea posible con los parámetros de entrada, bifurque el repositorio de GitHub y cambie los elementos adecuados.

Algunas opciones de personalización comunes incluyen (entre otras):

- VNet CIDR [variable en azuredeploy.json]
- Tamaño de máquina virtual de bastión [variable en azuredeploy.json]
- Convenciones de nomenclatura [variables en azuredeploy.json]
- Especificaciones del clúster de OpenShift: modificadas mediante el archivo de hosts [deployOpenShift.sh]

### <a name="configure-parameters-file"></a>Configuración del archivo de parámetros

Use el valor `appId` de la entidad de servicio que creó anteriormente para el parámetro `aadClientId`. 

En el ejemplo siguiente se crea un archivo de parámetros llamado **azuredeploy.parameters.json** con todas las entradas necesarias.

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

Reemplace los elementos entre corchetes {} por la información pertinente.

### <a name="deploy-using-azure-cli"></a>Implementación con la CLI de Azure

> [!NOTE] 
> El comando siguiente requiere la CLI de Azure 2.0.8 o una versión posterior. Puede comprobar la versión de az CLI con el comando `az --version`. Para actualizar la versión de la CLI, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli).

En el ejemplo siguiente se implementa el clúster de OpenShift y todos los recursos relacionados en un grupo de recursos llamado myResourceGroup con una implementación llamada myOpenShiftCluster. Se hace referencia a la plantilla directamente desde el repositorio de GitHub y se usa un archivo de parámetros local denominado **azuredeploy.parameters.json**.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

La implementación tarda al menos 30 minutos en completarse según el número total de nodos implementados. La dirección URL de la consola de OpenShift y el nombre DNS del maestro OpenShift se imprime en el terminal cuando se haya completado la implementación.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="deploy-using-openshift-container-platform-marketplace-offer"></a>Implementación con la oferta de Marketplace de OpenShift Container Platform

La manera más sencilla de implementar OpenShift Container Platform en Azure es usar la [oferta de Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Esta opción es la más sencilla, pero también tiene funcionalidades de personalización limitadas. La oferta incluye tres opciones de configuración:

- Pequeña: implementa un clúster sin alta disponibilidad con un nodo maestro, un nodo de infraestructura, dos nodos de aplicación y un nodo bastión. Todos los nodos tienen tamaños de máquina virtual DS2v2 estándar. Este clúster requiere 10 núcleos en total y es adecuado para pruebas a pequeña escala.
- Media: implementa un clúster de alta disponibilidad con tres nodos maestros, dos nodos de infraestructura, cuatro nodos de aplicación y un nodo bastión. Todos los nodos excepto el bastión tienen tamaños de máquina virtual S3v2 estándar. El nodo bastión es un DS2v2 estándar. Este clúster requiere 38 núcleos.
- Grande: implementa un clúster de alta disponibilidad con tres nodos maestros, dos nodos de infraestructura, seis nodos de aplicación y un nodo bastión. Los nodos maestros y de infraestructura son tamaños de máquina virtual DS3v2 estándar, los nodos de aplicación son tamaños de máquina virtual DS4v2 estándar y el nodo bastión es un tamaño DS2v2 estándar. Este clúster requiere 70 núcleos.

La configuración del proveedor de nube de Azure es opcional para los tamaños de clúster mediano y grande. El tamaño de clúster pequeño no ofrece una opción para configurar el proveedor de nube de Azure.

## <a name="connect-to-the-openshift-cluster"></a>Conexión con el clúster de OpenShift

Cuando se haya completado la implementación, conéctese a la consola de OpenShift con el explorador usando `OpenShift Console Uri`. Como alternativa, puede conectarse al maestro de OpenShift mediante el siguiente comando:

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
- [Getting Started with OpenShift Container Platform](https://docs.openshift.com/container-platform/3.6/getting_started/index.html) (Introducción a OpenShift Container Platform)

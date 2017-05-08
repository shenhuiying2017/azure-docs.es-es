---
title: "Entidad de servicio de clúster de Azure Kubernetes | Microsoft Docs"
description: "Cree y administre una entidad de servicio de Azure Active Directory en un clúster de Azure Container Service con Kubernetes"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/21/2017
ms.author: danlep
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: b76020e3e5855a63c416851d9b9adefdbdc5874a
ms.contentlocale: es-es
ms.lasthandoff: 05/03/2017


---

# <a name="about-the-azure-active-directory-service-principal-for-a-kubernetes-cluster-in-azure-container-service"></a>Acerca de la entidad de servicio de Azure Active Directory para un clúster de Kubernetes en Azure Container Service



En Azure Container Service, Kubernetes requiere una [entidad de servicio de Azure Active Directory](../active-directory/active-directory-application-objects.md), como una cuenta de servicio para interactuar con las API de Azure. La entidad de servicio se necesita para administrar dinámicamente recursos como las [rutas definidas por el usuario](../virtual-network/virtual-networks-udr-overview.md) y [Azure Load Balancer](../load-balancer/load-balancer-overview.md) de nivel 4.

Este artículo muestra distintas opciones para especificar a una entidad de servicio para el clúster de Kubernetes. Por ejemplo, si instaló y configuró la [CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2), puede ejecutar el comando [`az acs create`](https://docs.microsoft.com/en-us/cli/azure/acs#create) para crear el clúster de Kubernetes y la entidad de servicio al mismo tiempo.



## <a name="requirements-for-the-service-principal"></a>Requisitos de la entidad de servicio

A continuación se indican los requisitos de la entidad de servicio de Azure Active Directory en un clúster de Kubernetes en Azure Container Service. 

* **Ámbito**: el grupo de recursos en el que se implementa el clúster

* **Rol**: **colaborador**

* **Secreto de cliente**: debe ser una contraseña. Actualmente, no se puede usar a una entidad de servicio configurado para la autenticación de certificados.

> [!NOTE]
> Cada entidad de servicio está asociada a una aplicación de Azure Active Directory. La entidad de servicio de un clúster de Kubernetes puede asociarse a cualquier nombre de aplicación de Azure Active Directory válido.
> 


## <a name="service-principal-options-for-a-kubernetes-cluster"></a>Opciones de entidad de servicio para un clúster de Kubernetes

### <a name="option-1-pass-the-service-principal-client-id-and-client-secret"></a>Opción 1: Pasar el secreto e identificador de cliente de la entidad de servicio

Especifique el **identificador de cliente** (a menudo denominado `appId`, para el identificador de aplicación) y el **secreto de cliente** (`password`) de una entidad de servicio existente como parámetros al crear el clúster de Kubernetes. Si utiliza una entidad de servicio existente, asegúrese de que cumple los requisitos de la sección anterior. Si necesita crear una entidad de servicio, consulte [Creación de una entidad de servicio](#create-a-service-principal-in-azure-active-directory) en este mismo artículo.

Estos parámetros se pueden especificar al [implementar el clúster de Kubernetes](./container-service-deployment.md) desde el portal, la interfaz de la línea de comandos (CLI) de Azure 2.0, Azure PowerShell u otros métodos.

>[!TIP] 
>Al especificar el **identificador de cliente**, asegúrese de utilizar `appId`, no `ObjectId`, de la entidad de servicio.
>

En el ejemplo siguiente se muestra una forma de pasar los parámetros con la CLI de Azure 2.0 (consulte las [instrucciones de instalación y configuración](/cli/azure/install-az-cli2)). En este ejemplo se utiliza la [plantilla de inicio rápido de Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes).

1. [Descargue](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.parameters.json) el archivo de parámetros de plantilla `azuredeploy.parameters.json` de GitHub.

2. Para especificar la entidad de servicio, especifique los valores de `servicePrincipalClientId` y `servicePrincipalClientSecret` en el archivo. (También tendrá que especificar sus propios valores para `dnsNamePrefix` y `sshRSAPublicKey`. El segundo es la clave pública de SSH para acceder al clúster.) Guarde el archivo .

    ![Pasar los parámetros de la entidad de servicio](./media/container-service-kubernetes-service-principal/service-principal-params.png)

3. Ejecute el siguiente comando y use `--parameters` para establecer la ruta de acceso al archivo azuredeploy.parameters.json. Este comando implementa el clúster en un grupo de recursos que se crea llamado `myResourceGroup` en la región oeste de EE. UU.

    ```azurecli
    az login

    az account set --subscription "mySubscriptionID"

    az group create --name "myResourceGroup" --location "westus" 
    
    az group deployment create -g "myResourceGroup" --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.json" --parameters @azuredeploy.parameters.json
    ```


### <a name="option-2-generate-the-service-principal-when-creating-the-cluster-with-the-azure-cli-20"></a>Opción 2: Generar la entidad de servicio al crear el clúster con la CLI de Azure 2.0

Si ha instalado y configurado la [CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2), puede ejecutar el comando [`az acs create`](https://docs.microsoft.com/en-us/cli/azure/acs#create) para [crear el clúster](./container-service-create-acs-cluster-cli.md).

Al igual que sucede con otras opciones de creación de clústeres de Kubernetes, al ejecutar `az acs create` se pueden especificar los parámetros de una entidad de servicio existente. Sin embargo, si se omiten estos parámetros, Azure Container Service crea una entidad de servicio automáticamente. Esta operación se realiza de forma transparente durante la implementación. 

El siguiente comando crea un clúster de Kubernetes y genera tanto las claves de SSH como las credenciales de la entidad de servicio:

```console
az acs create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys --orchestrator-type kubernetes
```

## <a name="create-a-service-principal-in-azure-active-directory"></a>Creación de una entidad de servicio en Azure Active Directory

Si desea crear una entidad de servicio en Azure Active Directory para usarla en un clúster de Kubernetes, Azure le proporciona varios métodos. 

Los siguientes comandos de ejemplo muestran cómo hacerlo con la [ CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2). Como alternativa, puede crear una entidad de servicio mediante [Azure PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md), el [portal clásico](../azure-resource-manager/resource-group-create-service-principal-portal.md) u otros métodos.

> [!IMPORTANT]
> Asegúrese de revisar los requisitos de la entidad de servicio, que encontrará al principio de este artículo.
>

```azurecli
az login

az account set --subscription "mySubscriptionID"

az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/mySubscriptionID"
```

Devuelve un resultado similar al siguiente (que se muestra aquí):

![Creación de una entidad de servicio](./media/container-service-kubernetes-service-principal/service-principal-creds.png)

Se han resaltado el **identificador de cliente** (`appId`) y el **secreto de cliente** (`password`) que se usan como parámetros de la entidad de servicio para la implementación del clúster.


Confirme la entidad de servicio, para lo que debe abrir un nuevo shell y ejecutar los comandos siguientes, en donde se sustituyen `appId`, `password` y `tenant`:

```azurecli 
az login --service-principal -u yourClientID -p yourClientSecret --tenant yourTenant

az vm list-sizes --location westus
```

## <a name="additional-considerations"></a>Consideraciones adicionales


* Si especifica la entidad de servicio **Id. de cliente**, puede utilizar el valor de `appId` (como se muestra en este artículo) o la entidad de servicio correspondiente `name` (por ejemplo, `https://www.contoso.org/example`).

* Si usa el comando `az acs create` para generar la entidad de servicio automáticamente, sus credenciales se escriben en el archivo ~/.azure/acsServicePrincipal.json de la máquina que se usa para ejecutar el comando.

* En las máquinas virtuales principal y del nodo del clúster de Kubernetes, las credenciales de la entidad de servicio se almacenan en el archivo /etc/kubernetes/azure.json.

## <a name="next-steps"></a>Pasos siguientes

* [Introducción a Kubernetes](container-service-kubernetes-walkthrough.md) en el clúster de Container Service.


---
title: "Entidad de servicio de clúster de Azure Kubernetes | Microsoft Docs"
description: "Cree y administre una entidad de servicio de Azure Active Directory para un clúster de Kubernetes en Azure Container Service"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 14975454cbc0afcfbdbd3aa6b52983be4d4b1785
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-an-azure-ad-service-principal-for-a-kubernetes-cluster-in-container-service"></a>Configuración de una entidad de servicio de Azure AD para un clúster de Kubernetes en Container Service


En Azure Container Service, un clúster de Kubernetes requiere una [entidad de servicio de Azure Active Directory](../../active-directory/develop/active-directory-application-objects.md) para interactuar con las API de Azure. La entidad de servicio se necesita para administrar dinámicamente recursos como las [rutas definidas por el usuario](../../virtual-network/virtual-networks-udr-overview.md) y [Azure Load Balancer](../../load-balancer/load-balancer-overview.md) de nivel 4.


Este artículo muestra distintas opciones para configurar una entidad de servicio para el clúster de Kubernetes. Por ejemplo, si instaló y configuró la [CLI de Azure 2.0](/cli/azure/install-az-cli2), puede ejecutar el comando [`az acs create`](/cli/azure/acs#create) para crear el clúster de Kubernetes y la entidad de servicio al mismo tiempo.


## <a name="requirements-for-the-service-principal"></a>Requisitos de la entidad de servicio

Puede usar una entidad de servicio de Azure AD existente que cumpla los requisitos siguientes o crear una.

* **Ámbito**: la suscripción usada para implementar el clúster.

* **Rol**: **colaborador**

* **Secreto de cliente**: debe ser una contraseña. Actualmente, no se puede usar a una entidad de servicio configurado para la autenticación de certificados.

> [!IMPORTANT]
> Para crear una entidad de servicio, debe tener permisos suficientes para registrar una aplicación en su inquilino de Azure AD y asignar la aplicación a un rol en su suscripción. Para ver si tiene los permisos necesarios, [compruébelo en el portal](../../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions).
>

## <a name="option-1-create-a-service-principal-in-azure-ad"></a>Opción 1: Creación de una entidad de servicio en Azure AD

Si desea crear una entidad de servicio de Azure AD antes de implementar el clúster de Kubernetes, Azure le proporciona varios métodos.

Los siguientes comandos de ejemplo muestran cómo hacerlo con la [ CLI de Azure 2.0](../../azure-resource-manager/resource-group-authenticate-service-principal-cli.md). Como alternativa, puede crear una entidad de servicio mediante [Azure PowerShell](../../azure-resource-manager/resource-group-authenticate-service-principal.md), el [portal](../../azure-resource-manager/resource-group-create-service-principal-portal.md) u otros métodos.

```azurecli
az login

az account set --subscription "mySubscriptionID"

az group create --name "myResourceGroup" --location "westus"

az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/mySubscriptionID"
```

Devuelve una salida similar a la siguiente (que se muestra aquí):

![Creación de una entidad de servicio](./media/container-service-kubernetes-service-principal/service-principal-creds.png)

Se han resaltado el **identificador de cliente** (`appId`) y el **secreto de cliente** (`password`) que se usan como parámetros de la entidad de servicio para la implementación del clúster.


### <a name="specify-service-principal-when-creating-the-kubernetes-cluster"></a>Especificación de la entidad de servicio al crear el clúster de Kubernetes

Especifique el **identificador de cliente** (a menudo denominado `appId`, para el identificador de aplicación) y el **secreto de cliente** (`password`) de una entidad de servicio existente como parámetros al crear el clúster de Kubernetes. Asegúrese de que la entidad de servicio cumpla los requisitos al principio de este artículo.

Estos parámetros se pueden especificar al implementar el clúster de Kubernetes desde la [interfaz de la línea de comandos (CLI) de Azure 2.0](container-service-kubernetes-walkthrough.md), [Azure Portal](../dcos-swarm/container-service-deployment.md) u otros métodos.

>[!TIP]
>Al especificar el **identificador de cliente**, asegúrese de utilizar `appId`, no `ObjectId`, de la entidad de servicio.
>

En el ejemplo siguiente se muestra una forma de pasar los parámetros con la CLI de Azure 2.0. En este ejemplo se utiliza la [plantilla de inicio rápido de Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes).

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


## <a name="option-2-generate-a-service-principal-when-creating-the-cluster-with-az-acs-create"></a>Opción 2: Generación de una entidad de servicio al crear el clúster con `az acs create`

Si ejecuta el comando [`az acs create`](/cli/azure/acs#create) para crear el clúster de Kubernetes, tiene la opción para generar automáticamente una entidad de servicio.

Al igual que sucede con otras opciones de creación de clústeres de Kubernetes, al ejecutar `az acs create` se pueden especificar los parámetros de una entidad de servicio existente. Sin embargo, cuando se omiten estos parámetros, la CLI de Azure crea una automáticamente para usarla con Container Service. Esta operación se realiza de forma transparente durante la implementación.

El siguiente comando crea un clúster de Kubernetes y genera tanto las claves de SSH como las credenciales de la entidad de servicio:

```console
az acs create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys --orchestrator-type kubernetes
```

> [!IMPORTANT]
> Si su cuenta no tiene los permisos de Azure AD y de suscripción para crear una entidad de servicio, el comando genera un error similar a `Insufficient privileges to complete the operation.`
>

## <a name="additional-considerations"></a>Consideraciones adicionales

* Si no tiene permisos para crear una entidad de servicio en su suscripción, deberá solicitar al administrador de Azure AD o de su suscripción que se los asigne o pedirle una entidad de servicio que pueda usar con Azure Container Service.

* La entidad de servicio para Kubernetes forma parte de la configuración del clúster. Sin embargo, no use la identidad para implementar el clúster.

* Cada entidad de servicio está asociada a una aplicación de Azure AD. La entidad de servicio de un clúster de Kubernetes puede asociarse con cualquier nombre de aplicación de Azure AD válido (por ejemplo, `https://www.contoso.org/example`). La dirección URL de la aplicación no tiene por qué ser un punto de conexión real.

* Si especifica el valor **Id. de cliente** para la entidad de servicio, puede usar el valor de `appId` (como se muestra en este artículo) o la entidad de servicio correspondiente `name` (por ejemplo, `https://www.contoso.org/example`).

* En las máquinas virtuales principal y del agente en el clúster de Kubernetes, las credenciales de la entidad de servicio se almacenan en el archivo /etc/kubernetes/azure.json.

* Cuando use el comando `az acs create` para generar la entidad de servicio automáticamente, sus credenciales se escriben en el archivo ~/.azure/acsServicePrincipal.json de la máquina que se usa para ejecutar el comando.

* Cuando use el comando `az acs create` para generar automáticamente la entidad de servicio, esta también se puede autenticar con una [instancia de Azure Container Registry](../../container-registry/container-registry-intro.md) creada en la misma suscripción.

## <a name="next-steps"></a>Pasos siguientes

* [Introducción a Kubernetes](container-service-kubernetes-walkthrough.md) en el clúster de Container Service.

* Para solucionar problemas de la entidad de servicio para Kubernetes, consulte la [documentación del motor de ACS](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes.md#troubleshooting).
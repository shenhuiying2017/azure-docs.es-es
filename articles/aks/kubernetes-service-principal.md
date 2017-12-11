---
title: "Entidad de servicio del clúster de Azure Kubernetes"
description: "Cree y administre una entidad de servicio de Azure Active Directory para un clúster de Kubernetes en AKS"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: get-started-article
ms.date: 11/30/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a217f4cc8ac18888de8dfa803b4b8667a566dc0b
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2017
---
# <a name="service-principals-with-azure-container-service-aks"></a>Entidades de servicio con Azure Container Service (AKS)

Los clústeres de AKS requieren una [entidad de servicio de Azure Active Directory](../active-directory/develop/active-directory-application-objects.md) para interactuar con las API de Azure. La entidad de servicio se necesita para administrar dinámicamente recursos como las [rutas definidas por el usuario](../virtual-network/virtual-networks-udr-overview.md) y [Azure Load Balancer](../load-balancer/load-balancer-overview.md) de nivel 4.

Este artículo muestra distintas opciones para configurar una entidad de servicio para un clúster de Kubernetes en AKS.

## <a name="before-you-begin"></a>Antes de empezar


Para crear una entidad de servicio de Azure AD, es preciso tener los permisos suficientes para registrar una aplicación en un inquilino de Azure AD y asignarle un rol en una suscripción. Si no tiene los permisos necesarios, es posible que tenga que pedir al administrador de Azure AD o de la suscripción que asigne los permisos necesarios o que crear previamente una entidad de servicio para el clúster de Kubernetes.

También es preciso que esté instalada y configurada la versión 2.0.21 de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, vea [Instalación de la CLI de Azure](/cli/azure/install-azure-cli).

## <a name="create-sp-with-aks-cluster"></a>Creación de entidades de servicio con un clúster de AKS

Al implementar un clúster de AKS clúster con el comando `az aks create`, tiene la opción de que se genere automáticamente una entidad de servicio.

En el siguiente ejemplo, se crea un clúster de AKS y, como no se ha especificado una entidad de servicio existente, se crea una para el clúster. Para completar esta operación, la cuenta debe tener los derechos apropiados para crear un servicio principal.

```azurecli
az aks create --name myK8SCluster --resource-group myResourceGroup --generate-ssh-keys
```

## <a name="use-an-existing-sp"></a>Uso de una entidad de servicio existente

Con un clúster AKS se pueden usar las entidades de servicio de Azure AD existentes o se puede crear una previamente. Esto resulta útil cuando se implementa un clúster desde Azure Portal, donde hay que proporcionar la información de la entidad de servicio. Cuando se usa una entidad de servicio existente, el secreto de cliente debe configurarse como una contraseña.

## <a name="pre-create-a-new-sp"></a>Creación previa de una entidad de servicio nueva

Para crear la entidad de servicio con la CLI de Azure, utilice el comando [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac).

```azurecli
az ad sp create-for-rbac --skip-assignment
```

La salida es similar a la siguiente. Tome nota de `appId` y `password`. Estos valores se usan al crear un clúster de AKS.

```json
{
  "appId": "7248f250-0000-0000-0000-dbdeb8400d85",
  "displayName": "azure-cli-2017-10-15-02-20-15",
  "name": "http://azure-cli-2017-10-15-02-20-15",
  "password": "77851d2c-0000-0000-0000-cb3ebc97975a",
  "tenant": "72f988bf-0000-0000-0000-2d7cd011db47"
}
```

## <a name="use-an-existing-sp"></a>Uso de una entidad de servicio existente

Si usa una entidad de servicio creada previamente, incluya `appId` y `password` como valores de los argumentos del comando `az aks create`.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myK8SCluster --service-principal <appId> --client-secret <password>
```

Si va a implementar un clúster de AKS mediante Azure Portal, escriba el valor `appId` en el campo **Identificador de cliente de la entidad de servicio** y el valor `password` en el campo **Service principal client secret** (Secreto de cliente de la entidad de servicio) en el formulario de configuración del clúster de AKS.

![Imagen de la exploración hasta Azure Vote](media/container-service-kubernetes-service-principal/sp-portal.png)

## <a name="additional-considerations"></a>Consideraciones adicionales

Cuando trabaje con entidades de servicio de AKS y Azure AD, tenga en cuenta lo siguiente.

* La entidad de servicio para Kubernetes forma parte de la configuración del clúster. Sin embargo, no use la identidad para implementar el clúster.
* Cada entidad de servicio está asociada a una aplicación de Azure AD. La entidad de servicio de un clúster de Kubernetes puede asociarse con cualquier nombre de aplicación de Azure AD válido (por ejemplo, `https://www.contoso.org/example`). La dirección URL de la aplicación no tiene por qué ser un punto de conexión real.
* Si especifica el valor **Id. de cliente** para la entidad de servicio, puede usar el valor de `appId` (como se muestra en este artículo) o la entidad de servicio correspondiente `name` (por ejemplo, `https://www.contoso.org/example`).
* En las máquinas virtuales principal y nodo del clúster de Kubernetes, las credenciales de la entidad de servicio se almacenan en el archivo `/etc/kubernetes/azure.json`.
* Cuando use el comando `az aks create` para generar la entidad de servicio automáticamente, sus credenciales se escriben en el archivo `~/.azure/acsServicePrincipal.json` de la máquina que se usa para ejecutar el comando.
* Cuando use el comando `az aks create` para generar automáticamente la entidad de servicio, esta también se puede autenticar con una [instancia de Azure Container Registry](../container-registry/container-registry-intro.md) creada en la misma suscripción.
* Al eliminar un clúster de AKS creado mediante `az aks create`, no se eliminará la entidad de servicio que se creó automáticamente. Puede usar `az ad sp delete --id $clientID` para eliminarlo.

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de las entidades de servicio de Azure Active Directory, consulte la documentación de las aplicaciones de Azure AD.

> [!div class="nextstepaction"]
> [Objetos de aplicación y de entidad de servicio](../active-directory/develop/active-directory-application-objects.md)

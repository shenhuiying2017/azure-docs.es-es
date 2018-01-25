---
title: "Autenticación con Azure Container Registry desde Azure Container Service"
description: "Aprenda a proporcionar acceso a las imágenes del registro de contenedor privado desde Azure Container Service mediante una entidad de servicio de Azure Active Directory."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 1/12/2018
ms.author: nepeters
ms.openlocfilehash: d6f6688011ddebe2b486bb6ae00f1f3e095a931d
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/17/2018
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-service"></a>Autenticación con Azure Container Registry desde Azure Container Service

Cuando se usa Azure Container Registry (ACR) con Azure Container Service (AKS), es preciso establecer un mecanismo de autenticación. En este documento se detallan las configuraciones recomendadas para la autenticación entre estos dos servicios de Azure.

## <a name="grant-aks-access-to-acr"></a>Concesión de acceso de AKS a ACR

Cuando se crea un clúster de AKS, también se crea una entidad de servicio que administre su operatividad con los recursos de Azure. Esta entidad de servicio también se puede utilizar para la autenticación con un registro de control de acceso. Para ello, es preciso crear una asignación de roles para conceder a la entidad de servicio acceso de lectura al recurso de ACR. 

Para completar esta operación se puede usar el siguiente ejemplo.

```bash
#!/bin/bash

AKS_RESOURCE_GROUP=myAKSResourceGroup
AKS_CLUSTER_NAME=myAKSCluster
ACR_RESOURCE_GROUP=myACRResourceGroup
ACR_NAME=myACRRegistry

# Get the id of the service principal configured for AKS
CLIENT_ID=$(az aks show --resource-group $AKS_RESOURCE_GROUP --name $AKS_CLUSTER_NAME --query "servicePrincipalProfile.clientId" --output tsv)

# Get the ACR registry resource id
ACR_ID=$(az acr show --name $ACR_NAME --resource-group $ACR_RESOURCE_GROUP --query "id" --output tsv)

# Create role assignment
az role assignment create --assignee $CLIENT_ID --role Reader --scope $ACR_ID
```

## <a name="access-with-kubernetes-secret"></a>Acceso con un secreto de Kubernetes

En algunos casos, la entidad de servicio que usa AKS no tendrá el ámbito en el registro de ACR. Para estos casos, puede crear a una entidad de servicio único y que su ámbito sea solo el registro de ACR.

El siguiente script se puede utilizar para crear la entidad de servicio. 

```bash
#!/bin/bash

ACR_NAME=myacrinstance
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate the ACR login server and resource id. 
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create a contributor role assignment with a scope of the ACR resource. 
SP_PASSWD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME --role Reader --scopes $ACR_REGISTRY_ID --query password --output tsv)

# Get the service principle client id.
CLIENT_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output used when creating Kubernetes secret.
echo "Service principal ID: $CLIENT_ID"
echo "Service principal password: $SP_PASSWD"
```

Las credenciales de la entidad de servicio ahora se pueden almacenar en un [secreto de extracción de imágenes][image-pull-secret] de Kubernetes y se puede hacer referencia a ellas al ejecutar contenedores en un clúster de AKS. 

El siguiente comando crea el secreto de Kubernetes. Reemplace el nombre del servidor por el servidor de inicio de sesión de ACR, el nombre de usuario por el identificador de la entidad de servicio y la contraseña por la contraseña de la entidad de servicio.

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> 
```

El secreto de Kubernetes se puede utilizar en una implementación de pod, para lo que debe usarse el parámetro `ImagePullSecrets`. 

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: acr-auth-example
spec:
  template:
    metadata:
      labels:
        app: acr-auth-example
    spec:
      containers:
      - name: acr-auth-example
        image: myacrregistry.azurecr.io/acr-auth-example
      imagePullSecrets:
      - name: acr-auth
```

<!-- LINKS - external -->
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[image-pull-secret]: https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets

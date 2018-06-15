---
title: Implementación en Azure Container Instances desde Azure Container Registry
description: Obtenga información acerca de cómo implementar contenedores en Azure Container Instances mediante imágenes de contenedor de Azure Container Registry.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/30/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 7a7d2aa61f25bc4782c6a1a6744e329935477f8c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32168122"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Implementación en Azure Container Instances desde Azure Container Registry

Azure Container Registry es un registro privado basado en Azure para imágenes de contenedor de Docker. En este artículo se describe cómo implementar imágenes de contenedor almacenadas en una instancia de Azure Container Registry en Azure Container Instances.

## <a name="prerequisites"></a>requisitos previos

**Azure Container Registry**: se necesita una instancia de Azure Container Registry, y al menos una imagen de contenedor en el registro, para completar los pasos descritos en este artículo. Si necesita un registro, consulte [Creación de un registro de contenedor con la CLI de Azure](../container-registry/container-registry-get-started-azure-cli.md).

**CLI de Azure**: los ejemplos de línea de comandos de este artículo utilizan la [CLI de Azure](/cli/azure/) y tienen un formato adecuado para el shell de Bash. Puede [instalar la CLI de Azure](/cli/azure/install-azure-cli) localmente o usar [Azure Cloud Shell][cloud-shell-bash].

## <a name="configure-registry-authentication"></a>Configurar la autenticación del registro

En cualquier escenario de producción, se debe proporcionar acceso a una instancia de Azure Container Registry mediante [entidades de servicio](../container-registry/container-registry-auth-service-principal.md). Las entidades de servicio permiten proporcionar control de acceso basado en rol a las imágenes de contenedor. Por ejemplo, puede configurar una entidad de servicio con acceso de solo extracción a un registro.

En esta sección, cree un almacén de claves de Azure y una entidad de servicio y almacene las credenciales de la entidad de servicio en el almacén.

### <a name="create-key-vault"></a>Creación del almacén de claves

Si todavía no tiene un almacén en [Azure Key Vault](/azure/key-vault/), cree uno con la CLI de Azure mediante los siguientes comandos.

Actualice la variable `RES_GROUP` con el nombre del grupo de recursos en el que crear el almacén de claves y la variable `ACR_NAME` con el nombre de su registro de contenedor. Especifique un nombre para el nuevo almacén de claves en `AKV_NAME`. El nombre del almacén debe ser exclusivo en Azure, tener entre 3 y 24 caracteres alfanuméricos, comenzar con una letra y terminar con una letra o un número y no puede contener guiones consecutivos.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Creación de la entidad de servicio y almacenamiento de credenciales

Ahora debe crear una entidad de servicio y almacenar sus credenciales en el almacén de claves.

El siguiente comando usa [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] para crear la entidad de servicio y [az keyvault secret set][az-keyvault-secret-set] para almacenar la **contraseña** de la entidad de servicio en el almacén.

```azurecli
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name $ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role reader \
                --query password \
                --output tsv)
```

El argumento `--role` en el comando anterior configura la entidad de servicio con el rol *lector*, que le concede de solo extracción acceso al registro. Para conceder acceso de inserción y extracción, cambie el argumento `--role` a *colaborador*.

A continuación, almacene el *appId* de la entidad de servicio en el almacén, que es el **nombre de usuario** que pasa a Azure Container Registry para la autenticación.

```azurecli
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Ha creado una instancia de Azure Key Vault y almacenado dos secretos en ella:

* `$ACR_NAME-pull-usr`: el identificador de la entidad de servicio, para su uso como **nombre de usuario** del registro de contenedor.
* `$ACR_NAME-pull-pwd`: la contraseña de la entidad de servicio, para su uso como **contraseña** del registro de contenedor.

Ahora puede hacer referencia a estos secretos por nombre cuando usted o sus aplicaciones y servicios extraigan imágenes del registro.

## <a name="deploy-container-with-azure-cli"></a>Implementación de un contenedor con la CLI de Azure

Ahora que las credenciales de la entidad de servicio están almacenadas en secretos de Azure Key Vault, las aplicaciones y servicios pueden utilizarlas para acceder a su registro privado.

Ejecute el comando [az container create][az-container-create] siguiente para implementar una instancia del contenedor. El comando usa las credenciales de la entidad de servicio almacenadas en Azure Key Vault para autenticarse en el registro de contenedor y da por supuesto que ha insertado previamente la imagen [aci-helloworld](container-instances-quickstart.md) en el registro. Actualice el valor `--image` si desea usar otra imagen del registro.

```azurecli
az container create \
    --name aci-demo \
    --resource-group $RES_GROUP \
    --image $ACR_NAME.azurecr.io/aci-helloworld:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label aci-demo-$RANDOM \
    --query ipAddress.fqdn
```

El valor `--dns-name-label` debe ser único en Azure, por lo que el comando anterior anexa un número aleatorio a la etiqueta de nombre DNS del contenedor. La salida del comando muestra el nombre de dominio completo (FQDN) del contenedor, por ejemplo:

```console
$ az container create --name aci-demo --resource-group $RES_GROUP --image $ACR_NAME.azurecr.io/aci-helloworld:v1 --registry-login-server $ACR_NAME.azurecr.io --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) --dns-name-label aci-demo-$RANDOM --query ipAddress.fqdn
"aci-demo-25007.eastus.azurecontainer.io"
```

Una vez que se ha iniciado correctamente el contenedor, puede desplazarse a su FQDN en el explorador para comprobar que la aplicación se ejecuta correctamente.

## <a name="deploy-with-azure-resource-manager-template"></a>Implementación con plantillas de Azure Resource Manager

Puede especificar las propiedades de Azure Container Registry en una plantilla de Azure Resource Manager; para ello, se debe incluir la propiedad `imageRegistryCredentials` en la definición del grupo de contenedores:

```JSON
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

Para más información acerca de cómo hacer referencia a secretos de Azure Key Vault en una plantilla de Resource Manager, consulte [Uso de Azure Key Vault para pasar el valor de parámetro seguro durante la implementación](../azure-resource-manager/resource-manager-keyvault-parameter.md).

## <a name="deploy-with-azure-portal"></a>Implementación con Azure Portal

Si se mantienen imágenes de contenedor en Azure Container Registry, se puede crear fácilmente un contenedor en Azure Container Instances a través de Azure Portal.

1. En Azure Portal, vaya al registro de contenedor.

1. Seleccione **Repositorios**, elija el repositorio desde el que va a implementar, haga clic con el botón derecho en la etiqueta de la imagen de contenedor que desea implementar y seleccione **Ejecutar instancia**.

    !["Ejecutar instancia" en Azure Container Registry de Azure Portal][acr-runinstance-contextmenu]

1. Escriba un nombre para el contenedor y un nombre para el grupo de recursos. También puede cambiar los valores predeterminados si lo desea.

    ![Menú Crear para Azure Container Instances][acr-create-deeplink]

1. Una vez completada la implementación, puede ir al grupo de contenedores desde el panel de notificaciones para buscar la dirección IP y otras propiedades.

    ![Vista de detalles del grupo de contenedores de Azure Container Instances][aci-detailsview]

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de la autenticación de Azure Container Registry, consulte [Autenticación con un registro de contenedor de Azure](../container-registry/container-registry-authentication.md).

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-powershell]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-create]: /cli/azure/container#az_container_create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
---
title: 'Tutorial: Compilación de imágenes de contenedor en la nube con Azure Container Registry Build'
description: En este tutorial, aprenderá a compilar una imagen de contenedor de Docker en Azure con Azure Container Registry Build (ACR Build) para, después, implementarla en Azure Container Instances.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 0259f861016716ce2fdd10b866008cf5fcc305b4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-build-container-images-in-the-cloud-with-azure-container-registry-build"></a>Tutorial: Compilación de imágenes de contenedor en la nube con Azure Container Registry Build

**ACR Build** es un conjunto de características de Azure Container Registry que proporcionan compilaciones de imágenes de contenedor de Docker optimizadas y eficientes en Azure. En este artículo, aprenderá a usar la característica *Compilación rápida* de ACR Build. Compilación rápida amplía su "bucle interno" de desarrollo a la nube, proporcionándole una validación de compilaciones correctas y la inserción automática de las imágenes compiladas correctamente en el registro de contenedor. Las imágenes se compilan de forma nativa en la nube, cerca del registro, lo cual permite una implementación más rápida.

Todos sus conocimientos sobre archivos de Docker se pueden transferir directamente a ACR Build. No tiene que cambiar sus archivos de Docker para compilar en la nube con ACR Build, solo ejecutar el comando.

Este tutorial es la primera parte de una serie:

> [!div class="checklist"]
> * Obtención del código fuente de la aplicación de ejemplo
> * Compilación de una imagen de contenedor en Azure
> * Implementación de un contenedor en Azure Container Instances

En tutoriales posteriores, aprenderá a usar tareas de compilación de ACR Build para compilaciones automatizadas de imágenes de contenedor después de una confirmación de código y la actualización de la imagen base.

> [!IMPORTANT]
> ACR Build se encuentra actualmente en versión preliminar, y solo se admite en los registros de contenedor de Azure de las regiones **Este de EE. UU.** y **Europa Occidental**. Las versiones preliminares están a su disposición con la condición de que acepte los [términos de uso adicionales][terms-of-use]. Es posible que algunos de los aspectos de esta característica cambien antes de ofrecer disponibilidad general.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si quiere usar la CLI de Azure de forma local, debe tener la versión **2.0.32** de la CLI de Azure u otra posterior instalada. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0][azure-cli].

## <a name="prerequisites"></a>requisitos previos

### <a name="github-account"></a>Cuenta de GitHub

Cree una cuenta en https://github.com si aún no tiene una. Esta serie de tutoriales utiliza un repositorio de GitHub para mostrar compilaciones de imágenes automatizadas en ACR Build.

### <a name="fork-sample-repository"></a>Bifurcación del repositorio de ejemplo

A continuación, use la interfaz de usuario de GitHub para bifurcar el repositorio de ejemplo de la cuenta de GitHub. En este tutorial, compilará una imagen de contenedor desde el origen del repositorio y, en el tutorial siguiente, insertará una confirmación en la bifurcación del repositorio para iniciar una compilación automatizada.

Bifurque este repositorio: https://github.com/Azure-Samples/acr-build-helloworld-node

![Captura de pantalla del botón Bifurcar (resaltado) en GitHub][quick-build-01-fork]

### <a name="clone-your-fork"></a>Clonación de la bifurcación

Una vez que se ha bifurcado el repositorio, clone la bifurcación y especifique el directorio que contiene el clon local.

Clone el repositorio con `git`, reemplace **\<your-github-username\>** por su nombre de usuario de GitHub:

```azurecli-interactive
git clone https://github.com/<your-github-username>/acr-build-helloworld-node
```

Especifique el directorio que contiene el código fuente:

```azurecli-interactive
cd acr-build-helloworld-node
```

### <a name="bash-shell"></a>Shell de Bash

Los comandos de esta serie de tutoriales están formateados para el shell de Bash. Si prefiere usar PowerShell, el símbolo del sistema u otro shell, deberá ajustar la continuación de línea y el formato de la variable de entorno según sea necesario.

## <a name="build-in-azure-with-acr-build"></a>Compilación en Azure con ACR Build

Ahora que ha extraído el código fuente en su máquina, siga estos pasos para crear un registro de contenedor y compilar la imagen de contenedor con ACR Build.

Para facilitar la ejecución de comandos de ejemplo, los tutoriales de esta serie usan variables de entorno de shell. Ejecute el siguiente comando para establecer la variable `ACR_NAME`. Reemplace **\<registry-name\>** por un nombre único para el nuevo registro de contenedor. El nombre del registro debe ser único dentro de Azure y contener entre 5 y 50 caracteres alfanuméricos. Los demás recursos que cree en el tutorial se basan en este nombre con lo cual solo tendría que modificar esta primera variable.

```azurecli-interactive
ACR_NAME=<registry-name>
```

Con la variable de entorno del registro de contenedor rellena, ya puede copiar y pegar el resto de los comandos del tutorial sin necesidad de editar ningún valor. Ejecute los siguientes comandos para crear un grupo de recursos y un registro de contenedor:

```azurecli-interactive
RES_GROUP=$ACR_NAME # Resource Group name

az group create --resource-group $RES_GROUP --location eastus
az acr create --resource-group $RES_GROUP --name $ACR_NAME --sku Standard --location eastus
```

Ahora que tiene un registro, utilice ACR Build para compilar una imagen de contenedor desde el código de ejemplo. Ejecute el comando [az acr build][az-acr-build] para efectuar una *Compilación rápida*:

```azurecli-interactive
az acr build --registry $ACR_NAME --image helloacrbuild:v1 .
```

La salida del comando [az acr build][az-acr-build] se parece a la siguiente. Puede ver la carga del código fuente (el "contexto") en Azure y los detalles de la operación `docker build` que ACR Build ejecuta en la nube. Como ACR Build usa `docker build` para compilar las imágenes, no se requiere ningún cambio en los archivos de Docker para empezar a usar inmediatamente ACR Build.

```console
$ az acr build --registry $ACR_NAME --image helloacrbuild:v1 .
Sending build context (41.042 KiB) to ACR
Queued a build with ID: eastus1
Sending build context to Docker daemon  191.5kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
605ce1bd3f31: Pulling fs layer
f10758dcda1f: Pulling fs layer
4cbe43d669e5: Pulling fs layer

[...]

Removing intermediate container 2dbac02fb0e6
 ---> 670bbc77128d
Step 4/5 : EXPOSE 80
 ---> Running in 1d09ee337a47
Removing intermediate container 1d09ee337a47
 ---> f0739d333913
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in 1f019c4e4b24
Removing intermediate container 1f019c4e4b24
fbd7c8b9c17e: Preparing
26b0c207c4a9: Preparing
917e7cdebc8b: Preparing
9dfa40a0da3b: Preparing
7d7224b439b3: Pushed
9dfa40a0da3b: Pushed
fbd7c8b9c17e: Pushed
26b0c207c4a9: Pushed
917e7cdebc8b: Pushed
v1: digest: sha256:60d78f0a336a387ba93f04ecf22538d01bca985a277ac77d3813ce360aba0cb1 size: 1367
time="2018-04-18T18:28:29Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" mycontainerregistry.azurecr.io/helloacrbuild:v1"
"["mycontainerregistry.azurecr.io/helloacrbuild@sha256:60d78f0a336a387ba93f04ecf22538d01bca985a277ac77d3813ce360aba0cb1"]"
time="2018-04-18T18:28:30Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" node:9-alpine"
"["node@sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3"]"
ACR Builder discovered the following dependencies:
- image:
    registry: mycontainerregistry.azurecr.io
    repository: helloacrbuild
    tag: v1
    digest: sha256:60d78f0a336a387ba93f04ecf22538d01bca985a277ac77d3813ce360aba0cb1
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3

Build complete
Build ID: eastus1 was successful after 38.116951381s
```

Casi al final de la salida, ACR Build muestra las dependencias que se han detectado para la imagen. Esto permite a ACR Build automatizar las compilaciones de imágenes después de las actualizaciones de imágenes base como, por ejemplo, cuando se actualiza una imagen base con revisiones del sistema operativo o la plataforma. Puede aprender más información sobre la compatibilidad de ACR Build con las actualizaciones de imágenes base más adelante en esta serie de tutoriales.

## <a name="deploy-to-azure-container-instances"></a>Implementación en Azure Container Instances

De forma predeterminada, ACR Build inserta automáticamente las imágenes compiladas correctamente en el registro, lo que le permite implementarlas desde el registro de forma inmediata.

En esta sección, creará una instancia de Azure Key Vault y una entidad de servicio, posteriormente implementará el contenedor en Azure Container Instances (ACI) mediante las credenciales de la entidad de servicio.

### <a name="configure-registry-authentication"></a>Configurar la autenticación del registro

Todos los escenarios de producción deben usar [entidades de servicio][service-principal-auth] para acceder a un registro de contenedor de Azure. Las entidades de servicio permiten proporcionar control de acceso basado en rol a las imágenes de contenedor. Por ejemplo, puede configurar una entidad de servicio con acceso de solo extracción a un registro.

#### <a name="create-key-vault"></a>Creación del almacén de claves

Si todavía no tiene un almacén en [Azure Key Vault](/azure/key-vault/), cree uno con la CLI de Azure mediante los siguientes comandos.

```azurecli-interactive
AKV_NAME=$ACR_NAME-vault

az keyvault create --resource-group $RES_GROUP --name $AKV_NAME
```

#### <a name="create-service-principal-and-store-credentials"></a>Creación de la entidad de servicio y almacenamiento de credenciales

Ahora debe crear una entidad de servicio y almacenar sus credenciales en el almacén de claves.

Use el comando [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] para crear la entidad de servicio y [az keyvault secret set][az-keyvault-secret-set] para almacenar la **contraseña** de la entidad de servicio en el almacén:

```azurecli-interactive
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

A continuación, almacene el *appId* de la entidad de servicio en el almacén, que es el **nombre de usuario** que pasa a Azure Container Registry para la autenticación:

```azurecli-interactive
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

### <a name="deploy-container-with-azure-cli"></a>Implementación de un contenedor con la CLI de Azure

Ahora que las credenciales de la entidad de servicio están almacenadas como secretos de Azure Key Vault, las aplicaciones y servicios pueden utilizarlas para acceder a su registro privado.

Ejecute el comando [az container create][az-container-create] siguiente para implementar una instancia del contenedor. El comando usa las credenciales de la entidad de servicio almacenadas en Azure Key Vault para autenticarse en el registro de contenedor.

```azurecli-interactive
az container create \
    --resource-group $RES_GROUP \
    --name acr-build \
    --image $ACR_NAME.azurecr.io/helloacrbuild:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label acr-build-$ACR_NAME \
    --query "{FQDN:ipAddress.fqdn}" \
    --output table
```

El valor `--dns-name-label` debe ser único en Azure, por lo que el comando anterior anexa el nombre del registro de contenedor a la etiqueta de nombre DNS del contenedor. La salida del comando muestra el nombre de dominio completo (FQDN) del contenedor, por ejemplo:

```console
$ az container create \
>     --resource-group $RES_GROUP \
>     --name acr-build \
>     --image $ACR_NAME.azurecr.io/helloacrbuild:v1 \
>     --registry-login-server $ACR_NAME.azurecr.io \
>     --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
>     --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
>     --dns-name-label acr-build-$ACR_NAME \
>     --query "{FQDN:ipAddress.fqdn}" \
>     --output table
FQDN
-------------------------------------------
acr-build-1175.eastus.azurecontainer.io
```

Tome nota del nombre de dominio completo del contenedor ya que lo utilizará en la sección siguiente.

### <a name="verify-deployment"></a>Comprobación de la implementación

Para inspeccionar el proceso de inicio del contenedor, use el comando [az container attach][az-container-attach]:

```azurecli-interactive
az container attach --resource-group $RES_GROUP --name acr-build
```

La salida `az container attach` muestra en primer lugar el estado del contenedor cuando este extrae la imagen y se inicia y, luego, enlaza los flujos STDOUT y STDERR de la consola local con los del contenedor.

```console
$ az container attach --resource-group $RES_GROUP --name acr-build
Container 'acr-build' is in state 'Waiting'...
Container 'acr-build' is in state 'Running'...
(count: 1) (last timestamp: 2018-04-03 19:45:37+00:00) pulling image "mycontainerregistry.azurecr.io/helloacrbuild:v1"
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Successfully pulled image "mycontainerregistry.azurecr.io/helloacrbuild:v1"
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Created container with id 094ab4da40138b36ca15fc2ad5cac351c358a7540a32e22b52f78e96a4cb3413
(count: 1) (last timestamp: 2018-04-03 19:45:44+00:00) Started container with id 094ab4da40138b36ca15fc2ad5cac351c358a7540a32e22b52f78e96a4cb3413

Start streaming logs:
Server running at http://localhost:80
```

Cuando aparezca `Server running at http://localhost:80`, vaya al nombre completo de dominio del contenedor en el explorador para ver la aplicación en ejecución:

![Captura de pantalla de una aplicación de ejemplo representada en el explorador][quick-build-02-browser]

Para desasociar la consola del contenedor, pulse `Control+C`.

## <a name="clean-up-resources"></a>Limpieza de recursos

Detenga la instancia del contenedor con el comando [az container delete][az-container-delete]:

```azurecli-interactive
az container delete --resource-group $RES_GROUP --name acr-build
```

Para quitar *todos* los recursos que ha creado en este tutorial, incluido el registro de contenedor, el almacén de claves y la entidad de servicio, ejecute los comandos siguientes. Estos recursos se utilizarán en el [siguiente tutorial](container-registry-tutorial-build-task.md) de la serie, por lo que puede que quiera conservarlos si pasa directamente al tutorial siguiente.

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha probado el bucle interno con una compilación rápida, configure una **tarea de compilación** que desencadene las compilaciones de imágenes de contenedor cuando confirma el código fuente en un repositorio de Git:

> [!div class="nextstepaction"]
> [Desencadenamiento de compilaciones automáticas con tareas de compilación](container-registry-tutorial-build-task.md)

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-keyvault-create]: /cli/azure/keyvault/secret#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[service-principal-auth]: container-registry-auth-service-principal.md

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png

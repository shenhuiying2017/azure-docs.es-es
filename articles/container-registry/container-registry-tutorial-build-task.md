---
title: 'Tutorial: Automatización de compilaciones de imágenes de contenedor con Azure Container Registry Build'
description: En este tutorial, aprenderá a configurar una tarea de compilación que desencadena automáticamente compilaciones de imágenes de contenedor en la nube cuando envía código fuente a un repositorio Git.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: fba499441d092f4dce09d13d607dfc5de65d98b2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-automate-container-image-builds-with-azure-container-registry-build"></a>Tutorial: Automatización de compilaciones de imágenes de contenedor con Azure Container Registry Build

Además de [Compilación rápida](container-registry-tutorial-quick-build.md), ACR Build admite las compilaciones automatizadas de imágenes de contenedor de Docker con la *tarea de compilación*. En este tutorial va a usar la CLI de Azure para crear una tarea de compilación que desencadena automáticamente compilaciones de imágenes en la nube cuando confirma código fuente en un repositorio Git.

En este tutorial, segunda parte de la serie, se realizan las siguientes operaciones:

> [!div class="checklist"]
> * Creación de una tarea de compilación
> * Comprobación de la tarea de compilación
> * Visualización del estado de la compilación
> * Desencadenamiento de la tarea de compilación con una confirmación de código

En este tutorial se da por hecho que ya ha realizado los pasos de los [tutoriales anteriores](container-registry-tutorial-quick-build.md). Si aún no lo ha hecho, complete los pasos de la sección [Requisitos previos](container-registry-tutorial-quick-build.md#prerequisites) del tutorial anterior antes de continuar.

> [!IMPORTANT]
> ACR Build se encuentra actualmente en versión preliminar, y solo se admite en los registros de contenedor de Azure de las regiones **Este de EE. UU.** y **Europa Occidental**. Las versiones preliminares están a su disposición con la condición de que acepte los [términos de uso adicionales][terms-of-use]. Es posible que algunos de los aspectos de esta característica cambien antes de ofrecer disponibilidad general.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si quiere usar la CLI de Azure de forma local, debe tener la versión **2.0.32** de la CLI de Azure u otra posterior instalada. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0][azure-cli].

## <a name="prerequisites"></a>requisitos previos

### <a name="get-sample-code"></a>Obtención de código de ejemplo

En este tutorial se da por hecho que ya ha realizado los pasos de los [tutoriales anteriores](container-registry-tutorial-quick-build.md) y que ha bifurcado y clonado el repositorio de ejemplo. Si aún no lo ha hecho, complete los pasos de la sección [Requisitos previos](container-registry-tutorial-quick-build.md#prerequisites) del tutorial anterior antes de continuar.

### <a name="container-registry"></a>Registro de contenedor

Debe tener un registro de contenedor de Azure en la suscripción de Azure para completar este tutorial. Si necesita un registro, consulte el [tutorial anterior](container-registry-tutorial-quick-build.md) o esta [guía de inicio rápido: Creación de un registro de contenedor con la CLI de Azure](container-registry-get-started-azure-cli.md).

## <a name="build-task"></a>Tarea de compilación

Una tarea de compilación define las propiedades de una compilación automatizada, incluida la ubicación del código fuente de la imagen de contenedor y el evento que desencadena la compilación. Cuando se produce un evento definido en la tarea de compilación como, por ejemplo una confirmación en un repositorio Git, ACR Build inicia una compilación de imagen de contenedor en la nube. De forma predeterminada, a continuación, se inserta una imagen compilada correctamente en el registro de contenedor de Azure especificado en la tarea.

ACR Build admite actualmente los siguientes desencadenadores de la tarea de compilación:

* Confirmación en un repositorio Git
* Actualización de imagen base

## <a name="create-a-build-task"></a>Creación de una tarea de compilación

En esta sección, creará primero un token de acceso personal de GitHub (PAT) para usarlo con ACR Build. A continuación, creará una tarea de compilación que desencadena una compilación cuando se confirma el código en su bifurcación del repositorio.

### <a name="create-a-github-personal-access-token"></a>Creación de un token de acceso personal de GitHub

Para desencadenar una compilación en una confirmación en un repositorio Git, ACR Build necesita un token de acceso personal (PAT) para acceder al repositorio. Siga estos pasos para generar un token de acceso personal en GitHub:

1. Vaya a la página de creación del token de acceso personal en GitHub en https://github.com/settings/tokens/new
1. Escriba una breve **descripción** para el token, por ejemplo, "ACR Build Task Demo"
1. En **repo**, active **repo:status** y **public_repo**

   ![Captura de pantalla de la página de generación de un token de acceso personal en GitHub][build-task-01-new-token]

1. Seleccione el botón **Generar token** (puede que se le pida que confirme la contraseña)
1. Copie y guarde el token generado en una **ubicación segura** (usará este token cuando defina una tarea de compilación en la siguiente sección)

   ![Captura de pantalla del token de acceso personal que se generó en GitHub][build-task-02-generated-token]

### <a name="create-the-build-task"></a>Creación de la tarea de compilación

Ahora que ha completado los pasos necesarios para habilitar ACR Build para que lea el estado de confirmación y cree webhooks en un repositorio, puede crear una tarea de compilación que desencadene una compilación de imagen de contenedor después de las confirmaciones en el repositorio.

Primero, rellene estas variables de entorno de shell con valores adecuados para el entorno. Esto no es estrictamente necesario, pero hace que la ejecución de los comandos de varias líneas de la CLI de Azure en este tutorial sea un poco más fácil. Si no los rellena, deberá reemplazar manualmente cada valor siempre que aparezcan en los comandos de ejemplo.

```azurecli-interactive
ACR_NAME=mycontainerregistry # The name of your Azure container registry
GIT_USER=gituser             # Your GitHub user account name
GIT_PAT=personalaccesstoken  # The PAT you generated in the previous section
```

Ahora, cree la tarea de compilación mediante la ejecución del siguiente comando [az acr build-task create][az-acr-build-task-create]:

```azurecli-interactive
az acr build-task create \
    --registry $ACR_NAME \
    --name buildhelloworld \
    --image helloworld:{{.Build.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node \
    --branch master \
    --git-access-token $GIT_PAT
```

Esta tarea de compilación especifica que siempre que confirme código en la rama *maestra* del repositorio especificado por `--context`, ACR Build compilará la imagen de contenedor desde el código de esa rama. El argumento `--image` especifica un valor parametrizado de `{{.Build.ID}}` para la porción de la versión de la etiqueta de la imagen, lo que garantiza que la imagen compilada es correlativa a una compilación específica y que está etiquetada de forma exclusiva.

La salida de un comando [az acr build-task create][az-acr-build-task-create] que se ha ejecutado correctamente debe ser parecida a la siguiente:

```console
$ az acr build-task create \
>     --registry $ACR_NAME \
>     --name buildhelloworld \
>     --image helloworld:{{.Build.ID}} \
>     --context https://github.com/$GIT_USER/acr-build-helloworld-node \
>     --branch master \
>     --git-access-token $GIT_PAT
{
  "additionalProperties": {},
  "alias": "buildhelloworld",
  "creationDate": "2018-04-18T23:14:45.905395+00:00",
  "id": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry/buildTasks/buildhelloworld",
  "location": "eastus",
  "name": "buildhelloworld",
  "platform": {
    "additionalProperties": {},
    "cpu": 1,
    "osType": "Linux"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sourceRepository": {
    "additionalProperties": {},
    "isCommitTriggerEnabled": true,
    "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node",
    "sourceControlAuthProperties": null,
    "sourceControlType": "Github"
  },
  "status": "enabled",
  "tags": null,
  "timeout": null,
  "type": "Microsoft.ContainerRegistry/registries/buildTasks"
}

```

## <a name="test-the-build-task"></a>Comprobación de la tarea de compilación

Ahora tiene una tarea de compilación que define la compilación. Para probar la definición de compilación, desencadene una compilación manualmente mediante la ejecución del comando [az acr build-task run][az-acr-build-task-run]:

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld
```

De forma predeterminada, el comando `az acr build-task run` hace streaming de la salida del registro a la consola cuando se ejecuta el comando. En este caso, el resultado muestra que la compilación **eastus2** se ha puesto en cola y se ha compilado.

```console
$ az acr build-task run --registry mycontainerregistry --name buildhelloworld
Queued a build with build-id: eastus2.
Starting to stream the logs...
Cloning into '/root/acr-builder/src'...
time="2018-04-19T00:06:20Z" level=info msg="Running command git checkout master"
Already on 'master'
Your branch is up to date with 'origin/master'.
ffef1347389a008c9a8bfdf8c6a0ed78b0479894
time="2018-04-19T00:06:20Z" level=info msg="Running command git rev-parse --verify HEAD"
time="2018-04-19T00:06:20Z" level=info msg="Running command docker build --pull -f Dockerfile -t mycontainerregistry.azurecr.io/helloworld:eastus2 ."
Sending build context to Docker daemon  182.8kB
Step 1/5 : FROM node:9-alpine
9: Pulling from library/node
Digest: sha256:bd7b9aaf77ab2ce1e83e7e79fc0969229214f9126ced222c64eab49dc0bdae90
Status: Image is up to date for node:9-alpine
 ---> aa3e171e4e95
Step 2/5 : COPY . /src
 ---> e1c04dc2993b

[...]

6e5e20cbf4a7: Layer already exists
b69680cb4898: Pushed
b54af9b858b7: Pushed
eastus2: digest: sha256:9a7b73d06077ced2a02f7462f53e31a3e51e95ea5544fbcdb01e2fef094da1b6 size: 2423
time="2018-04-19T00:06:51Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" mycontainerregistry.azurecr.io/helloworld:eastus2"
"["mycontainerregistry.azurecr.io/helloworld@sha256:9a7b73d06077ced2a02f7462f53e31a3e51e95ea5544fbcdb01e2fef094da1b6"]"
time="2018-04-19T00:06:51Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" node:9-alpine"
"["node@sha256:bd7b9aaf77ab2ce1e83e7e79fc0969229214f9126ced222c64eab49dc0bdae90"]"
ACR Builder discovered the following dependencies:
- image:
    registry: mycontainerregistry.azurecr.io
    repository: helloworld
    tag: eastus2
    digest: sha256:9a7b73d06077ced2a02f7462f53e31a3e51e95ea5544fbcdb01e2fef094da1b6
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3
  git:
    git-head-revision: 6944c6bd0602f96e5fecf56ff8d66e2d268223e3

Build complete
Build ID: eastus2 was successful after 39.789138274s
```

## <a name="view-build-status"></a>Visualización del estado de la compilación

En ocasiones, esto quizás resulte útil para ver el estado de una compilación en curso que no ha desencadenado manualmente. Por ejemplo, cuando soluciona problemas en compilaciones desencadenadas por confirmaciones de código fuente. En esta sección, desencadenará una compilación manual, pero suprimirá el comportamiento predeterminado del streaming del registro de compilación a la consola. Posteriormente, puede usar el comando `az acr build-task logs` para supervisar la compilación en curso.

En primer lugar, desencadene una compilación manualmente como lo hizo anteriormente, pero especifique el argumento `--no-logs` para suprimir el registro en la consola:

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld --no-logs
```

A continuación, use el comando `az build-task logs` para ver el registro de la compilación que se está ejecutando actualmente:

```azurecli-interactive
az acr build-task logs --registry $ACR_NAME
```

El registro de la compilación que se está ejecutando actualmente se transmite a la consola y debe ser parecido a la salida siguiente (que se muestra aquí truncada):

```console
$ az acr build-task logs --registry $ACR_NAME
Showing logs for the last updated build...
Build-id: eastus3

[...]

Build complete
Build ID: eastus3 was successful after 30.076988169s
```

## <a name="trigger-a-build-with-a-commit"></a>Desencadenamiento de una compilación con una confirmación

Ahora que ha probado la tarea de compilación ejecutándola manualmente, desencadénela automáticamente con un cambio en el código fuente.

En primer lugar, asegúrese de que se encuentra en el directorio que contiene el clon local del [repositorio][sample-repo]:

```azurecli-interactive
cd acr-build-helloworld-node
```

A continuación, ejecute los siguientes comandos para crear, confirmar e insertar un nuevo archivo en la bifurcación del repositorio en GitHub:

```azurecli-interactive
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Build"
git push origin master
```

Se le pedirá que proporcione sus credenciales de GitHub cuando ejecute el comando `git push`. Proporcione el nombre de usuario de GitHub y escriba el token de acceso personal (PAT) que creó anteriormente para la contraseña.

```console
$ git push origin master
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Una vez que ha insertado una confirmación en el repositorio, el webhook que creó ACR Build activa e inicia una compilación en Azure Container Registry. Muestre los registros de compilación de la compilación que se está ejecutando actualmente para comprobar y supervisar el progreso de la compilación:

```azurecli-interactive
az acr build-task logs --registry $ACR_NAME
```

La salida es parecida a la siguiente, que muestra la compilación que se está ejecutando actualmente (o la última que se ejecutó):

```console
$ az acr build-task logs --registry $ACR_NAME
Showing logs for the last updated build...
Build-id: eastus4

[...]

Build complete
Build ID: eastus4 was successful after 28.9587031s
```

## <a name="list-builds"></a>Lista de compilaciones

Para ver una lista de las compilaciones que ACR Build ha completado para el registro, ejecute el comando [az acr build-task list-builds][az-acr-build-task-list-builds]:

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

La salida del comando debe ser parecida a la siguiente. Se muestran las compilaciones que ha ejecutado ACR Build y aparece "Confirmación de Git" en la columna DESENCADENADOR de la compilación más reciente:

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER     STARTED               DURATION
----------  ---------------  ----------  ---------  ----------  --------------------  ----------
eastus4     buildhelloworld  Linux       Succeeded  Git Commit  2018-04-20T22:50:27Z  00:00:35
eastus3     buildhelloworld  Linux       Succeeded  Manual      2018-04-20T22:47:19Z  00:00:30
eastus2     buildhelloworld  Linux       Succeeded  Manual      2018-04-20T22:46:14Z  00:00:55
eastus1                                  Succeeded  Manual      2018-04-20T22:38:22Z  00:00:55
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, aprenderá a usar una tarea de compilación que desencadena automáticamente compilaciones de imágenes de contenedor en Azure cuando confirma código fuente en un repositorio Git. Pase al siguiente tutorial para aprender a crear tareas de compilación que desencadenan compilaciones cuando se actualiza la imagen base de la imagen de contenedor.

> [!div class="nextstepaction"]
> [Automatización de compilaciones después de una actualización de imagen base](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build-task]: /cli/azure/acr#az-acr-build-task
[az-acr-build-task-create]: /cli/azure/acr#az-acr-build-task-create
[az-acr-build-task-run]: /cli/azure/acr#az-acr-build-task-run
[az-acr-build-task-list-builds]: /cli/azure/acr#az-acr-build-task-list-build

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png

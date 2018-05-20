---
title: 'Tutorial: Automatización de compilaciones de imágenes de contenedor en la actualización de imagen base con Azure Container Registry Build'
description: En este tutorial, aprenderá a configurar una tarea de compilación que desencadena automáticamente compilaciones de imágenes de contenedor en la nube cuando se actualiza una imagen base.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 976f61d99b88d241b39bfec9d95e16de272d9c14
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-automate-image-builds-on-base-image-update-with-azure-container-registry-build"></a>Tutorial: Automatización de compilaciones de imágenes en la actualización de imagen base con Azure Container Registry Build

ACR Build admite la ejecución automatizada de compilaciones cuando se actualiza una imagen base del contenedor, como cuando se revisa el sistema operativo o el marco de trabajo de la aplicación de una de las imágenes base. En este tutorial, aprenderá a crear una tarea de compilación en ACR Build que desencadena una compilación en la nube cuando se inserta una imagen base de contenedor en el registro.

En este tutorial, el último de la serie, se realizan las siguientes operaciones:

> [!div class="checklist"]
> * Compilación de la imagen base
> * Creación de una tarea de compilación de la imagen de aplicación
> * Actualización de la imagen base para que desencadene una compilación de imagen de aplicación
> * Mostrar la compilación desencadenada
> * Comprobación de la imagen de aplicación actualizada

> [!IMPORTANT]
> ACR Build se encuentra actualmente en versión preliminar, y solo se admite en los registros de contenedor de Azure de las regiones **Este de EE. UU.** y **Europa Occidental**. Las versiones preliminares están a su disposición con la condición de que acepte los [términos de uso adicionales][terms-of-use]. Es posible que algunos de los aspectos de esta característica cambien antes de ofrecer disponibilidad general.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si quiere usar la CLI de Azure de forma local, debe tener la versión **2.0.32** de la CLI de Azure u otra posterior instalada. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0][azure-cli].

## <a name="prerequisites"></a>requisitos previos

### <a name="complete-previous-tutorials"></a>Complete los tutoriales anteriores

En este tutorial se da por supuesto que ya ha completado los pasos de los dos primeros tutoriales de la serie, en los cuales puede realizar la:

* Creación de una instancia de Azure Container Registry
* Bifurcación del repositorio de ejemplo
* Clonación del repositorio de ejemplo
* Creación de un token de acceso personal de GitHub

Si aún no lo ha hecho, complete los dos primeros tutoriales antes de continuar:

[Compilación de imágenes de contenedor en la nube con Azure Container Registry Build](container-registry-tutorial-quick-build.md)

[Automatización de compilaciones de imágenes de contenedor con Azure Container Registry Build](container-registry-tutorial-build-task.md)

### <a name="configure-environment"></a>Configuración del entorno

Rellene estas variables de entorno de shell con valores adecuados para el entorno. Esto no es estrictamente necesario, pero hace que la ejecución de los comandos de varias líneas de la CLI de Azure en este tutorial sea un poco más fácil. Si no los rellena, deberá reemplazar manualmente cada valor siempre que aparezcan en los comandos de ejemplo.

```azurecli-interactive
ACR_NAME=mycontainerregistry # The name of your Azure container registry
GIT_USER=gituser             # Your GitHub user account name
GIT_PAT=personalaccesstoken  # The PAT you generated in the second tutorial
```

## <a name="base-images"></a>Imágenes base

Los archivos de Docker que definen la mayoría de las imágenes de contenedor especifican una imagen primaria en la que se basan y que se denomina su *imagen base*. Las imágenes base normalmente contienen el sistema operativo, por ejemplo [Alpine Linux][base-alpine] o [Windows Nano Server][base-windows], sobre el que se aplica el resto de capas del contenedor. También pueden incluir marcos de trabajo de la aplicación como [Node.js][base-node] o [.NET Core][base-dotnet].

### <a name="base-image-updates"></a>Actualizaciones de imagen base

El mantenedor de la imagen actualiza frecuentemente la imagen base para que incluya nuevas características o mejoras en el sistema operativo o plataforma de la imagen. Las revisiones de seguridad son otra causa habitual de una actualización de la imagen base.

Cuando se actualiza una imagen base, se le presenta la necesidad de recompilar todas las imágenes de contenedor del registro que se basan en ella para que incluya las nuevas características y correcciones. ACR Build incluye la posibilidad de compilar automáticamente las imágenes cuando se actualiza una imagen base del contenedor.

### <a name="base-image-update-scenario"></a>Escenario de actualización de imagen base

Este tutorial le guía a través de un escenario de actualización de imagen base. El [código de ejemplo][code-sample] incluye dos archivos de Docker: una imagen de aplicación y una imagen que especifica como su base. En las secciones siguientes creará una tarea de ACR Build que desencadena automáticamente una compilación de la imagen de aplicación cuando se inserta una nueva versión de la imagen base en el registro de contenedor.

[Dockerfile-app][dockerfile-app]: una pequeña aplicación web de Node.js que representa una página web estática que muestra la versión de Node.js en la que se basa. Se simula la cadena de versión en el sentido de que se muestra el contenido de una variable de entorno, `NODE_VERSION`, que se definió en la imagen base.

[Dockerfile-base][dockerfile-base]: la imagen que `Dockerfile-app` especifica como su base. Está basada en una imagen de [Node][base-node] e incluye la variable de entorno `NODE_VERSION`.

En las secciones siguientes, va a crear una tarea de compilación, actualizar el valor `NODE_VERSION` en el archivo de Docker de la imagen base y, finalmente, a usar ACR Build para compilar la imagen base. Cuando ACR Build inserta la nueva imagen base en el registro, este desencadena automáticamente una compilación de la imagen de aplicación. Si lo desea, puede ejecutar la imagen de contenedor de la aplicación localmente para ver las diferentes cadenas de versión de las imágenes compiladas.

## <a name="build-base-image"></a>Compilación de la imagen base

Empiece por crear la imagen base con la característica *Compilación rápida* de ACR Build. Como se ha descrito en el [primer tutorial](container-registry-tutorial-quick-build.md) de la serie, esto no solo sirve para compilar la imagen, si no también para insertarla en el registro de contenedor si la compilación es correcta.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-build-task"></a>Creación de una tarea de compilación

A continuación, cree una tarea de compilación con [az acr build-task create][az-acr-build-task-create]:

```azurecli-interactive
az acr build-task create \
    --registry $ACR_NAME \
    --name buildhelloworld \
    --image helloworld:{{.Build.ID}} \
    --build-arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node \
    --file Dockerfile-app \
    --branch master \
    --git-access-token $GIT_PAT
```

Esta tarea de compilación es similar a la tarea creada en el [tutorial anterior](container-registry-tutorial-build-task.md). Indica a ACR Build que desencadene una compilación de la imagen cuando se inserten confirmaciones en el repositorio especificado por `--context`.

La diferencia estriba en su comportamiento, ya que también desencadena una compilación de la imagen cuando se actualiza su *imagen base*. El archivo de Docker especificado por el argumento `--file`, [Dockerfile-app][dockerfile-app], admite la especificación de una imagen del mismo registro que su imagen base:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

Cuando se ejecuta una tarea de compilación, ACR Build detecta las dependencias de una imagen. Si la imagen base especificada en la instrucción `FROM` reside en el mismo registro, esta agrega un enlace para asegurarse de que esta imagen se recompila siempre que se actualiza su base.

> [!NOTE]
> Durante la versión preliminar, ACR Build admite el desencadenamiento de una compilación de imagen derivada solo si la imagen base y la imagen que hace referencia a esta residen en el mismo registro de contenedor de Azure.

## <a name="build-application-container"></a>Compilación del contenedor de aplicación

Para habilitar ACR Build para que determine y realice el seguimiento de las dependencias de una imagen de contenedor, lo cual incluye su imagen base, **debe** primero desencadenar su tarea de compilación **al menos una vez**.

Use [az acr build-task run][az-acr-build-task-run] para desencadenar manualmente la tarea de compilación y compilar la imagen de aplicación:

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld
```

Una vez finalizada la compilación, tome nota del **Id. de compilación** (por ejemplo, "eastus6") si desea completar el siguiente paso opcional.

### <a name="optional-run-application-container-locally"></a>Opcional: Ejecutar contenedor de aplicación localmente

Si está trabajando de forma local (no en Cloud Shell), y tiene Docker instalado, ejecute el contenedor para ver la aplicación representada en un explorador web antes de recompilar su imagen base. Si va a usar Cloud Shell, omita esta sección (Cloud Shell no admite `az acr login` ni `docker run`).

En primer lugar, inicie sesión en el registro de contenedor con [az acr login][az-acr-login]:

```azurecli
az acr login --name $ACR_NAME
```

Ahora, ejecute el contenedor localmente con `docker run`. Reemplace **\<build-id\>** por el Id. de compilación que se encuentra en la salida del paso anterior (por ejemplo, "eastus5").

```azurecli
docker run -d -p 8080:80 $ACR_NAME.azurecr.io/helloworld:<build-id>
```

Vaya a http://localhost:8080 en el explorador, allí verá el número de versión de Node.js representado en la página web de manera parecida a la siguiente. En un paso posterior, cambiará la versión agregando una "a" a la cadena de versión.

![Captura de pantalla de una aplicación de ejemplo representada en el explorador][base-update-01]

## <a name="list-builds"></a>Lista de compilaciones

A continuación, se enumeran las compilaciones que ACR Build ha completado para el registro:

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

Si completó el tutorial anterior (y no eliminó el registro), debería ver una salida parecida a la siguiente. Tome nota del número de compilaciones y del identificador de compilación más reciente, para que pueda comparar la salida después de actualizar la imagen base en la sección siguiente.

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER       STARTED               DURATION
----------  ---------------  ----------  ---------  ------------  --------------------  ----------
eastus6     buildhelloworld  Linux       Succeeded  Manual        2018-04-22T00:03:46Z  00:00:40
eastus5                                  Succeeded  Manual        2018-04-22T00:01:45Z  00:00:25
eastus4     buildhelloworld  Linux       Succeeded  Git Commit    2018-04-21T23:52:33Z  00:00:30
eastus3     buildhelloworld  Linux       Succeeded  Manual        2018-04-21T23:50:10Z  00:00:35
eastus2     buildhelloworld  Linux       Succeeded  Manual        2018-04-21T23:46:15Z  00:00:55
eastus1                                  Succeeded  Manual        2018-04-21T23:24:05Z  00:00:35
```

## <a name="update-base-image"></a>Actualización de la imagen base

Aquí puede simular una revisión de la plataforma en la imagen base. Edite **Dockerfile-base** y agregue una "a" después del número de versión definido en `NODE_VERSION`:

```Dockerfile
ENV NODE_VERSION 9.11.1a
```

Ejecute la característica Compilación rápida de ACR Build para compilar la imagen base modificada. Tome nota del **identificador de compilación** en la salida.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

Una vez que haya finalizado la compilación y que ACR Build haya insertado la nueva imagen base en el registro, este desencadena una compilación de la imagen de aplicación. Es posible que la tarea de ACR Build que creó anteriormente tarde unos instantes en desencadenar la compilación de la imagen de aplicación, ya que debe detectar la imagen base recién completada e insertada.

## <a name="list-builds"></a>Lista de compilaciones

Ahora que ha actualizado la imagen base, vuelva a enumerar las compilaciones para compararlas con la lista anterior. Si al principio la salida no es diferente, ejecute periódicamente el comando hasta ver cómo aparece la nueva compilación en la lista.

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

La salida es similar a la siguiente. El DESENCADENADOR de la última compilación ejecutada debe ser "Image Update", lo cual indica que la tarea de compilación se inició durante la compilación rápida de la imagen base.

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER       STARTED               DURATION
----------  ---------------  ----------  ---------  ----------    --------------------  ----------
eastus8     buildhelloworld  Linux       Succeeded  Image Update  2018-04-22T00:09:24Z  00:00:50
eastus7                                  Succeeded  Manual        2018-04-22T00:08:49Z  00:00:40
eastus6     buildhelloworld  Linux       Succeeded  Image Update  2018-04-20T00:15:30Z  00:00:43
eastus5     buildhelloworld  Linux       Succeeded  Manual        2018-04-20T00:10:05Z  00:00:45
eastus4     buildhelloworld  Linux       Succeeded  Git Commit    2018-04-19T23:40:38Z  00:00:40
eastus3     buildhelloworld  Linux       Succeeded  Manual        2018-04-19T23:36:37Z  00:00:40
eastus2     buildhelloworld  Linux       Succeeded  Manual        2018-04-19T23:35:27Z  00:00:40
eastus1                                  Succeeded  Manual        2018-04-19T22:51:13Z  00:00:30
```

Si quiere realizar el siguiente paso opcional y ejecutar el contenedor recién compilado para ver el número de versión actualizado, anote el valor del **identificador de compilación** de la compilación que desencadenó "Image Update" (en la salida anterior es "eastus6").

### <a name="optional-run-newly-built-image"></a>Opcional: Ejecución de la imagen recién compilada

Si está trabajando de forma local (no en Cloud Shell), y tiene Docker instalado, ejecute la nueva imagen de aplicación después de terminar su compilación. Sustituya `<build-id>` por el identificador de compilación que obtuvo en el paso anterior. Si va a usar Cloud Shell, omita esta sección (Cloud Shell no admite `docker run`).

```bash
docker run -d -p 8081:80 $ACR_NAME.azurecr.io/helloworld:<build-id>
```

Vaya a http://localhost:8081 en el explorador, allí verá el número de versión de Node.js actualizado (con la "a") en la página web:

![Captura de pantalla de una aplicación de ejemplo representada en el explorador][base-update-02]

Es importante observar que ha actualizado la imagen **base** con un nuevo número de versión, pero que también la imagen de **aplicación** recién compilada muestra la nueva versión. ACR Build recopiló el cambio en la imagen base y recompiló automáticamente la imagen de aplicación.

## <a name="clean-up-resources"></a>Limpieza de recursos

Para quitar todos los recursos que ha creado en esta serie de tutoriales, incluido el registro de contenedor, la instancia del contenedor, el almacén de claves y la entidad de servicio, ejecute los comandos siguientes:

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a usar una tarea de compilación para desencadenar automáticamente compilaciones de imágenes de contenedor cuando se actualiza la imagen base de la imagen. Ahora, continúe para aprender más información acerca de la autenticación del registro de contenedor.

> [!div class="nextstepaction"]
> [Autenticación en Azure Container Registry](container-registry-authentication.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[code-sample]: https://github.com/Azure-Samples/acr-build-helloworld-node
[dockerfile-app]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-app
[dockerfile-base]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-base
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build-run
[az-acr-build-task-create]: /cli/azure/acr#az-acr-build-task-create
[az-acr-build-task-run]: /cli/azure/acr#az-acr-build-task-run
[az-acr-login]: /cli/azure/acr#az-acr-login

<!-- IMAGES -->
[base-update-01]: ./media/container-registry-tutorial-base-image-update/base-update-01.png
[base-update-02]: ./media/container-registry-tutorial-base-image-update/base-update-02.png
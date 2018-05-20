---
title: Automatización de la aplicación de revisiones de sistema operativo y marco con Azure Container Registry Build (ACR Build)
description: Una introducción a ACR Build, un conjunto de características de Azure Container Registry que proporcionan compilaciones de imágenes de contenedor y aplicación de revisiones automatizadas y seguras en la nube.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 05/01/2018
ms.author: marsma
ms.openlocfilehash: 3ef91270bceb5865bdbdf9c436e4519595a3dc09
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/11/2018
---
# <a name="automate-os-and-framework-patching-with-acr-build"></a>Automatización de aplicación de revisiones de sistema operativo y marco con ACR Build

Los contenedores proporcionan nuevos niveles de virtualización, que aíslan las dependencias de aplicaciones y desarrolladores de los requisitos operativos y la infraestructura. Sin embargo, lo que permanece es la necesidad de abordar cómo se aplican las revisiones de esta virtualización de aplicaciones.

**ACR Build**, un conjunto de características de Azure Container Registry, no solo proporciona funcionalidad nativa de compilación de imágenes, sino que también automatiza la [aplicación de revisiones de sistema operativo y marco](#automate-os-and-framework-patching) para sus contenedores de Docker.

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

## <a name="what-is-acr-build"></a>¿Qué es ACR Build?

Azure Container Registry Build es un servicio de compilación de imágenes nativas de contenedor de Azure. ACR Build permite el desarrollo de bucle interno en la nube con compilaciones de imágenes de contenedor a petición, así como compilaciones automatizadas a la confirmación del código fuente y la actualización de las imágenes base.

La imagen de contenedor del desencadenador se compila automáticamente cuando el código se confirma en un repositorio de Git o cuando se actualiza una imagen base del contenedor. Con los desencadenadores de actualización de imágenes base, puede automatizar el flujo de trabajo de aplicación de revisiones de sistema operativo y marco de aplicación, y mantener los entornos seguros al tiempo que se respetan los principios de los contenedores inmutables.

## <a name="quick-build-inner-loop-extended-to-the-cloud"></a>Compilación rápida: bucle interno extendido a la nube

El comienzo de la administración del ciclo de vida se inicia antes de que los desarrolladores confirmen sus primeras líneas de código. La característica [Compilación rápida](container-registry-tutorial-quick-build.md) de ACR Build permite una experiencia de desarrollo de bucle interno local integrada, que descarga las compilaciones en Azure. Con las compilaciones rápidas, puede verificar las definiciones de compilación automatizadas antes de confirmar el código.

Mediante el conocido formato `docker build`, el comando [az acr build][az-acr-build] de la CLI de Azure toma un contexto local, lo envía al servicio ACR Build y, de forma predeterminada, inserta la imagen compilada en su registro tras completarse. ACR Build sigue los registros de replicación geográfica, lo que permite que equipos de desarrollo dispersos aprovechen el registro replicado más cercano. Durante la versión preliminar, ACR Build está disponible en las regiones de Este de EE. UU. y Europa Occidental.

ACR Build está diseñado como un primitivo de ciclo de vida de contenedor. Por ejemplo, puede integrar ACR Build en su solución de CI/CD. Al ejecutar [az login][az-login] con una [entidad de servicio][az-login-service-principal], la solución de CI/CD podría emitir entonces comandos [az acr build][az-acr-build] para iniciar las compilaciones de imágenes.

Aprenda a usar las compilaciones rápidas en el primer tutorial de ACR Build, [Build container images in the cloud with Azure Container Registry Build](container-registry-tutorial-quick-build.md) (Compilación de imágenes de contenedor en la nube con Azure Container Registry Build).

## <a name="automatic-build-on-source-code-commit"></a>Compilación automática al confirmar el código fuente

Use ACR Build para desencadenar automáticamente una compilación de imágenes de contenedor cuando el código se confirma en un repositorio de Git. Las tareas de compilación, configurables con el comando [az acr build-task][az-acr-build-task] de la CLI de Azure, le permiten especificar un repositorio de Git y, opcionalmente, una rama y un Dockerfile. Cuando el equipo confirma el código en el repositorio, un webhook creado por ACR Build desencadena una compilación de la imagen de contenedor definida en el repositorio.

Aprenda a desencadenar compilaciones a la confirmación del código fuente en el segundo tutorial de ACR Build, [Automate container image builds with Azure Container Registry Build](container-registry-tutorial-build-task.md) (Automatización de compilaciones de imágenes de contenedor con Azure Container Registry Build).

## <a name="automate-os-and-framework-patching"></a>Automatización de aplicación de revisiones de sistema operativo y marco

La eficacia de ACR Build para mejorar realmente la canalización de compilaciones de contenedor radica en su capacidad para detectar una actualización de una imagen base. Cuando la imagen base actualizada se inserta en el registro, ACR Build puede compilar automáticamente cualquier imagen de aplicación basada en ella.

Las imágenes de contenedor pueden clasificarse a grandes rasgos en imágenes *base* e imágenes de *aplicación*. Normalmente, las imágenes base incluyen el sistema operativo y los marcos de aplicaciones sobre los que se compila la aplicación, junto con otras personalizaciones. Estas imágenes base se basan normalmente en imágenes ascendentes públicas, por ejemplo, [Alpine Linux][base-alpine] o [Node.js][base-node]. Algunas de las imágenes de aplicación podrían compartir una imagen base común.

Cuando el mantenedor ascendente actualiza una imagen de sistema operativo o marco de aplicación, por ejemplo, con una revisión de seguridad crítica del sistema operativo, también debe actualizar las imágenes base para incluir la corrección crítica. Cada imagen de aplicación se debe entonces volver a compilar para incluir estas correcciones ascendentes ahora incluidas en la imagen base.

Como ACR Build detecta dinámicamente las dependencias de la imagen base cuando compila una imagen de contenedor, puede detectar cuándo la imagen base de una aplicación está actualizada. ACR Build se sirve de una [tarea de compilación](container-registry-tutorial-base-image-update.md#create-build-task) preconfigurada para **recompilar automáticamente cada imagen de aplicación**. Gracias a esta detección y recompilación automáticas, ACR Build le ahorra el tiempo y el trabajo que normalmente son necesarios para actualizar y realizar el seguimiento de cada una de las imágenes de aplicación que hacen referencia a la imagen base actualizada.

Aprenda sobre la aplicación de revisiones de sistema operativo y marco en el tercer tutorial de ACR Build, [Automate image builds on base image update with Azure Container Registry Build](container-registry-tutorial-base-image-update.md) (Automatización de compilaciones de imágenes al actualizar la imagen base con Azure Container Registry Build).

> [!NOTE]
> En la versión preliminar inicial, la imagen base actualiza las compilaciones del desencadenador solo cuando las imágenes base y de aplicación residen ambas en el mismo registro de contenedor de Azure.

## <a name="next-steps"></a>Pasos siguientes

Cuando esté listo para automatizar la aplicación de revisiones de sistema operativo y marco mediante la compilación de las imágenes de contenedor en la nube, consulte la serie de tutoriales de tres partes de ACR Build.

> [!div class="nextstepaction"]
> [Build container images in the cloud with Azure Container Registry Build](container-registry-tutorial-quick-build.md) (Compilación de imágenes de contenedor en la nube con Azure Container Registry Build)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-build-task]: /cli/azure/acr#az-acr-build-task
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli#log-in-with-a-service-principal

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png

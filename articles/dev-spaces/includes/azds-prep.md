---
title: archivo de inclusión
description: archivo de inclusión
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: e9f97f804985f948e5442c64a31d95e7931b03cd
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198833"
---
## <a name="preparing-code-for-docker-and-kubernetes-development"></a>Preparación del código para el desarrollo de Docker y Kubernetes
Hasta ahora, tiene una aplicación web básica que se puede ejecutar localmente. Ahora podrá incluirla en contenedores creando activos que definen el contenedor de la aplicación y cómo se implementará en Kubernetes. Esto es fácil de hacer con Azure Dev Spaces: 

1. Ejecute VS Code y abra la carpeta `webfrontend`. (Puede omitir cualquier mensaje predeterminado para agregar recursos de depuración o restaurar el proyecto).
1. Abra el terminal integrado en VS Code (mediante el menú **Ver | Terminal integrado**.
1. Ejecute este comando (asegúrese de que **webfrontend** es la carpeta actual):

    ```cmd
    azds prep --public
    ```

El comando `azds prep` de la CLI de Azure comando genera recursos de Docker y Kubernetes con la configuración predeterminada:
* `./Dockerfile` describe la imagen de contenedor de la aplicación y cómo el código fuente se compila y se ejecuta dentro del contenedor.
* A [gráfico de Helm](https://docs.helm.sh) bajo `./charts/webfrontend` describe cómo implementar el contenedor en Kubernetes.

Por ahora no es necesario comprender todo el contenido de estos archivos. Sin embargo, vale la pena señalar que **los mismos recursos de configuración como código de Kubernetes y Docker se pueden usar desde el desarrollo hasta la producción, lo que proporciona una mejor coherencia entre diferentes entornos.**
 
Un archivo denominado `./azds.yaml` también se genera mediante el comando `prep`; se trata del archivo de configuración para Azure Dev Spaces. Complementa los artefactos de Docker y Kubernetes sin configuración adicional que posibilita una experiencia de desarrollo iterativo en Azure. Por ejemplo, el gráfico de Helm predeterminado no expone ningún punto de conexión público. A veces, sin embargo, es útil abrir temporalmente un punto de conexión público durante el desarrollo para que pueda probar el código, por ejemplo, desde un dispositivo móvil o una dirección URL de webhook. Un archivo azds.yaml creado con `azds prep --public` invalida los parámetros predeterminados de Helm para exponer un punto de conexión público solo para tiempo de desarrollo.

---
title: "Ejecución de tareas en contenedores en Azure Container Instances"
description: "Aprenda a usar Azure Container Instances para ejecutar tareas hasta su terminación, como compilaciones, pruebas o trabajos de representación de imágenes."
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 11/16/2017
ms.author: marsma
ms.openlocfilehash: a922525970eac9af6657e58daae971912183b369
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="run-a-containerized-task-in-azure-container-instances"></a>Ejecución de tareas en contenedores en Azure Container Instances

La facilidad y rapidez con que se implementan contenedores convierten a Azure Container Instances en una plataforma convincente para la ejecución de tareas que se ejecutan una única vez, como compilaciones, pruebas y representaciones de imágenes en una instancia de contenedor.

Gracias a una directiva de reinicio configurable, puede especificar que los contenedores se detengan cuando sus procesos se hayan completado. Dado que las instancias de contenedor se facturan por segundo, solo se le cobra por los recursos de proceso usados mientras el contenedor que ejecuta su tarea está en funcionamiento.

En los ejemplos presentados en este artículo se usa la CLI de Azure. Debe tener [instalada localmente][azure-cli-install] la CLI de Azure 2.0.21 o posterior o usar la CLI de [Azure Cloud Shell](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Directiva de reinicio de contenedor

Cuando se crea un contenedor en Azure Container Instances, se puede especificar una de tres configuraciones de directiva de reinicio.

| Directiva de reinicio   | DESCRIPCIÓN |
| ---------------- | :---------- |
| `Always` | Siempre se reinician los contenedores del grupo de contenedores. Este es el valor de configuración **predeterminado** aplicado cuando no se especifica ninguna directiva de reinicio durante la creación del contenedor. |
| `Never` | Nunca se reinician los contenedores del grupo de contenedores. Los contenedores se ejecutan al menos una vez. |
| `OnFailure` | Los contenedores del grupo de contenedores se reinician solo cuando se produce un error en el proceso ejecutado en el contenedor (cuando se cierra con un código de salida distinto de cero). Los contenedores se ejecutan al menos una vez. |

## <a name="specify-a-restart-policy"></a>Especificación de una directiva de reinicio

El modo en que especifique una directiva de reinicio depende de cómo cree sus instancias de contenedor, es decir, con la CLI de Azure, los cmdlets de Azure PowerShell o Azure Portal. En la CLI de Azure, especifique el parámetro `--restart-policy` cuando llame a [az container create][az-container-create].

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Ejemplo de ejecución hasta terminación

Para ver la directiva de reinicio en acción, cree una instancia de contenedor a partir de la imagen [microsoft/aci-wordcount][aci-wordcount-image] y especifique la directiva de reinicio `OnFailure`. Este contenedor de ejemplo ejecuta un script de Python que, de forma predeterminada, analiza el texto de la obra [Hamlet](http://shakespeare.mit.edu/hamlet/full.html) de Shakespeare, escribe las 10 palabras más comunes en STDOUT y, a continuación, se cierra.

Ejecute el contenedor de ejemplo con el siguiente comando [az container create][az-container-create]:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Azure Container Instances inicia el contenedor y, luego, lo detiene cuando su aplicación (o script, en este caso) se cierra. Cuando Azure Container Instances detiene un contenedor cuya directiva de reinicio es `Never` o `OnFailure`, el estado del contenedor se establece en **Terminado**. Puede comprobar el estado de un contenedor con el comando [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query containers[0].instanceView.currentState.state
```

Salida de ejemplo:

```bash
"Terminated"
```

Una vez que el estado del contenedor de ejemplo muestra *Terminado*, puede ver la salida de las tareas en los registros del contenedor. Ejecute el comando [az container logs][az-container-logs] para ver la salida del script:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Salida:

```bash
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

En este ejemplo se muestra la salida que el script envía a STDOUT. No obstante, puede que las tareas en contenedor escriban su salida en almacenamiento persistente para su posterior recuperación. Por ejemplo, en un [recurso compartido de archivos de Azure](container-instances-mounting-azure-files-volume.md).

## <a name="configure-containers-at-runtime"></a>Configuración de los contenedores en tiempo de ejecución

Cuando crea una instancia de contenedor, puede establecer sus **variables de entorno**, así como especificar que se ejecute una **línea de comandos** personalizada cuando se inicie el contenedor. Puede usar esta configuración en los trabajos por lotes para preparar cada contenedor con la configuración específica de la tarea.

## <a name="environment-variables"></a>Variables de entorno

Establezca variables de entorno en el contenedor para proporcionar configuración dinámica de la aplicación o el script ejecutados por el contenedor. Esto es casi lo mismo que el argumento de línea de comandos `--env` para `docker run`.

Por ejemplo, puede modificar el comportamiento del script en el contenedor de ejemplo mediante la especificación de las siguientes variables de entorno al crear la instancia de contenedor:

*NumWords*: el número de palabras enviadas a STDOUT.

*MinLength*: el número mínimo de caracteres en una palabra para que se tenga en cuenta. Un número mayor omite palabras comunes como "de" y "la".

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

Al especificar `NumWords=5` y `MinLength=8` para las variables de entorno del contenedor, los registros de contenedor deben mostrar una salida diferente. Una vez que el estado del contenedor se muestre como *Terminado* (use `az container show` para comprobar su estado), visualice sus registros para ver la nueva salida:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Salida:

```bash
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="command-line-override"></a>Invalidación de la línea de comandos

Especifique una línea de comandos al crear una instancia de contenedor para invalidar la línea de comandos incorporada a la imagen de contenedor. Esto es casi lo mismo que el argumento de línea de comandos `--entrypoint` para `docker run`.

Por ejemplo, puede hacer que el contenedor de ejemplo analice otro texto distinto de *Hamlet* mediante la especificación de una línea de comandos diferente. El script de Python ejecutado por el contenedor, *wordcount.py*, acepta una dirección URL como argumento y procesa el contenido de esa página en lugar del valor predeterminado.

Por ejemplo, para determinar las tres palabras principales de cinco letras en *Romeo y Julieta*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer3 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Una vez más, cuando el contenedor muestra el estado *Terminado*, puede ver la salida en los registros del contenedor:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer3
```

Salida:

```bash
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>pasos siguientes

### <a name="persist-task-output"></a>Conservar la salida de la tarea

Para más información acerca de cómo conservar la salida de los contenedores que se ejecutan hasta completarse, consulte [Montaje de un recurso compartido de archivos de Azure en Azure Container Instances](container-instances-mounting-azure-files-volume.md).

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/r/microsoft/aci-wordcount/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az_container_logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show
[azure-cli-install]: /cli/azure/install-azure-cli

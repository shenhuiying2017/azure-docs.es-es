---
title: "Solución de problemas de Azure Container Instances"
description: "Obtenga información acerca de la solución de problemas relacionados con Azure Container Instances"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 1fd3b2c251860e883519744b11fcfc2b925cd2fa
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="troubleshoot-deployment-issues-with-azure-container-instances"></a>Solución de problemas en las implementaciones en Azure Container Instances

Este artículo muestra cómo solucionar problemas al implementar contenedores en Azure Container Instances. También describe algunos de los problemas comunes con los que podría encontrarse.

## <a name="get-diagnostic-events"></a>Obtención de eventos de diagnóstico

Para ver los registros desde el código de la aplicación dentro de un contenedor, puede utilizar el comando [az container logs][az-container-logs]. Pero si el contenedor no se implementa correctamente, debe revisar la información de diagnóstico proporcionada por el proveedor de recursos de Azure Container Instances. Para ver los eventos del contenedor, ejecute el comando [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

La salida incluye las propiedades básicas del contenedor, junto con los eventos de implementación (que se muestran aquí truncados):

```JSON
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "microsoft/aci-helloworld",
      ...
        "events": [
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:49+00:00",
            "lastTimestamp": "2017-12-21T22:50:49+00:00",
            "message": "pulling image \"microsoft/aci-helloworld\"",
            "name": "Pulling",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Successfully pulled image \"microsoft/aci-helloworld\"",
            "name": "Pulled",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Created container with id 2677c7fd54478e5adf6f07e48fb71357d9d18bccebd4a91486113da7b863f91f",
            "name": "Created",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Started container with id 2677c7fd54478e5adf6f07e48fb71357d9d18bccebd4a91486113da7b863f91f",
            "name": "Started",
            "type": "Normal"
          }
        ],
        "previousState": null,
        "restartCount": 0
      },
      "name": "mycontainer",
      "ports": [
        {
          "port": 80,
          "protocol": null
        }
      ],
      ...
    }
  ],
  ...
}
```

## <a name="common-deployment-issues"></a>Problemas comunes de implementación

Hay algunos problemas comunes relacionados con la mayoría de los errores de implementación.

## <a name="unable-to-pull-image"></a>No se puede extraer la imagen

Si Azure Container Instances no puede extraer la imagen inicialmente, lo reintenta durante algún tiempo antes de generar un error. Si no se puede extraer la imagen, se muestran eventos similares al siguiente en la salida de [az container show][az-container-show]:

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"microsoft/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"microsoft/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"microsoft/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```

Para resolverlo, elimine el contenedor y vuelva a intentar la implementación, prestando atención a la escritura correcta del nombre de imagen.

## <a name="container-continually-exits-and-restarts"></a>El contenedor continuamente se cierra y se reinicia

Si el contenedor se ejecuta por completo y se reinicia automáticamente, tendrá que establecer la [directiva de reinicio](container-instances-restart-policy.md) **OnFailure** o **Never**. Si especifica **OnFailure** y sigue observando reinicios continuos, podría haber un problema con la aplicación o el script que se ejecuta en el contenedor.

La API de Container Instances incluye una propiedad `restartCount`. Para comprobar el número de reinicios de un contenedor, puede utilizar el comando [az container show][az-container-show] de la CLI de Azure 2.0. En la siguiente salida de ejemplo (que se ha truncado por razones de brevedad), puede ver la propiedad `restartCount` al final de la salida.

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> La mayoría de las imágenes de contenedor para las distribuciones de Linux establecen una shell, como bash, como el comando predeterminado. Puesto que un shell por sí mismo no es un servicio de ejecución prolongada, estos contenedores se cierran inmediatamente y caen en un bucle de reinicio cuando se configuran con la directiva de reinicio predeterminada **Always**.

## <a name="container-takes-a-long-time-to-start"></a>El contenedor tarda mucho tiempo en iniciar

Si el contenedor tarda mucho tiempo en iniciar, pero al final se realiza correctamente, comience por mirar el tamaño de la imagen del contenedor. Como Azure Container Instances extrae la imagen del contenedor a petición, el tiempo de inicio que experimenta está directamente relacionado con su tamaño.

Puede ver el tamaño de la imagen del contenedor mediante la CLI de Docker:

```bash
docker images
```

Salida:

```bash
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
microsoft/aci-helloworld               latest              7f78509b568e        13 days ago         68.1MB
```

La clave para mantener tamaños de imagen pequeños es asegurarse de que la imagen final no contiene nada que no sea necesario en tiempo de ejecución. Una manera de hacerlo es con [compilaciones en varias etapas][docker-multi-stage-builds]. Las compilaciones en varias etapas hacen más sencillo asegurarse de que la imagen final contiene solo los artefactos necesarios para la aplicación y no los del contenido extra que se requiere en tiempo de compilación.

La otra forma de reducir el impacto de la extracción de la imagen en el tiempo de inicio del contenedor es hospedar la imagen del contenedor con Azure Container Registry en la misma región en la que va a utilizar Azure Container Instances. Esto reduce la ruta de acceso de red que debe recorrer la imagen del contenedor, lo que reduce significativamente el tiempo de descarga.

## <a name="resource-not-available-error"></a>Error de recurso no disponible

Debido a la carga variable de recursos regionales en Azure, podría recibir el siguiente error al intentar implementar una instancia del contenedor:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Este error indica que, debido a una carga elevada en la región en la que está intentando la implementación, no se pueden asignar los recursos especificados para el contenedor en ese momento. Utilice uno o varios de los siguientes pasos de mitigación para ayudar a resolver el problema.

* Compruebe que la configuración de implementación del contenedor se encuentra dentro de los parámetros definidos en [Quotas and region availability for Azure Container Instances](container-instances-quotas.md#region-availability) (Disponibilidad de cuotas y regiones en instancias de Azure Container).
* Especifique la configuración de CPU y memoria más baja para el contenedor
* Implemente en una región distinta de Azure
* Implemente en un momento posterior

<!-- LINKS - External -->
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/

<!-- LINKS - Internal -->
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
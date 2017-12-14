---
title: "Administración de un clúster DC/OS de Azure con la API de REST de Marathon"
description: "Implemente contenedores en un clúster de DC/OS de Azure Container Service mediante la API de REST de Marathon."
services: container-service
author: dlepow
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 04/04/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: c9322756c30011305ebe6f4f2fd38554f275a1b3
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2017
---
# <a name="dcos-container-management-through-the-marathon-rest-api"></a>Administración de contenedores de DC/OS a través de la API de REST de Marathon

DC/OS proporciona un entorno para implementar y escalar cargas de trabajo agrupadas, al tiempo que reduce el hardware subyacente. Por encima de DC/OS hay un marco que administra la programación y ejecución de cargas de trabajo de proceso. Aunque hay marcos de trabajo disponibles para muchas cargas de trabajo conocidas, este documento es una introducción a la creación y el escalado de implementaciones de contenedor con la API de REST de Marathon. 

## <a name="prerequisites"></a>Requisitos previos

Antes de trabajar con estos ejemplos, necesita un clúster de DC/OS configurado en el servicio Contenedor de Azure. También debe tener conectividad remota con este clúster. Para más información sobre estos aspectos, consulte los siguientes artículos:

* [Implementación de un clúster del servicio Contenedor de Azure](container-service-deployment.md)
* [Conexión a un clúster del servicio Contenedor de Azure](../container-service-connect.md)

## <a name="access-the-dcos-apis"></a>Acceso a las API de DC/OS
Una vez conectado al clúster de Azure Container Service, podrá acceder a DC/OS y a las API de REST relacionadas a través de http://localhost:local-port. Los ejemplos de este documento suponen que está realizando la tunelización en el puerto 80. Por ejemplo, a los puntos de conexión de Marathon se puede acceder en los identificadores URI que comienzan por `http://localhost/marathon/v2/`. 

Para más información sobre las diferentes API, consulte la documentación de Mesosphere sobre [Marathon API](https://mesosphere.github.io/marathon/docs/rest-api.html) y [Chronos API](https://mesos.github.io/chronos/docs/api.html), así como la documentación de Apache sobre [Mesos Scheduler API](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

## <a name="gather-information-from-dcos-and-marathon"></a>Recopilación de información de DC/OS y Marathon
Antes de implementar contenedores en el clúster de DC/OS, recopile información sobre él, como los nombres y el estado de los agentes de DC/OS. Para ello, consulte el punto de conexión `master/slaves` de la API de REST de DC/OS. Si todo va bien, la consulta devuelve una lista de agentes de DC/OS y varias propiedades de cada uno de ellos.

```bash
curl http://localhost/mesos/master/slaves
```

Ahora, utilice el punto de conexión `/apps` de Marathon para buscar las implementaciones actuales de aplicaciones en el clúster de DC/OS. Si se trata de un nuevo clúster, verá una matriz vacía para las aplicaciones.

```bash
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Implementación de un contenedor con formato Docker
Los contenedores con formato Docker se implementan a través de la API de REST de Marathon mediante un archivo JSON que describe la implementación deseada. El ejemplo siguiente implementa un contenedor de Nginx en un agente privado del clúster. 

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 32.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Para implementar un contenedor con formato Docker, almacene el archivo JSON en una ubicación a la que se pueda accesible. A continuación, ejecute el siguiente comando para implementar el contenedor. Especifique el nombre del archivo JSON (`marathon.json` en este ejemplo).

```bash
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

La salida será similar a la siguiente:

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

Ahora, si consulta Marathon sobre las aplicaciones, esta nueva aplicación se muestra en el resultado.

```bash
curl localhost/marathon/v2/apps
```

## <a name="reach-the-container"></a>Cobertura del contenedor

Puede comprobar que Nginx se ejecuta en un contenedor de uno de los agentes privados del clúster. Para buscar el host y el puerto donde se ejecuta el contenedor, consulte estas tareas de ejecución en Marathon: 

```bash
curl localhost/marathon/v2/tasks
```

Busque el valor de `host` en el resultado (una dirección IP similar a `10.32.0.x`) y el valor de `ports`.


Ahora puede realizar una conexión de terminal de SSH (no una conexión de túnel) en el FQDN de administración del clúster. Una vez conectado, sustituya los valores correctos de `host` y `ports`, y realice esta solicitud:

```bash
curl http://host:ports
```

La salida del servidor de Nginx es similar a la siguiente:

![Nginx desde el contenedor](./media/container-service-mesos-marathon-rest/nginx.png)




## <a name="scale-your-containers"></a>Escalado de los contenedores
La API de Marathon se puede utilizar para escalar horizontalmente implementaciones de aplicaciones o reducirlas horizontalmente. En el ejemplo anterior, ha implementado una instancia de una aplicación. Vamos a escalarla horizontalmente a tres instancias de una aplicación. Para ello, cree un archivo JSON mediante el siguiente texto JSON y almacénelo en una ubicación accesible.

```json
{ "instances": 3 }
```

Desde la conexión de túnel, ejecute el comando siguiente para escalar la aplicación horizontalmente.

> [!NOTE]
> El identificador URI es http://localhost/marathon/v2/apps/, seguido del identificador de la aplicación que se va a escalar. Si utiliza el ejemplo de Nginx que se incluye aquí, el identificador URI sería http://localhost/marathon/v2/apps/nginx.
> 
> 

```bash
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Por último, consulte el punto de conexión de Marathon para las aplicaciones. Verá que ahora hay tres contenedores de Nginx.

```bash
curl localhost/marathon/v2/apps
```

## <a name="equivalent-powershell-commands"></a>Comandos de PowerShell equivalentes
Puede realizar las mismas acciones mediante comandos de PowerShell en un sistema Windows.

Para recopilar información sobre el clúster de DC/OS, como los nombres y los estados de los agentes, ejecute el siguiente comando:

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Los contenedores con formato Docker se implementan a través de Marathon mediante un archivo JSON que describe la implementación deseada. En el ejemplo siguiente, se implementa el contenedor Nginx y se enlaza el puerto 80 del agente de DC/OS al puerto 80 del contenedor.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 32.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Para implementar un contenedor con formato Docker, almacene el archivo JSON en una ubicación a la que se pueda accesible. A continuación, ejecute el siguiente comando para implementar el contenedor. Especifique la ruta del archivo JSON (`marathon.json` de este ejemplo).

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

La API de Marathon también se puede utilizar para escalar horizontalmente implementaciones de aplicaciones o reducirlas horizontalmente. En el ejemplo anterior, ha implementado una instancia de una aplicación. Vamos a escalarla horizontalmente a tres instancias de una aplicación. Para ello, cree un archivo JSON mediante el siguiente texto JSON y almacénelo en una ubicación accesible.

```json
{ "instances": 3 }
```

Ejecute el comando siguiente para escalar la aplicación horizontalmente:

> [!NOTE]
> El identificador URI es http://localhost/marathon/v2/apps/, seguido del identificador de la aplicación que se va a escalar. Si utiliza el ejemplo de Nginx que se incluye aquí, el identificador URI sería http://localhost/marathon/v2/apps/nginx.
> 
> 

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>Pasos siguientes
* [Más información acerca de los puntos de conexión HTTP de Mesos](http://mesos.apache.org/documentation/latest/endpoints/)
* [Más información acerca de la API de REST de Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html)


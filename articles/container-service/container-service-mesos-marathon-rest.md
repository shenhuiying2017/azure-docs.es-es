---
title: "Administración de un clúster DC/OS de Azure con la API de REST de Marathon | Microsoft Docs"
description: "Implemente contenedores en un clúster de Mesos del servicio Contenedor de Azure mediante la API de REST de Marathon."
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, contenedores, microservicios, Mesos, Azure
ms.assetid: c7175446-4507-4a33-a7a2-63583e5996e3
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 0aa9b3ae14f586fc79e6ebee898e794d526c19bd
ms.openlocfilehash: 0a5f75a085991f8e14d0949e2d13da1072ca6978


---
# <a name="dcos-container-management-through-the-marathon-rest-api"></a>Administración de contenedores de DC/OS a través de la API de REST de Marathon
DC/OS proporciona un entorno para implementar y escalar cargas de trabajo agrupadas, al tiempo que reduce el hardware subyacente. Por encima de DC/OS hay un marco que administra la programación y ejecución de cargas de trabajo de proceso.

Aunque hay marcos de trabajo disponibles para muchas cargas de trabajo conocidas, en este documento se detalla cómo crear y escalar implementaciones de contenedores mediante Marathon. Antes de trabajar con estos ejemplos, necesita un clúster de DC/OS configurado en el servicio Contenedor de Azure. También debe tener conectividad remota con este clúster. Para más información sobre estos aspectos, consulte los siguientes artículos:

* [Implementación de un clúster del servicio Contenedor de Azure](container-service-deployment.md)
* [Conexión a un clúster del servicio Contenedor de Azure](container-service-connect.md)

Una vez conectado al clúster de Azure Container Service, podrá acceder a DC/OS y a las API de REST relacionadas a través de http://localhost:local-port. Los ejemplos de este documento suponen que está realizando la tunelización en el puerto 80. Por ejemplo, se puede alcanzar el punto de conexión de Marathon en `http://localhost/marathon/v2/`. Para más información sobre las diferentes API, consulte la documentación de Mesosphere sobre [Marathon API](https://mesosphere.github.io/marathon/docs/rest-api.html) y [Chronos API](https://mesos.github.io/chronos/docs/api.html), así como la documentación de Apache sobre [Mesos Scheduler API](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

## <a name="gather-information-from-dcos-and-marathon"></a>Recopilación de información de DC/OS y Marathon
Antes de implementar contenedores en el clúster de DC/OS, recopile información sobre dicho clúster, como los nombres y el estado actual de los agentes de DC/OS. Para ello, consulte el punto de conexión `master/slaves` de la API de REST de DC/OS. Si todo va bien, la consulta devuelve una lista de agentes de DC/OS y varias propiedades de cada uno de ellos.

```bash
curl http://localhost/mesos/master/slaves
```

Ahora, utilice el punto de conexión `/apps` de Marathon para buscar las implementaciones actuales de aplicaciones en el clúster de DC/OS. Si se trata de un nuevo clúster, verá una matriz vacía para las aplicaciones.

```
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Implementación de un contenedor con formato Docker
Los contenedores con formato Docker se implementan a través de Marathon mediante un archivo JSON que describe la implementación deseada. En el ejemplo siguiente, se implementa el contenedor Nginx y se enlaza el puerto 80 del agente de DC/OS al puerto 80 del contenedor. Tenga en cuenta que la propiedad 'acceptedResourceRoles' se establece en 'slave_public'. El contenedor se implementa en un agente del conjunto de escalado de agentes públicos.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
    "acceptedResourceRoles": [
    "slave_public"
  ],
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Para implementar un contenedor con formato Docker, cree su propio archivo JSON o utilice el ejemplo proporcionado en la [demostración del servicio Contenedor de Azure](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json). Almacénelo en una ubicación accesible. A continuación, ejecute el siguiente comando para implementar el contenedor. Especifique el nombre del archivo JSON.

```
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

El resultado será similar al siguiente:

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

Ahora, si consulta Marathon sobre las aplicaciones, esta nueva aplicación se muestra en el resultado.

```
curl localhost/marathon/v2/apps
```

## <a name="scale-your-containers"></a>Escalado de los contenedores
La API de Marathon también se puede utilizar para escalar horizontalmente implementaciones de aplicaciones o reducirlas horizontalmente. En el ejemplo anterior, ha implementado una instancia de una aplicación. Vamos a escalarla horizontalmente a tres instancias de una aplicación. Para ello, cree un archivo JSON mediante el siguiente texto JSON y almacénelo en una ubicación accesible.

```json
{ "instances": 3 }
```

Ejecute el comando siguiente para escalar la aplicación horizontalmente.

> [!NOTE]
> El identificador URI es http://localhost/marathon/v2/apps/, seguido del identificador de la aplicación que se va a escalar. Si utiliza el ejemplo de Nginx que se incluye aquí, el identificador URI sería http://localhost/marathon/v2/apps/nginx.
> 
> 

```json
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Por último, consulte el punto de conexión de Marathon para las aplicaciones. Verá que ahora hay tres contenedores de Nginx.

```
curl localhost/marathon/v2/apps
```

## <a name="use-powershell-for-this-exercise-marathon-rest-api-interaction-with-powershell"></a>Uso de PowerShell para este ejercicio: interacción de la API de REST de Marathon con PowerShell
Puede realizar las mismas acciones mediante comandos de PowerShell en un sistema Windows.

Para recopilar información sobre el clúster de DC/OS, como los nombres y los estados de los agentes, ejecute el siguiente comando.

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Los contenedores con formato Docker se implementan a través de Marathon mediante un archivo JSON que describe la implementación deseada. En el ejemplo siguiente, se implementa el contenedor Nginx y se enlaza el puerto 80 del agente de DC/OS al puerto 80 del contenedor.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Cree su propio archivo JSON o utilice el ejemplo proporcionado en la [demostración del servicio Contenedor de Azure](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json). Almacénelo en una ubicación accesible. A continuación, ejecute el siguiente comando para implementar el contenedor. Especifique el nombre del archivo JSON.

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

La API de Marathon también se puede utilizar para escalar horizontalmente implementaciones de aplicaciones o reducirlas horizontalmente. En el ejemplo anterior, ha implementado una instancia de una aplicación. Vamos a escalarla horizontalmente a tres instancias de una aplicación. Para ello, cree un archivo JSON mediante el siguiente texto JSON y almacénelo en una ubicación accesible.

```json
{ "instances": 3 }
```

Ejecute el comando siguiente para escalar la aplicación horizontalmente.

> [!NOTE]
> El identificador URI es http://localhost/marathon/v2/apps/, seguido del identificador de la aplicación que se va a escalar. Si utiliza el ejemplo de Nginx que se incluye aquí, el identificador URI sería http://localhost/marathon/v2/apps/nginx.
> 
> 

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>Pasos siguientes
* [Más información sobre los puntos de conexión HTTP de Mesos](http://mesos.apache.org/documentation/latest/endpoints/).
* [Más información sobre la API de REST de Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html).




<!--HONumber=Jan17_HO4-->



<properties
   pageTitle="Equilibrio de carga de un clúster de Azure Container Service | Microsoft Azure"
   description="Equilibre la carga de un clúster de Azure Container Service."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Contenedores, microservicios, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/18/2016"
   ms.author="rogardle"/>

# Equilibrio de carga de un clúster de Azure Container Service

En este artículo configuraremos un front-end web que se puede escalar verticalmente para proporcionar servicios detrás de Azure Load Balancer.

## Requisitos previos

[Implemente una instancia del Servicio de contenedor de Azure](container-service-deployment.md) con el tipo de orquestador DC/OS y [asegúrese de que el cliente se puede conectar a su clúster](container-service-connect.md). Además, siga estos pasos.[AZURE.INCLUDE [Install the DC/OS command-line interface (CLI)](../../includes/container-service-install-dcos-cli-include.md)]


## Equilibrio de carga

En un clúster del Servicio de contenedor hay dos niveles de equilibrio de carga: Azure Load Balancer para los puntos de entrada públicos (los que seleccionarán los usuarios finales) y el equilibrador de carga de Marathon (marathon-lb) subyacente, que enruta las solicitudes de entrada a las instancias del contenedor que atienden las solicitudes. A medida que escalamos los contenedores que proporcionan el servicio, marathon-lb se adapta dinámicamente.

## Equilibrador de carga de Marathon

El equilibrador de carga de Marathon se reconfigura dinámicamente basándose en los contenedores que ha implementado. También es resistente a la pérdida de un contenedor o un agente; si esto ocurre, Mesos simplemente reiniciará el contenedor en otro lugar y volverá a configurar el equilibrador de carga de Marathon.

Para instalar el equilibrador de carga de Marathon, ejecute el siguiente comando desde el equipo cliente:

```bash
dcos package install marathon-lb
```

Ahora que tenemos el paquete marathon-lb, podemos implementar un servidor web sencillo con la siguiente configuración:


```json
{
  "id": "web",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "tutum/hello-world",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  }
}

```

Las partes importantes de este procedimiento son:
  * Establezca el valor de HAProxy\_0\_VHOST en el FQDN del equilibrador de carga de sus agentes. Tendrá el formato `<acsName>agents.<region>.cloudapp.azure.com`. Por ejemplo, si ha creado un clúster del Servicio de contenedor con el nombre `myacs` en la región `West US`, el nombre de dominio completo sería `myacsagents.westus.cloudapp.azure.com`. Para encontrarlo, también puede buscar el equilibrador de carga escribiendo "agente" en el nombre al buscar en los recursos del grupo de recursos creado para el Servicio de contenedor en el [Portal de Azure](https://portal.azure.com).
  * Establezca el valor de servicePort en un puerto > = 10 000. Esto identifica el servicio que se ejecuta en este contenedor; marathon-lb usa este valor para identificar los servicios entre los que debe equilibrar la carga.
  * Establezca la etiqueta HAPROXY\_GROUP en "external".
  * Establezca hostPort en 0. Eso significa que Marathon asignará un puerto disponible de manera arbitraria.

Copie este código JSON en un archivo llamado `hello-web.json` y úselo para implementar un contenedor:

```bash
dcos marathon app add hello-web.json
```

## Equilibrador de carga de Azure

De forma predeterminada, Azure Load Balancer expone los puertos 80, 8080 y 443. Si está utilizando uno de estos tres puertos (como ocurre en el ejemplo anterior), no hay nada que debe hacer. Debe ser capaz de alcanzar el agente del nombre de dominio completo del equilibrador de carga; cada vez que actualice, alcanzará uno de los tres servidores web de forma round robin. Sin embargo, si utiliza otro puerto, deberá agregar una regla de operación round robin y realizar un sondeo en el equilibrador de carga para ver cuál es el puerto utilizado. Puede realizar este procedimiento desde la [CLI de Azure](../xplat-cli-azure-resource-manager.md), con los comandos `azure lb rule create` y `azure lb probe create`.


## Otros escenarios

Puede darse una situación en la que utilice diferentes dominios para exponer diferentes servicios. Por ejemplo:

mydomain1.com -> Azure LB:80 -> marathon-lb:10001 -> mycontainer1:33292 mydomain2.com -> Azure LB:80 -> marathon-lb:10002 -> mycontainer2:22321

Para lograrlo, consulte los [hosts virtuales](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/), que proporcionan una manera de asociar los dominios a rutas de acceso específicas de marathon-lb.

Como alternativa, podría exponer puertos diferentes y reasignarlos al servicio correcto detrás de marathon-lb. Por ejemplo:

Azure lb:80 -> marathon-lb:10001 -> mycontainer:233423 Azure lb:8080 -> marathon-lb:1002 -> mycontainer2:33432


## Pasos siguientes

Consulte este [artículo del blog sobre el equilibrio de carga](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/) para más información sobre el equilibrador de carga de Marathon.

<!---HONumber=AcomDC_0622_2016-->
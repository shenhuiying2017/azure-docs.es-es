<properties
   pageTitle="Equilibrio de carga de contenedores en un clúster de Azure Container Service | Microsoft Azure"
   description="Equilibrio de carga en varios contenedores en un clúster de Azure Container Service."
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
   ms.date="07/11/2016"
   ms.author="rogardle"/>

# Equilibrio de carga de contenedores en un clúster de Azure Container Service

En este artículo, exploraremos cómo crear un equilibrador de carga interno en una instancia de Azure Container Service administrada por DC/OS mediante Marathon-LB. Esto le permitirá escalar aplicaciones horizontalmente. También le permitirá aprovechar sacar provecho de los clústeres de agente públicos y privados mediante la colocación de los equilibradores de carga en el clúster público y los contenedores de su aplicación en el clúster privado.

## Requisitos previos

[Implemente una instancia del Servicio de contenedor de Azure](container-service-deployment.md) con el tipo de orquestador DC/OS y [asegúrese de que el cliente se puede conectar a su clúster](container-service-connect.md).

## Equilibrio de carga

Hay dos niveles de equilibrio de carga en el clúster del Servicio de contenedores que se creará:

  1. Azure Load Balancer proporciona puntos de entrada públicos (los que llegarán a los usuarios finales). Esto lo proporciona automáticamente el Servicio de contenedores de Azure y, de forma predeterminada, se configura para exponer los puertos 80, 443 y 8080.
  2. Marathon Load Balancer (marathon-lb) enruta las solicitudes entrantes a instancias de contenedor que sirven a esas solicitudes. Marathon-lb se adapta dinámicamente a medida que escalamos los contenedores que proporcionan el servicio web. De manera predeterminada no se proporciona este equilibrador de carga en el Servicio de contenedores, pero se instala muy fácilmente.

## Equilibrador de carga de Marathon

El equilibrador de carga de Marathon se reconfigura dinámicamente basándose en los contenedores que ha implementado. También es resistente a la pérdida de contenedores o agentes; en caso de que ocurra, Apache Mesos simplemente reinicia el contenedor en otro lugar y marathon-lb se adapta.

Para instalar Marathon Load Balancer puede usar la IU web de DC/OS o la línea de comandos.

### Instalación de Marathon-LB con la IU web de DC/OS

  1. Haga clic en "Universe" (Universo)
  2. Busque "Marathon-LB"
  3. Haga clic en "Install" (Instalar)

![Instalación de maratón lb a través de la interfaz de Web DC/OS](./media/dcos/marathon-lb-install.png)

### Instalación de Marathon-LB con la CLI de DC/OS

Después de instalar la CLI de DC/OS y garantizar puede conectarse al clúster, ejecute el siguiente comando desde la máquina cliente:

```bash
dcos package install marathon-lb
```

Este comando instala automáticamente el equilibrador de carga en el clúster de agentes público.

## Implementación de una aplicación web de equilibrador de carga

Ahora que tenemos el paquete de marathon-lb, podemos implementar el contenedor de aplicaciones cuya carga se desea equilibrar. En este ejemplo se implementará un servidor web simple con la siguiente configuración:

```json
{
  "id": "web",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "yeasy/simple-web",
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

  * Establezca el valor de `HAProxy_0_VHOST` en el FQDN del equilibrador de carga de sus agentes. Tiene el formato `<acsName>agents.<region>.cloudapp.azure.com`. Por ejemplo, si crea un clúster de Container Service con el nombre `myacs` en la región `West US`, el FQDN sería `myacsagents.westus.cloudapp.azure.com`. Para encontrarlo, también puede buscar el equilibrador de carga que tenga el término "agent" en el nombre al examinar los recursos en el grupo que creó para Container Service en [Azure Portal](https://portal.azure.com).
  * Establezca el valor de servicePort en un puerto > = 10 000. Esto identifica el servicio que se ejecuta en este contenedor; marathon-lb usa este valor para identificar los servicios entre los que debe equilibrar la carga.
  * Establezca la etiqueta `HAPROXY_GROUP` en "external".
  * Establezca `hostPort` en 0. Eso significa que Marathon asignará un puerto disponible de manera arbitraria.
  * Establezca `instances` en el número de instancias que desea crear. Siempre puede escalar hacia arriba y abajo más adelante.

Merece la pena indicar que Marathon se implementará en el clúster privado de forma predeterminada, lo que significa que solo se podrá acceder a la implementación anterior a través de un equilibrador de carga, que normalmente es el comportamiento que se desea.

### Implementación mediante la IU web de DC/OS

  1. Visite la página de Marathon en http://localhost/marathon (después de configurar un [túnel SSH](container-service-connect.md)) y haga clic en `Create Appliction`
  2. En el cuadro de diálogo `New Application`, haga clic en `JSON Mode` en la esquina superior derecha
  3. Pegue el código anterior JSON en el editor
  4. Haga clic en `Create Appliction`

### Implementación mediante la CLI de DC/OS

Para implementar esta aplicación con la CLI de DC/OS, solo hay que copiar el JSON anterior en un archivo denominado `hello-web.json` y ejecutar:

```bash
dcos marathon app add hello-web.json
```

## Equilibrador de carga de Azure

De forma predeterminada, Azure Load Balancer expone los puertos 80, 8080 y 443. Si está utilizando uno de estos tres puertos (como ocurre en el ejemplo anterior), no hay nada que debe hacer. Debe ser capaz de alcanzar el agente del nombre de dominio completo del equilibrador de carga; cada vez que actualice, alcanzará uno de los tres servidores web de forma round robin. Sin embargo, si utiliza otro puerto, deberá agregar una regla de operación round robin y realizar un sondeo en el equilibrador de carga para ver cuál es el puerto utilizado. Este procedimiento se puede realizar desde la [CLI de Azure](../xplat-cli-azure-resource-manager.md), con los comandos `azure lb rule create` y `azure lb probe create`. También puede hacerlo mediante el Portal de Azure.


## Otros escenarios

Puede darse una situación en la que utilice diferentes dominios para exponer diferentes servicios. Por ejemplo:

mydomain1.com -> Azure LB:80 -> marathon-lb:10001 -> mycontainer1:33292 mydomain2.com -> Azure LB:80 -> marathon-lb:10002 -> mycontainer2:22321

Para lograrlo, consulte los [hosts virtuales](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/), que proporcionan una forma de asociar dominios a rutas de acceso concretas de marathon-lb.

Como alternativa, podría exponer puertos diferentes y reasignarlos al servicio correcto detrás de marathon-lb. Por ejemplo:

Azure lb:80 -> marathon-lb:10001 -> mycontainer:233423 Azure lb:8080 -> marathon-lb:1002 -> mycontainer2:33432


## Pasos siguientes

Para más información sobre [marathon-lb](https://dcos.io/docs/1.7/usage/service-discovery/marathon-lb/), consulte la documentación de DC/OS.

<!---HONumber=AcomDC_0921_2016-->
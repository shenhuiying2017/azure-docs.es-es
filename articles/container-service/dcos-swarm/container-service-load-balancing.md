---
title: "Equilibrio de carga de contenedores en un clúster DC/OS de Azure"
description: "Equilibrio de carga en varios contenedores en un clúster DC/OS de Azure Container Service."
services: container-service
author: rgardler
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 06/02/2017
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 6f5467d0fbcc577a548f1100ed6e4d380fe38759
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/13/2017
---
# <a name="load-balance-containers-in-an-azure-container-service-dcos-cluster"></a>Equilibrio de carga de contenedores en un clúster DC/OS de Azure Container Service

En este artículo, exploramos cómo crear un equilibrador de carga interno en una instancia de Azure Container Service administrada por DC/OS mediante Marathon-LB. Esta configuración le permite escalar aplicaciones horizontalmente. También le permite aprovechar sacar provecho de los clústeres de agente públicos y privados mediante la colocación de los equilibradores de carga en el clúster público y los contenedores de su aplicación en el clúster privado. En este tutorial, hizo lo siguiente:

> [!div class="checklist"]
> * Configurar un equilibrador de carga de Marathon
> * Implementación de una aplicación con el equilibrador de carga
> * Configurar Azure Load Balancer

Necesita un clúster de ACS con DC/OS para completar los pasos de este tutorial. Si es necesario, este [script de ejemplo](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) puede crear uno.

Para realizar este tutorial es necesaria la versión 2.0.4 o superior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="load-balancing-overview"></a>Introducción al equilibrio de carga

Hay dos capas de equilibrio de carga en un clúster DC/OS de Azure Container Service : 

**Azure Load Balancer** proporciona puntos de entrada públicos (los que llegarán a los usuarios finales). Azure Load Balancer lo proporciona automáticamente Azure Container Service y, de forma predeterminada, se configura para exponer los puertos 80, 443 y 8080.

**Marathon Load Balancer (marathon-lb)** enruta las solicitudes entrantes a instancias de contenedor que sirven a esas solicitudes. Marathon-lb se adapta dinámicamente a medida que escalamos los contenedores que proporcionan el servicio web. De manera predeterminada no se proporciona este equilibrador de carga en Azure Container Service, pero se instala fácilmente.

## <a name="configure-marathon-load-balancer"></a>Configuración de un equilibrador de carga de Marathon

El equilibrador de carga de Marathon se reconfigura dinámicamente basándose en los contenedores que ha implementado. También es resistente a la pérdida de contenedores o agentes; en caso de que ocurra, Apache Mesos reinicia el contenedor en otro lugar y marathon-lb se adapta.

Ejecute el siguiente comando para instalar el equilibrador de carga de Marathon en el clúster del agente público.

```azurecli-interactive
dcos package install marathon-lb
```

## <a name="deploy-load-balanced-application"></a>Implementación de una aplicación de carga equilibrada

Ahora que tenemos el paquete de marathon-lb, podemos implementar el contenedor de aplicaciones cuya carga se desea equilibrar. 

En primer lugar, obtenga el FQDN de los agentes expuestos públicamente.

```azurecli-interactive
az acs list --resource-group myResourceGroup --query "[0].agentPoolProfiles[0].fqdn" --output tsv
```

A continuación, cree un archivo denominado *hello-web.json* y cópielo en el contenido siguiente. La etiqueta `HAPROXY_0_VHOST` debe actualizarse con el FQDN de los agentes de DC/OS. 

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

Utilice la CLI de DC/OS para ejecutar la aplicación. De forma predeterminada, Marathon implementa la aplicación en el clúster privado. Esto significa que la implementación anterior solo es accesible a través del equilibrador de carga, lo que suele ser el comportamiento deseado.

```azurecli-interactive
dcos marathon app add hello-web.json
```

Una vez que se ha implementado la aplicación, busque el FQDN del clúster de agente para ver la aplicación de carga equilibrada.

![Imagen de la aplicación de carga equilibrada](./media/container-service-load-balancing/lb-app.png)

## <a name="configure-azure-load-balancer"></a>Configuración de Azure Load Balancer

De forma predeterminada, Azure Load Balancer expone los puertos 80, 8080 y 443. Si está utilizando uno de estos tres puertos (como ocurre en el ejemplo anterior), no hay nada que debe hacer. Debe ser capaz de alcanzar el agente del nombre de dominio completo del equilibrador de carga; cada vez que actualice, alcanzará uno de los tres servidores web con el mecanismo round robin. 

Si utiliza otro puerto, deberá agregar una regla de operación round robin y realizar un sondeo en el equilibrador de carga para ver cuál es el puerto utilizado. Este procedimiento se puede realizar desde la [CLI de Azure](../../azure-resource-manager/xplat-cli-azure-resource-manager.md), con los comandos `azure network lb rule create` y `azure network lb probe create`.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido acerca de equilibrio de carga en ACS con los equilibradores de carga de Azure y Marathon, incluidas las acciones siguientes:

> [!div class="checklist"]
> * Configurar un equilibrador de carga de Marathon
> * Implementación de una aplicación con el equilibrador de carga
> * Configurar Azure Load Balancer

Avance al tutorial siguiente para obtener información sobre la integración de Azure Storage con DC/OS en Azure.

> [!div class="nextstepaction"]
> [Montaje del recurso compartido de archivos de Azure en un clúster de DC/OS](container-service-dcos-fileshare.md)
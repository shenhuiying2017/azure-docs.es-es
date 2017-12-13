---
title: "Lanzamiento controlado con Vamp en un clúster de DC/OS en Azure"
description: "Cómo usar Vamp para lanzar servicios de forma controlada y aplicar el filtrado inteligente de tráfico en un clúster de DC/OS de Azure Container Service"
services: container-service
author: gggina
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 04/17/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: e23b316311ff88d1587da8d5ef777b91bfe0a2e9
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2017
---
# <a name="canary-release-microservices-with-vamp-on-an-azure-container-service-dcos-cluster"></a>Lanzamiento controlado de microservicios con Vamp en un clúster de DC/OS de Azure Container Service

En este tutorial, se configura Vamp en Azure Container Service con un clúster de DC/OS. Se lanza de forma controlada el servicio de demostración de Vamp "sava" y, después, se resuelve una incompatibilidad del servicio con Firefox aplicando el filtrado inteligente de tráfico. 

> [!TIP] 
> En este tutorial, Vamp se ejecuta en un clúster de DC/OS, pero también se puede usar Vamp con Kubernetes como orquestador.
>

## <a name="about-canary-releases-and-vamp"></a>Acerca de los lanzamientos controlados y Vamp


El [lanzamiento controlado](https://martinfowler.com/bliki/CanaryRelease.html) es una estrategia de implementación inteligente adoptada por organizaciones innovadoras como Netflix, Facebook y Spotify. Es un método que tiene sentido, ya que reduce los problemas, incluye redes de seguridad y aumenta la innovación. ¿Y por qué no se usa en todas las compañías? Extender una canalización CI/CD para incluir estrategias controladas agrega complejidad y requiere amplios conocimientos y experiencia en DevOps. Esto basta para disuadir a empresas de todos los tamaños antes incluso de empezar. 

[Vamp](http://vamp.io/) es un sistema de código abierto diseñado para facilitar esta transición y aportar características de lanzamiento controlado a su programador de contenedor preferido. La funcionalidad controlada de Vamp va más allá de las implementaciones basadas en porcentajes. El tráfico se puede filtrar y dividir en función de una amplia variedad de condiciones, por ejemplo, para dirigirlo a usuarios, intervalos de direcciones IP o dispositivos específicos. Vamp realiza un seguimiento y un análisis de las métricas de rendimiento, lo que permite una automatización basada en datos reales. Puede configurar la reversión automática en caso de error o escalar variantes de servicios individuales en función de la carga o la latencia.

## <a name="set-up-azure-container-service-with-dcos"></a>Configuración de Azure Container Service con DC/OS



1. [Implemente un clúster de DC/OS](container-service-deployment.md) con un maestro y dos agentes de tamaño predeterminado. 

2. [Cree un túnel SSH](../container-service-connect.md) para conectarse al clúster de DC/OS. En este artículo se da por supuesto que crea un túnel al clúster en el puerto local 80.


## <a name="set-up-vamp"></a>Configuración de Vamp

Ahora que tiene un clúster de DC/OS en ejecución, puede instalar Vamp desde la interfaz de usuario de DC/OS (http://localhost:80). 

![Interfaz de usuario de DC/OS](./media/container-service-dcos-vamp-canary-release/01_set_up_vamp.png)

La instalación se lleva a cabo en dos fases:

1. **Implemente Elasticsearch**.

2. A continuación, **implemente Vamp** instalando el paquete del universo de Vamp DC/OS.

### <a name="deploy-elasticsearch"></a>Implementación de Elasticsearch

Vamp necesita Elasticsearch para recopilar y agregar métricas. Puede usar las [imágenes magneticio de Docker](https://hub.docker.com/r/magneticio/elastic/) para implementar una pila de Elasticsearch de Vamp compatible.

1. En la interfaz de usuario de DC/OS, vaya a **Services** (Servicios) y haga clic en **Deploy Service** (Implementar servicio).

2. Seleccione **JSON mode** (Modo JSON) en el cuadro emergente **Deploy New Service** (Implementar nuevo servicio).

  ![Selección del modo JSON](./media/container-service-dcos-vamp-canary-release/02_deploy_service_json_mode.png)

3. Pegue el siguiente código JSON. Esta configuración ejecuta el contenedor con 1 GB de RAM y una comprobación básica del estado en el puerto de Elasticsearch.
  
  ```JSON
  {
    "id": "elasticsearch",
    "instances": 1,
    "cpus": 0.2,
    "mem": 1024.0,
    "container": {
      "docker": {
        "image": "magneticio/elastic:2.2",
        "network": "HOST",
        "forcePullImage": true
      }
    },
    "healthChecks": [
      {
        "protocol": "TCP",
        "gracePeriodSeconds": 30,
        "intervalSeconds": 10,
        "timeoutSeconds": 5,
        "port": 9200,
        "maxConsecutiveFailures": 0
      }
    ]
  }
  ```
  

3. Haga clic en **Implementar**.

  DC/OS implementa el contenedor de Elasticsearch. Puede realizar un seguimiento del progreso en la página **Services** (Servicios).  

  ![Implementación de Elasticsearch](./media/container-service-dcos-vamp-canary-release/03_deply_elasticsearch.png)

### <a name="deploy-vamp"></a>Implementación de Vamp

Una vez que se notifica que Elasticsearch se **está ejecutando**, puede agregar el paquete de universo de Vamp DC/OS. 

1. Vaya a **Universe** (Universo) y busque **vamp**. 
  ![Vamp en el universo de DC/OS](./media/container-service-dcos-vamp-canary-release/04_universe_deploy_vamp.png)

2. Haga clic en **Install** (Instalar) junto al paquete de vamp y elija **Advanced Installation** (Instalación avanzada).

3. Desplácese hacia abajo y escriba lo siguiente en elasticsearch-url: `http://elasticsearch.marathon.mesos:9200`. 

  ![Escriba la dirección URL de Elasticsearch](./media/container-service-dcos-vamp-canary-release/05_universe_elasticsearch_url.png)

4. Haga clic en **Review and Install** (Revisar e instalar) y en **Install** (Instalar) para iniciar la implementación.  

  DC/OS implementa todos los componentes de Vamp necesarios. Puede realizar un seguimiento del progreso en la página **Services** (Servicios).
  
  ![Implementación de Vamp como paquete de universo](./media/container-service-dcos-vamp-canary-release/06_deploy_vamp.png)
  
5. Una vez completada la implementación, puede acceder a la interfaz de usuario de Vamp:

  ![Servicio de Vamp en DC/OS](./media/container-service-dcos-vamp-canary-release/07_deploy_vamp_complete.png)
  
  ![Interfaz de usuario de Vamp](./media/container-service-dcos-vamp-canary-release/08_vamp_ui.png)


## <a name="deploy-your-first-service"></a>Implementación de su primer servicio

Ahora que Vamp está en funcionamiento, implemente un servicio desde un plano. 

En su forma más simple, un [plano de Vamp](http://vamp.io/documentation/using-vamp/blueprints/) describe los puntos de conexión (puertas de enlace), los clústeres y los servicios que se van a implementar. Vamp usa clústeres para agrupar variantes diferentes del mismo servicio en grupos lógicos para realizar el lanzamiento controlado o realizar pruebas A/B.  

Este escenario usa una aplicación monolítica de ejemplo denominada [**sava**](https://github.com/magneticio/sava), que se encuentra en la versión 1.0. El monolito está empaquetado en un contenedor de Docker, que se encuentra en Docker Hub en magneticio/sava:1.0.0. Normalmente, la aplicación se ejecuta en el puerto 8080, pero desea exponerla en el puerto 9050 en este caso. Implemente la aplicación a través de Vamp con un plano simple.

1. Vaya a **Deployments** (Implementaciones).

2. Haga clic en **Agregar**.

3. Pegue el siguiente código YAML del plano. Este plano contiene un clúster con solo una variante de servicio, que se cambia en un paso posterior:

  ```YAML
  name: sava                        # deployment name
  gateways:
    9050: sava_cluster/webport      # stable endpoint
  clusters:
    sava_cluster:               # cluster to create
     services:
        -
          breed:
            name: sava:1.0.0        # service variant name
            deployable: magneticio/sava:1.0.0
            ports:
              webport: 8080/http # cluster endpoint, used for canary releasing
  ```

4. Haga clic en **Guardar**. Vamp inicia la implementación.

La implementación se muestra en la página **Deployments** (Implementaciones). Haga clic en la implementación para supervisar su estado.

![Interfaz de usuario de Vamp: implementar sava](./media/container-service-dcos-vamp-canary-release/09_sava100.png)

![Servicio sava en la interfaz de usuario de Vamp](./media/container-service-dcos-vamp-canary-release/09a_sava100.png)

Se crean dos puertas de enlace, que se muestran en la página **Gateways** (Puertas de enlace):

* un punto de conexión estable para acceder al servicio en ejecución (puerto 9050) 
* una puerta de enlace interna administrada por Vamp (información sobre esta puerta de enlace más adelante). 

![Interfaz de usuario de Vamp: puertas de enlace de sava](./media/container-service-dcos-vamp-canary-release/10_vamp_sava_gateways.png)

El servicio sava ya está implementado, pero no puede acceder a él desde fuera porque Azure Load Balancer aún no sabe que debe reenviar el tráfico a él. Para acceder al servicio, actualice la configuración de red de Azure.


## <a name="update-the-azure-network-configuration"></a>Actualización de la configuración de red de Azure

Vamp implementó el servicio sava en los nodos de agente DC/OS y expuso un punto de conexión estable en el puerto 9050. Para acceder al servicio desde fuera del clúster de DC/OS, realice los cambios siguientes en la configuración de red de Azure en la implementación de clúster: 

1. **Configure Azure Load Balancer** para los agentes (el recurso denominado **dcos-agent-lb-xxxx**) con un sondeo de estado y una regla para reenviar tráfico en el puerto 9050 a las instancias de sava. 

2. **Actualice el grupo de seguridad de red** para los agentes públicos (el recurso denominado **XXXX-agent-public-nsg-XXXX**) para permitir tráfico en el puerto 9050.

Para pasos detallados para completar estas tareas mediante Azure Portal, consulte [Permitir el acceso público a una aplicación de Azure Container Service](container-service-enable-public-access.md). Especifique el puerto 9050 en todas las configuraciones de puerto.


Una vez creado todo, vaya a la hoja **Overview** (Información general) del equilibrador de carga del agente DC/OS (el recurso denominado **dcos-agent-lb-xxxx**). Busque la **dirección IP pública** y úsela para acceder a sava en el puerto 9050.

![Azure Portal: obtener la dirección IP pública](./media/container-service-dcos-vamp-canary-release/18_public_ip_address.png)

![sava](./media/container-service-dcos-vamp-canary-release/19_sava100.png)


## <a name="run-a-canary-release"></a>Ejecución de un lanzamiento controlado

Suponga que tiene una nueva versión de esta aplicación que quiera lanzar de forma controlada a producción. La tiene incluida en un contenedor como magneticio/sava:1.1.0 y está listo para empezar. Vamp le permite agregar fácilmente nuevos servicios a la implementación en ejecución. Estos servicios "combinados" se implementan junto con los existentes en el clúster y se les asigna un peso de 0 %. No se enruta ningún tráfico a un servicio recién combinado hasta que se ajusta la distribución del tráfico. El control deslizante de peso en la interfaz de usuario de Vamp ofrece un control completo sobre la distribución y permite hacer ajustes incrementales (lanzamiento controlado) o revertir de inmediato.

### <a name="merge-a-new-service-variant"></a>Combinación de una nueva variante de servicio

Para combinar el nuevo servicio sava 1.1 con la implementación en ejecución:

1. En la interfaz de usuario de Vamp, haga clic en **Blueprints** (Planos).

2. Haga clic en **Add** (Agregar) y pegue el siguiente código YAML de plano: este plano describe una nueva variante de un servicio (sava:1.1.0) para implementarla en el clúster existente (sava_cluster).

  ```YAML
  name: sava:1.1.0      # blueprint name
  clusters:
    sava_cluster:       # cluster to update
      services:
        -
          breed:
            name: sava:1.1.0    # service variant name
            deployable: magneticio/sava:1.1.0    
            ports:
              webport: 8080/http # cluster endpoint to update
  ```
  
3. Haga clic en **Guardar**. El plano se almacena y se muestra en la página **Blueprints** (Planos).

4. Abra el menú de acción en el plano de sava:1.1 y haga clic en **Merge to** (Combinar con).

  ![Interfaz de usuario de Vamp: planos](./media/container-service-dcos-vamp-canary-release/20_sava110_mergeto.png)

5. Seleccione la implementación **sava** y haga clic en **Merge** (Combinar).

  ![Interfaz de usuario de Vamp: combinación del plano con la implementación](./media/container-service-dcos-vamp-canary-release/21_sava110_merge.png)

Vamp implementa la nueva variante de servicio sava:1.1.0 descrita en el plano junto con sava:1.0.0 en el clúster **sava_cluster** de la implementación en ejecución. 

![Interfaz de usuario de Vamp: implementación de sava actualizada](./media/container-service-dcos-vamp-canary-release/22_sava_cluster.png)

También se actualiza la puerta de enlace **sava/sava_cluster/webport** (el punto de conexión del clúster), lo que agrega una ruta al servicio sava:1.1.0 recién implementado. En este momento, aquí no se enruta ningún tráfico (el peso, **WEIGHT**, está establecido en 0 %).

![Interfaz de usuario de Vamp: puerta de enlace de clúster](./media/container-service-dcos-vamp-canary-release/23_sava_cluster_webport.png)

### <a name="canary-release"></a>Lanzamiento controlado

Con ambas versiones de sava implementadas en el mismo clúster, ajuste la distribución de tráfico entre ellas con el control deslizante **WEIGHT** (Peso).

1. Haga clic en ![Vamp UI - edit](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) junto a **WEIGHT** (Peso).

2. Establezca la distribución de peso en 50 %/50 % y haga clic en **Save** (Guardar).

  ![Interfaz de usuario de Vamp: control deslizante de peso de puerta de enlace](./media/container-service-dcos-vamp-canary-release/24_sava_cluster_webport_weight.png)

3. Vuelva al explorador y actualice la página de sava varias veces. Ahora la aplicación sava cambia entre una página sava:1.0 y una sava:1.1.

  ![Alternar entre los servicios sava1.0 y sava1.1](./media/container-service-dcos-vamp-canary-release/25_sava_100_101.png)


  > [!NOTE]
  > Esta alternancia de la página funciona mejor con el modo "Incógnito" o "Anónimo" del explorador a causa del almacenamiento en caché de recursos estáticos.
  >

### <a name="filter-traffic"></a>Filtrado del tráfico

Suponga que, después de la implementación, detecta una incompatibilidad en sava:1.1.0 que causa problemas de visualización en los exploradores Firefox. Puede establecer Vamp para que filtre el tráfico entrante y dirija a todos los usuarios de Firefox hacia el servicio sava:1.0.0 que se sabe que es estable. Este filtro resuelve al instante la interrupción para los usuarios de Firefox, mientras que los demás siguen disfrutando de las ventajas del servicio sava:1.1.0 mejorado.

Vamp usa **condiciones** para filtrar el tráfico entre rutas en una puerta de enlace. En primer lugar, el tráfico se filtra y dirige de acuerdo con las condiciones que se aplican a cada ruta. Todo el tráfico restante se distribuye según la configuración de peso de la puerta de enlace.

Puede crear una condición para filtrar a todos los usuarios de Firefox y dirigirles al servicio anterior sava:1.0.0:

1. En la página **Gateways** (Puertas de enlace) de sava/sava_cluster/webport, haga clic en ![Vamp UI - edit](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) para agregar una **condición** a la ruta sava/sava_cluster/sava:1.0.0/webport. 

2. Escriba la condición **user-agent == Firefox** y haga clic en ![Vamp UI - save](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png).

  Vamp agrega la condición con la intensidad predeterminada de 0 %. Para empezar a filtrar el tráfico, debe ajustar la intensidad de la condición.

3. Haga clic en ![Vamp UI - edit](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) para cambiar el valor de **STRENGTH** (Intensidad) aplicado a la condición.
 
4. Establezca **STRENGTH** (Intensidad) en 100 % y haga clic en ![Vamp UI - save](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png) para guardar.

  Ahora Vamp envía todo el tráfico que coincide con la condición (todos los usuarios de Firefox) a sava:1.0.0.

  ![Interfaz de usuario de Vamp: aplicar la condición a la puerta de enlace](./media/container-service-dcos-vamp-canary-release/26_apply_condition.png)

5. Por último, ajuste el peso de la puerta de enlace para enviar todo el tráfico restante (todos los usuarios que no sean de Firefox) al nuevo servicio sava:1.1.0. Haga clic en ![Vamp UI - edit](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) junto a **WEIGHT** (Peso) y establezca la distribución de peso para que se dirija un 100 % a la ruta sava/sava_cluster/sava:1.1.0/webport.

  Ahora, todo el tráfico que la condición no filtre se dirige al nuevo servicio sava:1.1.0.

6. Para ver el filtro de acción, abra dos exploradores distintos (Firefox y otro) y acceda al servicio sava desde ambos. Todas las solicitudes de Firefox se envían a sava:1.0.0, mientras que se dirige a todos los demás exploradores hacia sava:1.1.0.

  ![Interfaz de usuario de Vamp: filtrar el tráfico](./media/container-service-dcos-vamp-canary-release/27_filter_traffic.png)

## <a name="summing-up"></a>Resumen

Este artículo ha sido una introducción rápida a Vamp en un clúster de DC/OS. Para empezar, puso Vamp en funcionamiento en el clúster de DC/OS de Azure Container Service, implementó un servicio con un plano de Vamp y accedió a él en el punto de conexión expuesto (puerta de enlace).

También se han tratado algunas versátiles características de Vamp, como combinar una nueva variante de servicio con la implementación en ejecución e introducirla de forma incremental y después filtrar el tráfico para resolver una incompatibilidad conocida.


## <a name="next-steps"></a>Pasos siguientes

* Aprenda sobre cómo administrar las acciones de Vamp por medio de la [API de REST de Vamp](http://vamp.io/documentation/api/api-reference/).

* Cree scripts de automatización de Vamp en Node.js y ejecútelos como [flujos de trabajo de Vamp](http://vamp.io/documentation/tutorials/create-a-workflow/).

* Consulte más [tutoriales sobre VAMP](http://vamp.io/documentation/tutorials/overview/).


---
title: "Supervisión de un clúster de Azure DC/OS: pila ELK"
description: "Supervisión de un clúster de DC/OS en un clúster de Azure Container Service con ELK (Elasticsearch, Logstash y Kibana)."
services: container-service
author: sauryadas
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: b378fc38233534df74582388e6e832d40f431d11
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2017
---
# <a name="monitor-an-azure-container-service-cluster-with-elk"></a>Supervisión de un clúster de Azure Container Service con ELK

En este artículo se muestra cómo implementar la pila ELK (Elasticsearch, Logstash, Kibana) en un clúster de DC/OS de Azure Container Service. 

## <a name="prerequisites"></a>Requisitos previos
[Implemente](container-service-deployment.md) y [conecte](../container-service-connect.md) un clúster de DC/OS configurado por Azure Container Service. Explore el panel de DC/OS y los servicios de Marathon [aquí](container-service-mesos-marathon-ui.md). Instale también el [equilibrador de carga de Marathon](container-service-load-balancing.md).


## <a name="elk-elasticsearch-logstash-kibana"></a>ELK (Elasticsearch, Logstash, Kibana)
La pila ELK es una combinación de Elasticsearch, Logstash y Kibana que proporciona una pila de un extremo a otro que se puede usar para supervisar y analizar los registros de un clúster.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>Configuración de la pila ELK en un clúster de DC/OS
Acceda a su interfaz de usuario de DC/OS a través de [http://localhost: 80 /](http://localhost:80/). Una vez que esté en ella, navegue a **Universe**. Busque e instale Elasticsearch, Logstash y Kibana desde DC/OS, y en ese orden específico. Para más información acerca de la configuración, siga el vínculo **Advanced Installation** (Instalación avanzada).

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

Una vez que los contenedores ELK están en funcionamiento, es preciso habilitar Kibana para que se pueda acceder a él a través de Marathon-LB. Navegue a **Services** > **kibana** (Servicios > kibana) y haga clic en **Edit** (Editar), como se muestra a continuación.

![ELK4](./media/container-service-monitoring-elk/elk4.PNG)


Alterne al **modo JSON** y desplácese hasta la sección de etiquetas.
Es preciso que agregue una entrada `"HAPROXY_GROUP": "external"` aquí tal como se muestra a continuación.
Una vez que haga clic en **Deploy changes** (Implementar cambios), se reinicia el contenedor.

![ELK5](./media/container-service-monitoring-elk/elk5.PNG)


Si desea comprobar si Kibana está registrado como servicio en el panel HAPROXY, es preciso que abra el puerto 9090 del clúster de agente cuando HAPROXY se ejecute en el puerto 9090.
De manera predeterminada, abrimos los puertos 80, 8080 y 443 en el clúster de agente de DC/OS.
Las instrucciones para abrir un puerto y proporcionar acceso público se encuentran [aquí](container-service-enable-public-access.md).

Para acceder al panel de HAPROXY, abra la interfaz de administración de Marathon-LB en: `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`.
Una vez que navegue a la dirección URL, debería ver el panel de HAPROXY, tal como se muestra a continuación, y debería ver una entrada de servicio de Kibana.

![ELK6](./media/container-service-monitoring-elk/elk6.PNG)


Para acceder al panel de Kibana, que se implementa en el puerto 5601, es preciso abrir el puerto 5601. Siga las instrucciones que encontrará [aquí](container-service-enable-public-access.md). Después, abra el panel de Kibana en: `http://localhost:5601`.

## <a name="next-steps"></a>Pasos siguientes

* Con respecto al reenvío y configuración de registros de aplicaciones y sistemas, consulte [Log Management in DC/OS with ELK](https://docs.mesosphere.com/1.8/administration/logging/elk/) (Administración de registros en DC/OS con ELK).

* En cuanto al filtrado de registros, vea [Filtering Logs with ELK](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/) (Filtrado de registros con ELK). 

 


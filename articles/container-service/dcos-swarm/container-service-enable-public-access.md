---
title: "Habilitación del acceso a la aplicación de contenedores de Azure DC/OS"
description: "Cómo permitir el acceso público a contenedores DC/OS de Azure Container Service."
services: container-service
author: sauryadas
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 08/26/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: aedc97335a0b9ad00cf653477b62bf530b556900
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2017
---
# <a name="enable-public-access-to-an-azure-container-service-application"></a>Permitir el acceso público a una aplicación de Azure Container Service

Cualquier contenedor de DC/OS del [grupo de agentes público](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) de ACS se expone automáticamente a Internet. De forma predeterminada, los puertos **80**, **443** y **8080** están abiertos y se puede tener acceso a cualquier contenedor (público) que escucha en esos puertos. En este artículo se explica cómo abrir más puertos para las aplicaciones de Azure Container Service.

## <a name="open-a-port-portal"></a>Abrir un puerto (portal)
En primer lugar, es necesario abrir el puerto que desee.

1. Inicie sesión en el portal.
2. Busque el grupo de recursos en el que ha implementado Azure Container Service.
3. Seleccione el equilibrador de carga del agente (cuya denominación es similar a **XXXX-agent-lb-XXXX**).
   
    ![Equilibrador de carga de Azure Container Service](./media/container-service-enable-public-access/agent-load-balancer.png)
4. Haga clic en **Sondeos** y luego en **Agregar**.
   
    ![Sondeos del equilibrador de carga de Azure Container Service](./media/container-service-enable-public-access/add-probe.png)
5. Rellene el formulario de sondeo y haga clic en **Aceptar**.
   
   | Campo | Description |
   | --- | --- |
   | Nombre |Un nombre descriptivo del sondeo. |
   | Port |El puerto del contenedor que se va a probar. |
   | Ruta de acceso |(Cuando está en modo HTTP) La ruta de acceso relativa del sitio de web para sondear. HTTPS no es compatible. |
   | Intervalo |La cantidad de tiempo entre los intentos de sonde, en segundos. |
   | Umbral incorrecto |Número de intentos de sondeo consecutivos antes de considerar que el contenedor es incorrecto. |
6. En las propiedades del equilibrador de carga del agente, haga clic en **Reglas de equilibrio de carga** y luego en **Agregar**.
   
    ![Reglas del equilibrador de carga de Azure Container Service](./media/container-service-enable-public-access/add-balancer-rule.png)
7. Rellene el formulario del equilibrador de carga y haga clic en **Aceptar**.
   
   | Campo | Description |
   | --- | --- |
   | Nombre |Un nombre descriptivo del equilibrador de carga. |
   | Port |El puerto de entrada público. |
   | Puerto back-end |El puerto público interno del contenedor al que enrutar el tráfico. |
   | Grupo back-end |Los contenedores de este grupo serán el destino de este equilibrador de carga. |
   | Sondeo |El sondeo que se utiliza para determinar si un destino del **grupo back-end** es correcto. |
   | Persistencia de la sesión |Determina cómo se debe controlar el tráfico de un cliente para la duración de la sesión.<br><br>**Ninguna**: cualquier contenedor puede controlar solicitudes sucesivas del mismo cliente.<br>**IP del cliente**: el mismo contenedor puede controlar solicitudes sucesivas de la dirección IP del mismo cliente.<br>**IP y protocolo del cliente**: el mismo contenedor puede controlar solicitudes sucesivas de la combinación del protocolo y la dirección IP del mismo cliente. |
   | Tiempo de espera inactividad |(Solo TCP) En minutos, el tiempo que se mantiene abierto el cliente TCP/HTTP sin depender de mensajes *keep-alive* . |

## <a name="add-a-security-rule-portal"></a>Agregar una regla de seguridad (portal)
A continuación, es necesario agregar una regla de seguridad que enruta el tráfico desde el puerto abierto a través del firewall.

1. Inicie sesión en el portal.
2. Busque el grupo de recursos en el que ha implementado Azure Container Service.
3. Seleccione el grupo de seguridad de red del agente **público** (cuya denominación es similar a **XXXX-agent-public-nsg-XXXX**).
   
    ![Grupo de seguridad de red de Azure Container Service](./media/container-service-enable-public-access/agent-nsg.png)
4. Seleccione **Reglas de seguridad de entrada** y luego **Agregar**.
   
    ![Reglas de grupos de seguridad de red de Azure Container Service](./media/container-service-enable-public-access/add-firewall-rule.png)
5. Rellene la regla de firewall para permitir el puerto público y haga clic en **Aceptar**.
   
   | Campo | Description |
   | --- | --- |
   | Nombre |Un nombre descriptivo de la regla de firewall. |
   | Prioridad |Clasificación de prioridad de la regla. Cuanto menor sea el número de prioridad, mayor será la prioridad de la regla. |
   | Origen |Restringir el intervalo de direcciones IP de entrada que esta regla admitirá o denegará. Utilizar **Cualquiera** para no especificar una restricción. |
   | Servicio |Seleccionar un conjunto de servicios predefinidos a los que vaya destinada esta regla de seguridad. De lo contrario, utilizar **Personalizado** para crear uno propio. |
   | Protocol |Restringir el tráfico basado en **TCP** o **UDP**. Utilizar **Cualquiera** para no especificar una restricción. |
   | Intervalo de puertos |Cuando **Servicio** es **Personalizado**, especifica el intervalo de puertos al que afecta esta regla. Puede usar un único puerto, como **80** o un intervalo como **1024-1500**. |
   | Acción |Permitir o denegar el tráfico que cumple los criterios. |

## <a name="next-steps"></a>Pasos siguientes
Obtenga información sobre la diferencia entre [agentes de DC/OS públicos y privados](container-service-dcos-agents.md).

Obtenga más información sobre cómo [administrar contenedores de DC/OS](container-service-mesos-marathon-ui.md).


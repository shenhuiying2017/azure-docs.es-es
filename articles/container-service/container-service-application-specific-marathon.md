---
title: "Servicio de Marathon específico del usuario o la aplicación | Microsoft Docs"
description: "Creación de un servicio de Marathon específico del usuario o la aplicación"
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Contenedores, Marathon, microservicios, DC/OS, Azure
ms.assetid: 16ecc16e-e504-480e-8dc3-cac14e9e1561
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2016
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: befd26888b653c660cc614221684732771496aed


---
# <a name="create-an-application-or-userspecific-marathon-service"></a>Creación de un servicio de Marathon específico del usuario o la aplicación
Azure Container Service proporciona un conjunto de servidores maestros en los que preconfiguramos Apache Mesos y Marathon. Aunque los servidores maestros se pueden usar para organizar las aplicaciones en el clúster, es mejor no utilizarlos para este fin. Por ejemplo, para cambiar la configuración de Marathon es necesario iniciar sesión en los servidores maestros y realizar los cambios; esto fomenta la existencia de servidores maestros únicos, que son algo diferentes de los estándar y que se deben atender y administrar de forma independiente. Además, la configuración que requiere un equipo puede no ser óptima para otro.

En este artículo explicaremos cómo agregar un servicio de Marathon específico del usuario o de la aplicación.

Puesto que este servicio pertenece a un solo usuario o equipo, se puede configurar libremente de la forma deseada. Además, el Servicio de contenedor de Azure garantiza que el servicio continúa ejecutándose. Si se produce un error en el servicio, el Servicio de contenedor de Azure se reiniciará automáticamente. La mayoría del tiempo no notará siquiera que ha habido tiempo de inactividad.

## <a name="prerequisites"></a>Requisitos previos
[Implemente una instancia de Azure Container Service](container-service-deployment.md) con el tipo de orquestador DC/OS y [asegúrese de que el cliente se puede conectar a su clúster](container-service-connect.md). Además, siga estos pasos.

[!INCLUDE [install the DC/OS CLI](../../includes/container-service-install-dcos-cli-include.md)]

## <a name="create-an-application-or-userspecific-marathon-service"></a>Creación de un servicio de Marathon específico del usuario o la aplicación
Para comenzar, cree un archivo de configuración JSON que defina el nombre del servicio de aplicación que quiere crear. Aquí usamos `marathon-alice` como nombre de la plataforma. Guarde el archivo como `marathon-alice.json`o algo similar:

```json
{"marathon": {"framework-name": "marathon-alice" }}
```

A continuación, use la CLI de DC/OS para instalar la instancia de Marathon con las opciones establecidas en el archivo de configuración:

```bash
dcos package install --options=marathon-alice.json marathon
```

Ahora debería ver que su servicio `marathon-alice` se ejecuta en la pestaña Servicios de la interfaz de usuario de DC/OS. La interfaz de usuario será `http://<hostname>/service/marathon-alice/` si quiere acceder a ella directamente.

## <a name="set-the-dcos-cli-to-access-the-service"></a>Configuración de la CLI de DC/OS para acceder al servicio
Opcionalmente, puede configurar la CLI de DC/OS para acceder a este servicio; para ello, establezca la propiedad `marathon.url` para que apunte a la instancia de `marathon-alice` de la manera siguiente:

```bash
dcos config set marathon.url http://<hostname>/service/marathon-alice/
```

Puede comprobar con qué instancia de Marathon funciona su CLI con el comando `dcos config show` . Puede volver a utilizar el servicio de Marathon maestro con el comando `dcos config unset marathon.url`.




<!--HONumber=Nov16_HO2-->



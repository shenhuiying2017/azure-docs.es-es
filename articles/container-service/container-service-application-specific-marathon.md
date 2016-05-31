<properties
   pageTitle="Servicio de Marathon específico del usuario o de la aplicación | Microsoft Azure"
   description="Creación de un servicio de Marathon específico del usuario o de la aplicación"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Contenedores, Marathon, microservicios, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/12/2016"
   ms.author="rogardle"/>

# Creación de un servicio de Marathon específico del usuario o de la aplicación

Azure Container Service proporciona un conjunto de servidores maestros en los que preconfiguramos Apache Mesos y Marathon. Aunque estos servidores se pueden usar para organizar las aplicaciones en el clúster, es mejor no utilizarlos para este fin. Por ejemplo, para cambiar la configuración de Marathon es necesario iniciar sesión en los servidores maestros y realizar los cambios; esto fomenta la existencia de servidores maestros únicos que son algo diferentes de los estándar y que se deben atender y administrar de forma independiente. Además, la configuración que requiere un equipo puede no ser óptima para otro. En este artículo explicaremos cómo agregar un servicio de Marathon específico del usuario o de la aplicación.

Dado que este servicio pertenece a un solo usuario o equipo, se puede configurar libremente de la forma deseada. Además, Azure Container Service garantiza que el servicio se sigue ejecutando; si el servicio da error, Azure Container Service lo reiniciará automáticamente. La mayoría del tiempo no notará siquiera que ha habido tiempo de inactividad.

## Requisitos previos

[Implemente una instancia de Azure Container Service](container-service-deployment.md) con el tipo de orquestador DCOS, [asegúrese de que el cliente se puede conectar a su clúster](container-service-connect.md) e[AZURE.INCLUDE [instale la CLI de DC/OS](../../includes/container-service-install-dcos-cli-include.md).].

## Creación de un servicio de Marathon específico del usuario o de la aplicación

Para comenzar, cree un archivo de configuración JSON que defina el nombre del servicio de aplicación que quiere crear. Aquí usamos `marathon-alice` como nombre del marco. Guarde el archivo como `marathon-alice.json` o algo similar:

```json
{"marathon": {"framework-name": "marathon-alice" }}
```

A continuación, use la CLI de DC/OS para instalar la instancia de Marathon con las opciones establecidas en el archivo de configuración:

```bash
dcos package install --options=marathon-alice.json marathon
```

Ahora debería ver que su servicio `marathon-alice` se ejecuta en la pestaña de servicios de la interfaz de usuario de DC/OS. La interfaz de usuario será `http://<hostname>/service/marathon-alice/` si quiere acceder a ella directamente.

## Configuración de la CLI de DC/OS para acceder al servicio

Opcionalmente, puede configurar la CLI de DC/OS para acceder a este servicio; para ello, establezca la propiedad `marathon.url` para que apunte a la instancia de `marathon-alice` de la manera siguiente:

```bash
dcos config set marathon.url http://<hostname>/service/marathon-alice/
```

Puede comprobar en qué instancia de Marathon funciona la CLI con el comando `dcos config show` y puede volver al uso del servicio maestro de Marathon con el comando `dcos config unset marathon.url`.

<!---HONumber=AcomDC_0525_2016-->
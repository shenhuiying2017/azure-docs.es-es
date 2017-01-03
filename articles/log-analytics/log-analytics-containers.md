---
title: "Solución Containers en Log Analytics | Microsoft Docs"
description: "La solución Containers en Log Analytics le ayuda a ver y administrar los hosts de contenedores de Docker en una sola ubicación."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: e1e4b52b-92d5-4bfa-8a09-ff8c6b5a9f78
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 6cdc0730d7632e41b393c4abb17badc255e21a8d
ms.openlocfilehash: 0bc5366417f08c63f5fd5588c94381faf6a2397d


---
# <a name="containers-preview-solution-log-analytics"></a>Solución Containers (versión preliminar) en Log Analytics
En este artículo se describe cómo configurar y usar la solución Containers de Log Analytics, que le permite ver y administrar los hosts de contenedores de Docker en una sola ubicación. Docker es un sistema de virtualización de software que usa para crear contenedores que automatizan la implementación de software en su infraestructura de TI.

Con la solución, puede ver qué contenedores se ejecutan en sus hosts de contenedores y qué imágenes se ejecutan en los contenedores. Puede ver información de auditoría detallada que muestra los comandos que se usan con los contenedores. Y, para solucionar los problemas de los contenedores, puede ver y buscar registros centralizados sin tener que ver los hosts de Docker de forma remota. Puede encontrar los contenedores que están causando ruido o realizando un consumo excesivo de recursos en un host. También puede ver la información centralizada acerca de la CPU, la memoria, el almacenamiento y el uso y el rendimiento de la red en relación con los contenedores.

## <a name="installing-and-configuring-the-solution"></a>Instalación y configuración de la solución
Utilice la siguiente información para instalar y configurar la solución.

Agregue la solución Containers a su área de trabajo de OMS mediante el proceso descrito en [Incorporación de soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md).

Hay dos maneras de instalar y usar Docker con OMS:

* En sistemas operativos compatibles con Linux, instale y ejecute Docker y, después, instale y configure el agente de OMS para Linux.
* En CoreOS, no se puede ejecutar el agente de OMS para Linux. En su lugar, ejecute una versión en contenedores del agente de OMS para Linux.

Consulte las versiones del sistema operativo Linux y de Docker compatibles con su host de contenedores en [GitHub](https://github.com/Microsoft/OMS-docker).

> [!IMPORTANT]
> Docker se debe estar ejecutando **antes de** instalar el [agente de OMS para Linux](log-analytics-linux-agents.md) en los hosts de contenedores. Si ya ha instalado el agente antes de instalar Docker, debe volver a instalar el agente de OMS para Linux. Para más información acerca de Docker, consulte el [sitio web de Docker](https://www.docker.com).
>
>

Necesita configurar las siguientes opciones en sus hosts de contenedores para poder supervisar los contenedores.

## <a name="configure-settings-for-the-linux-container-host"></a>Configuración de las opciones del host de contenedores de Linux

Las siguientes distribuciones x64 de Linux se admiten como hosts de contenedor:

- Ubuntu 14.04 LTS, 16.04 LTS
- CoreOS(stable)
- Amazon Linux 2016.03
- openSUSE 13.2
- CentOS 7
- SLES 12
- RHEL 7.2

Después de instalar Docker, use la siguientes opciones para el host de contenedores para configurar el agente que se usará con Docker. Necesitará [identificador y la clave de su área de trabajo de OMS](log-analytics-linux-agents.md).

### <a name="for-all-container-hosts-except-coreos"></a>Para todos los hosts de contenedor excepto CoreOS

- Siga las instrucciones en [Steps to install the OMS Agent for Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md)(Pasos para instalar el agente de OMS para Linux).

### <a name="for-all-container-hosts-including-coreos"></a>Para todos los hosts de contenedor incluido CoreOS

Inicie el contenedor de OMS que desea supervisar. Modifique y use el ejemplo siguiente.

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

### <a name="switching-from-using-an-installed-agent-to-one-in-a-container"></a>Cambio de un agente instalado a un agente en un contenedor
Si antes usaba el agente instalado directamente y ahora desea usar un agente que se ejecuta en un contenedor, primero debe quitar OMSAgent. Consulte los [pasos para instalar el agente de OMS para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md).

## <a name="containers-data-collection-details"></a>Información detallada sobre la recopilación de datos en contenedores
La solución Containers recopila diversos datos de registro y métricas de rendimiento de los hosts de contenedores y de los contenedores mediante los agentes de OMS para Linux que haya habilitado, y del agente OMSAgent que se ejecuta en los contenedores.

La siguiente tabla muestra los métodos de recopilación de datos y otros detalles sobre cómo se recopilan los datos para Containers.

| plataforma | Agente de OMS para Linux | Agente de SCOM | Almacenamiento de Azure | ¿Se necesita SCOM? | Datos del agente de SCOM enviados a través del grupo de administración | Frecuencia de recopilación |
| --- | --- | --- | --- | --- | --- | --- |
|  Linux |![Sí](./media/log-analytics-containers/oms-bullet-green.png) |![No](./media/log-analytics-containers/oms-bullet-red.png) |![No](./media/log-analytics-containers/oms-bullet-red.png) |![No](./media/log-analytics-containers/oms-bullet-red.png) |![No](./media/log-analytics-containers/oms-bullet-red.png) |Cada 3 minutos |

En la tabla siguiente se muestran ejemplos de tipos de datos recopilados por la solución Containers y los tipos de datos que se usan en las búsquedas de registros y los resultados:

| Tipo de datos | Tipo de datos en la búsqueda de registros | Fields |
| --- | --- | --- |
| Rendimiento de hosts y contenedores | `Type=Perf` | Computer, ObjectName, CounterName &#40;%Processor Time, Disk Reads MB, Disk Writes MB, Memory Usage MB, Network Receive Bytes, Network Send Bytes, Processor Usage sec, Network&#41;, CounterValue,TimeGenerated, CounterPath, SourceSystem |
| Inventario de contenedor | `Type=ContainerInventory` | TimeGenerated, Computer, container name, ContainerHostname, Image, ImageTag, ContinerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Inventario de imágenes de contenedor | `Type=ContainerImageInventory` | TimeGenerated, Computer, Image, ImageTag, ImageSize, VirtualSize, Running, Paused, Stopped, Failed, SourceSystem, ImageID, TotalContainer |
| Registro de contenedor | `Type=ContainerLog` | TimeGenerated, Computer, image ID, container name, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Registro del servicio de contenedores | `Type=ContainerServiceLog`  | TimeGenerated, Computer, TimeOfCommand, Image, Command, SourceSystem, ContainerID |

## <a name="monitor-containers"></a>Supervisión de contenedores
Una vez habilitada la solución en el portal de OMS, verá el icono **Containers** que muestra la información de resumen de los hosts de contenedores y de los contenedores que se ejecutan en los hosts.

![Icono de Containers](./media/log-analytics-containers/containers-title.png)

El icono muestra información general de cuántos contenedores hay en el entorno y si su estado es de error, en ejecución o detenido.

### <a name="using-the-containers-dashboard"></a>Uso del panel Containers
Haga clic en el icono **Containers**. Desde allí verá las vistas organizadas por:

* Eventos de contenedor
* Errors
* Estado de los contenedores
* Inventario de imágenes de contenedor
* Rendimiento de CPU y memoria

Cada sección del panel es una representación visual de una búsqueda que se ejecuta en los datos recopilados.

![Panel Containers](./media/log-analytics-containers/containers-dash01.png)

![Panel Containers](./media/log-analytics-containers/containers-dash02.png)

En la hoja **Container Status** (Estado del contenedor), haga clic en la parte superior, como se muestra a continuación.

![Estado de los contenedores](./media/log-analytics-containers/containers-status.png)

Se abre Búsqueda de registros, con información acerca de los hosts y los contenedores que se ejecutan en ellos.

![Búsqueda de registros para contenedores](./media/log-analytics-containers/containers-log-search.png)

Desde aquí, puede editar la consulta de búsqueda para modificarla y encontrar la información específica que le interesa. Para más información acerca del uso de la búsqueda de registros, consulte [Búsquedas de registros en Log Analytics](log-analytics-log-searches.md).

Por ejemplo, puede modificar la consulta de búsqueda para que muestre todos los contenedores detenidos en lugar de los contenedores en ejecución; para ello, cambie **En ejecución** por **Detenido** en la consulta de búsqueda.

## <a name="troubleshoot-by-finding-a-failed-container"></a>Solución de problemas mediante la búsqueda de un contenedor con error
OMS marca un contenedor como **Failed** (Error) si ha terminado con un código de salida distinto de cero. Puede ver un resumen de los errores del entorno en la hoja **Contenedores con errores**.

### <a name="to-find-failed-containers"></a>Para buscar contenedores con errores
1. Haga clic en la hoja **Eventos de contenedor**.  
   ![eventos de contenedor](./media/log-analytics-containers/containers-events.png)
2. Se abre Búsqueda de registros y muestra el estado de los contenedores, de forma similar a la siguiente.  
   ![estado de los contenedores](./media/log-analytics-containers/containers-container-state.png)
3. A continuación, haga clic en el valor de error para ver información adicional, como el tamaño de la imagen y el número de imágenes detenidas y con error. Expanda **mostrar más** para ver el identificador de la imagen.  
   ![contenedores con errores](./media/log-analytics-containers/containers-state-failed.png)
4. Después, busque el contenedor que está ejecutando esta imagen. Escriba lo siguiente en la consulta de búsqueda.
   `Type=ContainerInventory <ImageID>` Muestra los registros. Puede desplazarse para ver el contenedor con error.  
   ![contenedores con errores](./media/log-analytics-containers/containers-failed04.png)

## <a name="search-logs-for-container-data"></a>Registros de búsqueda de datos de contenedor
Cuando está solucionando un error específico, puede resultar de ayuda ver dónde se está produciendo en su entorno. Los siguientes tipos de registro le ayudarán a crear consultas que devuelven la información que desea.

* **ContainerInventory**: use este tipo si desea obtener información sobre la ubicación del contenedor, cuáles son sus nombres y qué imágenes se están ejecutando.
* **ContainerImageInventory**: use este tipo si desea encontrar información organizada por imagen, y para ver información sobre la imagen, por ejemplo, identificadores o tamaños de imagen.
* **ContainerLog**: use este tipo si desea buscar la información de registro de un error específico y sus entradas.
* **ContainerServiceLog**: use este tipo si desea encontrar información de seguimiento de auditoría para el daemon de Docker, tales como los comandos de inicio, detención, eliminación o extracción.

### <a name="to-search-logs-for-container-data"></a>Para buscar registros de datos de contenedor
* Elija una imagen que sabe que ha tenido errores recientemente y busque los registros de errores de ella. Para empezar, busque un nombre de contenedor que esté ejecutando esa imagen con una búsqueda **ContainerInventory**. Por ejemplo, busque `Type=ContainerInventory ubuntu Failed`.  
    ![Búsqueda de contenedores de Ubuntu](./media/log-analytics-containers/search-ubuntu.png)

  Anote el nombre del contenedor junto a **Nombre** y busque los registros. En este ejemplo, es `Type=ContainerLog adoring_meitner`.

**Ver información de rendimiento**

Si está empezando a crear consultas, puede resultar de ayuda ver antes qué es posible hacer. Por ejemplo, para ver todos los datos de rendimiento, pruebe con una consulta amplia como la siguiente consulta de búsqueda.

```
Type=Perf
```

![rendimiento de contenedores](./media/log-analytics-containers/containers-perf01.png)

Para ver esto de forma más gráfica, haga clic en la palabra **Métricas** en los resultados.

![rendimiento de contenedores](./media/log-analytics-containers/containers-perf02.png)

Para limitar el ámbito de los datos de rendimiento que se muestran a un contenedor específico, escriba el nombre del mismo a la derecha de su consulta.

```
Type=Perf <containerName>
```

Se muestra la lista de las métricas de rendimiento recopiladas para un contenedor individual.

![rendimiento de contenedores](./media/log-analytics-containers/containers-perf03.png)

## <a name="example-log-search-queries"></a>Ejemplos de consultas de búsqueda de registros
Suele resultar útil crear consultas a partir de un ejemplo o dos y modificarlas para adaptarlas a su entorno. Para empezar, puede experimentar con la hoja **Consultas destacadas** para ayudarle a crear consultas más avanzadas.

![Consultas de contenedores](./media/log-analytics-containers/containers-queries.png)

## <a name="saving-log-search-queries"></a>Guardado de consultas de búsqueda de registros
El guardado de consultas es una característica estándar de Log Analytics. Guárdelas para volver a usar en el futuro aquellas que haya encontrado más útiles.

Después de crear una consulta que encuentre útil, guárdela haciendo clic en **Favoritos** en la parte superior de la página Búsqueda de registros. Podrá volver a acceder fácilmente a ella más adelante desde la página **Mi panel**.

## <a name="next-steps"></a>Pasos siguientes
* [Búsqueda de registros](log-analytics-log-searches.md), para ver los registros de datos detallados de los contenedores.



<!--HONumber=Nov16_HO5-->



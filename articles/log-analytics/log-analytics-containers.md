---
title: "Solución Containers en Azure Log Analytics | Microsoft Docs"
description: "La solución Containers en Log Analytics le ayuda a ver y administrar los hosts de contenedores de Docker y Windows en una sola ubicación."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: e1e4b52b-92d5-4bfa-8a09-ff8c6b5a9f78
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 45b07f5dd29b7e920ac2abe6765020e0337ecf95
ms.lasthandoff: 03/11/2017


---
# <a name="containers-preview-solution-log-analytics"></a>Solución Containers (versión preliminar) en Log Analytics
En este artículo se describe cómo configurar y usar la solución Containers de Log Analytics, que le permite ver y administrar los hosts de contenedores de Docker y Windows en una sola ubicación. Docker es un sistema de virtualización de software que usa para crear contenedores que automatizan la implementación de software en su infraestructura de TI.

Con la solución, puede ver qué contenedores se ejecutan en sus hosts de contenedores y qué imágenes se ejecutan en los contenedores. Puede ver información de auditoría detallada que muestra los comandos que se usan con los contenedores. Y, para solucionar los problemas de los contenedores, puede ver y buscar registros centralizados sin tener que ver los hosts de Docker o Windows de forma remota. Puede encontrar los contenedores que están causando ruido o realizando un consumo excesivo de recursos en un host. También puede ver la información centralizada acerca de la CPU, la memoria, el almacenamiento y el uso y el rendimiento de la red en relación con los contenedores. En equipos con Windows, puede centralizar y comparar registros de Windows Server, Hyper-V y contenedores de Docker.

El siguiente diagrama muestra las relaciones entre varios hosts de contenedores y los agentes con OMS.

![Diagrama de contenedores](./media/log-analytics-containers/containers-diagram.png)

## <a name="installing-and-configuring-the-solution"></a>Instalación y configuración de la solución
Utilice la siguiente información para instalar y configurar la solución.

Agregue la solución Containers (contenedores) al área de trabajo de OMS desde [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) o mediante el proceso descrito en el artículo sobre [incorporación de soluciones de Log Analytics desde la Galería de soluciones](log-analytics-add-solutions.md).

Hay varias maneras de instalar y usar Docker con OMS:

* En sistemas operativos compatibles con Linux, instale y ejecute Docker y, después, instale y configure el agente de OMS para Linux.
* En CoreOS, no se puede ejecutar el agente de OMS para Linux. En su lugar, ejecute una versión en contenedores del agente de OMS para Linux.
* En Windows Server 2016 y Windows 10, instale el cliente y el motor de Docker y luego conecte un agente para recopilar información y enviarla a Log Analytics.


Puede revisar las versiones del sistema operativo Linux y de Docker compatibles con su host de contenedores en [GitHub](https://github.com/Microsoft/OMS-docker).

Revise el artículo [Motor de Docker en Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon) para obtener información adicional sobre cómo instalar y configurar los motores de Docker en equipos con Windows.

> [!IMPORTANT]
> Docker se debe estar ejecutando **antes de** instalar el [agente de OMS para Linux](log-analytics-linux-agents.md) en los hosts de contenedores. Si ya ha instalado el agente antes de instalar Docker, debe volver a instalar el agente de OMS para Linux. Para más información acerca de Docker, consulte el [sitio web de Docker](https://www.docker.com).
>
>

Necesita configurar las siguientes opciones en sus hosts de contenedores para poder supervisar los contenedores.

## <a name="configure-settings-for-a-linux-container-host"></a>Definición de la configuración para un host de contenedores de Linux

Las siguientes distribuciones x64 de Linux se admiten como hosts de contenedor:

- Ubuntu 14.04 LTS, 16.04 LTS
- CoreOS(stable)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- CentOS 7
- SLES 12
- RHEL 7.2

Después de instalar Docker, use la siguientes opciones para el host de contenedores para configurar el agente que se usará con Docker. Necesitará [identificador y la clave de su área de trabajo de OMS](log-analytics-linux-agents.md).


### <a name="for-all-linux-container-hosts-except-coreos"></a>Para todos los hosts de contenedores de Linux excepto CoreOS

- Siga las instrucciones en [Steps to install the OMS Agent for Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md)(Pasos para instalar el agente de OMS para Linux).

### <a name="for-all-linux-container-hosts-including-coreos"></a>Para todos los hosts de contenedores de Linux incluido CoreOS

Inicie el contenedor de OMS que desea supervisar. Modifique y use el ejemplo siguiente.

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

### <a name="switching-from-using-an-installed-linux-agent-to-one-in-a-container"></a>Cambio de un agente de Linux instalado a un agente en un contenedor
Si antes usaba el agente instalado directamente y ahora desea usar un agente que se ejecuta en un contenedor, primero debe quitar OMSAgent. Consulte los [pasos para instalar el agente de OMS para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md).

## <a name="supported-windows-versions"></a>Versiones de Windows admitidas

- Windows Server 2016
- Windows 10 Anniversary Edition (Professional o Enterprise)

### <a name="docker-versions-supported-on-windows"></a>Versiones de docker admitidas en Windows

- Docker 1.12 - 1.13

### <a name="preparation-before-installing-agents"></a>Preparación antes de instalar agentes

Antes de instalar agentes en equipos con Windows, debe configurar el servicio Docker. La configuración permite que el agente de Windows o la extensión de máquina virtual de Log Analytics utilice el socket TCP de Docker para que los agentes puedan acceder al demonio de Docker de forma remota y capturar datos para supervisión.

Los datos de rendimiento no se admiten en equipos con Windows.

Para más información sobre cómo configurar el demonio de Docker con Windows, consulte [Motor de Docker en Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon).

#### <a name="to-start-docker-and-verify-its-configuration"></a>Para iniciar Docker y comprobar su configuración

1.    En Windows PowerShell, habilite la canalización TCP y la canalización con nombre.

    ```
    Stop-Service docker
    dockerd --unregister-service
    dockerd -H npipe:// -H 0.0.0.0:2375 --register-service
    Start-Service docker
    ```

2.    Compruebe la configuración con netstat. Debería ver el puerto 2375.

    ```
    PS C:\Users\User1> netstat -a | sls 2375

    TCP    127.0.0.1:2375         Win2016TP5:0           LISTENING
    TCP    127.0.0.1:2375         Win2016TP5:49705       ESTABLISHED
    TCP    127.0.0.1:2375         Win2016TP5:49706       ESTABLISHED
    TCP    127.0.0.1:2375         Win2016TP5:49707       ESTABLISHED
    TCP    127.0.0.1:2375         Win2016TP5:49708       ESTABLISHED
    TCP    127.0.0.1:49705        Win2016TP5:2375        ESTABLISHED
    TCP    127.0.0.1:49706        Win2016TP5:2375        ESTABLISHED
    TCP    127.0.0.1:49707        Win2016TP5:2375        ESTABLISHED
    TCP    127.0.0.1:49708        Win2016TP5:2375        ESTABLISHED
    ```

### <a name="install-windows-agents"></a>Instalación de agentes de Windows

Para habilitar la supervisión de contenedores de Hyper-V y Windows, instale agentes en equipos Windows que sean hosts de contenedores. Para equipos con Windows en su entorno local, consulte [Conexión de equipos Windows a Log Analytics](log-analytics-windows-agents.md). Para máquinas virtuales que se ejecutan en Azure, conéctelas a Log Analytics utilizando la [extensión de máquina virtual](log-analytics-azure-vm-extension.md).

Para comprobar que la solución Containers está establecida correctamente:

- Compruebe si el módulo de administración no se descargó correctamente, busque *ContainerManagement.xxx*.
    - Los archivos deben estar en la carpeta C:\Archivos de programa\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs.
- Compruebe que el identificador de área de trabajo de OMS es correcto. Para ello, vaya a **Panel de control** > **Sistema y seguridad**.
    - Abra **Microsoft Monitoring Agent** y compruebe que la información del área de trabajo es correcta.


## <a name="containers-data-collection-details"></a>Información detallada sobre la recopilación de datos en contenedores
La solución Containers recopila diversos datos de registro y métricas de rendimiento de los hosts de contenedores y de los contenedores mediante agentes que el usuario habilita.

La siguiente tabla muestra los métodos de recopilación de datos y otros detalles sobre cómo se recopilan los datos para Containers.

| plataforma | [Agente de OMS para Linux](log-analytics-linux-agents.md) | Agente de SCOM | Almacenamiento de Azure | ¿Se necesita SCOM? | Datos del agente de SCOM enviados a través del grupo de administración | Frecuencia de recopilación |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |![Sí](./media/log-analytics-containers/oms-bullet-green.png) |![No](./media/log-analytics-containers/oms-bullet-red.png) |![No](./media/log-analytics-containers/oms-bullet-red.png) |![No](./media/log-analytics-containers/oms-bullet-red.png) |![No](./media/log-analytics-containers/oms-bullet-red.png) |Cada 3 minutos |

| plataforma | [Agente de Windows](log-analytics-windows-agents.md) | Agente de SCOM | Almacenamiento de Azure | ¿Se necesita SCOM? | Datos del agente de SCOM enviados a través del grupo de administración | Frecuencia de recopilación |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |![Sí](./media/log-analytics-containers/oms-bullet-green.png) |![No](./media/log-analytics-containers/oms-bullet-red.png) |![No](./media/log-analytics-containers/oms-bullet-red.png) |![No](./media/log-analytics-containers/oms-bullet-red.png) |![No](./media/log-analytics-containers/oms-bullet-red.png) |Cada 3 minutos |

| plataforma | [Extensión de VM de Log Analytics](log-analytics-azure-vm-extension.md) | Agente de SCOM | Almacenamiento de Azure | ¿Se necesita SCOM? | Datos del agente de SCOM enviados a través del grupo de administración | Frecuencia de recopilación |
| --- | --- | --- | --- | --- | --- | --- |
| Las tablas de Azure |![Sí](./media/log-analytics-containers/oms-bullet-green.png) |![No](./media/log-analytics-containers/oms-bullet-red.png) |![No](./media/log-analytics-containers/oms-bullet-red.png) |![No](./media/log-analytics-containers/oms-bullet-red.png) |![No](./media/log-analytics-containers/oms-bullet-red.png) |Cada 3 minutos |

En la tabla siguiente se muestran ejemplos de tipos de datos recopilados por la solución Containers y los tipos de datos que se usan en las búsquedas de registros y los resultados. Sin embargo, los datos de rendimiento no se admiten en equipos con Windows.

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


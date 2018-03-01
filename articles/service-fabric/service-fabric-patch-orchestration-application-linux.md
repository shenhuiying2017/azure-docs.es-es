---
title: "Aplicación de orquestación de revisiones de Azure Service Fabric para Linux| Microsoft Docs"
description: "Aplicación para automatizar la aplicación de revisiones de sistema operativo en un clúster Linux de Service Fabric."
services: service-fabric
documentationcenter: .net
author: novino
manager: timlt
editor: 
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/22/2018
ms.author: nachandr
ms.openlocfilehash: dac8068705e284b04d84d128eb1ce62c459d44ff
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="patch-the-linux-operating-system-in-your-service-fabric-cluster"></a>Revisión del sistema operativo Linux en el clúster de Service Fabric

> [!div class="op_single_selector"]
> * [Windows](service-fabric-patch-orchestration-application.md)
> * [Linux](service-fabric-patch-orchestration-application-linux.md)
>
>

La aplicación de orquestación de revisiones es una aplicación de Azure Service Fabric que automatiza la aplicación de revisiones de sistema operativo en un clúster de Service Fabric sin tiempo de inactividad.

La aplicación de orquestación de revisiones ofrece las siguientes características:

- **Instalación automática de actualizaciones de sistema operativo**. Las actualizaciones de sistema operativo se descargan e instalan automáticamente. Los nodos de clúster se reinician según sea necesario sin tiempo de inactividad del clúster.

- **Integración de la aplicación de revisiones y el estado del clúster**. Al aplicar las actualizaciones, la aplicación de orquestación de revisiones supervisa el estado de los nodos de clúster. Los nodos de clúster se actualizan de uno en uno o por dominio de actualización. Si el estado del clúster deja de funcionar debido al proceso de aplicación de revisiones, la aplicación de revisiones se detiene para evitar agravar el problema.

## <a name="internal-details-of-the-app"></a>Detalles internos de la aplicación

La aplicación de orquestación de revisiones consta de los siguientes subcomponentes:

- **Coordinator Service**: este servicio con estado es responsable de:
    - Coordinar el trabajo de actualización del sistema operativo en todo el clúster.
    - Almacenar el resultado de las operaciones de actualización del sistema operativo finalizadas.
- **Node Agent Service**: es un servicio sin estado que se ejecuta en todos los nodos del clúster de Service Fabric. El servicio es responsable de:
    - Iniciar el demonio del agente de nodo en Linux.
    - Supervisar el servicio del demonio.
- **Demonio del agente de nodo**: este servicio del demonio de Linux se ejecuta con un privilegio de nivel superior (raíz). En cambio, el servicio Node Agent Service y el servicio Coordinator Service se ejecutan con privilegios de nivel inferior. El servicio es responsable de realizar las siguientes tareas de actualización en todos los nodos de clúster:
    - Deshabilitar las actualizaciones automáticas del sistema operativo en el nodo.
    - Descargar e instalar la actualización del sistema operativo según la directiva que el usuario ha proporcionado.
    - Reiniciar el equipo después de la instalación de la actualización del sistema operativo en caso de ser necesario.
    - Realizar la carga de los resultados de las actualizaciones del sistema operativo al servicio Coordinator Service.
    - Realizar un informe de estado en caso de error en la operación después de agotar todos los reintentos.

> [!NOTE]
> La aplicación de orquestación de revisiones usa el servicio de sistema de administrador de reparaciones de Service Fabric para habilitar o deshabilitar el nodo, y llevar a cabo comprobaciones de estado. La tarea de reparación creada por la aplicación de orquestación de revisiones sigue el progreso de actualización en cada nodo.

## <a name="prerequisites"></a>requisitos previos

### <a name="ensure-that-your-azure-vms-are-running-ubuntu-1604"></a>Asegúrese de que las máquinas virtuales de Azure ejecutan Ubuntu 16.04
En el momento de escribir este documento, Ubuntu 16.04 (`Xenial Xerus`) es la única versión admitida.

### <a name="ensure-that-the-service-fabric-linux-cluster-is-version-61x-and-above"></a>Asegúrese de que el clúster Linux de Service Fabric tiene la versión 6.1. x, y las versiones posteriores

La aplicación de orquestación de revisiones para Linux utiliza determinadas características del runtime que solo están disponibles en la versión del runtime de Service Fabric 6.1. x, y las versiones posteriores.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Habilitar el servicio de administrador de reparaciones (si no se está ejecutando ya)

La aplicación de orquestación de revisiones requiere que el servicio del sistema de administrador de reparaciones esté habilitado en el clúster.

#### <a name="azure-clusters"></a>Clústeres de Azure

Los clústeres Linux de Azure en el nivel de durabilidad Plata y Oro tienen habilitado el servicio del administrador de reparaciones de forma predeterminada. Los clústeres de Azure en el plan de durabilidad Bronce no tienen habilitado el servicio de administrador de reparaciones. Si el servicio ya está habilitado, puede ver que se ejecuta en la sección de servicios del sistema en Service Fabric Explorer.

##### <a name="azure-portal"></a>Azure Portal
Puede habilitar el administrador de reparaciones desde Azure Portal en el momento de la configuración del clúster. Seleccione la opción **Incluir administrador de reparaciones** en **Características complementarias** en el momento de la configuración del clúster.
![Imagen de la habilitación del administrador de reparaciones de Azure Portal](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-deployment-model"></a>modelo de implementación de Azure Resource Manager
También puede usar el [modelo de implementación de Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) para habilitar el servicio del administrador de reparaciones en clústeres de Service Fabric nuevos y existentes. Obtenga la plantilla del clúster que desea implementar. Puede usar las plantillas de ejemplo o crear una plantilla del modelo de implementación de Azure Resource Manager personalizada. 

Para habilitar el servicio del administrador de reparaciones mediante la [plantilla del modelo de implementación de Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm):

1. En primer lugar, compruebe que `apiversion` está establecido en `2017-07-01-preview` para el recurso `Microsoft.ServiceFabric/clusters`. Si es diferente, debe actualizar `apiVersion` al valor `2017-07-01-preview` o superior:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Ahora, habilite el servicio de administrador de reparaciones agregando la sección `addonFeatures` siguiente después de la sección `fabricSettings`:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Una vez actualizada la plantilla del clúster con estos cambios, aplíquelos para completar la actualización. Ahora puede ver el servicio del sistema de administrador de reparaciones en ejecución en el clúster. Se denomina `fabric:/System/RepairManagerService` en la sección de servicios del sistema en Service Fabric Explorer. 

### <a name="standalone-on-premises-clusters"></a>Clústeres locales independientes

No se admiten clústeres Linux de Service Fabric independientes en el momento de escribir este documento.

### <a name="disable-automatic-os-update-on-all-nodes"></a>Deshabilitación de las actualizaciones automáticas del sistema operativo en todos los nodos

Las actualizaciones automáticas del sistema operativo pueden provocar la pérdida de disponibilidad y un cambio en el comportamiento de las aplicaciones en ejecución. La aplicación de orquestación de revisiones intenta deshabilitar las actualizaciones automáticas del sistema operativo en cada nodo del clúster de forma predeterminada para prevenir esos escenarios.
En Ubuntu, la aplicación de orquestación de revisiones deshabilita las [actualizaciones desatendidas](https://help.ubuntu.com/community/AutomaticSecurityUpdates).

## <a name="download-the-app-package"></a>Descargar el paquete de la aplicación

Descargue la aplicación desde el [enlace de descarga](https://go.microsoft.com/fwlink/?linkid=867984).

## <a name="configure-the-app"></a>Configuración de la aplicación

El comportamiento de la aplicación de orquestación de revisiones puede configurarse según sus necesidades. Invalide los valores predeterminados al pasar el parámetro de la aplicación durante la creación o actualización de la aplicación. Los parámetros de la aplicación se pueden proporcionar especificando `ApplicationParameter` en los cmdlets `Start-ServiceFabricApplicationUpgrade` o `New-ServiceFabricApplication`.

|**Parámetro**        |**Tipo**                          | **Detalles**|
|:-|-|-|
|MaxResultsToCache    |long                              | Número máximo de resultados de actualización que deben almacenarse en la memoria caché. <br>El valor predeterminado es 3000 suponiendo que: <br> - El número de nodos es 20. <br> - El número de actualizaciones en un nodo al mes es cinco. <br> - El número de resultados por cada operación es 10. <br> - Deben almacenarse los resultados de los últimos tres meses. |
|TaskApprovalPolicy   |Enum <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy indica la directiva que usará Coordinator Service para instalar las actualizaciones en todos los nodos del clúster de Service Fabric.<br>                         Los valores permitidos son: <br>                                                           <b>NodeWise</b>. Las actualizaciones se instalan en un nodo cada vez. <br>                                                           <b>UpgradeDomainWise</b>. Las actualizaciones se instalan en un dominio de actualización cada vez. (Como máximo, todos los nodos que pertenecen a un dominio de actualización son aptos para la actualización).
| UpdateOperationTimeOutInMinutes | int <br>(Predeterminado: 180)                   | Especifica el tiempo de espera para cualquier operación de actualización (descargar o instalar). Si la operación no se realiza en el tiempo de espera especificado, se anula.       |
| RescheduleCount      | int <br> (Valor predeterminado: 5)                  | El número máximo de veces que el servicio vuelve a programar la actualización del sistema operativo en caso de error de la operación de forma persistente.          |
| RescheduleTimeInMinutes  | int <br>(Valor predeterminado: 30) | El intervalo en el que el servicio vuelve a programar la actualización del sistema operativo en caso de que el error persista. |
| UpdateFrequency           | Cadena separada por comas (Valor predeterminado: "Weekly, Wednesday, 7:00:00")     | La frecuencia para la instalación de actualizaciones del sistema operativo en el clúster. El formato y los valores posibles son: <br>- Monthly, DD,HH:MM:SS, por ejemplo, Monthly, 5, 12:22:32. <br> - Weekly, DÍA,HH:MM:SS, por ejemplo, Weekly, martes, 12:22:32.  <br> -   Daily, HH:MM:SS, por ejemplo, Daily, 12:22:32.  <br> - None indica que no debe realizarse la actualización.  <br><br> Todas las horas están en formato UTC.|
| UpdateClassification | Cadena separada por comas (Valor predeterminado: "securityupdates") | Tipo de actualizaciones que se deben instalar en los nodos del clúster. Los valores aceptados son securityupdates y all. <br> - securityupdates: se instalan solo las actualizaciones de seguridad <br> - all: se instalan todas las actualizaciones disponibles en apt.|
| ApprovedPatches | Cadena separada por comas (valor predeterminado: "") | Se trata de la lista de actualizaciones aprobadas que debe instalarse en los nodos del clúster. La lista separada por comas contiene los paquetes aprobados y, de modo opcional, la versión de destino deseada.<br> Por ejemplo: "apt-utils = 1.2.10ubuntu1, python3-jwt, apt-transport-https < 1.2.194, libsystemd0 >= 229-4ubuntu16" <br> Los pasos anteriores instalan <br> - apt-utils con la versión 1.2.10ubuntu1 si está disponible en apt-cache. Si esa versión no está disponible, es una operación sin efecto. <br> - las actualizaciones de python3-jwt a la versión más reciente disponible. Si el paquete no está presente, es una operación sin efecto. <br> - las actualizaciones de apt-transport-https a la versión más reciente y que es menor que la 1.2.194. Si la versión no está presente, es una operación sin efecto. <br> - las actualizaciones de libsystemd0 a la versión más reciente que sea mayor o igual que la 229-4ubuntu16. Si no existe una versión de este tipo, es una operación sin efecto.|
| RejectedPatches | Cadena separada por comas (valor predeterminado: "") | Se trata de la lista de actualizaciones que no se deben instalar en los nodos del clúster <br> Por ejemplo: "bash, sudo" <br> El valor anterior filtra bash y sudo para que no reciban actualizaciones. |


> [!TIP]
> Si quiere que la actualización del sistema operativo se ejecute de forma inmediata, establezca `UpdateFrequency` en relación con la hora de implementación de la aplicación. Por ejemplo, suponga que tiene un clúster de prueba de cinco nodos y planea implementar la aplicación aproximadamente a las 17:00 UTC. Si se asume que la implementación o actualización de la aplicación tarda 30 minutos como máximo, establezca UpdateFrequency en "Daily, 17:30:00".

## <a name="deploy-the-app"></a>Implementar la aplicación

1. Finalice todos los pasos de requisitos previos para preparar el clúster.
2. Implemente la aplicación de orquestación de revisiones como cualquier otra aplicación de Service Fabric. Puede implementar la aplicación mediante PowerShell o mediante la CLI de Azure Service Fabric. Siga los pasos de [Implementación y eliminación de aplicaciones con PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications) o [Implementación de una aplicación mediante la CLI de Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/scripts/cli-deploy-application)
3. Para configurar la aplicación en el momento de la implementación, pase el parámetro `ApplicationParamater` al cmdlet `New-ServiceFabricApplication` o a los scripts proporcionados. Para su comodidad, junto con la aplicación se proporcionan los scripts de PowerShell (Deploy.ps1) y bash (Deploy.sh). Para usar el script:

    - Conéctese a un clúster de Service Fabric.
    - Ejecute el script de implementación. De modo opcional, puede pasar el parámetro de la aplicación al script. Por ejemplo: .\Deploy.ps1 -ApplicationParameter @{ UpdateFrequency = "Daily, 11:00:00"} o bien ./Deploy.sh "{\"UpdateFrequency\":\"Daily, 11:00:00\"}" 

> [!NOTE]
> Mantenga el script y la carpeta PatchOrchestrationApplication de la aplicación en el mismo directorio.

## <a name="upgrade-the-app"></a>Actualizar la aplicación

Para actualizar una aplicación de orquestación de revisiones existente, siga los pasos descritos en [Actualización de aplicaciones de Service Fabric con PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell) o [Actualización de una aplicación de Service Fabric con la CLI de Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-upgrade)

## <a name="remove-the-app"></a>Quitar la aplicación

Para eliminar la aplicación, siga los pasos de [Implementación y eliminación de aplicaciones con PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications) o [Eliminación de una aplicación mediante la CLI de Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-delete)

Para su comodidad, junto con la aplicación se proporcionan los scripts de PowerShell (Undeploy.ps1) y bash (Undeploy.sh). Para usar el script:

  - Conéctese a un clúster de Service Fabric.
  - Ejecución del script Undeploy.ps1 o Undeploy.sh

> [!NOTE]
> Mantenga el script y la carpeta PatchOrchestrationApplication de la aplicación en el mismo directorio.

## <a name="view-the-update-results"></a>Visualización de los resultados de la actualización

La aplicación de orquestación de revisiones expone API de REST para mostrar los resultados históricos al usuario. A continuación se muestra un resultado de ejemplo: ```testadm@bronze000001:~$ curl -X GET http://10.0.0.5:20002/PatchOrchestrationApplication/v1/GetResults```
```json
[ 
  { 
    "NodeName": "_bronze_0", 
    "UpdateOperationResults": [ 
      { 
        "OperationResult": "succeeded", 
        "NodeName": "_bronze_0", 
        "OperationTime": "2017-11-21T12:39:29.0435917Z", 
        "UpdateDetails": [ 
          { 
            "UpdateId": "linux-cloud-tools-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-headers-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-image-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-tools-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "python3-apport:amd64=2.20.1-0ubuntu2.13", 
            "ResultCode": "succeeded" 
          }, 
        ], 
        "OperationType": "installation", 
        "UpdateClassification": "securityupdates", 
        "UpdateFrequency": "Daily, 7:00:00", 
        "RebootRequired": true, 
        "ApprovedList": "", 
        "RejectedList": "" 
      } 
    ] 
  } 
] 
```

Los campos del archivo JSON se describen a continuación:

Campo | Valores | Detalles
-- | -- | --
OperationResult | 0: se realizó correctamente<br> 1: se realizó correctamente con errores<br> 2: con error<br> 3: anulada<br> 4: anulada con tiempo de espera | Indica el resultado de la operación global (que normalmente implica la instalación de una o más actualizaciones).
ResultCode | Igual que OperationResult | Este campo indica el resultado de la operación de instalación para una actualización individual.
OperationType | 1: instalación<br> 0: buscar y descargar.| La instalación es el único valor de OperationType que se muestra en los resultados de forma predeterminada.
UpdateClassification | El valor predeterminado es "securityupdates" | Tipo de actualizaciones que se instalan durante la operación de actualización
UpdateFrequency | El valor predeterminado es "Weekly, Wednesday, 7:00:00" ("Semanalmente, miércoles, 7:00:00") | Frecuencia de actualización configurada para esta actualización.
RebootRequired | true: se requiere reinicio<br> false: no se requiere reinicio | Indica si se requiere reiniciar para completar la instalación de actualizaciones.
ApprovedList | El valor predeterminado es "" | Lista de las revisiones aprobadas para esta actualización
RejectedList | El valor predeterminado es "" | Lista de las revisiones rechazadas para esta actualización

Si todavía no hay ninguna actualización programada, el resultado JSON está vacío.

Inicie sesión en el clúster para consultar los resultados de la actualización. Después, obtenga la dirección de la réplica principal del servicio Coordinator Service y vaya a la dirección URL desde el explorador: http://&lt;IP DE RÉPLICA&gt;:&lt;PuertoDeAplicación&gt;/PatchOrchestrationApplication/v1/GetResults.

El punto de conexión REST para el servicio Coordinator Service tiene un puerto dinámico. Para comprobar la dirección URL exacta, consulte Service Fabric Explorer. Por ejemplo, los resultados están disponibles en `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetResults`.

![Imagen de punto de conexión REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

## <a name="diagnosticshealth-events"></a>Eventos de diagnóstico y mantenimiento

### <a name="diagnostic-logs"></a>Registros de diagnóstico

Los registros de aplicación de orquestación de revisiones se recopilan como parte de los registros en tiempo de ejecución de Service Fabric.

En caso de que desee capturar los registros a través de la canalización o herramienta de diagnóstico de su elección. La aplicación de orquestación de revisiones usa los siguientes identificadores de proveedor fijos para registrar eventos a través de [eventsource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netstandard-2.0)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Informes de mantenimiento

La aplicación de orquestación de revisiones también publica los informes de estado en el servicio Coordinator Service o el servicio Node Agent Service en los siguientes casos:

#### <a name="an-update-operation-failed"></a>Una operación de actualización no se pudo realizar

Si se produce un error en la operación de actualización en un nodo, se genera un informe de mantenimiento en el servicio Node Agent Service. Los detalles del informe de estado contienen el nombre del nodo problemático.

Después de aplicar correctamente la revisión en el nodo problemático, el informe se borra automáticamente.

#### <a name="the-node-agent-daemon-service-is-down"></a>El servicio Node Agent Daemon está inactivo

Si el servicio Node Agent Daemon está inactivo en un nodo, se genera un informe de mantenimiento de nivel de advertencia en el servicio Node Agent Service.

#### <a name="the-repair-manager-service-is-not-enabled"></a>El servicio de administrador de reparaciones no está habilitado

Si el servicio de administrador de reparaciones no se encuentra en el clúster, se genera un informe de mantenimiento de nivel de advertencia para el servicio Coordinator Service.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

P: **¿Por qué veo el clúster en estado de error cuando se ejecuta la aplicación de orquestación de revisiones?**

A. Durante el proceso de instalación, la aplicación de orquestación de revisiones deshabilita o reinicia los nodos. Esta operación puede producir temporalmente que el estado de mantenimiento del clúster deje de estar activo.

En función de la directiva de la aplicación, un nodo puede dejar de estar activo durante una operación de aplicación de revisiones, *o bien* un dominio de actualización completo puede dejar de estar activo.

Al final de la instalación, los nodos se vuelven a habilitar después del reinicio.

En el ejemplo siguiente, el clúster entró en estado de error temporalmente porque dos nodos dejaron de estar activos y se infringió la directiva MaxPercentageUnhealthyNodes. El error es temporal hasta que la operación de aplicación de revisiones finalice.

![Imagen de clúster en mal estado](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Si el problema persiste, consulte la sección de solución de problemas.

P: **La aplicación de orquestación de revisiones está en estado de advertencia**

A. Compruebe si un informe de estado publicado a la aplicación es la causa principal. Normalmente la advertencia contiene los detalles del problema. Si el problema es transitorio, se espera que la aplicación se recupere automáticamente de este estado.

P: **¿Qué puedo hacer si el clúster está en mal estado y tengo que realizar una actualización urgente del sistema operativo?**

A. La aplicación de orquestación de revisiones no instala actualizaciones cuando el clúster está en mal estado. Intente devolver el clúster a un estado correcto para desbloquear el flujo de trabajo de la aplicación de orquestación de revisiones.

P: **¿Por qué la aplicación de revisiones en el clúster tarda tanto tiempo en ejecutarse?**

A. El tiempo que necesita la aplicación de orquestación de revisiones depende principalmente de los factores siguientes:

- La directiva del servicio Coordinator Service. 
  - La directiva predeterminada, `NodeWise`, da como resultado que solo se apliquen las revisiones a un nodo a la vez. Especialmente en el caso de los clústeres más grandes, se recomienda usar la directiva `UpgradeDomainWise` para aplicar las revisiones en los clústeres más rápidamente.
- El número de actualizaciones disponibles para su descarga e instalación. 
- El tiempo promedio necesario para descargar e instalar una actualización, que no debe superar un par de horas.
- El rendimiento de la máquina virtual y ancho de banda de la red.

P: **¿Cómo decide la aplicación de orquestación de revisiones qué actualizaciones son actualizaciones de seguridad?**

A. La aplicación de orquestación de revisiones utiliza una lógica específica de cada distribución para determinar qué actualizaciones entre las actualizaciones disponibles son actualizaciones de seguridad. Por ejemplo: en ubuntu la aplicación busca actualizaciones de los archivos $RELEASE-security y $RELEASE-updates ($RELEASE = xenial o la versión de lanzamiento de base estándar de Linux). 

 
P: **¿Cómo puedo bloquear en una versión específica del paquete?**

A. Use la configuración de ApprovedPatches para bloquear los paquetes en una versión determinada. 


P: **¿Qué ocurre con las actualizaciones automáticas habilitadas en Ubuntu?**

A. Tan pronto como instale la aplicación de orquestación de revisiones en el clúster, se deshabilitan las actualizaciones desatendidas en el nodo del clúster. Todo el flujo de trabajo de actualizaciones periódicas está determinado por la aplicación de orquestación de revisiones.
Para la coherencia de entorno en el clúster, se recomienda instalar las actualizaciones únicamente a través de la aplicación de orquestación de revisiones. 
 
P: **¿La aplicación de orquestación de revisiones realiza la limpieza de los paquetes no utilizados después de la actualización?**

A. Sí, la limpieza se produce como parte de los pasos posteriores a la instalación. 

## <a name="troubleshooting"></a>solución de problemas

### <a name="a-node-is-not-coming-back-to-up-state"></a>El nodo no vuelve a su estado activo

**Es posible que el nodo esté bloqueado en un estado deshabilitado porque**:

Hay una comprobación de seguridad pendiente. Para solucionar este problema, asegúrese de que hay suficientes nodos disponibles en estado correcto.

**Es posible que el nodo esté bloqueado en un estado deshabilitado porque**:

- El nodo se ha deshabilitado manualmente.
- El nodo se ha deshabilitado debido a un trabajo de infraestructura de Azure en curso.
- El nodo ha sido deshabilitado temporalmente por la aplicación de orquestación de revisiones para aplicar revisiones en el nodo.

**Es posible que el nodo esté bloqueado en un estado inactivo porque**:

- El nodo se ha desactivado manualmente.
- Se está llevando a cabo un reinicio en el nodo (que es posible que haya iniciado la aplicación de orquestación de revisiones).
- El nodo está inactivo por un problema en la máquina virtual o por problemas de conectividad de red.

### <a name="updates-were-skipped-on-some-nodes"></a>Las actualizaciones se omitieron en algunos nodos

La aplicación de orquestación de revisiones intenta instalar una actualización según la directiva de reprogramación. El servicio puede intentar recuperar el nodo y omitir la actualización en función de la directiva de la aplicación.

En ese caso, se genera un informe de estado de nivel de advertencia en el servicio Node Agent Service. El resultado de la actualización también contiene el motivo posible del error.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>El estado del clúster es de error mientras se instala la actualización

Una actualización fallida puede desactivar el mantenimiento de una aplicación o un clúster en un nodo concreto o un dominio de actualización. La aplicación de orquestación de revisiones interrumpe las operaciones posteriores de actualización hasta que el estado del clúster vuelva a ser correcto.

Un administrador debe intervenir y determinar por qué el mantenimiento de la aplicación o el clúster pasó a ser incorrecto debido a una actualización instalada anteriormente.

## <a name="disclaimer"></a>Renuncia de responsabilidades

La aplicación de orquestación de revisiones recopila datos de telemetría para realizar el seguimiento de uso y rendimiento. Los datos de telemetría de la aplicación siguen la configuración de telemetría del runtime de Service Fabric (que se activa de forma predeterminada).

## <a name="release-notes"></a>Notas de la versión

### <a name="version-010"></a>Versión 0.1.0
- Versión preliminar privada

### <a name="version-200-latest"></a>Versión 2.0.0 (más reciente)
- Versión pública

---
title: "Aplicación de orquestación de revisiones de Azure Service Fabric | Microsoft Docs"
description: "Aplicación para automatizar la aplicación de revisiones de sistema operativo en un clúster de Service Fabric."
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
ms.date: 5/9/2017
ms.author: nachandr
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: a3554881e7db894c602e73feb385b5b361837409
ms.contentlocale: es-es
ms.lasthandoff: 05/18/2017


---

# <a name="application-to-patch-windows-os-in-your-service-fabric-cluster"></a>Aplicación para aplicar revisiones del sistema operativo Windows en el clúster de Service Fabric

La aplicación de orquestación de revisiones es una aplicación de Service Fabric que le permite automatizar la aplicación de revisiones de sistema operativo en un clúster de Service Fabric en Azure o local, sin tiempo de inactividad.

La aplicación de orquestación de revisiones incluye las siguientes características:

1. **Instalación automática de actualizaciones de sistema operativo**: la aplicación de orquestación de revisiones garantiza que las actualizaciones se descarguen e instalen automáticamente; el nodo se reinicia si es necesario.
    Esto se realiza en todos los nodos de clúster sin tiempo de inactividad.

1. **Integración de la aplicación de revisiones y el estado del clúster**: la aplicación de orquestación de revisiones, al aplicar las actualizaciones, supervisa el estado del clúster a medida que actualiza un nodo cada vez o un dominio de actualización cada vez. 
    Si en cualquier momento detecta un empeoramiento del estado del clúster debido al proceso de aplicación de revisiones, detiene el proceso para evitar agravar el problema.

1. **Admite todos los clústeres de Service Fabric**: la aplicación es lo suficientemente genérica para trabajar en clústeres de Azure Service Fabric y clústeres independientes.
    > [!NOTE]
    > La compatibilidad con clústeres independientes estará disponible próximamente.

## <a name="link-to-download-the-application-package"></a>Enlace de descarga del paquete de aplicación

Descargue la aplicación desde el [enlace de descarga](https://go.microsoft.com/fwlink/P/?linkid=849590)

## <a name="internal-details-of-the-application"></a>Detalles internos de la aplicación

La aplicación de orquestación de revisiones consta de los siguientes subcomponentes:

- **Coordinator Service**: es un servicio con estado. El servicio es responsable de:
    - Coordinar el trabajo de Windows Update en todo el clúster.
    - Almacenar el resultado de las operaciones completadas de Windows Update.
- **Node Agent Service**: es un servicio sin estado que se ejecuta en todos los nodos del clúster de Service Fabric. El servicio es responsable de:
    - Iniciar el servicio Node Agent NTService.
    - Supervisar el servicio Node Agent NTService.
- **Node Agent NTService**: es un servicio de Windows NT. Node Agent NTService se ejecuta con privilegios elevados (SYSTEM). En cambio, Node Agent Service y Coordinator Service se ejecutan con privilegios de nivel inferior (NETWORK SERVICE). El servicio es responsable de realizar las siguientes tareas de Windows Update en todos los nodos de clúster.
    - Deshabilitar las actualizaciones automáticas de Windows en el nodo.
    - Descarga e instalación de actualizaciones de Windows siguiendo la directiva de usuario.
    - Reiniciar el equipo después de la instalación de actualizaciones de Windows.
    - Realizar la carga del resultado de Windows Update al servicio Coordinator Service.
    - Realizar un informe de estado en caso de error en la operación después de agotar todos los reintentos.

> [!NOTE]
> La aplicación de orquestación de revisiones utiliza el servicio de sistema de Service Fabric **Repair Manager** para habilitar o deshabilitar el nodo y llevar a cabo comprobaciones de estado. La tara de reparación creada por la aplicación de orquestación de revisiones sigue el progreso de Windows Update en cada nodo.

## <a name="prerequisites-for-using-the-application"></a>Requisitos previos para usar la aplicación

### <a name="ensure-service-fabric-version-is-55-or-above"></a>Asegúrese de que la versión de Service Fabric es la 5.5 o posterior

La aplicación de orquestación de revisiones se puede ejecutar en clústeres con la versión de runtime de Service Fabric v5.5 o posterior.

### <a name="enable-repair-manager-service-if-not-running-already"></a>Habilitar el servicio Repair Manager (si no se está ejecutando ya)

La aplicación de orquestación de revisiones requiere que el servicio del sistema Repair Manager esté habilitado en el clúster.

#### <a name="service-fabric-clusters-on-azure"></a>Clústeres de Service Fabric en Azure

Los clústeres de Azure en el nivel de durabilidad Silver tendrán Repair Manager habilitado de forma predeterminada. Los clústeres de Azure en el nivel de durabilidad Gold pueden o no tener habilitado Repair Manager, dependiendo de cuando fueron creados. Los clústeres de Azure en el plan de durabilidad Bronze no tienen habilitado el servicio Repair Manager. 

Puede usar la [plantilla de Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) para habilitar el servicio Repair Manager en clústeres de Service Fabric nuevos o existentes, en caso de que no aparezca en ejecución en la sección de servicios del sistema en el Explorador de Service Fabric.

En primer lugar, se obtiene la plantilla del clúster que desea implementar. Puede usar las plantillas de ejemplo o crear una plantilla personalizada de Resource Manager. Puede habilitar Repair Manager con los siguientes pasos:

1. En primer lugar, compruebe que `apiversion` está establecido en `2017-07-01-preview` para el recurso `Microsoft.ServiceFabric/clusters`, tal y como se muestra en el siguiente fragmento de código. Si es diferente, debe actualizar `apiVersion` al valor `2017-07-01-preview`

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Ahora, habilite el servicio Repair Manager agregando la sección `addonFeaturres` después de la sección `fabricSettings`, tal y como se muestra a continuación.

    ```json
    "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "RepairManager"
        ],
    ```

3. Una vez actualizada la plantilla del clúster con estos cambios, aplíquelos para completar la actualización. Una vez completado, podrá ver el servicio de sistema Repair Manager en ejecución en el clúster, denominado `fabric:/System/RepairManagerService`, en la sección de servicios del sistema en Service Fabric Explorer. 

#### <a name="standalone-on-premise-clusters"></a>Clústeres locales independientes

> [!NOTE]
> La compatibilidad con clústeres independientes estará disponible próximamente

### <a name="disable-automatic-windows-update-on-all-nodes"></a>Deshabilitar las actualizaciones automáticas de Windows en todos los nodos.

Tener habilitada la actualización automática de Windows en un clúster de Service Fabric podría provocar pérdida de disponibilidad, ya que varios nodos podrían reiniciarse a la vez para completar la actualización.

La aplicación de orquestación de revisiones intenta deshabilitar las actualizaciones automáticas de Windows en cada nodo del clúster de forma predeterminada.

Sin embargo, en caso de que la configuración se administre por directivas de administrador o de grupo, se recomienda configurar explícitamente la directiva de actualización de Windows en “Notificar antes de descargar”.


### <a name="optional-enable-windows-azure-diagnostics"></a>Opcional: habilitar Windows Azure Diagnostics

Próximamente los registros de la aplicación de orquestación de revisiones se recopilarán como parte de los propios registros de Service Fabric. Hasta entonces, los registros se recopilan localmente en cada nodo del clúster. Se recomienda configurar Windows Azure Diagnostics (WAD) para que los registros de todos los nodos se carguen en una ubicación central.

Los pasos para habilitar WAD se detallan [aquí.](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-how-to-setup-wad)

Los registros de la aplicación de orquestación de revisiones se generan en los identificadores de proveedor siguientes:

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

Agregue la siguiente sección en la sección "WadCfg" de la plantilla de Azure Resource Manager: 

```json
"PatchOrchestrationApplication": \[
  {
    "provider": "e39b723c-590c-4090-abb0-11e3e6616346",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
      "eventDestination": "PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "fc0028ff-bfdc-499f-80dc-ed922c52c5e9",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "24afa313-0d3b-4c7c-b485-1047fd964b60",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "05dc046c-60e9-4ef7-965e-91660adffa68",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  },
\]
```

> [!NOTE]
> En caso de que el clúster de Service Fabric conste de varios tipos de nodo, la sección anterior debe agregarse en todas las secciones “WadCfg”.

## <a name="configuring-the-application"></a>Configuración de la aplicación

El usuario puede establecer las siguientes configuraciones para ajustar el comportamiento de la aplicación de orquestación de revisiones según sus necesidades.

Se pueden invalidar los valores predeterminados al pasar el parámetro de la aplicación durante la creación o actualización de la aplicación. Los parámetros de la aplicación se pueden proporcionar especificando `ApplicationParameter` en el cmdlet `Start-ServiceFabricApplicationUpgrade` o en el `New-ServiceFabricApplication`

|**Parámetro**        |**Tipo**                          | **Detalles**|
|:-|-|-|
|MaxResultsToCache    |long                              | Número máximo de resultados históricos de Windows Update que deben almacenarse en caché. <br>El valor predeterminado es 3000 suponiendo que: <br> - El número de nodos es 20. <br> - El número de actualizaciones en un nodo al mes es 5. <br> - El número de resultados por cada operación es 10. <br> - Se deben almacenar los resultados de los últimos tres meses. |
|TaskApprovalPolicy   |Enum <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy indica la directiva que usará CoordinatorService para instalar actualizaciones de Windows en todos los nodos del clúster de Service Fabric<br>                         Los valores permitidos son: <br><br>                                                           <b>NodeWise</b> - las actualizaciones de Windows se instalan en un nodo cada vez <br>                                                           <b>UpgradeDomainWise</b> - las actualizaciones de Windows se instalan en un dominio de actualización cada vez (como máximo todos los nodos de un dominio de actualización pueden actualizar Windows)
|LogsDiskQuotaInMB   |long  <br> (Valor predeterminado: 1024)               |Tamaño máximo de los registros de la aplicación de orquestación de revisiones en MB que se pueden almacenar de forma persistente y local en un nodo
| WUQuery               | cadena<br>(Valor predeterminado: "IsInstalled=0")                | Consulta para obtener actualizaciones de Windows; para más información, consulte [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
| InstallWindowsOSOnlyUpdates | Booleano <br> (Valor predeterminado: True)                 | Esta marca permite instalar actualizaciones del sistema operativo Windows.            |
| WUOperationTimeOutInMinutes | int <br>(Valor predeterminado: 90)                   | Especifica el tiempo de espera para cualquier operación de actualización de Windows (buscar / descargar / instalar). Si no se realiza una operación en el tiempo de espera especificado, se anula.       |
| WURescheduleCount     | int <br> (Valor predeterminado: 5)                  | Esta configuración determina el número máximo de veces que el servicio podría volver a programar la actualización de Windows en caso de error de operación de forma persistente          |
| WURescheduleTimeInMinutes | int <br>(Valor predeterminado: 30) | Esta configuración decide el intervalo en el que el servicio debería volver a programar la actualización de Windows en caso de error persiste |
| WUFrequency           | Cadena separada por comas (Valor predeterminado: "Weekly, Wednesday, 7:00:00")     | Frecuencia para la instalación de actualizaciones de Windows. El formato y los valores posibles son los siguientes: <br>- Monthly,DD,HH:MM:SS Ej: Monthly,5,12:22:32 <br> - Weekly,DAY,HH:MM:SS Ej: Weekly, Tuesday, 12:22:32  <br> - Daily, HH:MM:SS Ej: Daily, 12:22:32  <br> - None - indica que no deben realizarse actualizaciones de Windows  <br><br> NOTA: Todas las horas están en UTC|
| AcceptWindowsUpdateEula | Booleano <br>(Valor predeterminado: true) | Al establecer este indicador, la aplicación acepta el CLUF de Windows Update en nombre del propietario de la máquina.              |


## <a name="steps-to-deploy-the-application"></a>Pasos para implementar la aplicación

1. Finalizar todos los pasos de requisitos previos para preparar el clúster.
1. Implementar la aplicación: puede hacerse desde PowerShell utilizando los pasos mencionados [aquí.](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)
1. Para configurar la aplicación en el momento de la implementación use `ApplicationParamater` en el cmdlet `New-ServiceFabricApplication`

    Para facilidad del usuario, se proporciona el script Deploy.ps1 junto con la aplicación. Para utilizarlo:

    - Conéctese al clúster de Service Fabric con `Connect-ServiceFabricCluster`
    - Ejecute el script de PowerShell Deploy.ps1 con el valor adecuado de `ApplicationParameter`

> [!NOTE]
> Mantenga el script y la carpeta PatchOrchestrationApplication de la aplicación en el mismo directorio.

## <a name="steps-to-upgrade-the-application"></a>Pasos para actualizar la aplicación

Para actualizar una aplicación de orquestación de revisiones existente con PowerShell, consulte los pasos para la actualización de la aplicación mencionados [aquí.](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell)

Para cambiar únicamente los parámetros de aplicación de la aplicación, proporcione el valor `ApplicationParamater` al cmdlet `Start-ServiceFabricApplicationUpgrade` con la versión existente de la aplicación.

## <a name="steps-to-remove-the-application"></a>Pasos para eliminar la aplicación

Para eliminar la aplicación, consulte los pasos mencionados [aquí.](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)

Para facilidad del usuario, se proporciona el script Undeploy.ps1 junto con la aplicación. Para utilizarlo:
    - Conéctese al clúster de Service Fabric con ```Connect-ServiceFabricCluster```
    - Ejecute el script de PowerShell Undeploy.ps1

> [!NOTE]
> Mantenga el script y la carpeta PatchOrchestrationApplication de la aplicación en el mismo directorio.

## <a name="viewing-the-windows-update-results"></a>Ver los resultados de Windows Update

La aplicación de orquestación de revisiones expone una API REST para mostrar los resultados históricos al usuario.

Los resultados de Windows Update se pueden consultar iniciando sesión en el clúster, averiguando la dirección de la réplica para la instancia principal de Coordinator Service para usar la dirección URL en el explorador.
http://&lt;REPLICA-IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetWindowsUpdateResults El punto de conexión REST de Coordinator Service tiene puerto dinámico, para comprobar la dirección URL exacta consulte el explorador de Service Fabric.
Ejemplo: en el ejemplo siguiente los resultados estarían disponibles en `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults`
![Imagen de un punto de conexión REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)


Los usuarios tienen acceso también a la dirección URL desde fuera del clúster, en caso de tener habilitado el proxy inverso en el clúster.
Punto de conexión: http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults El proxy inverso se puede habilitar en el clúster siguiendo los pasos mencionados [aquí.](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) 

> 
> [!WARNING]
> Después de configurar el proxy inverso, se podrá acceder de manera externa a todos los microservicios del clúster que exponen un punto de conexión HTTP.

## <a name="diagnostics--health-events"></a>Diagnósticos y eventos de estado

### <a name="collecting-patch-orchestration-application-logs"></a>Recopilación de los registros de la aplicación de orquestación de revisiones

Próximamente los registros de la aplicación de orquestación de revisiones se recopilarán como parte de los registros de Service Fabric.
Hasta entonces, los registros se pueden recopilar utilizando una de las maneras siguientes

#### <a name="locally-on-each-node"></a>Localmente en cada nodo

Los registros se recopilan localmente en cada nodo del clúster de Service Fabric. La ubicación para tener acceso a los registros es \[Unidad\_Instalación\_Service Fabric\]:\\PatchOrchestrationApplication\\logs

Ej: Si se instaló Service Fabric en unidad "D", la ruta de acceso sería D:\\PatchOrchestrationApplication\\logs

#### <a name="central-location"></a>Ubicación central

Si WAD está configurado como parte de los pasos de requisitos previos, los registros de la aplicación de orquestación de revisiones estarían disponibles en Azure Storage.

### <a name="health-reports"></a>Informes de estado

La aplicación de orquestación de revisiones también publica los informes de estado en CoordinatorService o NodeAgentService en los siguientes casos

#### <a name="windows-update-operation-failed"></a>Error de la operación de actualización de Windows

Si se produce un error en la operación de actualización de Windows en un nodo, se generará el informe de estado en NodeAgentService.
Los detalles del informe de estado contienen el nombre del nodo problemático.

El informe se borrará automáticamente después de aplicar correctamente la revisión en el nodo problemático.

#### <a name="node-agent-ntservice-is-down"></a>NodeAgentNTService está inactivo

Si NodeAgentNTService está inactivo en un nodo, se genera un informe de estado de nivel de advertencia en NodeAgentService

#### <a name="repair-manager-is-not-enabled"></a>Repair Manager no está habilitado

Si el servicio Repair Manager no se encuentra en el clúster, se genera un informe de estado de nivel de advertencia en CoordinatorService.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes:

P: **Veo el clúster en estado de error cuando se ejecuta la aplicación de orquestación de revisiones**.

A. La aplicación de orquestación de revisiones podría deshabilitar o reiniciar nodos, durante el proceso de instalación, puede dar lugar temporalmente a un estado de no disponibilidad del clúster.

En función de la directiva de la aplicación, un nodo puede dejar de estar activo durante una operación de aplicación de revisiones, o bien un dominio de actualización completo puede dejar de estar activo.

Tenga en cuenta que al final de la instalación de Windows Update, los nodos se vuelven a habilitar después del reinicio.

Ejemplo: en el caso siguiente, el clúster entró temporalmente en estado de error debido a 2 nodos inactivos, lo que infringe la directiva MaxPercentageUnhealthNodes. Es un error temporal hasta que la operación de aplicación de revisiones finalice.

![Imagen de clúster en mal estado](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Si el problema persiste, consulte la sección de solución de problemas

P: **¿Puedo usar la aplicación de orquestación de revisiones para clústeres independientes?**

A. No, la compatibilidad con clústeres independientes estará disponible próximamente.

P: **La aplicación de orquestación de revisiones está en estado de advertencia**

A. Compruebe si un informe de estado publicado a la aplicación es la causa principal. Normalmente la advertencia contiene los detalles del problema.
Se espera que la aplicación se recupere automáticamente de este estado en el caso de que el problema sea transitorio.

P: **El clúster está en mal estado. No obstante, necesito realizar la actualización del sistema operativo de un modo urgente**

A. La aplicación de orquestación de revisiones no instala actualizaciones cuando el clúster está en mal estado.
Intente devolver el clúster a un estado correcto para desbloquear el flujo de trabajo de la aplicación de orquestación de revisiones.

P: **¿Por qué la aplicación de revisiones en el clúster tarda tiempo en ejecutarse?**

A. El tiempo empleado por la aplicación de orquestación de revisiones depende principalmente de los factores siguientes:

- Directiva de CoordinatorService: la directiva predeterminada `NodeWise` implica la aplicación de revisiones en un único nodo a la vez, especialmente en el caso de clústeres grandes se recomienda usar la directiva `UpgradeDomainWise` para lograr una aplicación más rápida de las revisiones en el clúster.
- Número promedio de actualizaciones disponibles para su descarga e instalación. El tiempo empleado en descargar e instalar una actualización no debería superar un par de horas.
- Rendimiento de la máquina virtual y ancho de banda de la red.

## <a name="disclaimers"></a>Declinación de responsabilidades

- La aplicación de orquestación de revisiones acepta el CLUF de Windows Update en nombre del usuario. La configuración, opcionalmente, se puede desactivar en la configuración de la aplicación.

- La aplicación de orquestación de revisiones recopila datos de telemetría para realizar el seguimiento de uso y rendimiento.
    Los datos de telemetría de la aplicación siguen la configuración de telemetría del runtime de Service Fabric (que es activado de forma predeterminada).

## <a name="troubleshooting-help"></a>Ayuda para la solución de problemas

### <a name="node-not-coming-back-to-up-state"></a>El nodo no vuelve a su estado activo

**El nodo podría quedarse bloqueado en estado deshabilitado** Esto puede ocurrir cuando un nodo programado para la operación no se puede deshabilitar debido a una de comprobación de seguridad pendiente. Para solucionar este problema, asegúrese de que hay suficientes nodos disponibles en estado correcto.

**El nodo podría bloquearse en estado deshabilitado debido a los siguientes motivos**

- El nodo se ha deshabilitado manualmente.
- El nodo se ha deshabilitado por causa de un trabajo de infraestructura de Azure en curso.
- El nodo ha sido deshabilitado temporalmente por la aplicación de orquestación de revisiones para aplicar revisiones en el nodo.

**El nodo podría bloquearse en estado inactivo debido a los siguientes motivos**

- El nodo se ha desactivado manualmente.
- El nodo está realizando un reinicio (puede haber sido desencadenado por la aplicación de orquestación de revisiones).
- El nodo está inactivo por un problema en la máquina virtual o por problemas de conectividad de red.

### <a name="updates-were-skipped-on-some-nodes"></a>Las actualizaciones se omitieron en algunos nodos

La aplicación de orquestación de revisiones intenta instalar las actualizaciones de Windows según la directiva de reprogramación. El servicio puede intentar recuperar el nodo y omitir la actualización en función de la directiva de la aplicación.

En ese caso, se generaría un informe de estado de nivel de advertencia en Node Agent Service.
El resultado de Windows Update también contendría el motivo posible del error.

### <a name="health-of-the-cluster-goes-to-error-while-update-was-getting-installed"></a>Estado del clúster es de error mientras se instala la actualización

En caso de que una actualización incorrecta de Windows desactive el estado de la aplicación o del clúster en un nodo concreto o un dominio de actualización, la aplicación de orquestación de revisiones no continúa con las operaciones posteriores de Windows Update hasta que el clúster vuelve a un estado correcto.

El administrador debe intervenir y determinar por qué razón una actualización de Windows está causando un estado incorrecto de la aplicación o el clúster.


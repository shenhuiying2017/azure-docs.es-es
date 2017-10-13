---
title: "Supervisión de operaciones, eventos y contadores de Load Balancer | Microsoft Docs"
description: Aprenda a habilitar eventos de alerta y el registro del estado de mantenimiento del sondeo para el Equilibrador de carga de Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
ms.assetid: 56656d74-0241-4096-88c8-aa88515d676d
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 36b9379f88ce024c1dcbf9977a873d95076d10df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-for-azure-load-balancer"></a>Análisis del registros para el Equilibrador de carga de Azure

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Puede usar diferentes tipos de registros en Azure para administrar y solucionar problemas de equilibradores de carga. Se puede acceder a algunos de estos registros a través del portal. Se pueden extraer todos los registros desde Azure Blob Storage y visualizarse en distintas herramientas, como Excel y PowerBI. Puede obtener más información acerca de los diferentes tipos de registros en la lista siguiente.

* **Registros de auditoría:** puede usar los [registros de auditoría de Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (anteriormente conocido como registros operativos) para ver todas las operaciones enviadas a sus suscripciones de Azure, así como su estado. Los registros de auditoría están habilitados de forma predeterminada y se pueden ver en el Portal de Azure.
* **Registros de eventos de alerta:** puede utilizar este registro para las alertas generadas por el equilibrador de carga. El estado del equilibrador de carga se recopila cada cinco minutos. Este registro se escribe solo si se produce un evento de alerta del equilibrador de carga.
* **Registros de sondeo de estado:** puede utilizar este registro para ver los problemas detectados por el sondeo de estado, como el número de instancias en el grupo back-end que no reciben las solicitudes del equilibrador de carga debido a errores de sondeo de estado. Este registro se escribe cuando se produce un cambio en el estatus del sondeo de estado.

> [!IMPORTANT]
> El análisis de registros actualmente solo funciona para los equilibradores de carga orientados hacia Internet. Los registros solo están disponibles para los recursos implementados en el modelo de implementación del Administrador de recursos. No puede usar los registros de recursos del modelo de implementación clásica. Para más información sobre estos modelos de implementación, consulte [Understanding Resource Manager deployment and classic deployment](../azure-resource-manager/resource-manager-deployment-model.md) (Descripción de la implementación de Resource Manager y la implementación clásica).

## <a name="enable-logging"></a>Habilitación del registro

El registro de auditoría se habilita automáticamente para todos los recursos de Resource Manager. Debe habilitar el registro de eventos y de sondeos de estado para iniciar la recopilación de los datos disponibles a través de esos registros. Para habilitar el registro, realice los siguientes pasos.

Inicie sesión en el [Portal de Azure](http://portal.azure.com). Si aún no tiene un equilibrador de carga, [cree uno](load-balancer-get-started-internet-arm-ps.md) antes de continuar.

1. En el portal, haga clic en **Examinar**.
2. Seleccione **Equilibradores de carga**.

    ![portal - load-balancer](./media/load-balancer-monitor-log/load-balancer-browse.png)

3. Seleccione un equilibrador de carga existente >> **Toda la configuración**.
4. En el lado derecho del cuadro de diálogo, bajo el nombre del equilibrador de carga, desplácese a **Supervisión** y haga clic en **Diagnósticos**.

    ![portal - load-balancer-settings](./media/load-balancer-monitor-log/load-balancer-settings.png)

5. En el panel **Diagnósticos**, debajo de **Estado**, seleccione **Activar**.
6. Haga clic en **Cuenta de almacenamiento**.
7. En **Registros**, seleccione una cuenta de almacenamiento existente o cree una nueva. Utilice el control deslizante para determinar cuántos días de datos de eventos se almacenarán en los registros de eventos. 
8. Haga clic en **Guardar**.

    ![Portal - Registros de diagnósticos](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

> [!NOTE]
> Los registros de auditoría no requieren una cuenta de almacenamiento separada. El uso del almacenamiento para el registro de eventos y de sondeo de estado supondrán un costo adicional de servicio.

## <a name="audit-log"></a>Registro de auditoría

El registro de auditoría se genera de forma predeterminada. Los registros se conservan durante 90 días en el almacén de registros de eventos de Azure. Para obtener más información sobre estos registros, consulte el artículo [Visualización de eventos y registros de auditoría](../monitoring-and-diagnostics/insights-debugging-with-events.md) .

## <a name="alert-event-log"></a>Registro de eventos de alerta

Este registro solo se genera si lo habilitó para cada uno de los equilibradores de carga. Los eventos se registran en formato JSON y se almacenan en la cuenta de almacenamiento que especificó cuando habilitó el registro. A continuación, se muestra un ejemplo de un evento.

```json
{
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }
}
```

El resultado de JSON muestra la propiedad *eventname* que describirá el motivo de creación de una alerta por parte del equilibrador de carga. En este caso, la alerta generada se debió al agotamiento de puertos TCP causado por los límites de IP NAT de origen (SNAT).

## <a name="health-probe-log"></a>Registro de sondeo de estado

Este registro solo se genera si lo habilitó para cada uno de los equilibradores de carga, tal como se indicó anteriormente. Los datos se almacenan en la cuenta de almacenamiento que especificó cuando habilitó el registro. Se crea un contenedor denominado "insights-logs-loadbalancerprobehealthstatus" y se registran los datos siguientes:

```json
{
    "records":[
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 1,
            "healthPercentage": 50.000000
        }
    },
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 0,
            "healthPercentage": 100.000000
        }
    }]
}
```

El resultado JSON muestra en el campo de propiedades la información básica del estado de mantenimiento del sondeo. La propiedad *dipDownCount* muestra el número total de instancias en el back-end que no están recibiendo tráfico de red debido a las respuestas de sondeo con error.

## <a name="view-and-analyze-the-audit-log"></a>Visualización y análisis del registro de auditoría

Puede ver y analizar los datos del registro de auditoría mediante el uso de cualquiera de los métodos siguientes:

* **Herramientas de Azure:** puede recuperar información de los registros de auditoría a través de Azure PowerShell, de la interfaz de la línea de comandos (CLI) de Azure, la API de REST de Azure o el Portal de vista previa de Azure. En el artículo [Operaciones de auditoría con el Administrador de recursos](../azure-resource-manager/resource-group-audit.md) se detallan instrucciones paso a paso de cada método.
* **Power BI:** si todavía no tiene una cuenta de [Power BI](https://powerbi.microsoft.com/pricing), puede probarlo gratis. Con el [paquete de contenido de los registros de auditoría de Azure para Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs) puede analizar los datos con los paneles preconfigurados o puede personalizar las vistas para que se adapten a sus necesidades.

## <a name="view-and-analyze-the-health-probe-and-event-log"></a>Visualización y análisis del registro de eventos y de sondeos de estado

Debe conectarse a la cuenta de almacenamiento y recuperar las entradas del registro de JSON para los registros de eventos y de sondeos de estado. Cuando descargue los archivos JSON, se pueden convertir a CSV y consultarlos en Excel, PowerBI o cualquier otra herramienta de visualización de datos.

> [!TIP]
> Si está familiarizado con Visual Studio y con los conceptos básicos de cambio de los valores de constantes y variables de C#, puede usar las [herramientas convertidoras de registros](https://github.com/Azure-Samples/networking-dotnet-log-converter) que encontrará en GitHub.

## <a name="additional-resources"></a>Recursos adicionales

* [Visualize your Azure Audit Logs with Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) (Visualizar los registros de auditoría de Azure con Power BI).
* [View and analyze Azure Audit Logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) (Visualizar los registros de auditoría de Azure con Power BI).

## <a name="next-steps"></a>Pasos siguientes

[Descripción de los sondeos del equilibrador de carga](load-balancer-custom-probe-overview.md)

---
title: "Recopilación de alertas de Nagios y Zabbix en Log Analytics de OMS | Microsoft Docs"
description: "Nagios y Zabbix son herramientas de supervisión de código abierto. Con estas herramientas, puede recopilar alertas en Log Analytics con el fin de analizarlas, y también alertas de otros orígenes.  En este artículo se describe cómo configurar al agente de OMS para Linux para recopilar alertas de estos sistemas."
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: 0b64c32e1031e704d50aab0b38eaea41e27d134b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="collect-alerts-from-nagios-and-zabbix-in-log-analytics-from-oms-agent-for-linux"></a>Recopilación de alertas de Nagios y Zabbix en Log Analytics desde el agente de OMS para Linux 
[Nagios](https://www.nagios.org/) y [Zabbix](http://www.zabbix.com/) son herramientas de supervisión de código abierto.  Con estas herramientas, puede recopilar alertas en Log Analytics con el fin de analizarlas, y [también alertas de otros orígenes](log-analytics-alerts.md).  En este artículo se describe cómo configurar al agente de OMS para Linux para recopilar alertas de estos sistemas.
 
## <a name="configure-alert-collection"></a>Configuración de la recopilación de alertas

### <a name="configuring-nagios-alert-collection"></a>Configuración de la recopilación de alertas de Nagios
Realice los pasos siguientes en el servidor Nagios para recopilar alertas.

1. Conceda al usuario acceso de lectura de **omsagent** al archivo de registro de Nagios (es decir, `/var/log/nagios/nagios.log`). Suponiendo que el archivo nagios.log es propiedad del grupo `nagios`, puede agregar al usuario **omsagent** al grupo **nagios**. 

    sudo usermod -a -G nagios omsagent

2.  Modifique el archivo de configuración en (`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`). Asegúrese de que las entradas siguientes están presentes y no comentadas:  

        <source>  
          type tail  
          #Update path to point to your nagios.log  
          path /var/log/nagios/nagios.log  
          format none  
          tag oms.nagios  
        </source>  
      
        <filter oms.nagios>  
          type filter_nagios_log  
        </filter>  

3. Reinicio del demonio de omsagent

    ```
    sudo sh /opt/microsoft/omsagent/bin/service_control restart
    ```

### <a name="configuring-zabbix-alert-collection"></a>Configuración de la recopilación de alertas de Zabbix
Para recopilar alertas de un servidor de Zabbix, debe especificar un usuario y una contraseña en *texto sin formato*. Aunque esto no es lo idóneo, se recomienda que cree el usuario y conceda permisos solo de supervisión.

Realice los pasos siguientes en el servidor Nagios para recopilar alertas.

1. Modifique el archivo de configuración en (`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`). Asegúrese de que las entradas siguientes están presentes y no tienen comentarios:  Cambie el nombre de usuario y la contraseña por los valores de su entorno de Zabbix.

        <source>
         type zabbix_alerts
         run_interval 1m
         tag oms.zabbix
         zabbix_url http://localhost/zabbix/api_jsonrpc.php
         zabbix_username Admin
         zabbix_password zabbix
        </source>

2. Reinicio del demonio de omsagent

    sudo sh /opt/microsoft/omsagent/bin/service_control restart


## <a name="alert-records"></a>Registros de alerta
Puede recuperar registros de alertas de Nagios y Zabbix con [búsquedas de registros](log-analytics-log-searches.md) en Log Analytics.

### <a name="nagios-alert-records"></a>Registros de alertas de Nagios

Los registros de alertas recopilados por Nagios tienen como **tipo** una **alerta** y como **SourceSystem****Nagios**.  Tienen las propiedades de la tabla siguiente.

| Propiedad | Descripción |
|:--- |:--- |
| Tipo |*Alerta* |
| SourceSystem |*Nagios* |
| AlertName |Nombre de la alerta |
| AlertDescription | Descripción de la alerta |
| AlertState | Estado del servicio o host.<br><br>OK<br>ADVERTENCIA<br>ARRIBA<br>ABAJO |
| HostName | Nombre del host que creó la alerta. |
| PriorityNumber | Nivel de prioridad de la alerta. |
| StateType | El tipo de estado de la alerta.<br><br>SOFT: problema que no se ha vuelto a comprobar.<br>HARD: problema que se ha vuelto a comprobar un número de veces especificado.  |
| TimeGenerated |Fecha y hora en que se creó la alerta. |


### <a name="zabbix-alert-records"></a>Registros de alertas de Zabbix
Los registros de alertas recopilados por Zabbix tienen como **tipo** una **alerta** y como **SourceSystem****Zabbix**.  Tienen las propiedades de la tabla siguiente.

| Propiedad | Descripción |
|:--- |:--- |
| Tipo |*Alerta* |
| SourceSystem |*Zabbix* |
| AlertName | Nombre de la alerta |
| AlertPriority | Gravedad de la alerta.<br><br>no clasificada<br>informativo<br>Warning (Advertencia)<br>average<br>alta<br>desastre  |
| AlertState | Estado de la alerta.<br><br>0: el estado está actualizado.<br>1: el estado es desconocido.  |
| AlertTypeNumber | Especifica si la alerta puede generar varios eventos de problema.<br><br>0: el estado está actualizado.<br>1: el estado es desconocido.    |
| Comentarios | Comentarios adicionales de la alerta. |
| HostName | Nombre del host que creó la alerta. |
| PriorityNumber | Valor que indica la gravedad de la alerta.<br><br>0: no clasificada<br>1: información<br>2: advertencia<br>3: promedio<br>4: alta<br>5: desastre |
| TimeGenerated |Fecha y hora en que se creó la alerta. |
| TimeLastModified |Fecha y hora en que se cambió por última vez el estado de la alerta. |


## <a name="next-steps"></a>Pasos siguientes
* Más información sobre las [alertas](log-analytics-alerts.md) en Log Analytics.
* Obtenga información acerca de las [búsquedas de registros](log-analytics-log-searches.md) para analizar los datos recopilados de las soluciones y los orígenes de datos. 

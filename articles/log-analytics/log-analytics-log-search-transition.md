---
title: Hoja de referencia del lenguaje de consulta de Log Analytics de Azure | Microsoft Docs
description: "Este artículo proporciona ayuda sobre la transición al nuevo lenguaje de consulta de Log Analytics, si ya conoce el lenguaje heredado."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2017
ms.author: bwren
ms.openlocfilehash: 9c487ab33859ae453a0074ef0344f61de19c7b4d
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="transitioning-to-azure-log-analytics-new-query-language"></a>Transición al nuevo lenguaje de consulta de Log Analytics de Azure
Log Analytics implementó recientemente un nuevo lenguaje de consulta.  En este artículo se proporciona ayuda sobre la transición a este lenguaje de Log Analytics, si ya conoce el lenguaje heredado y aún necesita más información.

## <a name="resources"></a>Recursos


## <a name="language-converter"></a>Convertidor de lenguaje

Si conoce el lenguaje de consulta de Log Analytics heredado, la manera más fácil de crear la misma consulta en el nuevo lenguaje es usar el convertidor de lenguaje que se instala en el portal de la búsqueda de registros cuando se convierte el área de trabajo.  El uso del convertidor es tan sencillo como escribir una consulta heredada en el cuadro de texto superior y, a continuación, hacer clic en **Convertir**.  O bien, puede hacer clic en el botón de búsqueda para ejecutar la consulta o copiar y pegar para utilizarla en otro lugar.

![Convertidor de lenguaje](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="resources"></a>Recursos
El [sitio de documentación para el lenguaje de consultas de Log Analytics](https://docs.loganalytics.io) tiene todos los recursos que necesita para ponerse al día en el nuevo lenguaje.  Esto incluye tutoriales, ejemplos y una referencia completa del lenguaje.


## <a name="cheat-sheet"></a>Hoja de referencia rápida

En la tabla siguiente se proporciona una comparación entre diversas consultas comunes y los comandos equivalentes entre el lenguaje de consulta nuevo y el heredado de Azure Log Analytics.

| Descripción | Heredado | new |
|:--|:--|:--|
| Buscar en todas las tablas      | error | buscar "error" (no distingue mayúsculas de minúsculas) |
| Selección de datos de una tabla | Type=Event |  Evento |
|                        | Type=Event &#124; select Source, EventLog, EventID | Event &#124; project Source, EventLog, EventID |
|                        | Type=Event &#124; top 100 | Event &#124; take 100 |
| Comparación de cadenas      | Type=Event Computer=srv01.contoso.com   | Event &#124; where Computer == "srv01.contoso.com" |
|                        | Type=Event Computer=contains("contoso") | Event &#124; where Computer contains "contoso" (no distingue mayúsculas de minúsculas)<br>Event &#124; where Computer contains_cs "Contoso" (distingue mayúsculas de minúsculas) |
|                        | Type=Event Computer=RegEx("@contoso@")  | Event &#124; where Computer matches regex ".*contoso*" |
| Comparación de fechas        | Type=Event TimeGenerated > NOW-1DAYS | Event &#124; where TimeGenerated > ago(1d) |
|                        | Type=Event TimeGenerated>2017-05-01 TimeGenerated<2017-05-31 | Event &#124; where TimeGenerated between (datetime(2017-05-01) .. datetime(2017-05-31)) |
| Comparación de valores booleanos     | Type=Heartbeat IsGatewayInstalled=false  | Heartbeat \| where IsGatewayInstalled == false |
| Sort                   | Type=Event &#124; sort Computer asc, EventLog desc, EventLevelName asc | Event \| sort by Computer asc, EventLog desc, EventLevelName asc |
| Distinct               | Type=Event &#124; dedup Computer \| select Computer | Event &#124; summarize by Computer, EventLog |
| Extensión de columnas         | Type=Perf CounterName="% Processor Time" &#124; EXTEND if(map(CounterValue,0,50,0,1),"HIGH","LOW") as UTILIZATION | Perf &#124; where CounterName == "% Processor Time" \| extend Utilization = iff(CounterValue > 50, "HIGH", "LOW") |
| Agregación            | Type=Event &#124; measure count() as Count by Computer | Event &#124; summarize Count = count() by Computer |
|                                | Type=Perf ObjectName=Processor CounterName="% Processor Time" &#124; measure avg(CounterValue) by Computer interval 5minute | Perf &#124; where ObjectName=="Processor" and CounterName=="% Processor Time" &#124; summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min) |
| Agregación sin límite | Type=Event &#124; measure count() by Computer &#124; top 10 | Event &#124; summarize AggregatedValue = count() by Computer &#124; limit 10 |
| Unión                  | Type=Event or Type=Syslog | union Event, Syslog |
| Unión                   | Type=NetworkMonitoring &#124; join inner AgentIP (Type=Heartbeat) ComputerIP | NetworkMonitoring &#124; join kind=inner (search Type == "Heartbeat") on $left.AgentIP == $right.ComputerIP |



## <a name="next-steps"></a>Pasos siguientes
- Consulte un [tutorial sobre cómo escribir consultas](https://go.microsoft.com/fwlink/?linkid=856078) mediante el nuevo lenguaje de consulta.
- Remítase a la [Referencia del lenguaje de consulta](https://go.microsoft.com/fwlink/?linkid=856079) para obtener más detalles sobre todos los comandos, operadores y funciones del nuevo lenguaje de consulta.  

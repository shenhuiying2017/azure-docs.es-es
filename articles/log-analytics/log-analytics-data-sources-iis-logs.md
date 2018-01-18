---
title: Registros de IIS en Azure Log Analytics | Microsoft Docs
description: "Internet Information Services (IIS) almacena la actividad de usuario en archivos de registro que Log Analytics puede recopilar.  En este artículo se describe cómo configurar la recopilación de registros de IIS y detalles de los registros que crean en el área de trabajo de Log Analytics."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: cec5ff0a-01f5-4262-b2e8-e3db7b7467d2
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2018
ms.author: bwren
ms.openlocfilehash: b8ce4e6fe6e12aa3edb81abad1589924e3e121e4
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="iis-logs-in-log-analytics"></a>Registros de IIS en Log Analytics
Internet Information Services (IIS) almacena la actividad de usuario en archivos de registro que Log Analytics puede recopilar.  

![Registros IIS](media/log-analytics-data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Configuración de registros de IIS
Log Analytics recopila entradas de archivos de registro creados por IIS, por lo que debe [configurar IIS para el registro](https://technet.microsoft.com/library/hh831775.aspx).

Log Analytics solo admite archivos de registro de IIS almacenados en el formato W3C y no admite campos personalizados ni Advanced Logging de IIS.  
Log Analytics no recopila registros en formato nativo de IIS ni NCSA.

Configure los registros de IIS en Log Analytics en el [menú Datos en Configuración de Log Analytics](log-analytics-data-sources.md#configuring-data-sources).  No se requiere otra configuración que seleccionar **Collect W3C format IIS log files**(Recopilar archivos de registro de IIS en formato W3C).

Es recomendable que, cuando se habilite la recopilación de registros de IIS, se configure el valor de sustitución de registros de IIS en cada servidor.

## <a name="data-collection"></a>Colección de datos
Log Analytics recopila entradas de registro de IIS de cada origen conectado a intervalos de, aproximadamente, 15 minutos.  El agente registra su lugar en cada registro de eventos del que recopila entradas.  Si el agente se queda sin conexión, Log Analytics recopila eventos desde el punto en que se detuvo, incluso si dichos eventos se crearon mientras el agente estaba sin conexión.

## <a name="iis-log-record-properties"></a>Propiedades de registro de IIS
Los registros de IIS son del tipo **W3CIISLog** y tienen las propiedades que aparecen en la tabla siguiente:

| Propiedad | DESCRIPCIÓN |
|:--- |:--- |
| Equipo |Nombre del equipo desde el que se recopiló el evento. |
| cIP |Dirección IP del cliente. |
| csMethod |Método de la solicitud, como GET o POST. |
| csReferer |Sitio cuyo vínculo el usuario siguió desde el sitio actual. |
| csUserAgent |Tipo de explorador del cliente. |
| csUserName |Nombre del usuario autenticado que obtuvo acceso al servidor. Los usuarios anónimos se indican con un guion. |
| csUriStem |El destino de la solicitud, como una página web. |
| csUriQuery |La consulta que el cliente intentaba realizar, si corresponde. |
| ManagementGroupName |Nombre del grupo de administración de agentes de Operations Manager.  En el caso de los otros agentes, es AOI-\<id. de área de trabajo\>. |
| RemoteIPCountry |El país de la dirección IP del cliente. |
| RemoteIPLatitude |La latitud de la dirección IP del cliente. |
| RemoteIPLongitude |La longitud de la dirección IP del cliente. |
| scStatus |Código de estado HTTP. |
| scSubStatus |Código de error de subestado. |
| scWin32Status |Código de estado de Windows. |
| sIP |Dirección IP del servidor web. |
| SourceSystem |OpsMgr |
| sPort |Puerto en el servidor al que está conectado el cliente. |
| sSiteName |Nombre del sitio IIS. |
| TimeGenerated |Fecha y hora en que se registró la entrada. |
| TimeTaken |Duración del procesamiento de la solicitud, en milisegundos. |

## <a name="log-searches-with-iis-logs"></a>Búsquedas de registros con registros de IIS
La tabla siguiente proporciona ejemplos distintos de consultas de registro que recuperan registros de IIS.

| Consultar | DESCRIPCIÓN |
|:--- |:--- |
| W3CIISLog |Todos los registros de IIS. |
| W3CIISLog &#124; where scStatus==500 |Todas las entradas de registro IIS con un estado de retorno de 500. |
| W3CIISLog &#124; summarize count() by cIP |Contador de entradas de registro de IIS por dirección IP del cliente. |
| W3CIISLog &#124; where csHost=="www.contoso.com" &#124; summarize count() by csUriStem |Contador de entradas de registro de IIS por dirección URL para el host www.contoso.com. |
| W3CIISLog &#124; summarize sum(csBytes) by Computer &#124; take 500000 |Total de bytes recibidos por cada equipo de IIS |

## <a name="next-steps"></a>pasos siguientes
* Configure Log Analytics para recopilar otros [orígenes de datos](log-analytics-data-sources.md) para su análisis.
* Obtenga información acerca de las [búsquedas de registros](log-analytics-log-searches.md) para analizar los datos recopilados de soluciones y orígenes de datos.
* Configure alertas en Log Analytics para recibir notificaciones de manera nativa con respecto a condiciones importantes encontradas en los registros de IIS.

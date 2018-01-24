---
title: Solucionar problemas del agente de Linux de Azure Log Analytics | Microsoft Docs
description: "Aquí se describen los síntomas, las causas y las soluciones de los problemas más comunes que surgen con el agente de Linux de Log Analytics."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/17/2017
ms.author: magoedte
ms.openlocfilehash: 5f598da9b82b4425ca509a26a2e6e366ba4c3394
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/19/2017
---
# <a name="how-to-troubleshoot-issues-with-the-linux-agent-for-log-analytics"></a>Cómo solucionar problemas relacionados con el agente de Linux de Log Analytics

En este artículo se proporciona información acerca de los errores que puede experimentar con el agente de Linux de Log Analytics y se sugieren posibles soluciones para resolverlos.

## <a name="issue-unable-to-connect-through-proxy-to-log-analytics"></a>Problema: No es posible establecer la conexión a través del proxy a Log Analytics

### <a name="probable-causes"></a>Causas probables
* El proxy especificado durante la incorporación era incorrecto.
* Los puntos de conexión de servicios Log Analytics y Azure Automation no están en la lista de permitidos en el centro de datos 

### <a name="resolutions"></a>Soluciones
1. Vuelva a incorporase al servicio Log Analytics con el Agente de Operations Management Suite para Linux mediante el comando siguiente con la opción `-v` habilitada. Esto permite la salida detallada del agente que se conecta a través del proxy al servicio OMS. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`

2. Revise la sección [Configuración del agente para su uso con un servidor proxy o puerta de enlace de OMS](#configuring the-agent-for-use-with-a-proxy-server-or-oms-gateway) para comprobar que el agente se haya configurado correctamente para comunicarse a través de un servidor proxy.    
* Vuelva a comprobar que los siguientes puntos de conexión del servicio Log Analytics estén incluidos en la lista de permitidos:

    |Recurso del agente| Puertos |  
    |------|---------|  
    |*.ods.opinsights.azure.com | Puerto 443|   
    |* .oms.opinsights.azure.com | Puerto 443|   
    |ods.systemcenteradvisor.com | Puerto 443|   
    |*.blob.core.windows.net/ | Puerto 443|   

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Problema: Recibe un error 403 al intentar incorporarse

### <a name="probable-causes"></a>Causas probables
* La fecha y la hora son incorrectas en el servidor Linux 
* El identificador y la clave de área de trabajo usados son incorrectos

### <a name="resolution"></a>Resolución

1. Compruebe la hora en el servidor Linux con el comando date. Si la hora es +/-15 minutos de la hora actual, la incorporación produce un error. Para corregir este problema, actualice la fecha o la zona horaria del servidor Linux. 
2. Compruebe que ha instalado la versión más reciente del agente de OMS para Linux.  La versión más reciente notifica ahora si el desfase temporal está causando el error de incorporación.
3. Vuelva a realizar la incorporación con un identificador de área de trabajo correcto y la clave de área de trabajo según las instrucciones de instalación de este tema.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Problema: Ve un error 404 y 500 en el archivo de registro justo después de la incorporación
Se trata de un problema conocido que se produce con la primera carga de datos de Linux en un área de trabajo de Log Analytics. Esto no afecta a los datos que se envían ni a la experiencia del servicio.

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Problema: No aparece ningún dato en Azure Portal

### <a name="probable-causes"></a>Causas probables

- Se produjo un error en la incorporación al servicio Log Analytics
- Se bloqueó la conexión al servicio Log Analytics
- Se está haciendo la copia de seguridad de los datos del agente OMS para Linux

### <a name="resolutions"></a>Soluciones
1. Compruebe si la incorporación del servicio Log Analytics se realizó correctamente comprobando si existe el archivo siguiente: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Repita la incorporación con las instrucciones de la línea de comandos `omsadmin.sh`
3. Si utiliza un proxy, consulte los pasos de resolución de proxy que proporcionó anteriormente.
4. En algunos casos, cuando el Agente de Operations Management Suite para Linux no puede comunicarse con el servicio, los datos del agente ocupan el tamaño de búfer total de 50 MB. Se debe reiniciar el agente de OMS para Linux ejecutando el siguiente comando `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Este problema se ha corregido en la versión 1.1.0-28 y posteriores del agente.


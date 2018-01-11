---
title: Uso de sondeos personalizados de Load Balancer para supervisar el estado de mantenimiento | Microsoft Docs
description: "Aprenda a usar sondeos personalizados para el Equilibrador de carga de Azure para supervisar las instancias detrás de dicho Equilibrador."
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 266132d8cbb6f9922ce7b49759981132c2c17f47
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="understand-load-balancer-probes"></a>Descripción de los sondeos de Load Balancer

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

El Equilibrador de carga de Azure ofrece la capacidad de supervisar el estado de las instancias del servidor mediante sondeos. Cuando un sondeo no responde, el Equilibrador de carga deja de enviar nuevas conexiones a la instancia incorrecta. Las conexiones existentes no resultan afectadas y las nuevas conexiones se envían a instancias en buen estado.

Los roles del servicio en la nube (los roles de trabajo y los roles web) usan un agente invitado con supervisión del sondeo del agente invitado. Deben configurarse sondeos TCP o HTTP personalizados al usar máquinas virtuales detrás de Load Balancer.

## <a name="understand-probe-count-and-timeout"></a>Descripción del número y el tiempo de espera de los sondeos

El comportamiento de los sondeos depende de:

* El número de sondeos correctos que permiten que una instancia se etiquete como activa.
* El número de sondeos erróneos que permiten que una instancia se etiquete como inactiva.

Los valores de tiempo de expiración y frecuencia establecidos en SuccessFailCount confirman si una instancia está en ejecución o no. En el portal de Azure, el tiempo de espera se establece en dos veces el valor de la frecuencia.

La configuración de los sondeos de todas las instancias con carga equilibrada para un punto de conexión (es decir, un conjunto de carga equilibrada) debe ser igual. No puede tener una configuración de sondeo distinta para cada instancia de rol o máquina virtual en el mismo servicio hospedado para una combinación determinada de puntos de conexión. Por ejemplo, cada instancia debe tener puertos locales y tiempos de espera idénticos.

> [!IMPORTANT]
> Un sondeo del equilibrador de carga utiliza la dirección IP 168.63.129.16. Esta dirección IP pública facilita un canal de comunicación a los recursos internos de plataforma para el escenario de Azure Virtual Network que permite especificar su propia IP. La dirección IP pública virtual 168.63.129.16 se utiliza en todas las regiones y no cambia. Le recomendamos que permita esta dirección IP en cualquiera de las directivas de firewall local. No se debe considerar un riesgo de seguridad porque la plataforma interna de Azure es la única que puede originar un mensaje procedente de esa dirección. Si no permite esta dirección IP en las directivas de firewall, se produce un comportamiento inesperado en varios escenarios. El comportamiento incluye la configuración del mismo intervalo de direcciones IP de 168.63.129.16 y la duplicación de direcciones IP.

## <a name="learn-about-the-types-of-probes"></a>Obtención de información sobre el tipo de sondeo

### <a name="guest-agent-probe"></a>Sondeo de agente invitado

Este sondeo invitado solo está disponible para Azure Cloud Services. Load Balancer utiliza al agente invitado dentro de la máquina virtual. A continuación, escucha y responde con una respuesta HTTP 200 OK solo cuando la instancia está en estado Preparado. (Otros estados son Ocupado, Reciclando o Deteniendo).

Para obtener más información, consulte [Configuring the service definition file (csdef) for health probes](https://msdn.microsoft.com/library/azure/ee758710.aspx) (Configuración del archivo de definición de servicio (csdef) para los sondeos de estado) o [Get started by creating a public load balancer for cloud services](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services) (Introducción a la creación de un equilibrador de carga público para servicios en la nube).

### <a name="what-makes-a-guest-agent-probe-mark-an-instance-as-unhealthy"></a>¿Qué hace que un sondeo de agente invitado marque una instancia como en mal estado?

Si el agente invitado no responde con HTTP 200 OK, el equilibrador de carga marca la instancia como sin respuesta. A continuación, deja de enviar tráfico a esa instancia. El equilibrador de carga continúa para hacer ping a la instancia. Si el agente invitado responde con un HTTP 200, el equilibrador de carga envía de nuevo tráfico a esa instancia.

Cuando se usa un rol web, el código de sitio web normalmente se ejecuta en w3wp.exe, que no está supervisado por el agente invitado ni el tejido de Azure. Los errores en w3wp.exe (por ejemplo, las respuestas de HTTP 500) no se notifican al agente invitado. Por lo tanto, el equilibrador de carga no toma esa instancia fuera de la rotación.

### <a name="http-custom-probe"></a>Sondeo HTTP personalizado

El sondeo personalizado de HTTP invalida el sondeo predeterminado del agente invitado. Puede crear su propia lógica personalizada para determinar el estado de la instancia de rol. El equilibrador de carga sondea el punto de conexión cada 15 segundos de forma predeterminada. Se considera que la instancia está en la rotación del equilibrador de carga si responde con HTTP 200 dentro del período de tiempo de expiración. El período de tiempo de expiración es de 31 segundos de forma predeterminada.

Un sondeo personalizado de HTTP puede resultar útil si desea implementar su propia lógica para quitar instancias de la rotación del equilibrador de carga. Por ejemplo, podría decidir quitar una instancia si está por encima del 90 % de la CPU y devuelve un estado que no es 200. Si tiene roles web que utilizan w3wp.exe, también obtendrá una supervisión automática de su sitio web. Los errores en el código del sitio web devuelven un estado distinto de 200 para el sondeo del equilibrador de carga.

> [!NOTE]
> El sondeo personalizado HTTP admite solo rutas de acceso relativas y protocolo HTTP. HTTP no se admite.

### <a name="what-makes-an-http-custom-probe-mark-an-instance-as-unhealthy"></a>¿Qué hace que un sondeo HTTP personalizado marque una instancia como en mal estado?

* La aplicación HTTP devuelve un código HTTP de respuesta distinto de 200 (por ejemplo, 403, 404 o 500). Esta confirmación positiva le alerta de que desconecte la instancia de la aplicación del servicio inmediatamente.
* El servidor HTTP no responde después del período de tiempo de expiración. En función del valor del tiempo de expiración establecido, es posible que varias solicitudes de sondeo vayan sin respuesta antes de que el sondeo se marque como que no se está ejecutando (es decir, antes de que se envíen sondeos SuccessFailCount).
* El servidor cierra la conexión a través de un restablecimiento TCP.

### <a name="tcp-custom-probe"></a>Sondeo TCP personalizado

Los sondeos personalizados TCP inician una conexión mediante la realización de un protocolo de enlace de tres vías con el puerto definido.

### <a name="what-makes-a-tcp-custom-probe-mark-an-instance-as-unhealthy"></a>¿Qué hace que un sondeo TCP personalizado marque una instancia como en mal estado?

* El servidor TCP no responde después del período de tiempo de expiración. Cuando el sondeo se marca como no en ejecución depende del número de solicitudes de sondeo con error que se configuraron para ir sin respuesta antes de marcar el sondeo como no en ejecución.
* El sondeo recibe un restablecimiento TCP de la instancia de rol.

Para obtener más información sobre cómo configurar un sondeo de estado HTTP o un sondeo TCP, consulte [Get started creating a public load balancer in Resource Manager by using PowerShell](load-balancer-get-started-internet-arm-ps.md) (Introducción a la creación de un equilibrador de carga público en Resource Manager con PowerShell).

## <a name="add-healthy-instances-back-into-the-load-balancer-rotation"></a>Incorporación de instancias en estado correcto de nuevo en la rotación del equilibrador de carga

Los sondeos TCP y HTTP se consideran en buen estado y marcan la instancia de rol como en buen estado en los casos siguientes:

* El equilibrador de carga obtiene un sondeo positivo la primera vez que se inicia la máquina virtual.
* El número de elementos SuccessFailCount (descrito anteriormente) define el valor de los sondeos correctos que son necesarios para marcar la instancia de rol como en buen estado. Si se quitó una instancia de rol, el número de sondeos correctos y sucesivos debe ser igual o superior al valor de SuccessFailCount para marcar la instancia de rol como en ejecución.

> [!NOTE]
> Si el estado de una instancia de rol fluctúa, el equilibrador de carga espera más tiempo antes de devolver dicha instancia al estado correcto. Este tiempo de espera adicional protege el usuario y la infraestructura y es una directiva intencionada.

## <a name="use-log-analytics-for-a-load-balancer"></a>Uso del análisis de registros para un equilibrador de carga

Puede usar el [análisis de registros](load-balancer-monitor-log.md) para comprobar el estado de mantenimiento del sondeo del equilibrio de carga y el número de sondeos. El registro se puede utilizar con Power BI o con Azure Operational Insights para proporcionar estadísticas del estado de mantenimiento del equilibrador de carga.

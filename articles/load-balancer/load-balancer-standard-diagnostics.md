---
title: 'Azure Load Balancer Estándar: diagnóstico | Microsoft Docs'
description: Use las métricas y la información de mantenimiento disponibles para el diagnóstico de Azure Load Balancer Estándar.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: Kumud
ms.openlocfilehash: 9d5d596254f673b86650e8d9754dacdb70be0666
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32179801"
---
# <a name="metrics-and-health-diagnostics-for-standard-load-balancer"></a>Métricas y diagnóstico de mantenimiento de Load Balancer Estándar

Azure Load Balancer Estándar proporciona las siguientes funcionalidades de diagnóstico para los recursos:
* **Métricas multidimensionales**: proporciona nuevas funcionalidades de diagnóstico multidimensionales para configuraciones del equilibrador de carga tanto públicas como internas. Puede supervisar, administrar y solucionar problemas con los recursos del equilibrador de carga.

* **Resource Health**: la página de Load Balancer en Azure Portal y la página de Resource Health (en Monitor) exponen la sección Resource Health de la configuración del equilibrador de carga de Load Balancer Estándar.

En este artículo se proporciona una introducción a estas funcionalidades y maneras de usarlas con Load Balancer Estándar.

## <a name = "MultiDimensionalMetrics"></a>Métricas multidimensionales

Azure Load Balancer proporciona las nuevas métricas multidimensionales en la nueva página Métricas de Azure (versión preliminar) de Azure Portal y le ayuda a obtener información detallada de diagnóstico en tiempo real sobre los recursos del equilibrador de carga. 

Las distintas configuraciones de Load Balancer Estándar proporcionan las siguientes métricas:

| Métrica | Tipo de recurso | DESCRIPCIÓN | Agregación recomendada |
| --- | --- | --- | --- |
| Disponibilidad de VIP (disponibilidad de ruta de acceso de datos) | Equilibrador de carga público | Load Balancer Estándar usa continuamente la ruta de acceso a los datos desde una región hasta el servidor front-end del equilibrador de carga y, finalmente, hasta la pila de SDN que respalda la máquina virtual. Siempre que permanezcan las instancias correctas, la medida sigue la misma ruta de acceso que el tráfico con equilibrio de carga de las aplicaciones. También se valida la ruta de acceso a los datos que usan los clientes. La medida es invisible para la aplicación y no interfiere con otras operaciones.| Media |
| Disponibilidad de DIP (estado de sondeo de mantenimiento) |  Equilibrador de carga interno y público | Load Balancer Estándar usa un servicio de sondeo de mantenimiento distribuido que supervisa el mantenimiento del punto de conexión de la aplicación de acuerdo con la configuración. Esta métrica proporciona una vista agregada o filtrada por punto de conexión de cada punto de conexión de instancia del grupo del equilibrador de carga. Puede ver cómo Load Balancer observa el estado de su aplicación según se indica en la configuración de sondeo de estado. |  Media |
| Paquetes SYN (sincronizar) |  Equilibrador de carga público | Load Balancer Estándar no finaliza las conexiones de Protocolo de control de transmisión (TCP) ni interactúa con los flujos de paquetes TCP o UDP. Los flujos y los protocolos de enlace son siempre entre el origen y la instancia de máquina virtual. Para solucionar mejor los escenarios de protocolo TCP, puede hacer uso de estos contadores de paquetes SYN para saber el número de intentos de conexión TCP realizados. La métrica indica el número de paquetes TCP SYN recibidos.| Media |
| Conexiones SNAT |  Equilibrador de carga público |Load Balancer Estándar informa del número de flujos salientes enmascarados en el servidor front-end de dirección IP pública. Los puertos de traducción de direcciones de red de origen (SNAT) son un recurso agotable. Esta métrica puede proporcionar una indicación de la dependencia que su aplicación tiene de SNAT en los flujos salientes originados. Los contadores de los flujos de salida de SNAT que se realizaron con éxito y los que tuvieron algún error se notifican y se pueden utilizar para solucionar problemas y comprender el estado de los flujos de salida.| Media |
| Contadores de bytes |  Equilibrador de carga interno y público | Load Balancer Estándar informa de los datos procesados por front-end.| Media |
| Contadores de paquetes |  Equilibrador de carga interno y público | Load Balancer Estándar informa de los paquetes procesados por front-end.| Media |

### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Visualización de las métricas del equilibrador de carga en Azure Portal

Azure Portal expone las métricas del equilibrador de carga en la página Métricas (versión preliminar) que está disponible en la página de recursos del equilibrador de carga de un recurso específico y también en la página de Azure Monitor. 

Para ver las métricas de los recursos de Load Balancer Estándar:
1. Vaya a la página Métricas (versión preliminar) y realice una de las siguientes acciones:
   * En la página de recursos de equilibrador de carga, seleccione el tipo de métrica en la lista desplegable.
   * En la página de Azure Monitor, seleccione el recurso del equilibrador de carga.
2. Configure el tipo de agregación adecuado.
3. Opcionalmente, configure el filtrado y la agrupación necesarios.

![Versión preliminar de Métricas para Load Balancer Estándar](./media/load-balancer-standard-diagnostics/LBMetrics1.png)

*Figura: Métrica de disponibilidad de DIP o estado de sondeo de mantenimiento para Load Balancer Estándar*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Recuperación de las métricas multidimensionales mediante programación con API

Para obtener orientación de API para recuperar las definiciones y los valores de las métricas multidimensionales, consulte el [tutorial sobre la API REST de supervisión de Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api).

### <a name = "DiagnosticScenarios"></a>Escenarios comunes de diagnóstico y vistas recomendadas

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>¿Está lista y disponible la ruta de acceso de datos para VIP del equilibrador de carga?

La métrica de disponibilidad de VIP describe el mantenimiento de la ruta de acceso a los datos dentro de la región al host de proceso donde se encuentran las máquinas virtuales. Es un reflejo del mantenimiento de la infraestructura de Azure. Puede usar esta métrica para:
- Supervisar la disponibilidad externa del servicio
- Profundizar y decidir si la plataforma en la que se implementa el servicio es correcta o si el sistema operativo invitado o la instancia de la aplicación son correctas.
- Determinar si un evento está relacionado con el servicio o con el plano de datos subyacente. No debe confundirse esta métrica con el estado de sondeo de mantenimiento ("disponibilidad de DIP").

Para obtener la disponibilidad de VIP para los recursos de Load Balancer Estándar:
1. Asegúrese de que está seleccionado el recurso del equilibrador de carga correcto. 
2. Seleccione **Disponibilidad de VIP** en la lista desplegable **Métrica**. 
3. En la lista desplegable **Agregación**, seleccione **Promedio**. 
4. Además, agregue un filtro en la dirección VIP o el puerto de VIP como dimensión con la dirección IP de front-end o puerto de front-end requerido y agrúpelos por la dimensión seleccionada.

![Sondeo de VIP](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Figura: Detalles de sondeo de VIP de Load Balancer*

La métrica se genera mediante una medición activa en la banda. Un servicio de sondeo dentro de la región origina el tráfico para la medida. El servicio se activa tan pronto como cree una implementación con un front-end público, y continúa hasta que quite el front-end. 

>[!NOTE]
>No se admiten front-end internos en este momento. 

Se genera periódicamente un paquete que coincide con el front-end y la regla de la implementación. Recorre la región desde el origen hasta el host, donde se encuentra una máquina virtual en el grupo back-end. La infraestructura del equilibrador de carga llevará a cabo las mismas operaciones de traducción y equilibrio de carga que con el tráfico restante. Este sondeo está en la banda en el punto de conexión de la carga equilibrada. Una vez que el sondeo llega al host Compute donde se encuentra una máquina virtual correcta en el grupo de servidores back-end, el host Compute genera una respuesta al servicio de sondeo. La máquina virtual no ve este tráfico.

Se produce un error en la disponibilidad de VIP por las razones siguientes:
- Para la implementación no queda ninguna máquina virtual correcta en el grupo de servidores back-end. 
- Se ha producido una interrupción de la infraestructura.

Puede usar la [métrica de disponibilidad de VIP junto con el estado de sondeo de mantenimiento](#vipavailabilityandhealthprobes) con fines de diagnóstico.

Use **Average** (Promedio) como agregación para la mayoría de los escenarios.

#### <a name="are-the-back-end-instances-for-my-vip-responding-to-probes"></a>¿Responden las instancias de back-end de mi VIP a los sondeos?

La métrica de estado de sondeo de mantenimiento describe el mantenimiento de la implementación de la aplicación según la configurara al configurar el sondeo de mantenimiento del equilibrador de carga. El equilibrador de carga usa el estado de sondeo de mantenimiento para determinar dónde se deben enviar flujos nuevos. Los sondeos de mantenimiento se originan en una dirección de infraestructura de Azure y se ven en el sistema operativo invitado de la máquina virtual.

Para obtener la disponibilidad de DIP para los recursos de Load Balancer Estándar:
1. Seleccione la métrica **Disponibilidad de DIP** con **Promedio** como tipo de agregación. 
2. Aplique un filtro en la dirección IP o puerto de VIP necesarios.

![Disponibilidad de DIP](./media/load-balancer-standard-diagnostics/LBMetrics-DIPAvailability.png)

*Figura: Disponibilidad de VIP para Load Balancer*

Se producirá un error en el sondeo de mantenimiento por los motivos siguientes:
- Se configura un sondeo de mantenimiento en un puerto que no está escuchando o no responde, o que usa un protocolo incorrecto. Si el servicio utiliza reglas DSR (IP flotante), debe asegurarse de que el servicio está escuchando en la dirección IP de la configuración de IP de la NIC y no solo en el bucle invertido configurado con la dirección IP de front-end.
- El grupo de seguridad de red, el firewall del sistema operativo invitado de la máquina virtual o los filtros de nivel de aplicación no permiten el sondeo.

Use **Average** (Promedio) como agregación para la mayoría de los escenarios.

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>¿Cómo se comprueban las estadísticas de conexión saliente? 

La métrica de conexiones SNAT describe el volumen de conexiones correctas y con errores para los [flujos salientes](https://aka.ms/lboutbound).

Un volumen de conexión con errores mayor que cero indica el agotamiento de los puertos SNAT. Debe investigar en profundidad para determinar las posibles causas de estos errores. El agotamiento de los puertos SNAT se manifiesta como error al establecer un [flujo saliente](https://aka.ms/lboutbound). Revise el artículo sobre las conexiones salientes para comprender los escenarios y los mecanismos en el trabajo, y aprender a mitigar o diseñar para evitar el agotamiento de los puertos SNAT. 

Para obtener las estadísticas de conexión SNAT:
1. Seleccione el tipo de métrica **Conexiones SNAT** y **Suma** como agregación. 
2. Agrupe por **Estado de conexión** para el recuento de conexiones SNAT correctas y con errores representadas por diferentes líneas. 

![Conexión SNAT](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Figura: Recuento de conexiones SNAT para Load Balancer*


#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>¿Cómo se comprueban los intentos de conexión entrantes y salientes de mi servicio?

La métrica de paquetes SYN describe el volumen de los paquetes TCP SYN, que han llegado o que se enviaron (para [flujos salientes](https://aka.ms/lboutbound)), asociado con un front-end determinado. Esta métrica se puede utilizar para entender los intentos de conexión TCP al servicio.

Use **Total** como agregación para la mayoría de los escenarios.

![Conexión SYN](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Figura: Recuento de SYN para Load Balancer*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>¿Cómo se comprueba el consumo de ancho de banda de la red? 

La métrica de contadores de bytes/paquetes describe el volumen de bytes y paquetes enviados o recibidos por el servicio por front-end.

Use **Total** como agregación para la mayoría de los escenarios.

Para obtener las estadísticas de recuento de bytes o paquetes:
1. Seleccione el tipo de métrica **Recuento de bytes** o **Recuento de paquetes** con **Promedio** como agregación. 
2. Realice cualquiera de las siguientes acciones:
   * Aplique un filtro en una IP de front-end específica, un puerto front-end, una IP de back-end o un puerto de back-end.
   * Obtenga estadísticas generales para los recursos del equilibrador de carga sin filtrado.

![Recuento de bytes](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Figura: Recuento de bytes para Load Balancer*

#### <a name = "vipavailabilityandhealthprobes"></a>¿Cómo se diagnostica la implementación de Load Balancer?

El uso de una combinación de las métricas de disponibilidad de VIP y los sondeos de mantenimiento en un único gráfico permite identificar dónde buscar el problema para resolverlo. Puede estar seguro de que Azure funciona correctamente y basarse en esta certeza para determinar de forma concluyente la configuración o la aplicación con la causa principal.

Puede utilizar las métricas de sondeo de mantenimiento para entender cómo Azure ve el mantenimiento de la implementación según la configuración que ha proporcionado. Examinar los sondeos de mantenimiento siempre es un primer paso importante para supervisar o determinar una causa.

Puede dar otro paso y usar las métricas de disponibilidad de VIP para comprender mejor cómo Azure ve el mantenimiento del plano de datos subyacente responsable de la implementación específica. Cuando se combinan ambas métricas, se puede determinar el lugar del error, como se ilustra en este ejemplo:

![Diagnóstico de VIP](./media/load-balancer-standard-diagnostics/LBMetrics-DIPnVIPAvailability.png)

*Figura: Combinación de las métricas de disponibilidad DIP y VIP*

En este gráfico se muestra la siguiente información:
- La infraestructura era correcta, la infraestructura que hospeda las máquinas virtuales era alcanzable y más de una máquina virtual se colocó en el back-end. Esta información se indica mediante el seguimiento azul para la disponibilidad de VIP, que se muestra al 100 %. 
- Sin embargo, el estado de sondeo de mantenimiento (disponibilidad de DIP) es del 0 % al principio del gráfico, según se indica en el seguimiento de color naranja. El área en un círculo verde resalta dónde el estado (disponibilidad de DIP) cambió a correcto y en qué momento la implementación del cliente pudo aceptar nuevos flujos.

El gráfico permite a los clientes solucionar los problemas de la implementación por sí mismos, sin necesidad de adivinar o solicitar soporte técnico si se produjeron otros problemas. El servicio no estaba disponible porque los sondeos de mantenimiento producían errores debidos a una configuración incorrecta o errores en la aplicación.

### <a name = "Limitations"></a>Limitaciones

La disponibilidad de VIP actualmente solo está disponible para front-ends públicos.

## <a name = "ResourceHealth"></a>Estado de mantenimiento de los recursos

El estado de mantenimiento de los recursos de Load Balancer Estándar se expone en **Mantenimiento de los recursos**, en **Supervisar > Estado del servicio**.

>[!NOTE]
>El estado de mantenimiento de los recursos de Load Balancer está disponible actualmente solo para la configuración pública de Load Balancer Estándar. Los recursos internos del equilibrador de carga o la SKU de nivel básico de los recursos de Load Balancer no exponen el mantenimiento de los recursos.

Para ver el mantenimiento de los recursos públicos de Load Balancer Estándar:
1. Seleccione **Monitor** > **Service Health**.

   ![Página Supervisar](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Figura: Vínculo de Service Health en Azure Monitor*

2. Seleccione **Resource Health** y asegúrese de que **Id. de suscripción** y **Resource Type = Load Balancer** (Tipo de recurso = Load Balancer) están seleccionados.

   ![Estado de mantenimiento de los recursos](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Figura: Selección de un recurso para ver su mantenimiento*

3. Haga clic en el recurso de Load Balancer de la lista para ver sus datos históricos de estado de mantenimiento.

    ![Estado de mantenimiento de Load Balancer](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Figura: Vista del mantenimiento de los recursos de Load Balancer*
 
En la tabla siguiente se enumeran los estados de mantenimiento de varios recursos y su descripción. 

| Estado de mantenimiento de los recursos | DESCRIPCIÓN |
| --- | --- |
| Disponible | El recurso de Load Balancer Estándar público está listo y disponible. |
| No disponible | El recurso de Load Balancer Estándar público no es correcto. Diagnostique el estado seleccionando **Azure Monitor** > **Métricas**.<br>(El estado *No disponible* también puede significar que el recurso no está conectado al Load Balancer Estándar público). |
| Desconocido | El estado mantenimiento del recurso para Load Balancer Estándar público aún no se ha actualizado.<br>(El estado *Desconocido* también puede significar que el recurso no está conectado al Load Balancer Estándar público).  |

## <a name="next-steps"></a>Pasos siguientes

- Más información acerca de [Load Balancer Estándar](load-balancer-standard-overview.md).
- Más información sobre la [conectividad saliente de Load Balancer](https://aka.ms/lboutbound).



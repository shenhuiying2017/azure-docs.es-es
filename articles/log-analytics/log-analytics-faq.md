---
title: Preguntas frecuentes de Log Analytics | Microsoft Docs
description: Respuestas a las preguntas frecuentes sobre el servicio Log Analytics.
services: log-analytics
documentationcenter: ''
author: bandersmsft
manager: jwhit
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: banders

---
# <a name="log-analytics-faq"></a>Preguntas frecuentes sobre Log Analytics
En este artículo de Microsoft, se presenta una lista de preguntas frecuentes sobre Log Analytics en Microsoft Operations Management Suite (OMS). Si tiene alguna pregunta adicional sobre Log Analytics, vaya al [foro de discusión](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) y publíquela. Alguien de nuestra comunidad le ayudará a obtener respuestas. Si una pregunta es frecuente, se agregará a este artículo para que se pueda encontrar de forma rápida y sencilla.

## <a name="general"></a>General
**P. ¿Qué comprobaciones llevan a cabo las soluciones AD y Evaluación de SQL?**

a. La consulta siguiente muestra una descripción de todas las comprobaciones que se llevan a cabo actualmente:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Los resultados pueden exportarse a Excel para su revisión.

**P.: ¿Por qué se ve algo distinto a *OMS* en la sección de administración de SCOM?**

R.: En función del paquete acumulativo de actualizaciones de SCOM que use, es posible que vea un nodo para *System Center Advisor*, *Operational Insights* o *Log Analytics*.

La actualización de la cadena de texto de *OMS* se incluye en un módulo de administración, que debe importarse manualmente. Siga las instrucciones que encontrará en el artículo de KB del paquete acumulativo de actualizaciones de SCOM más reciente y actualice la consola DE OMS para ver las últimas actualizaciones en el nodo de *OMS* .

**P.: ¿existe una versión *local* de OMS?**

R.: No. Log Analytics procesa y almacena enormes cantidades de datos. En tanto servicio en la nube, Log Analytics es capaz de escalar verticalmente en caso necesario y, así, evitar cualquier impacto en el rendimiento de su entorno.

Esto también lo libera de la obligación de velar por el constante funcionamiento de la infraestructura de Log Analytics. Por último, en este modelo, puede recibir correcciones y actualizaciones de características frecuentes.

## <a name="configuration"></a>Configuración
**P. ¿Puedo cambiar el nombre del contenedor de blobs o tablas usado para leer desde Diagnósticos de Azure (WAD)?**  

R:  No, esta opción no es posible en estos momentos, pero está planeada para futuras versiones.

**P. ¿Qué direcciones IP usan los servicios de OMS? ¿Cómo me aseguro de que el firewall solo permita el tráfico hacia los servicios de OMS?**  

a. El servicio de Log Analytics se basa en Azure y los puntos de conexión reciben direcciones IP comprendidas en los [intervalos de direcciones IP de centros de datos de Microsoft Azure](http://www.microsoft.com/download/details.aspx?id=41653).

Cuando se realizan implementaciones de servicios, cambian las direcciones IP reales de los servicios de OMS. Los nombres DNS que debe permitir el firewall se documentan en [Configuración del proxy y del firewall (opcional) en Log Analytics](log-analytics-proxy-firewall.md).

**P. Uso ExpressRoute para realizar la conexión con Azure. ¿Usará el tráfico de Log Analytics la conexión ExpressRoute?**  

a. En la [documentación de ExpressRoute](../expressroute/expressroute-faqs.md#supported-services), se describen los distintos tipos de tráfico de dicho servicio.

El tráfico dirigido a Log Analytics utiliza el circuito de ExpressRoute de emparejamiento público.

**P. ¿Hay alguna manera fácil y directa de transferir un área de trabajo existente de Log Analytics a otra suscripción de Azure o a otra área de trabajo de Log Analytics?**   Estamos realizando pruebas y evaluaciones con áreas de trabajo de OMS de varios clientes en nuestra suscripción de Azure; pero, como van a pasar a la fase de producción, nuestro objetivo es realizar su migración a su propia suscripción de Azure u OMS.  

a. El cmdlet `Move-AzureRmResource` le permitirá mover un área de trabajo de Log Analytics y también una cuenta de Automation de una suscripción de Azure a otra. Para más información, consulte [Move-AzureRmResource](http://msdn.microsoft.com/library/mt652516.aspx).

También es posible realizar este cambio en el Portal de Azure.

No se pueden mover datos de un área de trabajo de Log Analytics a otra, ni cambiar la región en la que se almacenan los datos de dicha solución.

**P.: ¿Cómo agrego OMS a SCOM?**

R.: Con la implementación del paquete acumulativo de actualizaciones más reciente y la importación de los módulos de administración correspondientes, podrá conectar SCOM con Log Analytics.

Tenga en cuenta que la conexión de SCOM con Log Analytics solo está disponible para SCOM 2012 SP1 y versiones posteriores.

**P.: ¿Cómo puedo confirmar la comunicación entre un agente y Log Analytics?**

R.: Para asegurarse de que el agente pueda comunicarse con OMS, vaya a Panel de control, Security & Settings (Seguridad y configuración), **Microsoft Monitoring Agent**.

En la pestaña **Azure Log Analytics (OMS)** , busque un icono de marca de verificación verde. Este icono confirma que el agente es capaz de comunicarse con el servicio de OMS.

Por su parte, un icono de advertencia amarillo significa que el agente está teniendo problemas de comunicación con OMS. Una causa habitual es que el servicio de Microsoft Monitoring Agent se ha detenido y debe reiniciarse.

**P.: ¿Cómo detener la comunicación entre un agente y Log Analytics?**

R.: En SCOM, quite el equipo de la lista administrada de OMS. Con ello, se detienen todas las comunicaciones de ese agente a través de SCOM. En el caso de los agentes conectados directamente a OMS, puede detener la comunicación con OMS yendo a Panel de control, Security & Settings (Seguridad y configuración), **Microsoft Monitoring Agent**.
Quite todas las áreas de trabajo enumeradas en **Azure Log Analytics (OMS)**.

## <a name="agent-data"></a>Datos del agente
**P. ¿Qué cantidad de datos puedo enviar a Log Analytics a través del agente? ¿Hay un volumen máximo de información por cliente?**  
a. El plan gratuito establece un límite diario de 500 MB por área de trabajo. Los planes estándar y premium carecen de límites en cuanto a la cantidad de datos cargada. En tanto servicio en la nube, Log Analytics de OMS se ha concebido para escalarse verticalmente de forma automática con el fin de hacer frente al volumen procedente de un cliente (aun cuando se trate de varios terabytes diarios).

El agente de Log Analytics se ha diseñado con el mínimo tamaño en mente, por lo que lleva a cabo tareas de compresión básica de los datos. Uno de nuestros clientes escribió un blog acerca de las pruebas que realizó con nuestro agente y los excelentes resultados obtenidos. El volumen de datos variará en función de las soluciones que habiliten los clientes. Puede encontrar detalles sobre el volumen de datos y ver el desglose por solución bajo el icono de **uso** de la página de información general de OMS.

Para obtener más información, puede leer el [blog de un cliente](http://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) sobre el escaso espacio que consume el agente de OMS.

**P. ¿Cuánto ancho de banda de red usa el Agente de administración de Microsoft (MMA) al enviar datos a Log Analytics?**

a. El ancho de banda equivale a una función de la cantidad de datos enviados. Los datos se comprimen al enviarse por la red.

**P. ¿Qué cantidad de datos se envía por agente?**

a. El volumen depende en gran medida de los siguientes factores:

* Las soluciones habilitadas
* El número de registros y contadores de rendimiento recopilados
* El volumen de datos de los registros

El plan de tarifas gratuito constituye una buena forma de incorporar varios servidores y estimar el volumen de datos típico. El uso general se muestra en la página **Uso** .
En el caso de los equipos capaces de ejecutar al agente de WireData, podrá ver la cantidad de datos enviados mediante la siguiente consulta:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```



## <a name="next-steps"></a>Pasos siguientes
* [Introducción a Log Analytics](log-analytics-get-started.md) , encontrará más información sobre esta solución y cómo empezar a utilizarla en cuestión de minutos.

<!--HONumber=Oct16_HO2-->



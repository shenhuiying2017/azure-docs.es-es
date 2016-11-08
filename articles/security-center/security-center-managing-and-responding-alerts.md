---
title: Administración y respuesta a las alertas de seguridad en el Centro de seguridad de Azure | Microsoft Docs
description: Este documento le ayuda a usar las capacidades del Centro de seguridad de Azure para administrar y responder a las alertas de seguridad.
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: ''

ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2016
ms.author: yurid

---
# Administración y respuesta a las alertas de seguridad en el Centro de seguridad de Azure
Este documento le ayuda a usar Azure Security Center para administrar las alertas de seguridad y responder a ellas.

> [!NOTE]
> Para habilitar las detecciones avanzadas, actualice a la versión estándar de Azure Security Center. Hay una prueba de evaluación gratuita de 90 días disponible. Para realizar la actualización, seleccione el plan de tarifa en la [directiva de seguridad](security-center-policies.md). Para más información, consulte la [página de precios](https://azure.microsoft.com/pricing/details/security-center/).
> 
> 

## ¿Qué son las alertas de seguridad?
Security Center recopila, analiza e integra automáticamente los datos de registro de los recursos de Azure, la red y las soluciones de asociados conectados, como firewalls y soluciones de protección de puntos de conexión, para detectar amenazas reales y reducir los falsos positivos. En Security Center, se muestra una lista de alertas de seguridad prioritarias, junto con la información que necesita para investigar rápidamente y recomendaciones para corregir un ataque. Azure Security Center también agrupa las alertas que se alinean para terminar los patrones de cadena en el artículo sobre [incidentes](security-center-incident.md).

> [!NOTE]
> Para más información acerca de cómo actúan las funcionalidades de detección de Security Center, consulte [Funcionalidades de detección de Azure Security Center](security-center-detection-capabilities.md).
> 
> 

## Administración de alertas de seguridad
Puede revisar las alertas actuales en el icono **Alertas de seguridad**. Abra el Portal de Azure y siga los pasos siguientes para ver más detalles sobre cada alerta:

1. En el panel Security Center, verá el icono **Alertas de seguridad**.
   
    ![Icono Alertas de seguridad en Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-ga.png)
2. Haga clic en el icono para abrir la hoja **Alertas de seguridad**, que contiene más detalles acerca de las alertas, como se muestra a continuación.
   
   ![La hoja Alertas de seguridad en Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-ga.png)

En la parte inferior de esta hoja aparecen los detalles de cada alerta. Para ordenar, haga clic en la columna que desea ordenar. A continuación se muestra la definición de cada columna:

* **Alerta**: una breve explicación de la alerta.
* **Recuento**: una lista de todas las alertas de este tipo específico que se han detectado en un día concreto.
* **Detectado por**: el servicio responsable de desencadenar la alerta.
* **Fecha**: la fecha en que se ha producido el evento.
* **Estado**: el estado actual de esa alerta. Existen dos tipos de servicios:
  
  * **Activa**: se ha detectado la alerta de seguridad.
  * **Descartada**: el usuario ha descartado la alerta de seguridad. Este estado suele utilizarse para alertas que se han investigado pero que no se han mitigado, o se ha observado que no plantean un ataque real.
* **Gravedad**: el nivel de gravedad, que puede ser alto, medio o bajo.

### Filtrado de alertas
Puede filtrar alertas en función de la fecha, el estado y la gravedad. Puede resultar útil filtrar las alertas en aquellos escenarios en que necesite restringir el ámbito de las alertas de seguridad que se muestran. Por ejemplo, podría comprobar las alertas de seguridad que se produjeron en las 24 horas anteriores, ya que se está investigando una posible brecha en el sistema.

1. Haga clic en **Filtro** en la hoja **Alertas de seguridad**. Se abre la hoja **Filtro**, donde podrá seleccionar los valores de fecha, estado y gravedad que desee ver.
   
    ![Filtrado de alertas en Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3-ga.png)
2. Después de investigar una alerta de seguridad, es posible que descubra que es un falso positivo para su entorno o que indica un comportamiento normal de un recurso determinado. En cualquier caso, si determina que una alerta de seguridad no es aplicable, puede descartarla y después filtrarla de la vista. Hay dos formas de descartar una alerta de seguridad. Haga clic con el botón derecho en una alerta y seleccione **Descartar**, o bien mantenga el puntero sobre un elemento, haga clic en los tres puntos que aparecen a la derecha y seleccione **Descartar**. Para ver las alertas de seguridad descartadas, haga clic en **Filtro** y seleccione **Descartada**.
   
   ![Descarte de alertas en Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig4-ga.png)

### Responder a alertas de seguridad
Seleccione una alerta de seguridad para ver más información sobre el evento o los eventos que la desencadenaron y, si existen, los pasos que debe seguir para corregir un ataque. Las alertas de seguridad se agrupan según el tipo y la fecha. Haga clic en una alerta de seguridad y se abrirá una hoja que contiene una lista de las alertas agrupadas.

![Responder a alertas de seguridad en el Centro de seguridad de Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-ga.png)

En este caso, las alertas desencadenadas hacen referencia a las actividades sospechosas del Protocolo de escritorio remoto (RDP). En la primera columna se muestran los recursos atacados, en la segunda, las veces que el recurso fue atacado, en la tercera, la hora en la que se produjo el ataque, en la cuarta, el estado de la alerta y, en la quinta, la gravedad del ataque. Después de revisar esta información, haga clic en el recurso atacado y se abrirá una nueva hoja.

![Sugerencias sobre qué hacer con las alertas de seguridad en el Centro de seguridad de Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-ga.png)

En el campo **Descripción** de esta hoja encontrará más detalles sobre este evento. Estos detalles adicionales ofrecen información detallada sobre lo que activó la alerta de seguridad, el recurso de destino, la dirección IP de origen si corresponde, y recomendaciones sobre cómo corregirla. En algunos casos, la dirección IP de origen estará vacía (no disponible) porque no todos los registros de eventos de seguridad de Windows incluyen la dirección IP.

La corrección sugerida por el Centro de seguridad variará según la alerta de seguridad. En algunos casos, tendrá que utilizar otras capacidades de Azure para implementar la corrección recomendada. Por ejemplo, la solución para este ataque consiste en colocar la dirección IP que lo está generando en la lista negra mediante una [ACL de red](../virtual-network/virtual-networks-acl.md) o una regla de [grupo de seguridad de red](../virtual-network/virtual-networks-nsg.md).

> [!NOTE]
> Para más información sobre los distintos tipos de alertas, consulte [Alertas de seguridad por tipo en Azure Security Center](security-center-alerts-type.md).
> 
> 

## Otras referencias
En este documento ha aprendido a configurar directivas de seguridad en el Centro de seguridad. Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Control de incidentes de seguridad en Azure Security Center](security-center-incident.md)
* [Funcionalidades de detección de Azure Security Center](security-center-detection-capabilities.md)
* [Guía de planeamiento y operaciones de Azure Security Center](security-center-planning-and-operations-guide.md)
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md): encuentre las preguntas más frecuentes sobre el uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): encuentre publicaciones en el blog sobre el cumplimiento y la seguridad de Azure.

<!----HONumber=AcomDC_0921_2016-->
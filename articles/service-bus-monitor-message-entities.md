<properties linkid="service-bus-monitor-messaging-entitites" urlDisplayName="Traffic Manager" pageTitle="Monitor Service Bus Messaging Entities - Azure" metaKeywords="" description="Learn how to monitor your Service Bus entities using the Azure Management Portal." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="service-bus" documentationCenter="" title="How to Monitor Service Bus Messaging Entities" authors="sethm" solutions="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="sethm" />

# Supervisión de entidades de mensajería del bus de servicio

En este tema se describe cómo administrar y supervisar las entidades del bus de servicio mediante el [Portal de administración de Azure][Portal de administración de Azure]. Con el portal, se obtiene una visión integral del estado de las colas y los temas. También puede supervisar su uso.

## Supervisión de la actividad en las colas del bus de servicio

Para supervisar una cola del bus de servicio, realice lo siguiente:

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure].
2.  Haga clic en el icono **Bus de servicio** en la barra de navegación de la izquierda para obtener la lista de los espacios de nombres del servicio.
3.  Haga clic en el espacio de nombres que contiene la cola que desea supervisar.
4.  En la barra dinámica de la parte superior de la página, haga clic en **Colas**.
5.  Haga clic en el nombre de la cola que desea supervisar. Aparecerá el panel de colas.
6.  Puede ver las actividades en las colas que se han creado. Esta información puede verla en varias ventanas de tiempo. El valor predeterminado es 1 hora, pero puede hacer clic en el menú desplegable junto a la hora para elegir una ventana de tiempo diferente: las últimas 24 horas, los últimos 7 días o los últimos 30 días. Puede ver los datos con una precisión mínima de puntos de medición de 5 minutos para la ventana de una hora, 1 hora para la ventana de 24 horas y 1 día para las ventanas de 7 y 30 días.

Para cualquier cola, puede ver los gráficos de:

-   **Mensajes entrantes**: cantidad de mensajes en cola durante este intervalo de tiempo.
-   **Mensaje salientes**: cantidad de mensajes que han salido de la cola durante este intervalo de tiempo.
-   **Longitud**: cantidad de mensajes en la entidad al final de este intervalo de tiempo.
-   **Tamaño**: espacio de almacenamiento (en MB) que utiliza esta entidad al final de este intervalo de tiempo.

### Vista rápida

**Vista rápida** en el panel refleja el tamaño actual de la cola como **Longitud de la cola**. También muestra otras propiedades de la cola o el tema. Esta información se actualiza cada 10 segundos.

![][0]

## Supervisión de la actividad en temas del bus de servicio

Para supervisar un tema del bus de servicio, realice lo siguiente:

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure].
2.  Haga clic en el icono **Bus de servicio** en la barra de navegación de la izquierda para obtener la lista de los espacios de nombres del servicio.
3.  Haga clic en el espacio de nombres que contiene el tema que desea supervisar.
4.  En la barra dinámica de la parte superior de la página, haga clic en **Temas**.
5.  Haga clic en el nombre del tema que desea supervisar. Aparecerá el panel de temas.

Un panel de temas es similar a un panel de colas, salvo por las métricas de uso. Los mensajes salientes y la longitud no están presentes en el panel de temas, ya que esa información podría ser diferente para cada una de las suscripciones de un tema. La pestaña **Monitor** le permite agregar métricas de uso (cantidad de mensajes salientes y longitud), por suscripción de tema. Para agregar estas métricas, haga clic en la pestaña **Monitor**. A continuación, haga clic en **Agregar métricas** en la parte inferior de la página y, a continuación, seleccione entre las suscripciones del tema.

![][1]

  [Portal de administración de Azure]: http://manage.windowsazure.com
  [0]: ./media/service-bus-monitor-message-entities/QueueDashboard.png
  [1]: ./media/service-bus-monitor-message-entities/AddMetrics.png

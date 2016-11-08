---
title: Alertas en tiempo real en la red CDN de Azure | Microsoft Docs
description: Alertas en tiempo real en la red CDN de Microsoft Azure. Las alertas en tiempo real proporcionan notificaciones acerca del rendimiento de los puntos de conexión en un perfil de la red CDN.
services: cdn
documentationcenter: ''
author: camsoper
manager: erikre
editor: ''

ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2016
ms.author: casoper

---
# Alertas en tiempo real en la red CDN de Microsoft Azure
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## Información general
En este documento se explican las alertas en tiempo real en la red CDN de Microsoft Azure. Esta funcionalidad proporciona notificaciones en tiempo real acerca del rendimiento de los puntos de conexión en un perfil de la red CDN. Las alertas de correo o de HTTP se pueden configurar por:

* Ancho de banda
* Códigos de estado
* Estados de la memoria caché
* Conexiones

## Creación de una alerta en tiempo real
1. En el [Portal de Azure](https://portal.azure.com), vaya a su perfil de la red CDN.
   
    ![Hoja del perfil de red CDN](./media/cdn-real-time-alerts/cdn-profile-blade.png)
2. En la hoja de perfil de la red CDN, haga clic en el botón **Administrar**.
   
    ![Botón de administración de hoja de perfil de red CDN](./media/cdn-real-time-alerts/cdn-manage-btn.png)
   
    Se abre el Portal de administración de CDN.
3. Mantenga el cursor sobre la pestaña **Análisis** y después sobre el control flotante **Estadísticas en tiempo real**. Haga clic en **Real-Time Alerts** (Alertas en tiempo real).
   
    ![Portal de administración de la red CDN](./media/cdn-real-time-alerts/cdn-premium-portal.png)
   
    Se muestra la lista de las configuraciones de alerta existentes (si hay alguna).
4. Haga clic en el botón **Add Alert** (Agregar alerta).
   
    ![Botón Add Alert (Agregar alerta)](./media/cdn-real-time-alerts/cdn-add-alert.png)
   
    Se muestra un formulario para crear una alerta nueva.
   
    ![Formulario de alerta nueva](./media/cdn-real-time-alerts/cdn-new-alert.png)
5. Si desea que esta alerta se active al hacer clic en **Save** (Guardar), active la casilla **Alert Enabled** (Alerta habilitada).
6. Escriba un nombre descriptivo para la alerta en el campo **Name** (Nombre).
7. En la lista desplegable **Media Type** (Tipo de medio), seleccione **HTTP Large Object** (Objeto HTTP grande).
   
    ![Tipo de medio con objetos HTTP grande seleccionado](./media/cdn-real-time-alerts/cdn-http-large.png)
   
   > [!IMPORTANT]
   > Debe seleccionar **HTTP Large Object** (Objeto HTTP grande) en **Media Type** (Tipo de medio). **CDN de Azure de Verizon** no usa las restantes opciones. Si no se selecciona **HTTP Large Object** (Objeto HTTP grande), la alerta nunca se desencadenará.
   > 
   > 
8. Cree la **expresión** que se va a supervisar; para ello, es preciso seleccionar una **métrica**, un **operador** y un **valor desencadenador**.
   
   * En **Metric** (Métrica), seleccione el tipo de condición que desea supervisar. **Bandwidth Mbps** (Mbps de ancho de banda) es la cantidad de uso del ancho de banda, en megabits por segundo. **Total Connections** (Total de conexiones) es el número de conexiones HTTP simultáneas a nuestros servidores perimetrales. Para ver las definiciones de los diversos estados de la memoria caché y códigos de estado, consulte [Azure CDN Cache Status Codes](https://msdn.microsoft.com/library/mt759237.aspx) (Códigos de estado de la memoria caché de la red CDN de Azure) y [Azure CDN HTTP Status Codes](https://msdn.microsoft.com/library/mt759238.aspx) (Códigos de estado HTTP de la red CDN de Azure)
   * **Operator** (Operador) es el operador matemático que establece la relación entre la métrica y el valor del desencadenador.
   * **Trigger Value** (Valor de desencadenador) es el valor de umbral que debe cumplirse para enviarse una notificación.
     
     En el ejemplo siguiente, la expresión creada indica el deseo de recibir notificación cuando el número de códigos de estado 404 es superior a 25.
     
     ![Expresión de ejemplo de alerta en tiempo real](./media/cdn-real-time-alerts/cdn-expression.png)
9. En **Interval** (Intervalo), especifique la frecuencia con que desea que se evalúe la expresión.
10. En la lista desplegable **Notify on** (Notificar el), seleccione si desea recibir una notificación cuando la expresión sea verdadera.
    
    * **Condition Start** (Inicio de condición) indica que se enviará una notificación la primera vez que se detecte la condición especificada.
    * **Condition End** (Fin de condición) indica que se enviará una notificación cuando la condición especificada deje de detectarse. Esta notificación solo se puede desencadenar después de que nuestro sistema de supervisión de red detecta que se ha producido el problema especificado.
    * **Continuous** (Continuo) indica que se enviará una notificación cada vez que el sistema de supervisión de red detecte el problema especificado. Tenga en cuenta que el sistema de supervisión de red solo comprobará una vez por intervalo si existe el problema especificado.
    * **Condition Start and End** (Inicio y fin de condición) indica que se enviará una notificación la primera vez que se detecte el problema especificado y se volverá a enviar cuando deje de detectarse.
11. Si desea recibir notificaciones por correo electrónico, active la casilla **Notify by Email** (Notificar por correo electrónico).
    
    ![Formulario de notificación por correo electrónico](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    En el campo **To** (Para), escriba la dirección de correo electrónico a la que desea que se envíen las notificaciones. En **Subject** (Asunto) y **Body** (Cuerpo), puede dejar los valores predeterminado, o bien puede personalizar el mensaje mediante la lista **Available keywords** (Palabras clave disponibles) para insertar dinámicamente los datos de las alertas cuando se envía el mensaje.
    
    > [!NOTE]
    > Para probar la notificación que se va a enviar por correo electrónico, haga clic en el botón **Test Notification** (Probar notificación), pero solo después de que la configuración de alertas se haya guardado.
    > 
    > 
12. Si desea que las notificaciones se publique en un servidor web, active la casilla **Notify by HTTP Post** (Notificar mediante HTTP Post).
    
    ![Formulario de notificación de HTTP Post](./media/cdn-real-time-alerts/cdn-notify-http.png)
    
    En el campo **Url**, escriba la dirección URL en que desea que se publique el mensaje HTTP. En el cuadro de texto **Headers** (Encabezados), escriba los encabezados HTTP que se enviarán en la solicitud. En **Body** (Cuerpo), puede personalizar el mensaje mediante la lista **Available keywords** (Palabras clave disponibles) para insertar dinámicamente los datos de las alertas cuando se envía el mensaje. Los valores predeterminados de **Headers** (Encabezados) y **Body** (Cuerpo) son una carga XML similar a la del ejemplo siguiente.
    
    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```
    
    > [!NOTE]
    > Para probar la notificación mediante HTTP Post, haga clic en el botón **Test Notification** (Probar notificación), pero solo después de que la configuración de alertas se haya guardado.
    > 
    > 
13. Haga clic en el botón **Save** (Guardar) la configuración de las alertas. Si activó **Alert Enabled** (Alerta habilitada) en el paso 5, la alerta ya está activa.

## Pasos siguientes
* Análisis de [estadísticas en tiempo real en la CDN de Microsoft Azure](cdn-real-time-stats.md)
* Mayor profundización con [Informes de HTTP avanzados en la red CDN de Microsoft Azure](cdn-advanced-http-reports.md)
* [Análisis de patrones de uso de la red CDN de Azure](cdn-analyze-usage-patterns.md)

<!---HONumber=AcomDC_0824_2016-->
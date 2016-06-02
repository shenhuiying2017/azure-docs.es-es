## Creación de un Centro de eventos

1. Inicie sesión en el [Portal de Azure clásico][] y haga clic en **NUEVO** en la parte inferior de la pantalla.

2. Haga clic en **Servicios de aplicaciones**, **Bus de servicio**, **Centro de eventos** y, finalmente, en **Creación rápida**.

	![][1]

3. Escriba un nombre para el centro de eventos, seleccione la región deseada y, a continuación, haga clic en **Crear un centro de eventos**.

	![][2]

4. Si no seleccionó explícitamente un espacio de nombres existente de una región determinada, el portal crea un espacio de nombres (normalmente ***nombre del centro de eventos*-ns**). Haga clic en dicho espacio de nombres (en este ejemplo, **eventhub-ns**).

	![][3]

5. En la parte inferior de la página, haga clic en **Información de conexión**. Haga clic en el botón Copiar (se muestra en la figura siguiente) para copiar la cadena de conexión **RootManageSharedAccessKey** al Portapapeles. Guarde esta cadena de conexión para usarla más adelante en el tutorial.

	![][4]

Ya se ha creado Centro de eventos y cuenta con las cadenas de conexión que necesita para enviar y recibir eventos. 

[1]: ./media/event-hubs-create-event-hub/create-event-hub1.png
[2]: ./media/event-hubs-create-event-hub/create-event-hub2.png
[3]: ./media/event-hubs-create-event-hub/create-event-hub3.png
[4]: ./media/event-hubs-create-event-hub/create-conn-str1.png



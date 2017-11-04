---
title: "Automatización de procesos de Azure Application Insights con Logic Apps"
description: "Obtenga información sobre cómo automatizar rápidamente los procesos repetibles mediante la adición de Application Insights Connector a la aplicación lógica."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: mbullwin
ms.openlocfilehash: 92521bab2082f63fa01e816151e3da9400effe6d
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="automate-application-insights-processes-by-using-logic-apps"></a>Automatización de procesos de Application Insights con Logic Apps

¿Se encuentra a menudo ejecutando las mismas consultas en los datos de telemetría para comprobar si el servicio está funcionando correctamente? ¿Desea automatizar estas consultas para encontrar tendencias y anomalías, y crear sus propios flujos de trabajo en función de ellas? Azure Application Insights Connector (versión preliminar) para Logic Apps es la herramienta adecuada para este fin.

Con esta integración, se pueden automatizar numerosos procesos sin tener que escribir una sola línea de código. Puede crear una aplicación lógica con Application Insights Connector para automatizar rápidamente cualquier proceso de Application Insights. 

También puede agregar acciones adicionales. La característica Logic Apps de Azure App Service realiza cientos de acciones disponibles. Por ejemplo, si usa una aplicación lógica, puede enviar automáticamente una notificación por correo electrónico o crear un error en Visual Studio Team Services. También puede utilizar una de las muchas [plantillas](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates) disponibles para ayudar a acelerar el proceso de creación de la aplicación lógica. 

## <a name="create-a-logic-app-for-application-insights"></a>Creación de una aplicación lógica para Application Insights

En este tutorial, puede obtener información sobre cómo crear una aplicación lógica que usa el algoritmo de clústeres automáticos de Analytics para agrupar los atributos en los datos para una aplicación web. El flujo envía automáticamente los resultados por correo electrónico, y esto es solo un ejemplo de cómo puede usar Application Insights Analytics y Logic Apps conjuntamente. 

### <a name="step-1-create-a-logic-app"></a>Paso 1: Creación de una aplicación lógica
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En el panel **Nuevo**, seleccione **Web y móvil**, y, a continuación, **Aplicación lógica**.

    ![Ventana de la aplicación lógica nueva](./media/automate-with-logic-apps/logicapp1.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>Paso 2: Creación de un desencadenador para la aplicación lógica
1. En la ventana del **Diseñador de aplicación lógica**, en **Empezar con un desencadenador común**, seleccione **Periodicidad**.

    ![Ventana del Diseñador de aplicaciones lógicas](./media/automate-with-logic-apps/logicapp2.png)

2. En el cuadro **Frecuencia**, seleccione **Día** y, después, en el cuadro **Intervalo**, escriba **1**.

    ![Ventana "Recurrencia" del Diseñador de aplicaciones lógicas](./media/automate-with-logic-apps/step2b.png)

### <a name="step-3-add-an-application-insights-action"></a>Paso 3: Incorporación de una acción de Application Insights
1. Haga clic en **Nuevo paso** y, a continuación, en **Agregar una acción**.

2. En el cuadro de búsqueda **Elegir una acción**, escriba **Azure Application Insights**.

3. En **Acciones**, haga clic en **Azure Application Insights: Visualizar consulta de análisis Versión preliminar**.

    ![Ventana "Elegir una acción" del Diseñador de aplicaciones lógicas](./media/automate-with-logic-apps/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Paso 4: Conexión a un recurso de Application Insights

Para completar este paso, necesita un identificador de aplicación y una clave de API para el recurso. Puede recuperarlos desde Azure Portal, como se muestra en el diagrama siguiente:

![Identificador de aplicación en Azure Portal](./media/automate-with-logic-apps/appid.png) 

Proporcione un nombre para la conexión, el identificador de aplicación y la clave de API.

![Ventana de conexión del flujo del Diseñador de aplicaciones lógicas](./media/automate-with-logic-apps/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Paso 5: Especificación del tipo de gráfico y consulta de análisis
En el siguiente ejemplo, la consulta selecciona las solicitudes con error del último día y las pone en correlación con las excepciones que se han producido como parte de la operación. Analytics correlaciona las solicitudes erróneas en función del identificador operation_Id. La consulta, a continuación, segmenta los resultados mediante el algoritmo de clúster automático. 

Al crear sus propias consultas, asegúrese de comprobar que funcionan correctamente en Analytics antes de agregarlas al flujo.

1. En el cuadro **Consulta** , agregue la siguiente consulta de análisis: 

    ```
    requests
    | where timestamp > ago(1d)
    | where success == "False"
    | project name, operation_Id
    | join ( exceptions
        | project problemId, outerMessage, operation_Id
    ) on operation_Id
    | evaluate autocluster()
    ```

2. En el cuadro **Tipo de gráfico**, seleccione **Tabla Html**.

    ![Pantalla de configuración de consulta de Analytics](./media/automate-with-logic-apps/flow4.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>Paso 6: Configuración de la aplicación lógica para enviar correo electrónico

1. Haga clic en **Nuevo paso** y, a continuación, en **Agregar una acción**.

2. En el cuadro de búsqueda, escriba **Office 365 Outlook**.

3. Haga clic en **Office 365 Outlook: Enviar un correo electrónico**.

    ![Selección de Office 365 Outlook](./media/automate-with-logic-apps/flow2b.png)

4. En la ventana **Enviar un correo electrónico**, haga lo siguiente:

   a. Escriba la dirección de correo electrónico del destinatario.

   b. Escriba un asunto para el correo electrónico.

   c. Haga clic en cualquier parte del cuadro **Cuerpo** y, en el menú de contenido dinámico que se abre a la derecha, seleccione **Cuerpo**.

   d. Haga clic en **Mostrar opciones avanzadas**.

      ![Configuración de Office 365 Outlook](./media/automate-with-logic-apps/flow5.png)

5. En el menú de contenido dinámico, haga lo siguiente:

    a. Seleccione **Nombre de datos adjuntos**.

    b. Seleccione **Contenido de datos adjuntos**.
    
    c. En el campo **Es HTML**, seleccione **Sí**.

      ![Pantalla de configuración de correo electrónico de Office 365](./media/automate-with-logic-apps/flow7.png)

### <a name="step-7-save-and-test-your-logic-app"></a>Paso 7: Guardado y prueba de la aplicación lógica
* Haga clic en **Guardar** para guardar los cambios.

Puede esperar a que el desencadenador ejecute la aplicación lógica, o puede ejecutarla inmediatamente si selecciona **Ejecutar**.

![Pantalla de creación de la aplicación lógica](./media/automate-with-logic-apps/step7.png)

Cuando se ejecuta la aplicación lógica, los destinatarios que haya especificado en la lista de correo electrónico recibirán un correo electrónico similar al siguiente:

![Mensaje de correo electrónico de la aplicación lógica](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre cómo crear [consultas de análisis](app-insights-analytics-using.md).
- Más información acerca de [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).



<!--Link references-->






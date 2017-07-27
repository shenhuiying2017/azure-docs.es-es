---
title: "Automatice procesos de Azure Application Insights con una aplicación lógica de Azure."
description: "Obtenga información sobre cómo automatizar rápidamente los procesos repetibles mediante la adición de Application Insights Connector a la aplicación lógica de Azure."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 571a76253fa62a89f325e0c295e9a7e98e201079
ms.contentlocale: es-es
ms.lasthandoff: 07/01/2017

---

# <a name="automate-application-insights-processes-with-an-azure-logic-app"></a>Automatización de procesos de Application Insights con una aplicación lógica de Azure

¿Se encuentra a menudo ejecutando las mismas consultas en los datos de telemetría para comprobar que el servicio está funcionando correctamente? ¿Desea automatizar estas consultas para encontrar tendencias y anomalías, y crear sus propios flujos de trabajo en función de ellas? Application Insights Connector (versión preliminar) para Azure Logic Apps es exactamente lo que necesita.
Con esta integración, ahora se pueden automatizar numerosos procesos sin tener que escribir una sola línea de código. Puede crear la aplicación lógica con Application Insights Connector para automatizar rápidamente cualquier proceso de Application Insights. También puede agregar acciones adicionales. Logic Apps pone a su disposición cientos de acciones. Por ejemplo, puede enviar automáticamente una notificación por correo electrónico o crear un error en Visual Studio Team Services como parte de la aplicación lógica. También puede utilizar una de las numerosas [plantillas](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates) disponibles para Logic Apps. Estas plantillas aceleran el proceso de creación de una aplicación lógica. 

## <a name="tutorial-for-creating-an-azure-logic-app-for-application-insights"></a>Tutorial para crear una aplicación lógica de Azure para Application Insights

En este tutorial, puede obtener información sobre cómo crear una aplicación lógica de Azure que usa el algoritmo de clústeres automáticos de Analytics para agrupar los atributos en los datos para una aplicación web. El flujo envía automáticamente los resultados por correo electrónico. Es solo un ejemplo de cómo puede usar Application Insights Analytics y Azure Logic Apps conjuntamente. 

### <a name="step-1-create-a-logic-app"></a>Paso 1: Creación de una aplicación lógica
1. Inicie sesión en: https://portal.azure.com.
2. Cree una aplicación lógica; para ello, vaya al menú Nuevo/Web y móvil.

![Pantalla de la aplicación lógica nueva](./media/automate-with-logic-apps/logicapp1.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>Paso 2: Creación de un desencadenador para la aplicación lógica
1.  En el Diseñador de aplicación lógica, en Empezar con un desencadenador común, elija Periodicidad.
2.  Establezca la Frecuencia en Día con un intervalo de 1.

![Cuadro de diálogo Desencadenador de aplicación lógica](./media/automate-with-logic-apps/logicapp2.png)

![Cuadro de diálogo de frecuencia de aplicación lógica](./media/automate-with-logic-apps/step2b.png)

### <a name="step-3-add-an-azure-application-insights-action"></a>Paso 3: Incorporación de una acción de Azure Application Insights
1. Haga clic en **Nuevo paso** y, a continuación, en **Agregar una acción**.
2. Busque Azure Application Insights.
3. Haga clic en Azure Application Insights: Visualizar consulta de análisis Versión preliminar.

![Pantalla Ejecutar consulta de análisis](./media/automate-with-logic-apps/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Paso 4: Conexión a un recurso de Application Insights

**Requisito previo**

Para completar este paso, necesita un identificador de aplicación y una clave de API para el recurso. Puede recuperarlos desde Azure Portal, como se muestra en el diagrama siguiente:

![Identificador de aplicación en Azure Portal](./media/automate-with-logic-apps/appid.png) 

- Proporcione un nombre para la conexión junto con el identificador de aplicación y la clave de API.

![Pantalla de conexión de flujo](./media/automate-with-logic-apps/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Paso 5: Especificación del tipo de gráfico y consulta de análisis
Este ejemplo selecciona las solicitudes con error del último día y las pone en correlación con las excepciones que se han producido como parte de la operación. Analytics las correlaciona en función del identificador operation_Id. La consulta, a continuación, segmenta los resultados mediante el algoritmo de clúster automático. Al crear sus propias consultas, asegúrese de comprobar que funcionan correctamente en Analytics antes de agregarlas al flujo.

- Agregue la siguiente consulta de análisis y seleccione el tipo de gráfico de tabla HTML. 

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
![Pantalla de configuración de consulta de análisis](./media/automate-with-logic-apps/flow4.png)

### <a name="step-6-configure-the-app-to-send-email"></a>Paso 6: Configuración de la aplicación para enviar correo electrónico

1. Haga clic en **Nuevo paso** y, a continuación, en **Agregar una acción**.
2. Busque Office 365 Outlook.
3. Haga clic en Office 365 Outlook: Enviar un correo electrónico.
![Selección de Office 365 Outlook](./media/automate-with-logic-apps/flow2b.png)

4. En la acción de correo electrónico, agregue los siguientes datos:
 - Especifique la dirección de correo electrónico del destinatario.
 - Proporcione un asunto para el correo electrónico.
 - Coloque el cursor en campo **Cuerpo** y, en el menú de contenido dinámico que se abre a la derecha, seleccione **Cuerpo**.
 - Haga clic en **Mostrar opciones avanzadas**.

 ![Configuración de Office 365 Outlook](./media/automate-with-logic-apps/flow5.png)

5. En el menú de contenido dinámico, haga lo siguiente:
- Seleccione **Nombre de datos adjuntos**.
- Seleccione **Contenido de datos adjuntos**.
- Seleccione **Sí** en el campo **Es HTML**.

![Pantalla de configuración de correo electrónico de Office 365](./media/automate-with-logic-apps/flow7.png)
### <a name="step-7-save-and-test-your-logic-app"></a>Paso 7: Guardado y prueba de la aplicación lógica
1. Haga clic en **Guardar** para guardar los cambios.
1. Puede esperar a que el desencadenador ejecute la aplicación lógica, o puede ejecutarlo inmediatamente si selecciona **Ejecutar**.

![Pantalla de creación de la aplicación lógica](./media/automate-with-logic-apps/step7.png)

Cuando se ejecuta la aplicación lógica, los destinatarios que haya especificado en la lista de correo electrónico recibirán un correo electrónico similar al siguiente:

![Correo electrónico de la aplicación lógica](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre cómo crear [consultas de análisis](app-insights-analytics-using.md).
- Obtenga más información sobre [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).



<!--Link references-->







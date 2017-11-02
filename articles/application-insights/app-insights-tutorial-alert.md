---
title: "Envío de alertas desde Azure Application Insights | Microsoft Docs"
description: "Tutorial para enviar alertas en respuesta a errores en la aplicación con Azure Application Insights."
keywords: 
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 39e2f136e30ebb6dcfc003c435382f3384af1052
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Supervise el estado de la aplicación y envíe alertas con Azure Application Insights.

Azure Application Insights le permite supervisar su aplicación y le envía alertas cuando no está disponible, experimenta errores o sufre problemas de rendimiento.  Este tutorial le guía por el proceso de creación de pruebas para comprobar continuamente la disponibilidad de la aplicación y enviar diferentes tipos de alertas en respuesta a problemas detectados.  Aprenderá a:

> [!div class="checklist"]
> * Crear una prueba de disponibilidad para comprobar continuamente la respuesta de la aplicación
> * Enviar un correo electrónico a los administradores cuando se produzca un problema
> * Crear alertas basadas en métricas de rendimiento 
> * Usar una aplicación lógica para enviar telemetría resumida en función de una programación.


## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial:

- Instalar [Visual Studio 2017](https://www.visualstudio.com/downloads/) con las cargas de trabajo siguientes:
    - ASP.NET y desarrollo web
    - Desarrollo de Azure
    - Implemente una aplicación de .NET en Azure y [habilite el SDK de Application Insights](app-insights-asp-net.md). 


## <a name="log-in-to-azure"></a>Inicie sesión en Azure.
Inicie sesión en Azure Portal desde [https://portal.azure.com](https://portal.azure.com).

## <a name="create-availability-test"></a>Creación de una prueba de disponibilidad
Las pruebas de disponibilidad en Application Insights le permiten probar automáticamente la aplicación desde diferentes ubicaciones de todo el mundo.   En este tutorial, llevará a cabo una prueba sencilla para asegurarse de que la aplicación está disponible.  También puede crear un tutorial completo para probar su funcionamiento detallado. 

1. Seleccione **Application Insights** y, a continuación, seleccione la suscripción.  
1. Seleccione **Disponibilidad** en el menú **Investigar** y, a continuación, haga clic en **Agregar prueba**.
 
    ![Adición de prueba de disponibilidad](media/app-insights-tutorial-alert/add-test.png)

2. Escriba un nombre para la prueba y deje las demás opciones en sus valores predeterminados.  De este modo, se solicita la página principal de la aplicación cada 5 minutos desde 5 ubicaciones geográficas diferentes. 
3. Seleccione **Alertas** para abrir el panel **Alertas**, donde puede definir los detalles sobre cómo responder si se produce un error en la prueba. Escriba una dirección de correo electrónico a la que enviar los mensajes cuando se cumplan los criterios de alerta.  También puede escribir la dirección de un webhook al que llamar cuando se cumplan los criterios de alerta.

    ![Creación de prueba](media/app-insights-tutorial-alert/create-test.png)
 
4. Vuelva al panel de prueba y, después de unos minutos, debería comenzar a ver los resultados de la prueba de disponibilidad.  Haga clic en el nombre de la prueba para ver los detalles de cada ubicación.  El gráfico de dispersión muestra la duración de cada prueba y si se ha completado correctamente.

    ![Detalles de las pruebas](media/app-insights-tutorial-alert/test-details.png)

5.  Puede profundizar en los detalles de cualquier prueba determinada; para ello, haga clic en su punto en el gráfico de dispersión.  En el ejemplo siguiente se muestran los detalles de una solicitud con error.

    ![Resultado de la prueba](media/app-insights-tutorial-alert/test-result.png)
  
6. Si se cumplen los criterios de alerta, se envía un mensaje similar al siguiente a la dirección que ha especificado.

    ![Correo electrónico de alerta](media/app-insights-tutorial-alert/alert-mail.png)


## <a name="create-an-alert-from-metrics"></a>Creación de una alerta a partir de métricas
Además de enviar alertas desde una prueba de disponibilidad, puede crear una alerta desde cualquier métrica de rendimiento que se recopile para la aplicación.

2. Seleccione **Alertas** en el menú **Configurar**.  De este modo, se abre el panel de alertas de Azure.  Puede haber otras reglas de alerta configuradas aquí para otros servicios.
3. Haga clic en **Agregar alerta de métrica**.  Se abrirá el panel para crear una nueva regla de alerta.

    ![Adición de alerta de métrica](media/app-insights-tutorial-alert/add-metric-alert.png)

4. Escriba un **Nombre** para la alerta de regla y seleccione la aplicación en la lista desplegable para **Recurso**.
5. Seleccione una **Métrica** de ejemplo.  Se muestra un gráfico para indicar el valor de esta solicitud en las últimas 24 horas.  Esto le ayudará a configurar la condición para la métrica.

    ![Incorporación de regla de alerta](media/app-insights-tutorial-alert/add-alert-01.png)

6. Especifique una **Condición** y el **Umbral** para la alerta. Este es el número de veces que debe superarse la métrica para que se cree una alerta. 
6. En **Notificar mediante**, active la casilla **Enviar correo electrónico a propietarios, colaboradores y lectores** para enviar un correo electrónico a estos usuarios cuando se cumpla la condición de alerta y agregue la dirección de correo electrónico de los destinatarios adicionales.  También puede especificar un webhook o una aplicación lógica que se ejecute cuando se cumpla la condición.  Se puede utilizar par tratar de mitigar el problema detectado. 

    ![Incorporación de regla de alerta](media/app-insights-tutorial-alert/add-alert-02.png)


## <a name="proactively-send-information"></a>Envío de información de forma proactiva
Las alertas se crean como resultado de un conjunto determinado de problemas identificados en la aplicación, y normalmente se reservan alertas para las condiciones críticas que requieren atención inmediata.  Es posible recibir información proactiva sobre la aplicación con una instancia de Logic Apps que se ejecute automáticamente según una programación.  Por ejemplo, podría hacer que se enviara diariamente un mensaje a los administradores con información resumida que requiriese una mayor evaluación.

Para obtener más información sobre cómo crear una instancia de Logic Apps con Application Insights, consulte [Automatización de procesos de Application Insights con Logic Apps](automate-with-logic-apps.md).

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha aprendido cómo alertar sobre problemas, continúe con el siguiente tutorial para aprender a analizar el modo en que los usuarios interactúan con la aplicación.

> [!div class="nextstepaction"]
> [Entender a los usuarios](app-insights-tutorial-users.md)
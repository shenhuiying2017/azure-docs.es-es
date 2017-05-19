---
title: "Creación de un panel de redes sociales sin servidor en Azure | Microsoft Docs"
description: "Creación de un panel de redes sociales sin servidor en Azure"
services: functions, logic-apps, cognitive-services
keywords: "flujo de trabajo, aplicaciones de nube, servicios en la nube, procesos empresariales, integración de sistemas, integración de aplicaciones empresariales, EAI"
documentationcenter: 
author: rick-anderson
manager: wpickett
editor: 
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: riande
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 0d3eb2af197e9923d8e4a86bf1a0033f61e3c568
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---

# <a name="building-a-serverless-social-media-dashboard-in-azure"></a>Creación de un panel de redes sociales sin servidor en Azure

[Azure Functions](functions-overview.md) se integra con [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md) para permitirle crear orquestaciones complejas con otros servicios de Azure y de terceros. En este tema se muestra cómo desencadenar una aplicación lógica desde una fuente de redes sociales y analizar el texto con [Azure Cognitive Services](../cognitive-services/Welcome.md).

En este artículo se muestra cómo crear una aplicación lógica en Azure Portal que:

> [!div class="checklist"]
> * Comprueba si hay tweets nuevos por medio de una palabra clave o hashtag que proporcione.
> * Usa el conector de **Detect Sentiment** para estimar el sentimiento de los tweets (de malo a bueno).
> * Usa una función de Azure para procesar el sentimiento de los tweets en tres categorías (ROJO, AMARILLO o VERDE: malo, neutro y bueno).
> * Utiliza una condición para comprobar si el sentimiento es ROJO (malo).
> * Si la condición está en ROJO, envía un correo electrónico.

En la siguiente imagen se muestra una parte de la aplicación lógica en el diseñador:

![imagen con 2 primeros pasos de la aplicación en el diseñador de aplicaciones lógicas](media/functions-twitter-email/designer1.png)

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta de Azure. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.
* Una cuenta de Twitter.

## <a name="create-a-function-app"></a>Creación de una aplicación de función
 
[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal2.md)]

### <a name="create-a-categorize-function"></a>Creación de una función para categorizar

Una vez completada la implementación de la aplicación de función, ábrala. En esta sección, va a crear una función para categorizar los sentimientos de tweets en tres categorías (ROJO, AMARILLO o VERDE: malo, neutro y bueno).

![Hoja Instancias de Function App, Functions +](media/functions-twitter-email/add_fun.png)

Mantenga el valor predeterminado **Webhook y API**, **CSharp** y seleccione **Crear esta función**.

![Hoja Instancias de Function App, Functions +](media/functions-twitter-email/add_fun2.png)

Ha creado un Webhook/API (también conocido como desencadenador HTTP) que la aplicación que está creando puede llamar a petición. Si desea crear una función que se ejecute según una programación, debe crear una función de temporizador.

Reemplace el contenido del archivo *run.csx* por el código siguiente:

```c#
using System.Net;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");
    string category = "GREEN";

    // Get request body.
    double score = await req.Content.ReadAsAsync<double>();

    if (score < .3)
    {
        category = "RED";
    }
    else if (score < .6)
    {
        category = "YELLOW";
    }

    return req.CreateResponse(HttpStatusCode.OK, category);
}
```

Guarde el cambio.

### <a name="test-the-function"></a>Prueba de la función

Seleccione **Probar** (a la derecha del cuadro del código).  Escriba 0.2 en el cuadro de texto **Cuerpo de la solicitud** y seleccione **Ejecutar**. La salida muestra "ROJO" y el estado HTTP es 200 - Correcto.

 ![test ](media/functions-twitter-email/test.png)

## <a name="cognitive-services"></a>Cognitive Services

Cree una cuenta de Cognitive Services. Se requiere una cuenta de Cognitive Services para detectar el sentimiento de los tweets que se están supervisando.

Vaya a **Nuevo > Inteligencia y análisis > Cognitive Services**. Establezca cada campo obligatorio:

![Hoja para crear cuenta de Cognitive Services](media/functions-twitter-email/cog_svcs_account.png)

| Campo               | Valor de ejemplo | Comentario |
| ----------------- | ------------ | ------------- |
| Nombre de cuenta | MyCognitiveServicesAccnt | Escriba un nombre único. |
| Tipo de API | Text Analytics API | Seleccione Análisis de texto. |
| Ubicación | Oeste de EE. UU. | Actualmente, solo está disponible **Oeste de EE. UU.** |
| Nivel de precios | F0 | Si se queda sin llamadas, establézcalo en un nivel superior.|
| Grupos de recursos | rg1 | Use el grupo de recursos que especificó antes.|

### <a name="copy-the-cognitive-services-key"></a>Copie la clave de Cognitive Services.

Seleccione **Claves**. Se necesita una clave en un paso posterior.

 ![simétricas](media/functions-twitter-email/keys.png)

## <a name="create-a-logic-app"></a>Creación de una aplicación lógica

En Azure Portal, haga clic en **Nuevo >  Enterprise Integration > Logic App**.

![paso anterior paso de nueva aplicación lógica](media/functions-twitter-email/new_logicApp.png)

En la hoja **Crear aplicación lógica**, rellene cada campo y seleccione **Crear**.

![paso anterior paso para crear aplicación lógica](media/functions-twitter-email/new_logicApp2.png)

Una vez creada la aplicación lógica, se abre en el diseñador. Seleccione la plantilla **Aplicación lógica en blanco**.

![Aplicación lógica en blanco](media/functions-twitter-email/blank.png)

## <a name="add-a-trigger-to-twitter"></a>Incorporación de un desencadenador a Twitter

El **diseñador de aplicación lógica** muestra muchos servicios y desencadenadores a los que puede conectarse.

Seleccione el servicio **Twitter**.

![conector de Twitter](media/functions-twitter-email/twitter_connector.png)

Seleccione el desencadenador **Cuando se publique un nuevo tweet**.

![Desencadenador Cuando se publique un nuevo tweet](media/functions-twitter-email/tw_trig.png)

Inicie sesión en su cuenta de Twitter.

![Inicio de sesión en su cuenta de Twitter](media/functions-twitter-email/signin_twit.png)

Escriba la contraseña y seleccione **Autorizar aplicación**.

![autenticación de twitter en nueva ventana desde arriba](media/functions-twitter-email/auth_twit.png)

Escriba el texto de búsqueda, la frecuencia y el intervalo. Si especifica un hashtag popular (como #football, #soccer o #futbol), puede utilizar rápidamente todas las llamadas de servicio asignadas en su cuenta de Cognitive Services. Si se queda sin llamadas, puede aumentar el plan de tarifa. 

Busque #Azure cada 15 minutos:

![#Azure cada 15 minutos](media/functions-twitter-email/azure_tweet.png)

Guarde la aplicación.

### <a name="add-a-text-analytics-connector"></a>Incorporación de un conector **Análisis de texto**

El conector de análisis de texto detecta los sentimientos de tweets.

Seleccione **Nuevo paso** y **Agregar una acción**.

![Nuevo paso y Agregar una acción](media/functions-twitter-email/new_step.png)

Agregue el conector **Análisis de texto**.

![Ventana Elegir una acción](media/functions-twitter-email/choose_action.png)

Seleccione la acción **Detectar sentimiento**. La clasificación de sentimiento suele ser precisa, pero a veces malinterpreta el texto.

![Detectar sentimiento](media/functions-twitter-email/detect_sent.png)

### <a name="create-the-detect-sentiment-action"></a>Creación de la acción Detectar sentimiento

  * Escriba un nombre de conexión como **MyKey**.
  * Copie y pegue la clave creada en el paso de [creación de una cuenta de Cognitive Services](#cognitive-services).
  * Seleccione **Crear**.
  * Guarde la aplicación.

![Detectar sentimiento](media/functions-twitter-email/ta_detect_sent.png)

Seleccione el icono **Texto del tweet** para el **texto que se va a analizar**.

![Detectar sentimiento](media/functions-twitter-email/ds_tta.png)

![Detectar sentimiento](media/functions-twitter-email/ds_tta2.png)

Guarde la aplicación.

## <a name="connect-to-the-azure-function"></a>Conexión a la función de Azure

En esta sección, va a agregar la función que creó antes para clasificar sentimientos de tweets como ROJO, AMARILLO o VERDE.

* En el diseñador de aplicación lógica, seleccione **Nuevo paso** y **Agregar una acción**.
* Seleccione **Azure Functions**.
* Seleccione **Elegir una función de Azure**.

![Cuadro de función de Azure que muestra Elegir una función de Azure](media/functions-twitter-email/choose_fun.png)

* Seleccione la función de Azure que creó antes.
* Seleccione **Puntuación** para rellenar **Cuerpo de la solicitud**.

![Score](media/functions-twitter-email/trigger_score.png)

Guarde la aplicación.

## <a name="add-email-notification"></a>Incorporar la notificación por correo electrónico

En esta sección, se va a agregar una comprobación condicional de tweets con sentimientos negativos (condición en ROJO).

* Seleccione **Nuevo paso**.
* Seleccione **Agregar una condición**.
* Seleccione **Cuerpo** en el primer cuadro de texto **Elegir un valor**.
* Escriba "ROJO" en el segundo cuadro de texto **Elegir un valor**.
* Guarde la aplicación.

![cuadro de condición](media/functions-twitter-email/condition.png)

* En el cuadro **IF YES, DO NOTHING** (En caso afirmativo, no hacer nada), active **Agregar una acción**.
* Escriba Outlook o Gmail en el cuadro **Buscar todos los servicios y acciones**. En este tutorial se utiliza Outlook. Consulte [Incorporación de una acción de Gmail] (../logic-apps/logic-apps-create-a-logic-app.md#add-an-action-that-responds-to-your-trigger) para instrucciones sobre Gmail. Nota: Si tiene una [cuenta de Microsoft](https://account.microsoft.com/account) personal, úsela como cuenta de Outlook.com.

![Cuadro Elegir una acción](media/functions-twitter-email/outlook.png)

Selección **Outlook.com Enviar un correo electrónico**.

![Cuadro Outlook.com](media/functions-twitter-email/sendEmail.png)

Inicie sesión en Outlook.com.

![cuadro de inicio de sesión](media/functions-twitter-email/signin_outlook.png)

Indique los siguientes elementos:

   * **Para**: el correo electrónico al que se debe enviar el mensaje.
   * **Asunto**: Puntuación.
   * **Cuerpo**: la ubicación y el texto del tweet.

![Cuadro Enviar un correo electrónico](media/functions-twitter-email/sendEmail2.png)

Guarde la aplicación.
Seleccione **Ejecutar** para iniciar la aplicación.

### <a name="check-the-status"></a>Comprobación del estado

En la hoja Aplicación lógica, seleccione **Información general** y una fila en la columna **Todas las ejecuciones**:

![Hoja Información general](media/functions-twitter-email/over1.png)

En la siguiente imagen se muestran los detalles de ejecución cuando la condición no era verdadera y no se envió el correo electrónico.

![Hoja Información general](media/functions-twitter-email/skipped.png)

Si desea probar inmediatamente la función **Enviar un correo electrónico**:

* Cambie **Entradas** en el primer paso (**Cuando se publique un nuevo tweet**) a un término popular, como #football, #soccer o #futbol.

El procesamiento de términos populares consume más recursos que el de aquellos menos populares. Se recomienda cambiar el término de búsqueda tras comprobar que el correo electrónico esté funcionando.

En la siguiente imagen se muestran los detalles de ejecución cuando la condición era verdadera y se envió el correo electrónico.

![Hoja Información general](media/functions-twitter-email/sent.png)

Puede seleccionar cualquiera de los cuadros de servicio para mostrar más información sobre los datos utilizados para la ejecución. Seleccione **Cuando se publique un nuevo tweet**; muestra el texto de búsqueda y todas las salidas, incluso las que no se estén usando.

## <a name="next-steps"></a>Pasos siguientes

*  [Introducción a Azure Functions](functions-overview.md)
*  [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md)
*  [Adición de condiciones y ejecución de flujos de trabajo](../logic-apps/logic-apps-use-logic-app-features.md)
*  [Plantillas de aplicaciones lógicas](../logic-apps/logic-apps-use-logic-app-templates.md)
*  [Creación de aplicaciones lógicas a partir de plantillas de Azure Resource Manager](../logic-apps/logic-apps-arm-provision.md)

## <a name="get-help"></a>Obtener ayuda

Para formular preguntas, o responderlas, y saber lo que hacen otros usuarios de Azure Logic Apps, visite el [foro de Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Para ayudar a mejorar Azure Logic Apps y los conectores, vote o envíe ideas en el [sitio de comentarios de usuario de Azure Logic Apps](http://aka.ms/logicapps-wish).


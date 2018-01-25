---
title: "Escenario: creación de un panel de Customer Insights con Azure sin servidor | Microsoft Docs"
description: "Un ejemplo de cómo se puede crear un panel para administrar los comentarios de clientes, datos sociales, etc. Azure Logic Apps y Azure Functions."
keywords: 
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2017
ms.author: jehollan
ms.openlocfilehash: d3e07b8d7194d83e3ba3986177170edff21e1d7a
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="create-a-real-time-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Cree un panel de Customer Insights en tiempo real con Azure Logic Apps y Azure Functions

Las herramientas de Azure sin servidor proporcionan funcionalidades eficaces para generar rápidamente y hospedar aplicaciones en la nube, sin tener que preocuparse de la infraestructura.  En este escenario, se creará un panel para actuar en función de los comentarios de los clientes, analizarlos con Machine Learning y publicar información de un origen como Power BI o Azure Data Lake.

## <a name="overview-of-the-scenario-and-tools-used"></a>Información general acerca del escenario y las herramientas usadas

Para implementar esta solución, se usarán los dos componentes clave de las aplicaciones sin servidor de Azure: [Azure Functions](https://azure.microsoft.com/services/functions/) y [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/).

Logic Apps es un motor de flujo de trabajo sin servidor en la nube.  Proporciona orquestación en componentes sin servidor y también se conecta a más de 100 servicios y API.  En este escenario, se creará una aplicación lógica para actuar en función de los comentarios de los clientes.  Algunos de los conectores que pueden ayudar a reaccionar ante los comentarios de los clientes son: Outlook.com, Office 365, Survey Monkey, Twitter y una solicitud HTTP [desde un formulario web](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/).  En el flujo de trabajo siguiente, se supervisará un hashtag en Twitter.

Las funciones proporcionan proceso sin servidor en la nube.  En este escenario, se usará Azure Functions para marcar los tweets de los clientes en función de una serie de palabras clave predefinidas.

Toda la solución se puede [compilar en Visual Studio](logic-apps-deploy-from-vs.md) e [implementar como parte de una plantilla de recursos](logic-apps-create-deploy-template.md).  También hay tutorial en vídeo del escenario [en Channel 9](http://aka.ms/logicappsdemo).

## <a name="build-the-logic-app-to-trigger-on-customer-data"></a>Compilación de la aplicación lógica para actuar con datos del cliente

Después de [crear una aplicación de lógica](quickstart-create-first-logic-app-workflow.md) en Visual Studio o Azure Portal:

1. Agregue un desencadenador para **On New Tweets** (En los tweets nuevos) de Twitter
2. Configure el desencadenador para escuchar tweets con una palabra clave o hashtag.

   > [!NOTE]
   > La propiedad de periodicidad del desencadenador determinará la frecuencia con que la aplicación lógica busca nuevos elementos en los desencadenadores basados en sondeo.

   ![Ejemplo de desencadenador de Twitter][1]

Esta aplicación se activará ante todos los tweets nuevos.  Posteriormente, se pueden tomar los datos de los tweets y conocer mejor las opiniones expresadas.  Para ello, se utiliza [Azure Cognitive Service](https://azure.microsoft.com/services/cognitive-services/) para detectar las opiniones que hay en el texto.

1. Haga clic en **New Step** (Paso nuevo).
1. Seleccione o busque el conector de **Text Analytics**.
1. Seleccione la operación **Detect Sentiment** (Detectar opiniones).
1. Si se le solicita, proporcione una clave de Cognitive Services válida para el servicio Text Analytics.
1. Agregue el **texto del tweet** como texto que se va a analizar.

Ahora que tenemos los datos del tweet e información sobre el tweet, es posible que otros conectores sean relevantes:
* Power BI: agregar filas a un conjunto de datos de streaming: ver tweets en tiempo real en un panel de Power BI.
* Azure Data Lake: anexar archivo: agregar a un conjunto de datos de Azure Data Lake datos de clientes que se incluyen en los trabajos de análisis.
* SQL: agregar filas: almacenar datos en una base de datos para su posterior recuperación.
* Slack: Enviar mensaje: alertar a un canal de Slack sobre comentarios negativos que requieren acciones.

Una función de Azure también se puede usar para realizar más proceso personalizado en los datos.

## <a name="enriching-the-data-with-an-azure-function"></a>Enriquecimiento de los datos con una función de Azure

Para poder crear una función, es preciso tener una aplicación de función en la suscripción de Azure.  [Aquí se puede encontrar](../azure-functions/functions-create-first-azure-function-azure-portal.md) información acerca de cómo crear una función de Azure en el portal.

Para que una función pueda llamarse directamente desde una aplicación lógica, es preciso que tenga un enlace a un desencadenador de HTTP.  Se recomienda utilizar la plantilla **HttpTrigger**.

En este escenario, el cuerpo de la solicitud de la función de Azure sería el texto del mensaje.  En el código de la función, basta con definir la activación de la lógica si el texto de tweet contiene una palabra o frase claves.  La propia función puede ser tan simple o compleja como sea necesario en el escenario.

Al final de la función, solo hay que devolver una respuesta a la aplicación lógica con algunos datos.  Puede tratarse de un valor booleano simple (p. ej. `containsKeyword`) o de un objeto complejo.

![Paso de función de Azure configurado][2]

> [!TIP]
> Cuando acceda a una respuesta compleja desde una función de una aplicación lógica, use la acción Análisis del archivo JSON.

Una vez que se guarda la función, se puede agregar a la aplicación lógica creada anteriormente.  En la aplicación lógica:

1. Haga clic para agregar un **paso nuevo**.
1. Seleccione el conector de **Azure Functions**.
1. Seleccione la opción de elegir una función existente y navegue hasta la función creada.
1. Envíe el **texto del tweet** para el **cuerpo de la solicitud**

## <a name="running-and-monitoring-the-solution"></a>Ejecución y supervisión de la solución

Una de las ventajas de crear orquestaciones sin servidor en Logic Apps es la gran cantidad de funcionalidades de depuración y supervisión.  Todas las ejecuciones (actuales o históricas) se pueden verse desde dentro de Visual Studio, Azure Portal o a través de la API de REST y los SDK.

Una de las formas más sencillas de probar una aplicación de lógica es usar el botón **Run** (Ejecutar) en el diseñador.  Al hacer clic en **Run** (Ejecutar), se seguirá sondeando el desencadenador cada 5 segundos hasta que se detecte un evento y se proporcionará una vista en vivo a medida que la ejecución progresa.

Los historiales de las ejecuciones anteriores se pueden ver en la hoja Información general de Azure Portal o mediante Visual Studio Cloud Explorer.

## <a name="creating-a-deployment-template-for-automated-deployments"></a>Creación de una plantilla de implementación para implementaciones automatizadas

Una vez que se ha desarrollado una solución, se puede capturar e implementar a través de una plantilla de Azure en cualquier región de Azure del mundo.  Esto resulta útil no solo para modificar los parámetros de las diferentes versiones de este flujo de trabajo, sino también para integrar esta solución en una canalización de compilación y lanzamiento.  [En este artículo](logic-apps-create-deploy-template.md) se puede encontrar información acerca de cómo crear una plantilla de implementación.

Azure Functions también se puede incorporar a la plantilla de implementación (con el fin de que toda la solución, con todas las dependencias, se puede administrar como una sola plantilla).  Un ejemplo de una plantilla de implementación de funciones se puede encontrar en el [repositorio azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic).

## <a name="next-steps"></a>pasos siguientes

* [Vea otros ejemplos y escenarios comunes de Azure Logic Apps](logic-apps-examples-and-scenarios.md)
* [Vea un tutorial en vídeo sobre la creación de esta solución de un extremo a otro](http://aka.ms/logicappsdemo)
* [Aprenda a controlar errores y excepciones en Azure Logic Apps](logic-apps-exception-handling.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png
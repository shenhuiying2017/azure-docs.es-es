---
title: "Diagnóstico de errores: Azure Logic Apps | Microsoft Docs"
description: "Maneras comunes de identificar dónde se producen los errores en las aplicaciones lógicas"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: a6727ebd-39bd-4298-9e68-2ae98738576e
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 814e6f93088cdd96b0a663d2a7494b5a11470d99
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="diagnose-logic-app-failures"></a>Diagnóstico de errores en las aplicaciones lógicas
Si surgen problemas o errores con las aplicaciones lógicas, puede adoptar varios enfoques para conocer mejor su origen.  

## <a name="azure-portal-tools"></a>Herramientas del Portal de Azure
El Portal de Azure proporciona muchas herramientas para diagnosticar todas las aplicaciones lógicas en cada paso.

### <a name="trigger-history"></a>Historial de desencadenamiento

Cada aplicación lógica tiene al menos un desencadenador. Si observa que las aplicaciones no se activan, para más información, busque primero en el historial de desencadenadores, al cual se accede desde la hoja principal de la aplicación lógica.

![Ubicación del historial de desencadenamiento][1]

En el historial de desencadenadores se enumeran todos los intentos de desencadenamiento de la aplicación lógica. Puede hacer clic en ellos para conocer los detalles, en concreto, las entradas o salidas generadas por el intento de desencadenamiento. Si encuentra errores en los desencadenadores, seleccione el intento de desencadenamiento y elija el vínculo **Salidas** para revisar los mensajes de error generados, por ejemplo, credenciales de FTP no válidas.

Estos son los diferentes estados que puede ver:

* **Omitido**. Se sondeó el punto de conexión para buscar datos y se recibió como respuesta que no había datos disponibles.
* **Correcto**. El desencadenador recibió como respuesta que los datos estaban disponibles. Este estado podría proceder de un desencadenador manual, uno de periodicidad o uno de sondeo. Suele ir acompañado de un estado **desencadenado**, pero puede no ser así si tiene una condición o un comando SplitOn en la vista de código que no se haya satisfecho.
* **Error**. Se generó un error.

#### <a name="start-a-trigger-manually"></a>Inicio manual de un desencadenador

Si desea que la aplicación lógica compruebe si hay algún desencadenador disponible de inmediato (sin esperar a la siguiente repetición), haga clic el botón **Seleccionar un desencadenador** en la hoja principal para forzar una comprobación. Por ejemplo, al hacer clic en este vínculo con un desencadenador de Dropbox, provocará que el flujo de trabajo sondee Dropbox inmediatamente en busca de archivos nuevos.

### <a name="run-history"></a>Historial de ejecuciones

Cada desencadenador que se activa produce una ejecución. Puede acceder a información de la ejecución desde la hoja principal, que contiene numerosos detalles útiles para comprender lo sucedido durante el flujo de trabajo.

![Ubicación del historial de ejecuciones][2]

Una ejecución muestra uno de los siguientes estados:

* **Correcto**. Todas las acciones correctas. Si se produjo un error, este se corrigió mediante una acción posterior del flujo de trabajo. Es decir, el error se corrigió con una acción que se configuró para ejecutarse después de una acción con error.
* **Error**. Se produjo un error en al menos una acción que no se controló mediante una acción posteriormente en el flujo de trabajo.
* **Cancelado**. El flujo de trabajo se estaba ejecutando pero recibió una solicitud de cancelación.
* **En ejecución**. El flujo de trabajo se ejecuta actualmente. Este estado puede darse en flujos de trabajo con limitaciones o a causa del plan de precios actual. Para conocer los detalles, consulte [los límites de las acciones en la página de precios](https://azure.microsoft.com/pricing/details/app-service/plans/). La configuración del diagnóstico (los gráficos que figuran a continuación del historial de ejecución) también proporcionan información sobre los eventos de limitación que se están produciendo.

Cuando se encuentre ante un historial de ejecución, puede profundizar en él para obtener más detalles.  

#### <a name="trigger-outputs"></a>Salidas del desencadenador

Las salidas del desencadenador muestran los datos que se han recibido del desencadenador. Estas salidas pueden ayudarle a determinar si se devolvieron todas las propiedades según lo previsto.

> [!NOTE]
> Si ve cualquier contenido que no conozca, aprenda cómo Azure Logic Apps [administra distintos tipos de contenido](../logic-apps/logic-apps-content-type.md).
> 

![Ejemplos de salidas del desencadenador][3]

#### <a name="action-inputs-and-outputs"></a>Entradas y salidas de acciones

Puede profundizar en las entradas y salidas que recibió una acción. Estos datos resultan útiles para conocer el tamaño y la forma de las salidas, así como para encontrar los mensajes de error que se han generado.

![Entradas y salidas de acciones][4]

## <a name="debug-workflow-runtime"></a>Depuración del flujo de trabajo en tiempo de ejecución

Además de supervisar las entradas, las salidas y los desencadenadores de una ejecución, puede agregar algunos pasos al flujo de trabajo para facilitar la depuración. 
[RequestBin](http://requestb.in) . Mediante el uso de RequestBin, puede configurar un inspector de la solicitud HTTP para determinar el tamaño exacto, la forma y el formato de una solicitud HTTP. Puede crear una herramienta RequestBin y pegar la dirección URL en una acción HTTP POST de aplicación lógica junto con el contenido del cuerpo que desee probar, como una expresión, la salida de otro paso, etc. Después de ejecutar la aplicación lógica puede actualizar RequestBin para ver cómo se formó la solicitud cuando se generó desde el motor de Logic Apps.

<!-- image references -->
[1]: ./media/logic-apps-diagnosing-failures/triggerhistory.png
[2]: ./media/logic-apps-diagnosing-failures/runhistory.png
[3]: ./media/logic-apps-diagnosing-failures/triggeroutputslink.png
[4]: ./media/logic-apps-diagnosing-failures/actionoutputs.png

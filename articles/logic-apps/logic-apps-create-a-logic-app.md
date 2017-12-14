---
title: "Automatización de los flujos de trabajo entre sistemas y servicios en la nube: Azure Logic Apps | Microsoft Docs"
description: "Creación de aplicaciones lógicas para automatizar los flujos de trabajo en escenarios de integración de aplicaciones empresariales (EAI) e integración de sistemas"
author: ecfan
manager: anneta
editor: 
services: logic-apps
keywords: "flujo de trabajo, aplicaciones de nube, servicios en la nube, procesos empresariales, integración de sistemas, integración de aplicaciones empresariales, EAI"
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/20/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 5906605192f9b03f612e6ca3a445434a23713d7f
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2017
---
# <a name="automate-your-first-workflow-to-process-data-with-a-logic-app"></a>Automatización del primer flujo de trabajo para procesar datos con una aplicación lógica

Para integrar los sistemas y los servicios más rápidamente para la organización, puede automatizar los flujos de trabajo y los procesos empresariales con [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md). Este tutorial rápido muestra lo fácil que es compilar y ejecutar un flujo de trabajo automatizado mediante la creación de una aplicación de lógica. La aplicación de ejemplo muestra cómo automatizar un flujo de trabajo que comprueba si hay elementos nuevos en una fuente RSS de sitio web y envía un correo electrónico para cada uno de ellos.

Esta aplicación de lógica de ejemplo envía un correo electrónico como el de este ejemplo:

![Envío de correo electrónico por un elemento nuevo en la fuente RSS](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

Y este es el flujo de trabajo de aplicación lógica de alto nivel que ha compilado:

![Introducción: aplicación lógica de ejemplo](./media/logic-apps-create-a-logic-app/logic-app-simple-overview.png)

En esta guía de inicio rápido, ha aprendido a hacer lo siguiente:

> [!div class="checklist"]
> * Crear una aplicación lógica en blanco.
> * Agregar un desencadenador para iniciar el flujo de trabajo cuando aparece un nuevo elemento en la fuente RSS.
> * Agregar una acción para enviar un correo electrónico con información sobre el elemento de la fuente RSS.
> * Ejecutar el flujo de trabajo de la aplicación lógica.

Si no tiene ninguna suscripción a Azure, [cree una cuenta gratuita de Azure](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta de correo electrónico de cualquier proveedor de correo electrónico compatible con Azure Logic Apps para el envío de notificaciones. Por ejemplo, Office 365 Outlook, Outlook.com o Gmail. Para otros conectores de correo electrónico compatibles, [revise la lista de conectores](https://docs.microsoft.com/connectors/). En este tutorial rápido se usa Office 365 Outlook.

  > [!TIP]
  > Si tiene una [cuenta de Microsoft](https://account.microsoft.com/account) personal, tiene una cuenta de Outlook.com. Por otra parte, si dispone de una cuenta de Azure profesional o educativa, tiene una cuenta de Office 365 Outlook.

* Un vínculo a la fuente RSS de un sitio web. Este ejemplo utiliza la [fuente RSS para noticias destacadas desde el sitio web de Reuters](http://feeds.reuters.com/reuters/topNews): `http://feeds.reuters.com/reuters/topNews`

En este tutorial rápido no es necesario escribir código, pero Logic Apps es compatible con otros escenarios que utilizan código, por ejemplo, puede ejecutar su propio código desde una aplicación de lógica con [Azure Functions](../azure-functions/functions-overview.md).

## <a name="create-a-blank-logic-app"></a>Creación de una aplicación lógica en blanco 

1. Inicie sesión en [Azure Portal](https://portal.azure.com "Azure Portal"). 

2. En el menú principal de Azure, elija **Nuevo** > **Enterprise Integration** > **Logic App**.

   ![Portal de Azure, Nuevo, Enterprise Integration, Logic App](./media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

3. Cree la aplicación lógica con la configuración que se incluye en la tabla situada bajo la imagen:

   ![Proporcione los detalles de la aplicación lógica](./media/logic-apps-create-a-logic-app/logic-app-settings.png)

   | Configuración | Valor sugerido | Descripción | 
   | ------- | --------------- | ----------- | 
   | **Name** | *nombre-de-la-aplicación-lógica* | Proporcione un nombre único de aplicación lógica. | 
   | **Suscripción** | *your-Azure-subscription-name* | Seleccione la suscripción de Azure que quiera usar. | 
   | **Grupos de recursos** | *your-Azure-resource-group-name* | Cree un [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md) para esta aplicación lógica y para organizar todos los recursos asociados a esta aplicación. | 
   | **Ubicación** | *your-Azure-datacenter-region* | Seleccione la región del centro de datos para implementar la aplicación lógica, por ejemplo, Oeste de EE.UU. | 
   | **Log Analytics** | Off | Active el registro de diagnóstico para la aplicación lógica, pero para este tutorial rápido, mantenga el valor de configuración **desactivado**. | 
   |||| 

4. Cuando esté listo, elija **Anclar al panel**. De este modo, la aplicación lógica aparecerá automáticamente en el panel de Azure y se abrirá después de la implementación. Seleccione **Create**.

   > [!NOTE]
   > Si no quiere anclar la aplicación lógica, búsquela y ábrala manualmente después de la implementación para continuar.

   Una vez que Azure ha implementado la aplicación lógica, el Diseñador de Logic Apps se abre y muestra una página con un vídeo de introducción. 
   Bajo el vídeo encontrará plantillas de los patrones de aplicación lógica más comunes. 
   En este tutorial rápido se compila la aplicación lógica desde el principio. 

5. Pase el vídeo de introducción y los desencadenadores comunes. En **plantillas**, elija **Aplicación lógica en blanco**.

   ![Selección de la plantilla de aplicación lógica en blanco](./media/logic-apps-create-a-logic-app/choose-logic-app-template.png)

   El Diseñador de aplicaciones lógicas muestra los conectores disponibles y sus desencadenadores, que sirven para iniciar los flujos de trabajo de las aplicaciones lógicas.

   ![Desencadenadores de aplicación lógica](./media/logic-apps-create-a-logic-app/logic-app-triggers.png)

## <a name="add-a-trigger-to-detect-new-items"></a>Incorporación de un desencadenador para detectar nuevos elementos

Cada flujo de trabajo de aplicación lógica comienza con un [desencadenador](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts). El desencadenador se activa cuando se produce un evento específico o cuando nuevos datos cumplen la condición establecida. Cada vez que el desencadenador se activa, el motor de Logic Apps crea una instancia de aplicación lógica que inicia y ejecuta el flujo de trabajo.

1. En el cuadro de búsqueda, escriba "rss" como filtro. Seleccione este desencadenador: **RSS: cuando se publica un elemento de fuente** 

   ![Selección del desencadenador "RSS: cuando se publica un elemento de fuente"](./media/logic-apps-create-a-logic-app/rss-trigger.png)

2. Especifique el vínculo de la fuente RSS que desea supervisar, por ejemplo, `http://feeds.reuters.com/reuters/topNews`. Defina el intervalo y la frecuencia de periodicidad. En este ejemplo se comprueba la fuente cada cinco minutos.

   ![Configuración del desencadenador con la fuente RSS, la frecuencia y el intervalo](./media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

   Logic Apps crea una conexión a la fuente RSS.

   > [!TIP]
   > Para simplificar la vista en el diseñador, puede contraer y ocultar los detalles de una forma: simplemente haga clic en la barra de título de esta.

3. Guarde el trabajo. En la barra de herramientas del diseñador, haga clic en **Guardar**. 

   ![Guardado de la aplicación lógica](./media/logic-apps-create-a-logic-app/save-logic-app.png)

   La aplicación lógica estará activa, pero no hace más que comprobar la fuente RSS. Por lo tanto, vamos a agregar una acción que responda cuando el desencadenador se active.

## <a name="add-an-action-to-send-email"></a>Incorporación de una acción para enviar correo electrónico

Ahora que hay un desencadenador, agregue [una acción](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts) para enviar un correo electrónico cuando aparezca un nuevo elemento en la fuente RSS. El flujo de trabajo realiza esta acción cuando se activa el desencadenador.

1. En el diseñador de aplicaciones lógicas, en el desencadenador, elija **+ Nuevo paso** > **Agregar una acción**.

   ![Agregar una acción](./media/logic-apps-create-a-logic-app/add-new-action.png)

   El diseñador muestra las acciones que puede realizar la aplicación lógica cuando se activa el desencadenador.

   ![Selección de la lista de acciones](./media/logic-apps-create-a-logic-app/logic-app-actions.png)

2. En el cuadro de búsqueda, escriba "enviar correo electrónico" como filtro. Busque y seleccione el conector de correo electrónico que desea usar. A continuación, seleccione la acción "enviar correo electrónico" para el conector. Por ejemplo: 

   * Para las cuentas profesionales o educativas de Azure, seleccione Office 365 Outlook. 
   * Para las cuentas de Microsoft personales, seleccione Outlook.com. 
   * Para las cuentas de Gmail, seleccione Gmail. 

   En este tutorial rápido se usa Office 365 Outlook. 
   Si utiliza un proveedor de correo electrónico diferente, los pasos siguen siendo los mismos, pero la interfaz de usuario podría diferir. 

   ![Selección de la acción "Office 365 Outlook: enviar un correo electrónico"](./media/logic-apps-create-a-logic-app/actions.png)

3. Cuando se le pidan las credenciales, inicie sesión con el nombre de usuario y la contraseña de su cuenta de correo electrónico. 

   Logic Apps crea una conexión a la cuenta de correo electrónico.

4. Ahora, especifique los datos que se van a incluir en el correo electrónico. 

   1. En el cuadro **Para**, escriba la dirección de correo electrónico del destinatario. 
   Para realizar pruebas, puede usar su propia dirección de correo electrónico.

   2. En el cuadro **Asunto**, escriba el asunto del correo electrónico. 
   En este ejemplo, escriba "elemento RSS nuevo:" tal como se muestra:

      ![Incorporación del asunto del correo electrónico](./media/logic-apps-create-a-logic-app/logic-app-add-subject.png)

      Al hacer clic en el cuadro de edición, se abre la **lista para agregar contenido dinámico** para que pueda seleccionar de los campos de datos disponibles que incluir en la acción. 
      Si no se abre la lista de contenido dinámico, en el cuadro de edición correspondiente, elija **Agregar contenido dinámico**.

   3. De la lista **Agregar contenido dinámico**, seleccione **Título de fuente**, que incluye el título del elemento en el correo electrónico.

      ![Incorporación del asunto del correo electrónico](./media/logic-apps-create-a-logic-app/logic-app-select-field.png)

      Cuando haya terminado, el asunto del correo electrónico será similar a este ejemplo:

      ![Título de fuente agregado](./media/logic-apps-create-a-logic-app/added-feed-title.png)

      > [!NOTE] 
      > Si selecciona un campo que contiene una matriz, como **categorías-elemento**, el diseñador agrega automáticamente un bucle "Para cada uno" en la acción que haga referencia a ese campo. De este modo, la aplicación lógica realiza la acción en todos los elementos de la matriz. 
      > 
      > Para quitar el bucle, elija el botón de puntos suspensivos (**...**) de la barra de título del bucle y **Eliminar**.

   4. En el cuadro **Cuerpo**, escriba el contenido del cuerpo del correo electrónico. 
   En este ejemplo, escriba este texto y seleccione estos campos:

      ![Incorporación del contenido del cuerpo del correo electrónico](./media/logic-apps-create-a-logic-app/logic-app-complete.png)

      | Campo | Descripción | 
      | ----- | ----------- | 
      | **Título de fuente** | Muestra el título del elemento. | 
      | **Fuente publicada el** | Muestra la fecha y la hora de publicación del elemento. | 
      | **Vínculo de fuente principal** | Muestra la dirección URL del elemento. | 
      ||| 

      > [!TIP]
      > Para agregar líneas en blanco en el cuadro de edición, presione Mayús + Entrar. 
      
5. Guarde el trabajo. En la barra de herramientas del diseñador, haga clic en **Guardar**.

   ![Aplicación lógica completada](./media/logic-apps-create-a-logic-app/save-complete-logic-app.png)

## <a name="run-your-logic-app-workflow"></a>Ejecución del flujo de trabajo de la aplicación lógica

Para iniciar manualmente la aplicación lógica, en la barra de la barra de herramientas del diseñador, elija **Ejecutar**. Si no es lo que desea, espere a que la aplicación lógica se ejecute según el programa configurado.

![Ejecución de la aplicación lógica](./media/logic-apps-create-a-logic-app/run-complete-logic-app.png)

Si la fuente RSS tiene nuevos elementos, la aplicación lógica envía un correo electrónico para cada uno de ellos. Por ejemplo, este es un correo electrónico de Outlook de ejemplo que envía esta aplicación lógica:

![Envío de correo electrónico por un elemento nuevo en la fuente RSS](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

Si la fuente no tiene elementos nuevos, la aplicación lógica omite el paso de enviar correo electrónico y espera al siguiente intervalo para volver a realizar la comprobación. 

> [!TIP]
> Si no obtiene los mensajes de correo electrónico, compruebe la carpeta de correo electrónico no deseado. Si tampoco aparece allí y no está seguro de que la aplicación lógica se ejecutara correctamente, consulte el artículo de [solución de problemas en la aplicación lógica](../logic-apps/logic-apps-diagnosing-failures.md).

Enhorabuena, acaba de crear y ejecutar la primera aplicación lógica. En este tutorial rápido se ha explicado cómo crear flujos de trabajo automatizados fácil y rápidamente para la integración de sistemas y servicios.

## <a name="clean-up-resources"></a>Limpieza de recursos

La aplicación lógica continúa ejecutándose y posiblemente incurrirá en cargos en la suscripción de Azure hasta que desactive o elimine la aplicación. Además, al crear conexiones para la aplicación lógica, estas permanecen, incluso después de eliminar la aplicación lógica. 

Cuando haya terminado, asegúrese de deshabilitar o eliminar todos los recursos los cuales no desee conservar o que generen cargos. Para eliminar todos los recursos que ha creado para este tutorial rápido, elimine el grupo de recursos de Azure que creó para esta aplicación lógica. 

### <a name="delete-resource-group"></a>Eliminación de un grupo de recursos

Si no desea conservar nada relacionado con la lógica de aplicaciones, elimine el grupo de recursos que creó para este tutorial rápido y todos los recursos relacionados. Más información sobre la [administración de grupos de recursos de Azure](../azure-resource-manager/resource-group-portal.md#manage-resources).

1. En el menú de Azure, elija **Grupos de recursos**.

2. Elija el grupo de recursos que desea eliminar. En el menú del grupo de recursos, elija **Introducción**, si aún no está seleccionado. 

3. Revise todos los recursos del grupo de recursos que desea eliminar. Cuando esté listo, elija **Eliminar grupo de recursos** de la barra de herramientas del grupo de recursos.

### <a name="turn-off-logic-app"></a>Desactivación de la aplicación lógica

Para detener la ejecución de la aplicación lógica sin eliminar el trabajo, deshabilítela. 

En el menú de la aplicación lógica, elija **Overview** (Información general). En la barra de herramientas, elija **Deshabilitar**.

  ![Desactive la aplicación lógica](./media/logic-apps-create-a-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Si no ve el menú de la aplicación lógica, intente volver al panel de Azure y vuelva a abrir la aplicación lógica.

### <a name="delete-logic-app"></a>Eliminación de la aplicación lógica

Puede eliminar solo la aplicación lógica y conservar todos los demás recursos relacionados, como las conexiones creadas.

1. En el menú de la aplicación lógica, elija **Introducción**. En la barra de herramientas, elija **Eliminar**. 

   ![Eliminación de la aplicación lógica](./media/logic-apps-create-a-logic-app/delete-logic-app.png)

   > [!TIP]
   > Si no ve el menú de la aplicación lógica, intente volver al panel de Azure y vuelva a abrir la aplicación lógica.

2. Confirme que quiere eliminar la aplicación lógica y elija **Eliminar**.

## <a name="get-support"></a>Obtención de soporte técnico

* Si tiene alguna duda, visite el [foro de Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ideas sobre características o votar sobre ellas, visite el [sitio de comentarios de los usuarios de Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Create logic app workflows from prebuilt templates](../logic-apps/logic-apps-create-logic-apps-from-templates.md) (Creación de flujos de trabajo de aplicación lógica a partir de plantillas precompiladas)
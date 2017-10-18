---
title: "Creación del primer flujo de trabajo automatizado entre sistemas y servicios en la nube: Azure Logic Apps | Microsoft Docs"
description: "Automatización de los procesos y los flujos de trabajo empresariales en escenarios de integración de aplicaciones empresariales (EAI) e integración de sistemas mediante la creación y la ejecución de aplicaciones lógicas"
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
ms.date: 09/18/2017
ms.author: LADocs; estfan
ms.openlocfilehash: d62255ba6e3d5bdfbd792a47f3a92d4c88876742
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-logic-app-for-automating-workflows-and-processes-through-the-azure-portal"></a>Creación de la primera aplicación lógica para la automatización de procesos y flujos de trabajo mediante Azure Portal

Puede integrar sistemas y servicios sin escribir código; para ello, compile y ejecute flujos de trabajo automatizados con [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md). Para mostrar cómo se pueden automatizar fácilmente tareas con un flujo de trabajo, en este tutorial se crea una aplicación lógica básica que comprueba el contenido nuevo de un sitio web en una fuente RSS y envía un correo electrónico por cada elemento nuevo en la fuente. 

![Información general: ejemplo de primera aplicación lógica](./media/logic-apps-create-a-logic-app/logic-app-overview.png)

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una aplicación lógica en blanco.
> * Agregar un desencadenador para iniciar la aplicación lógica cuando se publique un elemento de fuente RSS.
> * Agregar una acción para enviar un correo electrónico con información sobre el elemento de la fuente RSS.
> * Ejecutar y probar la aplicación lógica.

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción de Azure. Si no tiene una suscripción, puede [comenzar con una cuenta de Azure gratuita](https://azure.microsoft.com/free/). También puede [registrarse para obtener una suscripción de pago por uso](https://azure.microsoft.com/pricing/purchase-options/).

* Una cuenta de correo electrónico de [cualquier proveedor de correo electrónico que sea compatible con Azure Logic Apps](../connectors/apis-list.md) para el envío de notificaciones. Por ejemplo, Office 365 Outlook, Outlook.com, Gmail u otro proveedor compatible. Este tutorial usa Office 365 Outlook.

  > [!TIP]
  > Si tiene una [cuenta de Microsoft](https://account.microsoft.com/account) personal, tiene una cuenta de Outlook.com. Por otra parte, si dispone de una cuenta de Azure profesional o educativa, tiene una cuenta de Office 365 Outlook.

* Un vínculo a la fuente RSS de un sitio web. Este ejemplo utiliza la [fuente RSS para noticias destacadas desde el sitio web de CNN.com](http://rss.cnn.com/rss/cnn_topstories.rss): `http://rss.cnn.com/rss/cnn_topstories.rss`

## <a name="1-create-a-blank-logic-app"></a>1. Creación de una aplicación lógica en blanco 

1. Inicie sesión en [Azure Portal](https://portal.azure.com "Azure Portal").

2. En el menú principal de Azure, elija **Nuevo** > **Enterprise Integration** > **Logic App**.

   ![Portal de Azure, Nuevo, Enterprise Integration, Logic App](media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

3. Cree la aplicación lógica con la configuración especificada en la tabla.

   ![Proporcione los detalles de la aplicación lógica](./media/logic-apps-create-a-logic-app/logic-app-settings.png)

   | Configuración | Valor sugerido | Descripción | 
   | ------- | --------------- | ----------- | 
   | **Name** | *nombre-de-la-aplicación-lógica* | Proporcione un nombre único de aplicación lógica. | 
   | **Suscripción** | *suscripción-de-Azure* | Seleccione la suscripción de Azure que quiera usar. | 
   | **Grupos de recursos** | *grupo-de-recursos-de-Azure* | Cree o seleccione un grupo de recursos de Azure, que le ayude a organizar y administrar los recursos de Azure relacionados. | 
   | **Ubicación** | *región-de-Azure* | Seleccione la región del centro de datos para implementar la aplicación lógica. | 
   |||| 

4. Cuando esté listo, elija **Anclar al panel** y **Crear**.

   Ahora ha creado un recurso de Azure para la aplicación lógica. 
   Después de que Azure implemente la aplicación lógica, el Diseñador de aplicaciones lógicas muestra plantillas de patrones comunes de modo que pueda empezar con mayor rapidez.

   > [!NOTE] 
   > Al seleccionar **Anclar al panel**, la aplicación lógica aparece en el panel de Azure después de la implementación y se abre automáticamente en el diseñador de aplicaciones lógicas. De no ser así, puede buscarla y abrirla manualmente.

5. En **Plantillas**, elija **Aplicación lógica en blanco** para que pueda compilar la aplicación lógica desde el principio.

   ![Selección de la plantilla de aplicación lógica](./media/logic-apps-create-a-logic-app/choose-logic-app-template.png)

   El diseñador de aplicaciones lógicas ahora muestra los [*conectores*](../connectors/apis-list.md) disponibles y sus [*desencadenadores*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts), que utilizará para iniciar el flujo de trabajo de la aplicación lógica.

   ![Desencadenadores de aplicación lógica](./media/logic-apps-create-a-logic-app/logic-app-triggers.png)

## <a name="2-add-a-trigger-for-starting-the-workflow"></a>2. Incorporación de un desencadenador para iniciar el flujo de trabajo

Todas las aplicaciones lógicas deben comenzar con un [*desencadenador*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts). El desencadenador se activa cuando se produce un evento específico o cuando nuevos datos cumplen la condición establecida. El motor de Logic Apps crea una instancia de aplicación lógica para ejecutar el flujo de trabajo. Cada vez que el desencadenador se activa, el motor crea otra instancia independiente que ejecuta el flujo de trabajo de la aplicación lógica.

1. En el cuadro de búsqueda, escriba "rss" como filtro. Seleccione este desencadenador: **RSS: cuando se publica un elemento de fuente** 

   ![Selección del desencadenador "RSS: cuando se publica un elemento de fuente"](./media/logic-apps-create-a-logic-app/rss-trigger.png)

2. Especifique el vínculo de la fuente RSS del sitio web del que desea realizar un seguimiento, por ejemplo, `http://rss.cnn.com/rss/cnn_topstories.rss`. Defina el intervalo y la frecuencia de periodicidad. En este ejemplo, estas propiedades se definen para que se compruebe la fuente todos los días. 

   ![Configuración del desencadenador con la fuente RSS, la frecuencia y el intervalo](./media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

3. Por ahora, guarde el trabajo. En la barra de herramientas del diseñador, haga clic en **Guardar**.
Para contraer y ocultar los detalles del desencadenador, elija la barra de título del desencadenador.

   ![Guardado de la aplicación lógica](./media/logic-apps-create-a-logic-app/save-logic-app.png)

   La aplicación lógica ahora está activa, pero no hará nada más que comprobar si hay elementos nuevos en la fuente RSS hasta que agregue acciones al flujo de trabajo. 

## <a name="3-add-an-action-that-responds-to-the-trigger"></a>3. Incorporación de una acción que responda al desencadenador

Ahora, agregue una [*acción*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts), que es una tarea que realiza el flujo de trabajo de la aplicación lógica. En este ejemplo, agregue una acción para enviar un correo electrónico cuando aparezca un nuevo elemento en la fuente RSS.

1. En el diseñador de aplicaciones lógicas, en el desencadenador, elija **+ Nuevo paso** > **Agregar una acción**.

   ![Agregar una acción](./media/logic-apps-create-a-logic-app/add-new-action.png)

   El diseñador muestra los [conectores disponibles](../connectors/apis-list.md) de modo que puede seleccionar una acción para realizar cuando se dispare el desencadenador.

   ![Selección de la lista de acciones](./media/logic-apps-create-a-logic-app/logic-app-actions.png)

2. En el cuadro de búsqueda, escriba "enviar correo electrónico" como filtro. En función de su proveedor de correo electrónico, busque y seleccione el conector correspondiente. A continuación, seleccione la acción "enviar correo electrónico" para el conector. Por ejemplo: 

   * Para una cuenta profesional o educativa de Azure, seleccione el conector de Office 365 Outlook. 
   * Para las cuentas de Microsoft personales, seleccione el conector de Outlook.com. 
   * Para las cuentas de Gmail, seleccione el conector de Gmail. 

   Vamos a continuar con el conector de Office 365 Outlook. 
   Si utiliza un proveedor diferente, los pasos siguen siendo los mismos, pero la interfaz de usuario podría ser diferente. 

   ![Selección de la acción "Office 365 Outlook: enviar un correo electrónico"](./media/logic-apps-create-a-logic-app/actions.png)

3. Cuando se le pidan las credenciales, inicie sesión con el nombre de usuario y la contraseña de su cuenta de correo electrónico. 

4. Proporcione los detalles especificados en la tabla y elija los campos que desee incluir en el mensaje.

   | Para | Pasos | 
   | -- | ----- | 
   | Seleccione los campos disponibles para el flujo de trabajo. | Haga clic en un cuadro de edición para que se abra la lista **Contenido dinámico** o elija **Agregar contenido dinámico**. | 
   | Ver otros campos disponibles. | En la lista **Contenido dinámico**, elija **Ver más** para cada sección.  | 
   | Agregar líneas en blanco en un cuadro de edición. | Presione Mayús + Entrar. | 
   | Cerrar la lista **Contenido dinámico**. | Elija **Agregar contenido dinámico** de nuevo. | 
   ||| 

   ![Selección de los datos que se incluirán en el correo electrónico](./media/logic-apps-create-a-logic-app/rss-action-setup.png)

   | Configuración | Valor sugerido | Descripción | 
   | ------- | --------------- | ----------- | 
   | **To** | *dirección-de-correo electrónico-del-destinatario* | Escriba la dirección de correo electrónico del destinatario. Para realizar pruebas, puede usar su propia dirección de correo electrónico. | 
   | **Asunto** | Nueva publicación de CNN: **título de fuente** | Escriba el contenido del asunto del correo electrónico. <p>Para este tutorial, escriba el texto sugerido y seleccione el campo **Título de fuente** del desencadenador, que muestra el título del elemento de la fuente. | 
   | **Cuerpo** | Título: **título de fuente** <p>Fecha de publicación: **vínculo principal a la fuente** <p>Vínculo: **vínculo de fuente principal** | Escriba el contenido del cuerpo del correo electrónico. <p>Para este tutorial, escriba el texto sugerido y seleccione estos campos del desencadenador: <p>- **Título de fuente**, que muestra de nuevo el título del elemento de la fuente </br>- **Fecha de publicación de la fuente**, que muestra la fecha y la hora de publicación del elemento </br>- **Vínculo de fuente principal**, que muestra la dirección URL del elemento de la fuente | 
   |||| 

   > [!NOTE] 
   > Si selecciona un campo que almacena una matriz, el diseñador agrega automáticamente un bucle "Para cada uno" en la acción que haga referencia a la matriz. De este modo, la aplicación lógica realiza la acción en cada elemento de la matriz.

5. Cuando haya terminado, guarde los cambios. En la barra de herramientas del diseñador, haga clic en **Guardar**.

   ![Aplicación lógica completada](./media/logic-apps-create-a-logic-app/save-complete-logic-app.png)

   Para probar la aplicación lógica ahora, pase a la sección siguiente.

## <a name="4-run-and-test-your-workflow"></a>4. Ejecución y prueba de la aplicación lógica

1. Para ejecutar manualmente la aplicación lógica para realizar pruebas, en la barra de la barra de herramientas del diseñador, elija **Ejecutar**. También puede dejar que la aplicación lógica compruebe la fuente RSS especificada según la programación que haya configurado.

   ![Ejecución de la aplicación lógica](./media/logic-apps-create-a-logic-app/run-complete-logic-app.png)

   Si la aplicación lógica encuentra nuevos elementos, envía un correo electrónico que incluye los datos seleccionados, por ejemplo:

   ![Envío de correo electrónico por un elemento nuevo en la fuente RSS](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

   Si la aplicación lógica no encuentra elementos nuevos, omite la acción de envío de correo electrónico y espera al siguiente intervalo para volver a realizar la comprobación. 

2. Para revisar el historial de ejecución y desencadenamiento de la aplicación lógica, elija **Introducción**.
Para ver más detalles acerca de una ejecución, elija la fila correspondiente a esa ejecución.

   ![Supervisión y visualización del historial de ejecución y desencadenamiento de la aplicación lógica](./media/logic-apps-create-a-logic-app/logic-app-run-trigger-history.png)

   > [!TIP]
   > Si no encuentra los datos previstos, en la barra de herramientas, elija **Actualizar**.

   Tanto si la ejecución se supera como si no, en la vista Detalles de ejecución se muestran los pasos superados y erróneos. 

   ![Visualización de los detalles de una ejecución de aplicación lógica](./media/logic-apps-create-a-logic-app/logic-app-run-details.png)

   Para más información sobre el estado de la aplicación lógica, el historial de ejecución o de desencadenamiento, o para diagnosticar la aplicación lógica, consulte el artículo de [solución de problemas con las aplicaciones lógicas](../logic-apps/logic-apps-diagnosing-failures.md).

3. Para ver las entradas y salidas de cada paso, expanda el paso que desea revisar. Esta información puede ayudarle a diagnosticar y depurar los problemas de la aplicación lógica. Por ejemplo:

   ![Visualización de los detalles del paso](./media/logic-apps-create-a-logic-app/logic-app-run-details-expanded.png)

   Para más información, consulte el artículo sobre la [supervisión de aplicaciones lógicas](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Enhorabuena, acaba de crear y ejecutar la primera aplicación lógica básica. Este ejemplo muestra cómo crear fácilmente flujos de trabajo que automaticen los procesos de integración de sistemas y servicios, sin código.

> [!NOTE]
> La aplicación lógica continúa ejecutándose hasta que desactiva la aplicación. Para desactivar temporalmente la aplicación, continúe con la sección siguiente.

## <a name="clean-up-resources"></a>Limpieza de recursos

Este tutorial utiliza recursos y realiza acciones que podrían generar cargos en su suscripción de Azure. Cuando haya terminado con el tutorial y las pruebas, asegúrese de deshabilitar o eliminar los recursos si no desea que generen cargos.

Puede detener la ejecución de la aplicación lógica y el envío de correo electrónico sin eliminar la aplicación. En el menú de la aplicación lógica, elija **Overview** (Información general). En la barra de herramientas, elija **Deshabilitar**.

![Desactive la aplicación lógica](./media/logic-apps-create-a-logic-app/turn-off-disable-logic-app.png)

## <a name="faq"></a>P+F

**P:** ¿Qué más puedo hacer con mi aplicación lógica? </br>
**R:** Hay otras tareas que puede realizar, por ejemplo, editar, ver la definición de JSON, revisar el registro de actividad o eliminar la aplicación lógica.

Para buscar otras tareas de administración de la aplicación lógica, revise estos comandos en el menú de esta:

| Tarea | Pasos | 
| ---- | ----- | 
| Ver el estado de la aplicación, el historial de ejecución y desencadenamiento e información general | Elija **Overview** (Información general). | 
| Editar la aplicación | Elija **Diseñador de aplicación lógica**. | 
| Ver la definición JSON del flujo de trabajo de la aplicación | Elija **Vista de código de aplicación lógica**. | 
| Ver las operaciones realizadas en la aplicación lógica | Elija **Registro de actividad**. | 
| Ver las versiones pasadas de la aplicación lógica | Elija **Versiones**. | 
| Desactivar la aplicación temporalmente | Elija **Introducción** y, en la barra de herramientas, elija **Desactivar**. | 
| Eliminar la aplicación | Elija **Introducción** y, en la barra de herramientas, elija **Eliminar**. Escriba el nombre de la aplicación lógica y elija **Eliminar**. | 
||| 

## <a name="get-support"></a>Obtención de soporte técnico

* Si tiene preguntas sobre Azure Logic Apps, visite el [foro de Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* ¿Desea ayudar a mejorar Azure Logic Apps y los conectores? Vote sobre las ideas en el [sitio Azure Logic Apps User Voice](http://aka.ms/logicapps-wish) o envíe las suyas.

## <a name="next-steps"></a>Pasos siguientes

* [Creación de aplicaciones lógicas con Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md)
* [Adición de condiciones y ejecución de flujos de trabajo](../logic-apps/logic-apps-use-logic-app-features.md)
*   [Plantillas de aplicaciones lógicas](../logic-apps/logic-apps-use-logic-app-templates.md)
* [Creación de aplicaciones lógicas a partir de plantillas de Azure Resource Manager](../logic-apps/logic-apps-arm-provision.md)
* [Medición del uso de Logic Apps](../logic-apps/logic-apps-pricing.md) 
* [Precios de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps)

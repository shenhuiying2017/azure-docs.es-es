---
title: "Creación del primer flujo de trabajo entre aplicaciones de nube y servicios en la nube: Azure Logic Apps | Microsoft Docs"
description: "Automatización de los procesos empresariales en escenarios de integración de aplicaciones empresariales (EAI) e integración de sistemas en Azure Logic Apps"
author: jeffhollan
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
ms.date: 03/31/2017
ms.author: LADocs; jehollan; estfan
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 204bf123509729b60b55c306050cef54aa7fecc5
ms.contentlocale: es-es
ms.lasthandoff: 05/17/2017

---

# <a name="create-your-first-logic-app-workflow-to-automate-processes-between-cloud-apps-and-cloud-services"></a>Creación de su primer flujo de trabajo de aplicación lógica para automatizar los procesos entre aplicaciones de nube y servicios en la nube

Puede automatizar los procesos empresariales de manera más fácil y rápida cuando crea y ejecuta flujos de trabajo con [Azure Logic Apps](logic-apps-what-are-logic-apps.md), sin necesidad de escribir ningún código. En este primer ejemplo se muestra cómo crear un flujo de trabajo básico de aplicaciones lógicas que busca nuevo contenido de un sitio web en una fuente RSS. Cuando aparecen nuevos elementos en la fuente del sitio web, la aplicación lógica envía un correo electrónico desde una cuenta de Outlook o Gmail.

Para crear y ejecutar una aplicación lógica, necesita estos elementos:

* Una suscripción de Azure. Si no tiene una suscripción, puede [comenzar con una cuenta de Azure gratuita](https://azure.microsoft.com/free/). También puede [registrarse para obtener una suscripción de pago por uso](https://azure.microsoft.com/pricing/purchase-options/).

  Su suscripción de Azure se usa para facturar el uso de la aplicación lógica. Aprenda cómo funciona el [medidor de uso](../logic-apps/logic-apps-pricing.md) y los [precios](https://azure.microsoft.com/pricing/details/logic-apps) en Azure Logic Apps.

Además, para este ejemplo se necesitan estos elementos:

* Una cuenta de Outlook.com, Office 365 Outlook o Gmail.

    > [!TIP]
    > Si tiene una [cuenta de Microsoft](https://account.microsoft.com/account) personal, tiene una cuenta de Outlook.com. También, si tiene una cuenta de Azure profesional o educativa, tiene una cuenta de **Office 365 Outlook**.

* Un vínculo a la fuente RSS de un sitio web. Este ejemplo utiliza la [fuente RSS para noticias destacadas desde el sitio web de CNN.com](http://rss.cnn.com/rss/cnn_topstories.rss): `http://rss.cnn.com/rss/cnn_topstories.rss`

## <a name="add-a-trigger-that-starts-your-workflow"></a>Adición de un desencadenador que inicie el flujo de trabajo

Un [*desencadenador*](./logic-apps-what-are-logic-apps.md#logic-app-concepts) es un evento que inicia el flujo de trabajo de la aplicación lógica y es el primer elemento que necesita la aplicación lógica.

1. Inicie sesión en [Azure Portal](https://portal.azure.com "Azure Portal").

2. En el menú izquierdo, elija **Nuevo** > **Enterprise Integration** > **Logic App**, como se muestra aquí.

     ![Portal de Azure, Nuevo, Enterprise Integration, Logic App](media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

   > [!TIP]
   > También puede elegir **Nuevo** y, después, en el cuadro de búsqueda escribir `logic app` y presionar Entrar. A continuación, elija **Logic App** > **Crear**.

3. Asigne un nombre a la aplicación lógica y seleccione su suscripción de Azure. Ahora, cree o seleccione un grupo de recursos de Azure, que le ayuda a organizar y administrar los recursos de Azure relacionados. Por último, seleccione la ubicación del centro de datos para hospedar la aplicación lógica. Cuando esté listo, elija **Anclar al panel** y, luego, **Crear**.

     ![Detalles de la aplicación lógica](media/logic-apps-create-a-logic-app/logic-app-settings.png)

   > [!NOTE]
   > Al seleccionar **Anclar al panel**, la aplicación lógica aparece en el panel de Azure después de la implementación y se abre automáticamente. Si la aplicación lógica no aparece en el panel, en el icono **Todos los recursos**, elija **See More** (Ver más) y seleccione la aplicación lógica. O, en el menú de la izquierda, elija **More services** (Más servicios). En **Enterprise Integration**, elija **Logic Apps** y seleccione su aplicación lógica.

4. La primera vez que se abre una aplicación lógica, el Diseñador de aplicación lógica muestra plantillas que puede usar para comenzar a trabajar. Por ahora, elija **Aplicación lógica en blanco** para que pueda crear la aplicación lógica desde el principio.

    Se abre el Diseñador de aplicación lógica que muestra los servicios y los *desencadenadores* disponibles que puede usar en la aplicación lógica.

5. En el cuadro de búsqueda, escriba `RSS` y seleccione este desencadenador: **RSS: Cuando se publica un elemento de fuente**. 

    ![Desencadenador de RSS](media/logic-apps-create-a-logic-app/rss-trigger.png)

6. Especifique el vínculo de la fuente RSS del sitio Web del que desea realizar un seguimiento. 

     También puede cambiar la **frecuencia** y el **intervalo**. 
     Esta configuración determina la frecuencia con que la aplicación lógica busca nuevos elementos y devuelve todos los elementos encontrados durante ese intervalo de tiempo.

     En este ejemplo, vamos a comprobar cada día las noticias destacadas publicadas en el sitio web de CNN.

     ![Configuración del desencadenador con la fuente RSS, la frecuencia y el intervalo](media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

7. Por ahora, guarde el trabajo. (En la barra de comandos del diseñador, elija **Guardar**).

   ![Guardado de la aplicación lógica](media/logic-apps-create-a-logic-app/save-logic-app.png)

   Al guardar, la aplicación lógica se activa; pero, actualmente, la aplicación lógica solo comprueba si hay nuevos elementos en la fuente RSS especificada. 
   Para que este ejemplo sea más útil, agregamos una acción que la aplicación lógica realiza después de que se dispara el desencadenador.

## <a name="add-an-action-that-responds-to-your-trigger"></a>Adición de una acción que responde al desencadenador

Una [*acción*](./logic-apps-what-are-logic-apps.md#logic-app-concepts) es una tarea realizada por el flujo de trabajo de la aplicación lógica. Después de agregar un desencadenador a la aplicación lógica, puede agregar una acción para llevar a cabo operaciones con los datos generados por ese desencadenador. En nuestro ejemplo, ahora agregamos una acción que envía un correo electrónico cuando aparecen nuevos elementos en la fuente RSS del sitio web.

1. En el diseñador, bajo el desencadenador, elija **Nuevo paso** > **Agregar una acción**, tal y como se muestra aquí:

   ![Agregar una acción](media/logic-apps-create-a-logic-app/add-new-action.png)

   El diseñador muestra los [conectores disponibles](../connectors/apis-list.md) de modo que puede seleccionar una acción para realizar cuando se dispare el desencadenador.

2. Según su cuenta de correo electrónico, siga los pasos para Outlook o Gmail.

   * Para enviar un correo electrónico desde su cuenta de Outlook, escriba `outlook` en el cuadro de búsqueda. En **Servicios**, elija **Outlook.com** para cuentas de Microsoft personales, o elija **Office 365 Outlook** para cuentas de Azure profesionales o educativas. 
   En **Acciones**, seleccione **Enviar un correo electrónico**.

       ![Selección de la acción "Enviar un correo electrónico" de Outlook](media/logic-apps-create-a-logic-app/actions.png)

   * Para enviar un correo electrónico desde su cuenta de Gmail, escriba `gmail` en el cuadro de búsqueda. 
   En **Acciones**, seleccione **Enviar un correo electrónico**.

       ![Selección de "Gmail: Enviar un correo electrónico"](media/logic-apps-create-a-logic-app/actions-gmail.png)

3. Cuando se le pidan las credenciales, inicie sesión con el nombre de usuario y la contraseña de su cuenta de correo electrónico. 

4. Proporcione los detalles de esta acción, como la dirección de correo electrónico de destino, y elija los parámetros de los datos que se incluirán en el correo electrónico, por ejemplo:

   ![Selección de los datos que se incluirán en el correo electrónico](media/logic-apps-create-a-logic-app/rss-action-setup.png)

    Si eligió Outlook, la aplicación lógica podría parecerse a este ejemplo:

    ![Aplicación lógica completada](media/logic-apps-create-a-logic-app/save-run-complete-logic-app.png)

5.    Guarde los cambios. (En la barra de comandos del diseñador, elija **Guardar**).

6. Ahora puede ejecutar manualmente la aplicación lógica para realizar pruebas. En la barra de comandos del diseñador, elija **Ejecutar**. También, puede dejar que la aplicación lógica compruebe la fuente RSS especificada según la programación que haya configurado.

   Si la aplicación lógica encuentra nuevos elementos, envía un correo electrónico que incluye los datos seleccionados. 
   Si no encuentra ningún elemento nuevo, la aplicación lógica omite la acción de enviar un correo electrónico.

7. Para supervisar y comprobar el historial de ejecución y desencadenamiento de la aplicación lógica, elija **Overview** (Información general).

   ![Supervisión y visualización del historial de ejecución y desencadenamiento de la aplicación lógica](media/logic-apps-create-a-logic-app/logic-app-run-trigger-history.png)

   > [!TIP]
   > Si no encuentra los datos que espera, en la barra de comandos, elija **Actualizar**.

   Para más información sobre el estado de la aplicación lógica o el historial de ejecución y desencadenamiento, o para diagnosticar la aplicación lógica, consulte [Solución de problemas de las aplicaciones lógicas](logic-apps-diagnosing-failures.md).

      > [!NOTE]
      > La aplicación lógica continúa ejecutándose hasta que desactiva la aplicación. Por ahora, para desactivar la aplicación elija **Overview** (Información general) en el menú de la aplicación lógica. En la barra de comandos, haga clic en **Deshabilitar**.

Enhorabuena, acaba de configurar y ejecutar la primera aplicación lógica básica. También ha aprendido cómo puede crear fácilmente flujos de trabajo que automatizan los procesos, y a integrar aplicaciones de nube y servicios en la nube sin necesidad de código.

## <a name="manage-your-logic-app"></a>Administración de la aplicación lógica

Para administrar la aplicación, puede realizar tareas como editar la aplicación lógica, desactivarla, eliminarla, comprobar su estado o ver su historial.

1. Inicie sesión en [Azure Portal](https://portal.azure.com "Azure Portal").

2. En el menú de la izquierda, elija **More services** (Más servicios). En **Enterprise Integration**, elija **Logic Apps**. Seleccione la aplicación lógica. 

   En el menú de la aplicación lógica, puede encontrar estas tareas de administración de aplicaciones lógicas:

   |Tarea|Pasos| 
   |:---|:---| 
   | Ver el estado de la aplicación, el historial de ejecución e información general| Elija **Overview** (Información general).| 
   | Editar la aplicación | Elija **Diseñador de aplicación lógica**. | 
   | Ver la definición JSON del flujo de trabajo de la aplicación | Elija **Vista de código de aplicación lógica**. | 
   | Ver las operaciones realizadas en la aplicación lógica | Elija **Registro de actividad**. | 
   | Ver las versiones pasadas de la aplicación lógica | Elija **Versiones**. | 
   | Desactivar la aplicación temporalmente | Elija **Overview** (Información general) y, en la barra de comandos, elija **Desactivar**. | 
   | Eliminar la aplicación | Elija **Overview** (Información general) y, en la barra de comandos, elija **Eliminar**. Escriba el nombre de la aplicación lógica y elija **Eliminar**. | 

## <a name="get-help"></a>Obtener ayuda

Para formular preguntas, o responderlas, y saber lo que hacen otros usuarios de Azure Logic Apps, visite el [foro de Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Para ayudar a mejorar Azure Logic Apps y los conectores, vote o envíe ideas en el [sitio de comentarios de usuario de Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Pasos siguientes

*  [Adición de condiciones y ejecución de flujos de trabajo](../logic-apps/logic-apps-use-logic-app-features.md)
*     [Plantillas de aplicaciones lógicas](../logic-apps/logic-apps-use-logic-app-templates.md)
*  [Creación de aplicaciones lógicas a partir de plantillas de Azure Resource Manager](../logic-apps/logic-apps-arm-provision.md)


---
title: "Creación de flujos de trabajo de aprobación para procesar solicitudes de la lista de distribución de correo: Azure Logic Apps | Microsoft Docs"
description: "Este tutorial muestra cómo crear flujos de trabajo de aprobación automatizados para el procesamiento de suscripciones de listas de distribución de correo con Azure Logic Apps"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/12/2018
ms.author: LADocs; estfan
ms.openlocfilehash: 26ef6f69ef2f2d50628f4d0b021159526c9a04a7
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="manage-mailing-list-requests-with-a-logic-app"></a>Administración de solicitudes de lista de distribución de correo con una aplicación lógica

Azure Logic Apps le ayuda a automatizar los flujos de trabajo y a integrar los datos entre servicios de Azure, servicios de Microsoft, otras aplicaciones de software como servicio (SaaS) y sistemas locales. Este tutorial muestra la compilación de una [aplicación lógica](../logic-apps/logic-apps-overview.md) que procesa las solicitudes de suscripción para obtener una lista de distribución de correo que administra el servicio [MailChimp](https://mailchimp.com/).
Esta aplicación lógica supervisa estas solicitudes en una cuenta de correo electrónico, las envía para su aprobación y agrega los miembros aprobados a la lista de distribución de correo.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una aplicación lógica en blanco.
> * Agregue un desencadenador que supervise las solicitudes de suscripción en los correos electrónicos.
> * Agregue una acción que envíe correos electrónicos para aprobar o rechazar estas solicitudes.
> * Agregue una condición que compruebe la respuesta de aprobación.
> * Agregue una acción que permita incorporar los miembros aprobados a la lista de distribución de correo.
> * Agregue una condición que compruebe si estos miembros se han unido correctamente a la lista.
> * Agregue una condición que envíe correos electrónicos que confirmen si estos miembros se han unido correctamente a la lista.

Cuando haya terminado, la aplicación lógica se parecerá a este flujo de trabajo, en un alto nivel:

![Aplicación lógica de alto nivel finalizada](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-overview.png)

Si no tiene ninguna suscripción a Azure, <a href="https://azure.microsoft.com/free/" target="_blank">cree una cuenta gratuita de Azure</a> antes de empezar.

## <a name="prerequisites"></a>requisitos previos

* Una cuenta de MailChimp. Cree una lista denominada "test-members-ML" en la que la aplicación lógica pueda agregar direcciones de correo electrónico para los miembros aprobados. Si no tiene una cuenta, [regístrese en una cuenta gratuita](https://login.mailchimp.com/signup/) y aprenda a [crear una lista](https://us17.admin.mailchimp.com/lists/#). 

* Una cuenta de correo electrónico con Office 365 Outlook o Outlook.com, que admita los flujos de trabajo de aprobación. Este artículo utiliza Office 365 Outlook. Si utiliza una cuenta de correo electrónico diferente, los pasos generales siguen siendo los mismos pero la interfaz de usuario podría ser ligeramente distinta.

## <a name="sign-in-to-the-azure-portal"></a>Inicie sesión en el Portal de Azure.

Inicie sesión en <a href="https://portal.azure.com" target="_blank">Azure Portal</a> con las credenciales de su cuenta de Azure.

## <a name="create-your-logic-app"></a>Creación de una aplicación lógica

1. En el menú principal de Azure, elija **Nuevo** > **Enterprise Integration** > **Logic App**.

   ![Creación de la aplicación lógica](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app.png)

2. En **Crear aplicación lógica**, proporcione esta información sobre la aplicación lógica tal como se muestra y se describe a continuación. Cuando esté listo, elija **Anclar al panel** > **Crear**.

   ![Especificación de información de la aplicación lógica](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Configuración | Valor | DESCRIPCIÓN | 
   | ------- | ----- | ----------- | 
   | **Name** | LA-MailingList | Nombre de la aplicación lógica | 
   | **Suscripción** | <*nombre-de-su-suscripción-a-Azure*> | El nombre de la suscripción a Azure | 
   | **Grupos de recursos** | LA-MailingList-RG | Nombre del [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md) que se utiliza para organizar recursos relacionados | 
   | **Ubicación** | Este de EE. UU. 2 | Región en la que desea almacenar información sobre la aplicación lógica | 
   | **Log Analytics** | Off | Mantenga el valor **Off** para el registro de diagnóstico. | 
   |||| 

3. Una vez que Azure haya implementado la aplicación, el Diseñador de aplicaciones lógicas se abre y muestra una página con un vídeo de introducción y plantillas para patrones de aplicaciones lógicas comunes. En **plantillas**, elija **Aplicación lógica en blanco**.

   ![Selección de la plantilla de aplicación lógica en blanco](./media/tutorial-process-mailing-list-subscriptions-workflow/choose-logic-app-template.png)

A continuación, agregue un [desencadenador](../logic-apps/logic-apps-overview.md#logic-app-concepts) que escuche los mensajes de correo electrónico entrantes que tienen solicitudes de suscripción.
Cada aplicación lógica debe comenzar con un desencadenador, que se activa cuando sucede un evento específico o cuando hay nuevos datos que cumplen una condición determinada. Para más información, consulte [Creación de una nueva aplicación lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-trigger-to-monitor-emails"></a>Agregar desencadenador para supervisar los correos electrónicos

1. En el diseñador, escriba "when email arrives" (cuando llega correo electrónico) en el cuadro de búsqueda. Seleccione el desencadenador para su proveedor de correo electrónico: **<*su proveedor de correo electrónico*> - Cuando llega un nuevo correo electrónico**
   
   ![Seleccionar este desencadenador para el proveedor de correo electrónico: "When a new email arrives" (Cuando llega un nuevo correo electrónico)](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

   * Para las cuentas profesionales o educativas de Azure, seleccione Office 365 Outlook.
   * Para las cuentas de Microsoft personales, seleccione Outlook.com.

2. Si se le piden credenciales, inicie sesión en su cuenta de correo electrónico para que Logic Apps pueda crear una conexión a su cuenta de correo electrónico.

3. Ahora puede especificar los criterios que el desencadenador debe comprobar en todo correo electrónico nuevo.

   1. Especifique la carpeta, el intervalo y la frecuencia de comprobación del correo electrónico.

      ![Especificar la carpeta, el intervalo y la frecuencia de comprobación de los correos electrónicos](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

      | Configuración | Valor | DESCRIPCIÓN | 
      | ------- | ----- | ----------- | 
      | **Carpeta** | Bandeja de entrada | La carpeta de correo electrónico para supervisar | 
      | **Intervalo** | 1 | Número de intervalos que se espera entre comprobaciones | 
      | **Frecuencia** | Hora | La unidad de tiempo de cada intervalo entre comprobaciones  | 
      |  |  |  | 

   2. Seleccione **Mostrar opciones avanzadas**. En el cuadro **Filtro de asunto**, escriba este texto para que el desencadenador lo busque en el asunto de los correos electrónicos: ```subscribe-test-members-ML```

      ![Establecimiento de opciones avanzadas](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-advanced-options.png)

4. Para ocultar por ahora los detalles del desencadenador, haga clic dentro de la barra de título del desencadenador.

   ![Contraer la forma para ocultar detalles](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

5. Guarde la aplicación lógica. En la barra de herramientas del diseñador, haga clic en **Guardar**.

   La aplicación lógica estará activa, pero no hace más que comprobar el correo electrónico de entrada. 
   Por lo tanto, agregue una acción que responda cuando se active el desencadenador.

## <a name="send-approval-email"></a>Enviar correo electrónico de aprobación

Ahora que tiene un desencadenador, agregue una [acción](../logic-apps/logic-apps-overview.md#logic-app-concepts) que envíe un correo electrónico de aprobación o rechazo de la solicitud. 

1. En el desencadenador, elija **+ Nuevo paso** > **Agregar una acción**. Busque "aprobación" y seleccione esta acción: **<*proveedor de correo electrónico*> - Enviar correo electrónico de aprobación**

   ![Seleccionar "<proveedor de correo electrónico> - Enviar correo electrónico de aprobación"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

2. Proporcione información de esta acción como se muestra y se describe a continuación: 

   ![Establecimiento de la configuración de correo electrónico de aprobación](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Configuración | Valor | DESCRIPCIÓN | 
   | ------- | ----- | ----------- | 
   | **To** | <*approver-email-address*> | Dirección de correo electrónico del aprobador. Para realizar pruebas, puede usar su propia dirección de correo electrónico. | 
   | **Opciones de usuario** | Aprobar, Rechazar | Las opciones de respuesta que puede elegir el aprobador. De forma predeterminada, el aprobador puede elegir "Aprobar" o "Rechazar" como respuesta. | 
   | **Asunto** | Aprobación de solicitud de miembro de test-members-ML | Un asunto de correo electrónico descriptivo | 
   |  |  |  | 

   Por ahora, omita la lista de contenido dinámico o la lista de parámetros insertada que aparece al hacer clic en los cuadros específicos de edición. 
   Esta lista le permite seleccionar los parámetros de las acciones anteriores que puede usar como entradas en el flujo de trabajo. 
   El ancho del explorador determina lo que aparece en la lista. 
 
4. Guarde la aplicación lógica.

A continuación, agregue una condición para comprobar la respuesta elegida del aprobador.

## <a name="check-approval-response"></a>Comprobación de la respuesta de aprobación

1. En la acción **Enviar correo electrónico de aprobación**, elija **+ Nuevo paso** > **Agregar una condición**.

   La forma de la condición aparece junto con todos los parámetros disponibles que puede incluir como entrada en el flujo de trabajo. 

2. Cambie el nombre de la condición por una descripción mejor.

   1. En la barra de título de la condición, elija el botón **puntos suspensivos** (**...** ) > **Rename** (Cambiar nombre).

      Por ejemplo, si el explorador se encuentra en vista reducida:

      ![Cambiar nombre de condición](./media/tutorial-process-mailing-list-subscriptions-workflow/condition-rename.png)

      Si el explorador se encuentra en vista amplia y la lista de contenido dinámico boquea el botón de puntos suspensivos, cierre la lista; para ello, elija **Add dynamic content** (Agregar contenido dinámico) dentro de la condición.

   2. Cambie el nombre de la condición con esta descripción: ```If request approved```

3. Cree una condición que compruebe si el aprobador ha seleccionado **Aprobar**:

   1. En la condición, haga clic dentro del cuadro **Elegir un valor**, que está a la izquierda (vista ampliada del explorador) o en la parte superior (vista reducida del explorador).
   En la lista de parámetros o la lista de contenido dinámico, seleccione el campo **SelectedOption** en **Enviar correo electrónico de aprobación**.

      Por ejemplo, si está trabajando en la vista ampliada, la condición debe ser similar a la de este ejemplo:

      ![En "Enviar correo electrónico de aprobación", seleccione "SelectedOption"](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   2. En el cuadro del operador de comparación, seleccione este operador: **es igual que**

   3. En la parte derecha (vista ampliada) o en la parte inferior (vista reducida), en el cuadro **Elegir un valor**, especifique este valor: ```Approve```

      Una vez que haya terminado, la condición debe ser parecida a la de este ejemplo:

      ![Condición completa](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

4. Guarde la aplicación lógica.

A continuación, especifique la acción que realizará la aplicación lógica cuando el revisor apruebe la solicitud. 

## <a name="add-member-to-mailchimp-list"></a>Adición de miembros a la lista MailChimp

Ahora, agregue una acción que incorpore al miembro aprobado a la lista de distribución de correo electrónico.

1. En la rama **If true** (Si es true) de la condición, elija **Agregar una acción**.
Busque "mailchimp" y seleccione esta acción: **MailChimp: Agregar un miembro a una lista**

   ![Seleccionar "MailChimp: Agregar un miembro a una lista"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

3. Si se le pide que inicie sesión en su cuenta de MailChimp, hágalo con sus credenciales correspondientes.

4. Proporcione información de esta acción como se muestra y se describe aquí:

   ![Proporcionar la información de "Agregar un miembro a una lista"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Configuración | Valor | DESCRIPCIÓN | 
   | ------- | ----- | ----------- | 
   | **Identificador de la lista** | test-members-ML | El nombre de la lista de distribución de correo electrónico de MailChimp | 
   | **Estado** | subscribed | El estado de la suscripción del nuevo miembro. Para más información, consulte <a href="https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/" target="_blank">Manage subscribers with the MailChimp API</a> (Administración de suscriptores con MailChimp API). | 
   | **Dirección de correo electrónico** | <*new-member-email-address*> | En la lista de parámetros o la lista de contenido dinámico, seleccione **From** (De) en **When a new email arrives** (Cuando llega un nuevo correo electrónico) que pasará la dirección de correo electrónico del nuevo miembro. 
   |  |  |  | 

5. Guarde la aplicación lógica.

A continuación, agregue una condición para comprobar si el nuevo miembro se ha unido correctamente a la lista de distribución de correo electrónico. De este modo, la aplicación lógica le notificará si esta operación se ha realizado correctamente o si se ha producido un error.

## <a name="check-for-success-or-failure"></a>Comprobación de si la operación se ha realizado correctamente o se ha producido un error

1. En la rama **If true** (Si es true), en la acción **Agregar un miembro a una lista**, elija **Más...** > **Agregar una condición**.

2. Cambie el nombre de la condición por esta descripción: ```If add member succeeded```

3. Cree una condición que compruebe si la unión del miembro aprobado a la lista de distribución de correo electrónico se ha realizado correctamente o se ha producido un error:

   1. En la condición, haga clic dentro del cuadro **Elegir un valor**, que está a la izquierda (vista ampliada del explorador) o en la parte superior (vista reducida del explorador).
   En la lista de parámetros o en la de contenido dinámico, seleccione el campo **Status** (Estado) en **Agregar un miembro a una lista**.

      Por ejemplo, si está trabajando en la vista ampliada, la condición debe ser similar a la de este ejemplo:

      ![En "Agregar un miembro a una lista", seleccionar "Status" (Estado)](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   2. En el cuadro del operador de comparación, seleccione este operador: **es igual que**

   3. En la parte derecha (vista ampliada) o en la parte inferior (vista reducida), en el cuadro **Elegir un valor**, especifique este valor: ```subscribed```

   Una vez que haya terminado, la condición debe ser parecida a la de este ejemplo:

   ![Condición completada](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

A continuación, configure los correos electrónicos que se enviarán si la unión del miembro aprobado a la lista de distribución de correo electrónico se ha realizado correctamente o si se ha producido un error.

## <a name="send-email-if-member-added"></a>Envío de correo electrónico si se ha agregado el miembro

1. En la rama **If true** (Si es true) de la condición **If add member succeeded** (Si la incorporación del miembro se ha realizado correctamente), elija **Agregar una acción**.

   ![En la rama "If true" (Si es true) de la condición, elija "Agregar una acción".](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

2. Busque "Outlook envía correos electrónicos" y seleccione esta acción: **<*proveedor de correo electrónico*> - Enviar un correo electrónico**

   ![Agregar acción para "Enviar un correo electrónico"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

3. Cambie el nombre de la acción por esta descripción: ```Send email on success```

4. Proporcione información de esta acción como se muestra y se describe a continuación:

   ![Proporcionar información para el correo electrónico de confirmación de unión correcta](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Configuración | Valor | DESCRIPCIÓN | 
   | ------- | ----- | ----------- | 
   | **To** | <*your-email-address*> | La dirección de correo electrónico a la que enviar el correo electrónico de confirmación de que la unión se ha realizado correctamente. Para realizar pruebas, puede usar su propia dirección de correo electrónico. | 
   | **Asunto** | <*subject-for-success-email*> | El asunto del correo electrónico de confirmación de que la unión se ha realizado correctamente. Para este tutorial, escriba este texto y seleccione el campo especificado en **Agregar un miembro a una lista** en la lista de parámetros o la lista de contenido dinámico: <p>"Correcto. Se ha agregado al miembro a 'test-members-ML': **Dirección de correo electrónico**" | 
   | **Cuerpo** | <*body-for-success-email*> | El contenido del cuerpo del correo electrónico de confirmación de unión correcta. Para este tutorial, escriba este texto y seleccione los campos especificados en **Agregar un miembro a una lista** en la lista de parámetros o la lista de contenido dinámico:  <p>"El nuevo miembro se ha unido a 'test-members-ML': **Dirección de correo electrónico**"</br>"Estado de participación del miembro: **Estado**" | 
   | | | | 

5. Guarde la aplicación lógica.

## <a name="send-email-if-member-not-added"></a>Envío de correo electrónico si el miembro no se ha agregado

1. En la rama **If false** (Si es false) de la condición **If add member succeeded** (Si la incorporación del miembro se ha realizado correctamente), elija **Agregar una acción**.

   ![En la rama "If false" (Si es false) de la condición, elija "Agregar una acción".](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

2. Busque "Outlook envía correos electrónicos" y seleccione esta acción: **<*proveedor de correo electrónico*> - Enviar un correo electrónico**

   ![Agregar acción para "Enviar un correo electrónico"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

3. Cambie el nombre de la acción por esta descripción: ```Send email on failure```

4. Proporcione información de esta acción como se muestra y se describe aquí:

   ![Proporcionar información para el correo electrónico de error](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Configuración | Valor | DESCRIPCIÓN | 
   | ------- | ----- | ----------- | 
   | **To** | <*your-email-address*> | La dirección de correo electrónico a la que enviar el correo electrónico de error. Para realizar pruebas, puede usar su propia dirección de correo electrónico. | 
   | **Asunto** | <*subject-for-failure-email*> | El asunto del correo electrónico de error. Para este tutorial, escriba este texto y seleccione el campo especificado en **Agregar un miembro a una lista** en la lista de parámetros o la lista de contenido dinámico: <p>"Error, el miembro no se ha agregado a 'test-members-ML': **Dirección de correo electrónico**" | 
   | **Cuerpo** | <*body-for-failure-email*> | El contenido del cuerpo del correo electrónico de error. Para este tutorial, escriba este texto: <p>"Puede que ya exista este miembro. Compruebe su cuenta de MailChimp". | 
   | | | | 

5. Guarde la aplicación lógica. 

A continuación, pruebe la aplicación lógica, que ahora es similar a este ejemplo:

 ![Aplicación lógica terminada](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-complete.png)

## <a name="run-your-logic-app"></a>Ejecución de la aplicación lógica

1. Envíese a sí mismo una solicitud de correo electrónico para unirse a la lista de distribución de correo electrónico.
Espere a que la solicitud aparezca en la bandeja de entrada.

3. Para iniciar manualmente la aplicación lógica, en la barra de la barra de herramientas del diseñador, elija **Ejecutar**. 

   Si el correo electrónico tiene un asunto que coincide con el del filtro de asunto del desencadenador, la aplicación lógica le enviará un correo electrónico para que apruebe la solicitud de suscripción.

4. En el correo electrónico de aprobación, elija **Aprobar**.

5. Si la dirección de correo electrónico del suscriptor no existe en la lista de distribución correspondiente, la aplicación lógica agregará la dirección de esta persona y enviará un correo electrónico parecido al de este ejemplo:

   ![Correo electrónico de confirmación de unión correcta](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-success.png)

   Si la aplicación lógica no puede agregar al suscriptor, recibirá un correo electrónico parecido al de este ejemplo:

   ![Correo electrónico de error](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-failed.png)

   Si no recibe ningún correo electrónico, compruebe la carpeta de correo electrónico no deseado. 
   El filtro de correo electrónico no deseado podría redirigir esta clase de correo. 
   Si tampoco aparece allí y no está seguro de que la aplicación lógica se ejecutara correctamente, consulte el artículo de [solución de problemas en la aplicación lógica](../logic-apps/logic-apps-diagnosing-failures.md).

Enhorabuena, acaba de crear y ejecutar una aplicación lógica que integra la información de Azure, los servicios de Microsoft y otras aplicaciones de SaaS.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no lo necesite, elimine el grupo de recursos que contiene la aplicación lógica y los recursos relacionados. En el menú principal de Azure, vaya a **Grupos de recursos** y seleccione el grupo de recursos de la aplicación lógica. Seleccione **Eliminar grupo de recursos**. Escriba el nombre del grupo de recursos como confirmación y elija **Eliminar**.

!["Introducción" > "Eliminar grupo de recursos"](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

## <a name="get-support"></a>Obtención de soporte técnico

* Si tiene alguna duda, visite el [foro de Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ideas sobre características o votar sobre ellas, visite el [sitio de comentarios de los usuarios de Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>pasos siguientes

En este tutorial, ha creado una aplicación lógica que administra las aprobaciones de las solicitudes de la lista de distribución de correo electrónico. Ahora, aprenda a crear una aplicación lógica que procesa y almacena datos adjuntos de correo electrónico mediante la integración de servicios de Azure, como Azure Storage y Azure Functions.

> [!div class="nextstepaction"]
> [Procesamiento de archivos adjuntos de correo electrónico](../logic-apps/tutorial-process-email-attachments-workflow.md)
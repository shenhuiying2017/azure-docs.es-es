---
title: Registro de conectores personalizados - Azure Logic Apps | Microsoft Docs
description: Configure conectores personalizados para utilizarlos en Azure Logic Apps.
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
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 9e3d88abe751b37700590cc68c458f208d5868d2
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="register-custom-connectors-in-azure-logic-apps"></a>Registro de conectores personalizados en Azure Logic Apps

Una vez que haya compilado la API de REST, configurado la autenticación y obtenido el archivo de definición de OpenAPI o una colección de Postman, estará listo para registrar el conector. 

## <a name="prerequisites"></a>Requisitos previos

Para registrar el conector personalizado, necesita lo siguiente:

* Información para registrar el conector de Azure, como el nombre, la suscripción de Azure, el grupo de recursos de Azure y la ubicación que desea usar.

* Un archivo de OpenAPI (Swagger) o una colección de Postman que describa la API.

  Para este tutorial, puede utilizar el [archivo de OpenAPI de ejemplo de Azure Resources Manager](http://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json).

* Un icono que represente al conector

* Una breve descripción del conector

* La ubicación del host de la API

## <a name="1-create-your-connector"></a>1. Creación del conector

1. En Azure Portal, en el menú principal de Azure, elija **Nuevo**. En el cuadro de búsqueda, escriba "conector de logic apps" como filtro y presione Entrar.

   ![Nuevo, búsqueda de "conector de logic apps"](./media/logic-apps-custom-connector-register/create-logic-apps-connector.png)

2. En la lista de resultados, elija **Conector de Logic Apps** > **Crear**.

   ![Creación del conector de Logic Apps](./media/logic-apps-custom-connector-register/choose-logic-apps-connector.png)

3. Especifique los detalles para registrar el conector tal y como se describe en la tabla. Cuando esté listo, elija **Anclar al panel** > **Crear**.

   ![Detalles del conector personalizado de Logic Apps](./media/logic-apps-custom-connector-register/logic-apps-connector-details.png)

   | Propiedad | Valor sugerido | Descripción | 
   | -------- | --------------- | ----------- | 
   | **Name** | *nombre-conector-personalizado* | Proporcione un nombre para el conector. | 
   | **Suscripción** | *nombre-suscripción-Azure* | Seleccione su suscripción a Azure. | 
   | **Grupos de recursos** | *nombre-grupo-recursos-Azure* | Cree o seleccione un grupo de Azure para organizar los recursos de Azure. | 
   | **Ubicación** | *región-implementación* | Seleccione una región de implementación para el conector. | 
   |||| 

   Una vez que Azure implemente el conector, el menú de conector personalizado se abrirá automáticamente. 
   Si no es así, elija el conector personalizado desde el panel de Azure.

## <a name="2-define-your-connector"></a>2. Definición del conector

Especifique ahora el archivo de OpenAPI o la colección de Postman para crear el conector, la autenticación que utiliza el conector, las acciones y los desencadenadores que proporciona el conector personalizado y los parámetros que pueden usar las acciones y los desencadenadores.

### <a name="2a-specify-the-openapi-file-or-postman-collection-for-your-connector"></a>2a. Especificación del archivo de OpenAPI o la colección de Postman para el conector

1. En el menú del conector, si aún no está seleccionado, elija **Conector de Logic Apps**. En la barra de herramientas, elija **Editar**.

   ![Edición del conector personalizado](./media/logic-apps-custom-connector-register/edit-custom-connector.png)

2. Elija **General** para proporcionar los detalles en estas tablas con el objeto de crear, proteger y definir las acciones y los desencadenadores para el conector personalizado.

   1. Para **Conectores personalizados**, seleccione una opción de manera que pueda proporcionar el archivo de OpenAPI (Swagger) que describe la API.

      ![Especificación del archivo de OpenAPI para la API](./media/logic-apps-custom-connector-register/provide-openapi-file.png)

      | Opción | Formato |Descripción | 
      | ------ | ------ | ----------- | 
      | **Cargar un archivo de OpenAPI** | *archivo-json-OpenAPI (Swagger)* | Vaya a la ubicación del archivo de OpenAPI y seleccione el archivo. | 
      | **Usar URL de OpenAPI** | http://*ruta-acceso-archivo-json-swagger* | Especifique la dirección URL del archivo de OpenAPI para la API. | 
      | **Cargar la colección de Postman V1** | *archivo-exportado-colección-Postman-V1* | Vaya a la ubicación de una colección de Postman exportada en formato V1. | 
      |||| 

   2. Para **Información general**, cargue un icono para el conector. 
   Normalmente, los campos **Descripción**, **Host**, y **URL Base** se rellenan automáticamente desde el archivo OpenAPI. 
   Si no es así, agregue esta información como se describe en la tabla y elija **Continuar**. 

      ![Detalles del conector](./media/logic-apps-custom-connector-register/add-connector-details.png)

      | Opción o valor de configuración | Formato | Descripción | 
      | ----------------- | ------ | ----------- | 
      | **Cargar icono** | *archivo-png-o-jpg-inferior-1-MB* | Un icono que representa al conector <p>Color: preferiblemente un logotipo blanco sobre un fondo de color. <p>Dimensiones: un logotipo de ~160 píxeles dentro de un cuadro de 230 píxeles | 
      | **Color de fondo del icono** | *código-hexadecimal-color-marca-icono* | <p>El color de detrás del icono que coincide con el color de fondo del archivo de icono. <p>Formato: hexadecimal. Por ejemplo, #007ee5 representa el color azul. | 
      | **Descripción** | *descripción-conector* | Proporcione una breve descripción del conector. | 
      | **Host** | *host-conector* | Proporcione el dominio de host para la API web. | 
      | **URL base** | *URL-base-conector* | Proporcione la URL base para la API web. | 
      |||| 

### <a name="2b-describe-the-authentication-that-your-connector-uses"></a>2b. Descripción de la autenticación que utiliza el conector

1. Ahora elija **Seguridad** para revisar o describir la autenticación que utiliza el conector. La autenticación se asegura de que las identidades de los usuarios fluyan de forma adecuada entre el servicio y los clientes.

   ![Tipo de autenticación](./media/logic-apps-custom-connector-register/security.png)

   * Si carga un archivo de OpenAPI, el Asistente para registro detecta automáticamente el tipo de autenticación que utiliza la API web y rellena automáticamente la sección **Seguridad** del asistente en función del objeto `securityDefinitions` de ese archivo. Por ejemplo, aquí se muestra una sección que especifica el uso de OAuth2.0:

     ``` json
     "securityDefinitions": {
       "AAD": {
       "type": "oauth2",
       "flow": "accessCode",
       "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
       "tokenUrl": "https://login.windows.net/common/oauth2/token",
       "scopes": {}
       }
     },
     ```

   * Si el archivo de OpenAPI no rellena el tipo de autenticación y las propiedades, elija **Editar** para agregar esta información. 
   
     Por ejemplo, si previamente [ha configurado la autenticación de Azure AD en este tutorial](../logic-apps/custom-connector-azure-active-directory-authentication.md), ha creado aplicaciones de Azure AD para proteger su API web y el conector. 
     Ahora puede indicar la clave de cliente y el identificador de aplicación que guardó anteriormente:
    
     | Configuración | Valor sugerido | Descripción | 
     | ------- | --------------- | ----------- | 
     | **Tipo de autenticación** | OAuth 2.0 | | 
     | **Proveedor de identidades** | Azure Active Directory | | 
     | **Id. de cliente** | *id-aplicación-para-aplicación-Azure-AD-conector* | El identificador de aplicación que guardó anteriormente para la aplicación de Azure AD del conector. | 
     | **Secreto de cliente** | *clave-cliente-para-aplicación-Azure-AD-conector* | La clave de cliente para aplicación de Azure AD del conector. | 
     | **Dirección URL de inicio de sesión** | `https://login.windows.net` | | 
     | **Dirección URL de recursos** | `https://management.core.windows.net/` | Asegúrese de que agrega la dirección URL exactamente como se especifica, incluida la barra diagonal final. | 
     |||| 

   * Una colección de Postman, que genera automáticamente un archivo de OpenAPI, rellena por sí misma el tipo de autenticación *solo* cuando se utilizan los tipos de autenticación admitidos, como OAuth 2.0 o Básica.

2. Para guardar el conector después de escribir la información de seguridad, en la parte superior de la página, elija **Actualizar conector** y, a continuación, **Continuar**. 

### <a name="2c-review-update-or-define-actions-and-triggers-for-your-connector"></a>2c. Revisión, actualización o definición de acciones y desencadenadores para el conector

1. Ahora elija **Definición** para poder revisar, editar o definir nuevas acciones y desencadenadores que los usuarios pueden agregar a los flujos de trabajo.

   Las acciones y desencadenadores se basan en las operaciones definidas en el archivo de OpenAPI o la colección de Postman, que rellenan automáticamente la página **Definición** e incluyen los valores de solicitud y respuesta. Por lo tanto, si las operaciones ya figuran aquí, puede avanzar al paso siguiente del proceso de registro sin realizar cambios en esta página.

   ![Definición de conector](./media/logic-apps-custom-connector-register/definition.png)

2. Opcionalmente, si desea modificar acciones y desencadenadores existentes o agregar otros nuevos, continúe con estos pasos.

<a name="add-action-or-trigger"></a> 

#### <a name="edit-or-add-actions-for-your-connector"></a>Edición o adición de acciones para el conector

1. Para editar una acción existente, seleccione el número de esa acción. Para agregar una acción que no existía en el archivo de OpenAPI o la colección de Postman, en **Acciones**, elija **Nueva acción**.

2. En **General**, proporcione o edite esta información para la acción:
   
   | Configuración | Valor sugerido | Descripción | 
   | ------- | --------------- | ----------- | 
   | **Resumen** | *nombre-operación* | El título de esta acción. | 
   | **Descripción** | *descripción-operación* | La descripción de esta acción. <p>**Sugerencia**: asegúrese de que la descripción termina con un punto. |
   | **Identificador de operación** | *identificador-operación* | Un nombre único para identificar esta acción. Utilice mayúsculas y minúsculas, por ejemplo: "GetPullRequest". | 
   |**Visibilidad**| **none**, **advanced**, **internal** o **important** | La visibilidad de cara al usuario para esta acción. Para obtener más información, consulte [OpenAPI extensions](../logic-apps/custom-connector-openapi-extensions.md#visibility) (Extensiones de OpenAPI). | 
   |||| 

3. Ahora, defina la solicitud para la acción.
  
   1. En la sección **Solicitud**, elija **Importar desde ejemplo**. 

   2. En la página **Importar desde ejemplo**, pegue una solicitud de ejemplo. 

      Por lo general, hay solicitudes de ejemplo disponibles en la documentación de API, donde puede obtener información para los campos **Verbo**, **Dirección URL**, **Encabezados** y **Cuerpo**. Por ejemplo, consulte la [documentación de la API de Text Analytics](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7).

      ![Importación de solicitud de ejemplo](./media/logic-apps-custom-connector-register/import-sample-operation-request.png)

      > [!IMPORTANT]
      > Si crea un conector de una colección de Postman, asegúrese de quitar el encabezado `Content-type` de las acciones y los desencadenadores. Logic Apps agrega automáticamente este encabezado. Además, quite los encabezados de autenticación que ha definido en la sección `Security` de las acciones y los desencadenadores.

      Para la funcionalidad avanzada de OpenAPI, consulte [Extensiones de OpenAPI para conectores personalizados](../logic-apps/custom-connector-openapi-extensions.md).

   3. Para finalizar la definición de la solicitud, elija **Importar**.

4. Ahora, defina la respuesta para la acción.

   1. En **Respuesta**, puede especificar una respuesta predeterminada. 
   Elija **Agregar respuesta predeterminada**.

   2. En la página **Importar desde ejemplo**, pegue una respuesta de ejemplo y, a continuación, elija **Importar**.

5. Por último, en **Validación**, revise y corrija los posibles problemas detectados para la acción.

#### <a name="edit-or-add-triggers-for-your-connector"></a>Edición o adición de desencadenadores para el conector

1. Para editar un desencadenador existente, seleccione el número de ese desencadenador. Para agregar un desencadenador que no existe en el archivo de OpenAPI o la colección de Postman, en **Desencadenadores**, elija **Nuevo desencadenador**.

2. En **General**, proporcione o edite esta información para el desencadenador:

   | Configuración | Valor sugerido | Descripción | 
   | ------- | --------------- | ----------- | 
   | **Resumen** | *nombre-operación* | El título de este desencadenador. | 
   | **Descripción** | *descripción-operación* | La descripción de este desencadenador. <p>**Sugerencia**: asegúrese de que la descripción termina con un punto. | 
   | **Identificador de operación** | *identificador-operación* | Un nombre único para identificar este desencadenador. Utilice mayúsculas y minúsculas, por ejemplo: "TriggerOnGitHubPushEvent". | 
   |**Visibilidad**| **none**, **advanced**, **internal** o **important** | La visibilidad de cara al usuario para este desencadenador. Para obtener más información, consulte [OpenAPI extensions](../logic-apps/custom-connector-openapi-extensions.md#visibility) (Extensiones de OpenAPI). | 
   | **Tipo de desencadenador** | **Webhook** o **Sondeo** | El tipo de este desencadenador. Por ejemplo, un desencadenador de webhook espera a que ocurra un evento determinado para activarse. Un desencadenador de sondeo comprueba periódicamente un punto de conexión de servicio en función de un intervalo y una frecuencia especificados. <p>Para obtener más información acerca de los modelos de desencadenador de webhook y sondeo, consulte [Creación de API personalizadas](../logic-apps/logic-apps-create-api-app.md). | 
   |||| 

3. Ahora, defina la solicitud que crea el desencadenador. 

   1. En la sección **Solicitud**, elija **Importar desde ejemplo**.

   2. En la página **Importar desde ejemplo**, pegue una solicitud de ejemplo. 

      Por lo general, hay solicitudes de ejemplo disponibles en la documentación de API, donde puede obtener información para los campos **Verbo**, **Dirección URL**, **Encabezados** y **Cuerpo**. Por ejemplo, consulte la [documentación de la API de Text Analytics](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7).

      ![Importación de solicitud de ejemplo](./media/logic-apps-custom-connector-register/import-sample-operation-request.png)

      > [!IMPORTANT]
      > Si crea un conector de una colección de Postman, asegúrese de quitar el encabezado `Content-type` de las acciones y los desencadenadores. Logic Apps agrega automáticamente este encabezado. Además, quite los encabezados de autenticación que ha definido en la sección `Security` de las acciones y los desencadenadores.

      Para la funcionalidad avanzada de OpenAPI, consulte [Extensiones de OpenAPI para conectores personalizados](../logic-apps/custom-connector-openapi-extensions.md).

   3. Para finalizar la definición de la solicitud, elija **Importar**. 

4. Ahora, defina la respuesta del desencadenador. En la sección **Respuesta**, en función de tipo del desencadenador, siga estos pasos:

   **Desencadenador de webhook**
   1. En **Respuesta del webhook**, elija **Importar desde ejemplo**. 

   2. En la página **Importar desde ejemplo**, pegue una respuesta de ejemplo y, a continuación, elija **Importar**. Para ver una respuesta de ejemplo, consulte la [referencia de la API de GitHub para crear un webhook](https://developer.github.com/v3/repos/hooks/#create-a-hook).

   3. En **Configuración de desencadenador**, seleccione un parámetro que se utilizará para la solicitud de creación de webhook. Logic Apps utiliza este valor de parámetro para rellenar la URL de devolución de llamada empleada por el servicio para comunicarse con el desencadenador.

   **Desencadenador de sondeo**
   1. En **Respuesta**, puede especificar una respuesta predeterminada. 
   Elija **Agregar respuesta predeterminada**.

   2. En la página **Importar desde ejemplo**, pegue una respuesta de ejemplo y, a continuación, elija **Importar**.

   3. En **Configuración de desencadenador**, especifique el parámetro de consulta, el valor que se pasará al parámetro y una *sugerencia de desencadenador* que especifique un intervalo de sondeo adecuado para la solicitud siguiente.

5. Por último, en **Validación**, revise y corrija los posibles problemas detectados para el desencadenador.

#### <a name="review-reference-definitions-for-your-connector"></a>Revisión de las definiciones de referencia para el conector

La sección **Referencias** se completa automáticamente a partir de la descripción de la API y describe los campos de parámetro a los que pueden hacer referencia las acciones y los desencadenadores. 

1. En **Referencias**, elija el número de la definición de referencia que desea revisar.

2. En **Nombre**, revise o actualice el nombre de la definición de referencia.

3. En **Validación**, revise y corrija los posibles problemas detectados para la definición de referencia.

## <a name="3-finish-creating-your-connector"></a>3. Fin de la creación del conector

Cuando esté listo, elija **Crear** a fin de implementar el conector. Si va a actualizar un conector existente, elija **Actualizar conector**.

¡Enhorabuena! Ahora, cuando cree una aplicación lógica, podrá encontrar el conector en el Diseñador de Logic Apps y agregarlo a la aplicación lógica.

![En el Diseñador de Logic Apps, búsqueda del conector](./media/logic-apps-custom-connector-register/custom-connector-created.png)

## <a name="share-your-connector-with-other-logic-apps-users"></a>Uso compartido del conector con otros usuarios de Logic Apps

Los conectores personalizados registrados pero no certificados funcionan como conectores administrados por Microsoft, pero son visibles y están disponibles *solo* para el autor del conector y los usuarios que tienen el mismo inquilino de Azure Active Directory y la misma suscripción de Azure para aplicaciones lógicas en la región en la que se implementan tales aplicaciones. Aunque el uso compartido es opcional, es posible que se encuentre con situaciones en las que desee compartir los conectores con otros usuarios. 

> [!IMPORTANT]
> Si comparte un conector, otros usuarios podrían empezar a depender de ese conector. 
> ***Al eliminar el conector, se eliminan todas las conexiones con ese conector.***
 
Para compartir su conector con usuarios externos (por ejemplo, todos los usuarios de Logic Apps, Flow, y PowerApps), [presente su conector para que obtenga la certificación de Microsoft](../logic-apps/custom-connector-submit-certification.md).

## <a name="faq"></a>P+F

**P:**  ¿Existen límites para los conectores personalizados? </br>
**R:** Sí, consulte [los límites para conectores personalizados aquí](../logic-apps/logic-apps-limits-and-config.md#custom-connector-limits).

## <a name="get-support"></a>Obtención de soporte técnico

* Para obtener soporte técnico en relación con el desarrollo y la incorporación, o para solicitar características que no están disponibles en el Asistente para registro, póngase en contacto con [condevhelp@microsoft.com](mailto:condevhelp@microsoft.com). Microsoft supervisa esta cuenta para los problemas y las preguntas de los desarrolladores, y los dirige al equipo adecuado.

* Para formular preguntas, o responderlas, o ver lo que hacen otros usuarios de Azure Logic Apps, visite el [foro de Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Para ayudar a mejorar Logic Apps, vote o envíe ideas en el [sitio de comentarios de usuario de Logic Apps](http://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>Pasos siguientes

* [Optional: Certify your connector](../logic-apps/custom-connector-submit-certification.md) (Opcional: certificación del conector)
* [Preguntas más frecuentes sobre conectores personalizados](../logic-apps/custom-connector-faq.md)
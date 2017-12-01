---
title: "Creación y registro de conectores SOAP - Azure Logic Apps | Microsoft Docs"
description: "Configuración de conectores SOAP para utilizarlos en Azure Logic Apps"
author: divyaswarnkar
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
ms.date: 10/24/2017
ms.author: LADocs; divswa
ms.openlocfilehash: 0323b0f7ee03dce209d5a71c6711988a34ba7633
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2017
---
# <a name="create-and-register-soap-connectors-in-azure-logic-apps"></a>Creación y registro de conectores SOAP en Azure Logic Apps

Para integrar servicios SOAP en sus flujos de trabajo de aplicación lógica, puede crear y registrar un conector de protocolo simple de acceso a objetos (SOAP) personalizado mediante el lenguaje de descripción de servicios Web (WSDL) que describe el servicio SOAP. Los conectores SOAP funcionan como conectores creados previamente, por lo que puede usarlos de la misma forma que otros conectores de sus aplicaciones lógicas.


## <a name="prerequisites"></a>Requisitos previos

Para registrar el conector SOAP, necesita lo siguiente:

* Una suscripción de Azure. Si no tiene una suscripción, puede [comenzar con una cuenta de Azure gratuita](https://azure.microsoft.com/free/). También puede registrarse para obtener una [suscripción de pago por uso](https://azure.microsoft.com/pricing/purchase-options/).

* Cualquiera de estos elementos:
  * Una dirección URL a una instancia de WSDL que define el servicio SOAP y las API.
  * Un archivo WSDL que define el servicio SOAP y las API.

  Para este tutorial, puede usar nuestro ejemplo de [servicio SOAP de pedidos](http://fazioapisoap.azurewebsites.net/FazioService.svc?singleWsdl).

* Opcional: una imagen que se usará como icono para el conector personalizado


## <a name="1-create-your-connector"></a>1. Creación del conector

1. En Azure Portal, en el menú principal de Azure, elija **Nuevo**. En el cuadro de búsqueda, escriba "conector de logic apps" como filtro y presione Entrar.

   ![Nuevo, búsqueda de "conector de logic apps"](./media/logic-apps-soap-connector-create-register/create-logic-apps-connector.png)

2. En la lista de resultados, elija **Conector de Logic Apps** > **Crear**.

   ![Creación del conector de Logic Apps](./media/logic-apps-soap-connector-create-register/choose-logic-apps-connector.png)

3. Especifique los detalles para registrar el conector tal y como se describe en la tabla. Cuando esté listo, elija **Anclar al panel** > **Crear**.

   ![Detalles del conector personalizado de Logic Apps](./media/logic-apps-soap-connector-create-register/logic-apps-soap-connector-details.png)

   | Propiedad | Valor sugerido | Descripción | 
   | -------- | --------------- | ----------- | 
   | **Name** | *nombre-conector-SOAP* | Proporcione un nombre para el conector. | 
   | **Suscripción** | *nombre-suscripción-Azure* | Seleccione su suscripción a Azure. | 
   | **Grupos de recursos** | *nombre-grupo-recursos-Azure* | Cree o seleccione un grupo de Azure para organizar los recursos de Azure. | 
   | **Ubicación** | *región-implementación* | Seleccione una región de implementación para el conector. | 
   |||| 

   Una vez que Azure implemente el conector, el menú de conector de aplicaciones lógicas se abrirá automáticamente. 
   Si no es así, elija el conector SOAP desde el panel de Azure.

## <a name="2-define-your-connector"></a>2. Definición del conector

Ahora especifique la dirección URL o el archivo WSDL para crear el conector, la autenticación que utiliza el conector y las acciones y los desencadenadores que proporciona el conector SOAP.


### <a name="2a-specify-the-wsdl-file-or-url-for-your-connector"></a>2a. Especifique la dirección URL o el archivo WSDL para el conector.

1. En el menú del conector, si aún no está seleccionado, elija **Conector de Logic Apps**. En la barra de herramientas, elija **Editar**.

   ![Edición del conector personalizado](./media/logic-apps-soap-connector-create-register/edit-soap-connector.png)

2. Elija **General** para proporcionar los detalles en estas tablas con el objeto de crear, proteger y definir las acciones y los desencadenadores para el conector SOAP.

   1. Para **Conectores personalizados**, seleccione **SOAP** como **Punto de conexión de API** de manera que pueda proporcionar el archivo WDSL que describe la API.

      ![Especificación del archivo WDSL para la API](./media/logic-apps-soap-connector-create-register/provide-wsdl-file.png)

      | Opción | Formato |Descripción | 
      | ------ | ------ | ----------- | 
      | **Cargar WSDL desde archivo** | *archivo-WSDL* | Vaya a la ubicación del archivo WDSL y seleccione el archivo. | 
      | **Cargar WSDL desde dirección URL** | http://*dirección-a-archivo-wsdl* | Proporcione la dirección URL para el archivo WDSL del servicio. | 
      | **SOAP a REST** |   | Transforme API del servicio SOAP en API de REST. | 
      |||| 

   2. Para **Información general**, cargue un icono para el conector. 
   Normalmente, los campos **Descripción**, **Host** y **URL base** se rellenan automáticamente con el archivo WDSL. 
   Si no es así, agregue esta información como se describe en la tabla y elija **Continuar**. 

      ![Detalles del conector](./media/logic-apps-soap-connector-create-register/add-general-details.png)

      | Opción o valor de configuración | Formato | Descripción | 
      | ----------------- | ------ | ----------- | 
      | **Cargar icono** | *archivo-png-o-jpg-inferior-1-MB* | Un icono que representa al conector <p>Color: preferiblemente un logotipo blanco sobre un fondo de color. <p>Dimensiones: un logotipo de ~160 píxeles dentro de un cuadro de 230 píxeles | 
      | **Color de fondo del icono** | *código-hexadecimal-color-marca-icono* | <p>El color de detrás del icono que coincide con el color de fondo del archivo de icono. <p>Formato: hexadecimal. Por ejemplo, #007ee5 representa el color azul. | 
      | **Descripción** | *descripción-conector* | Proporcione una breve descripción del conector. | 
      | **Host** | *host-conector* | Proporcione el dominio de host para el servicio SOAP. | 
      | **URL base** | *URL-base-conector* | Proporcione la dirección URL base para el servicio SOAP. | 
      |||| 

### <a name="2b-describe-the-authentication-that-your-connector-uses"></a>2b. Descripción de la autenticación que utiliza el conector

1. Ahora elija **Seguridad** para revisar o describir la autenticación que utiliza el conector. La autenticación se asegura de que las identidades de los usuarios fluyan de forma adecuada entre el servicio y los clientes.

   De forma predeterminada, el **tipo de autenticación** del conector está establecido en **Sin autenticación**.
   
   ![Tipo de autenticación](./media/logic-apps-soap-connector-create-register/security-authentication-options.png)

   Para cambiar el tipo de autenticación, elija **Editar**. Puede seleccionar **Autenticación básica**. Para utilizar etiquetas de parámetro distintas a los valores predeterminados, actualícelas en **Etiqueta de parámetro**.

   ![Autenticación básica](./media/logic-apps-soap-connector-create-register/security.png)

   
2. Para guardar el conector después de escribir la información de seguridad, en la parte superior de la página, elija **Actualizar conector** y, a continuación, **Continuar**. 

### <a name="2c-review-update-or-define-actions-and-triggers-for-your-connector"></a>2c. Revisión, actualización o definición de acciones y desencadenadores para el conector

1. Ahora elija **Definición** para poder revisar, editar o definir nuevas acciones y desencadenadores que los usuarios pueden agregar a los flujos de trabajo.

   Las acciones y los desencadenadores se basan en las operaciones definidas en el archivo WDSL, que rellenan automáticamente la página **Definición** e incluyen los valores de solicitud y respuesta. Por lo tanto, si las operaciones ya figuran aquí, puede avanzar al paso siguiente del proceso de registro sin realizar cambios en esta página.

   ![Definición de conector](./media/logic-apps-soap-connector-create-register/definition.png)

2. Opcionalmente, si desea modificar acciones y desencadenadores existentes o agregar otros nuevos, [continúe con estos pasos](logic-apps-custom-connector-register.md#add-action-or-trigger).


## <a name="3-finish-creating-your-connector"></a>3. Fin de la creación del conector

Cuando esté listo, elija **Actualizar conector** a fin de implementar el conector. 

¡Enhorabuena! Ahora, cuando cree una aplicación lógica, podrá encontrar el conector en el Diseñador de Logic Apps y agregarlo a la aplicación lógica.

![En el Diseñador de Logic Apps, búsqueda del conector](./media/logic-apps-soap-connector-create-register/soap-connector-created.png)

## <a name="share-your-connector-with-other-logic-apps-users"></a>Uso compartido del conector con otros usuarios de Logic Apps

Los conectores personalizados registrados pero no certificados funcionan como conectores administrados por Microsoft, pero son visibles y están disponibles *solo* para el autor del conector y los usuarios que tienen el mismo inquilino de Azure Active Directory y la misma suscripción de Azure para aplicaciones lógicas en la región en la que se implementan tales aplicaciones. Aunque el uso compartido es opcional, es posible que se encuentre con situaciones en las que desee compartir los conectores con otros usuarios. 

> [!IMPORTANT]
> Si comparte un conector, otros usuarios podrían empezar a depender de ese conector. 
> ***Al eliminar el conector, se eliminan todas las conexiones con ese conector.***
 
Para compartir su conector con usuarios externos (por ejemplo, todos los usuarios de Logic Apps), [presente su conector para que obtenga la certificación de Microsoft](../logic-apps/custom-connector-submit-certification.md).

## <a name="faq"></a>P+F

**P:** ¿Está el conector SOAP disponible de manera general (GA)? </br>
**R:** El conector SOAP está en su **versión preliminar** y todavía no es un servicio que esté disponible de manera general.

**P:** ¿Existen restricciones y problemas conocidos del conector SOAP? </br>
**R:** Sí, consulte las [restricciones y problemas conocidos del conector SOAP](../api-management/api-management-api-import-restrictions.md#wsdl).

**P:**  ¿Existen límites para los conectores personalizados? </br>
**R:** Sí, consulte [los límites para conectores personalizados aquí](../logic-apps/logic-apps-limits-and-config.md#custom-connector-limits).

## <a name="get-support"></a>Obtención de soporte técnico

* Para obtener soporte técnico en relación con el desarrollo y la incorporación, o para solicitar características que no están disponibles en el Asistente para registro, póngase en contacto con [condevhelp@microsoft.com](mailto:condevhelp@microsoft.com). Microsoft supervisa esta cuenta para los problemas y las preguntas de los desarrolladores, y los dirige al equipo adecuado.

* Para formular preguntas, o responderlas, o ver lo que hacen otros usuarios de Azure Logic Apps, visite el [foro de Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Para ayudar a mejorar Logic Apps, vote o envíe ideas en el [sitio de comentarios de usuario de Logic Apps](http://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>Pasos siguientes

* [Optional: Certify your connector](../logic-apps/custom-connector-submit-certification.md) (Opcional: certificación del conector)
* [Preguntas más frecuentes sobre conectores personalizados](../logic-apps/custom-connector-faq.md)
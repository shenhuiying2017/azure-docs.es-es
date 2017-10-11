---
title: "Validación de documentos XML: Azure Logic Apps | Microsoft Docs"
description: "Validación de documentos XML con esquemas para Azure Logic Apps y escenarios B2B mediante Enterprise Integration Pack"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: d700588f-2d8a-4c92-93eb-e1e6e250e760
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 8558efffa354cc4bb93820c837077ee997924c95
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="validate-xml-for-enterprise-integration"></a>Validación de documentos XML para la integración de empresas

A menudo, en los escenarios B2B, los asociados de un contrato deben asegurarse que los mensajes que intercambian son válidos para que pueda comenzar el procesamiento de datos. En Enterprise Integration Pack, puede usar el conector de validación XML para validar documentos con un esquema predefinido.

## <a name="validate-a-document-with-the-xml-validation-connector"></a>Validación de un documento con el conector de validación XML

1. Cree una aplicación lógica y [vincúlela a la cuenta de integración](../logic-apps/logic-apps-enterprise-integration-accounts.md "Aprenda a vincular una cuenta de integración a una aplicación lógica") que contenga el esquema que utilizará para validar los datos XML.

2. Agregue un desencadenador **Request - When an HTTP request is received** (Solicitar: cuando se reciba una solicitud HTTP) a la aplicación lógica.

    ![](./media/logic-apps-enterprise-integration-xml/xml-1.png)

3. Agregue la acción **XML Validation** (Validación XML), pero seleccione antes **Agregar una acción**.

4. Escriba *xml* en el cuadro de búsqueda para filtrar todas las acciones hasta encontrar la que desea usar. Seleccione **XML Validation** (Validación XML).

    ![](./media/logic-apps-enterprise-integration-xml/xml-2.png)

5. Para especificar el contenido XML que desea validar, seleccione **CONTENIDO**.

    ![](./media/logic-apps-enterprise-integration-xml/xml-1-5.png)

6. Elija la etiqueta Cuerpo como el contenido que quiere validar.

    ![](./media/logic-apps-enterprise-integration-xml/xml-3.png)

7. Para especificar el esquema que desea usar para validar la entrada de *contenido* anterior, elija **NOMBRE DE ESQUEMA**.

    ![](./media/logic-apps-enterprise-integration-xml/xml-4.png)

8. Guarde el trabajo  

    ![](./media/logic-apps-enterprise-integration-xml/xml-5.png)

Con esto, ha terminado de configurar el conector de validación. En una aplicación real, podría almacenar los datos validados en una aplicación de línea de negocio (LOB) como SalesForce. Para enviar la salida validada a Salesforce, agregue una acción.

Para probar la acción de validación realice una solicitud al punto de conexión HTTP.

## <a name="next-steps"></a>Pasos siguientes
[Más información sobre Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack")   


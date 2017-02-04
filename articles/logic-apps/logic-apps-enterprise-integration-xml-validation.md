---
title: "Información general sobre la validación XML en Enterprise Integration Pack | Microsoft Docs"
description: "Sepa cómo funciona el proceso de validación con las Aplicaciones lógicas y Enterprise Integration Pack."
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
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: 967d06e3df21aa1c194cdc81fb7cd0bdf53e82e4


---
# <a name="enterprise-integration-with-xml-validation"></a>Integración de empresas con validación XML
## <a name="overview"></a>Información general
A menudo, en los escenarios B2B, los partners de un contrato deben validar que los mensajes que se intercambian entre sí son válidos antes de iniciar el procesamiento de los datos. En Enterprise Integration Pack, puede usar el conector de validación XML para validar documentos con un esquema predefinido.  

## <a name="how-to-validate-a-document-with-the-xml-validation-connector"></a>Procedimiento para validar documentos con el conector de validación XML
1. Cree una aplicación lógica y [vincúlela a la cuenta de integración](../logic-apps/logic-apps-enterprise-integration-accounts.md "Aprenda a vincular una cuenta de integración a una aplicación lógica") que contenga el esquema que utilizará para validar los datos XML.
2. Agregue un desencadenador **Request - When an HTTP request is received** (Solicitar: cuando se reciba una solicitud HTTP) a la aplicación lógica  
   ![](./media/logic-apps-enterprise-integration-xml/xml-1.png)    
3. Agregue la acción **XML Validation** (Validación XML), pero seleccione antes **Agregar una acción**  
4. Escriba *xml* en el cuadro de búsqueda para filtrar todas las acciones que quiera usar. 
5. Seleccione **XML Validation**    (Validación XML)  
   ![](./media/logic-apps-enterprise-integration-xml/xml-2.png)   
6. Seleccione el cuadro de texto **CONTENIDO**  
   ![](./media/logic-apps-enterprise-integration-xml/xml-1-5.png)
7. Seleccione la etiqueta Cuerpo como el contenido que quiere validar.   
   ![](./media/logic-apps-enterprise-integration-xml/xml-3.png)  
8. Seleccione el cuadro de lista **NOMBRE DE ESQUEMA** y elija el esquema que desee utilizar para validar el *contenido* de entrada anterior     
   ![](./media/logic-apps-enterprise-integration-xml/xml-4.png) 
9. Guarde el trabajo  
   ![](./media/logic-apps-enterprise-integration-xml/xml-5.png) 

En este momento, ya ha terminado de configurar el conector de validación. En una aplicación real, puede almacenar los datos validados en una aplicación LOB como SalesForce. Puede agregar fácilmente una acción para enviar el resultado de la acción de validación a Salesforce. 

Ahora puede probar la acción de validación realizando una solicitud al punto de conexión HTTP.  

## <a name="next-steps"></a>Pasos siguientes
[Más información sobre Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack")   




<!--HONumber=Jan17_HO3-->



<properties 
	pageTitle="Información general sobre la validación XML en Enterprise Integration Pack | Servicio de aplicaciones de Microsoft Azure | Microsoft Azure" 
	description="Sepa cómo funciona el proceso de validación con las Aplicaciones lógicas y Enterprise Integration Pack." 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# Integración de empresas con validación XML

## Información general
A menudo, en los escenarios B2B, los partners de un contrato deben validar que los mensajes que se intercambian entre sí son válidos antes de iniciar el procesamiento de los datos. En Enterprise Integration Pack, puede usar el conector de validación XML para validar documentos con un esquema predefinido.

## Procedimiento para validar documentos con el conector de validación XML
1. Cree una Aplicación lógica y [vincúlela a la cuenta de integración](./app-service-logic-enterprise-integration-accounts.md "Aprenda a vincular una cuenta de integración a una Aplicación lógica.") que contenga el esquema que utilizará para validar los datos XML.
2. Agregue un desencadenador **Request - When an HTTP request is received** (Solicitar: cuando se reciba una solicitud HTTP) a la Aplicación lógica. ![](./media/app-service-logic-enterprise-integration-xml/xml-1.png)
3. Agregue la acción **XML Validation** (Validación XML) seleccionando primero **Agregar una acción**.
4. Escriba *xml* en el cuadro de búsqueda para filtrar todas las acciones que quiera usar.
5. Seleccione **XML Validation (Validación XML)**. ![](./media/app-service-logic-enterprise-integration-xml/xml-2.png)
6. Elija el cuadro de texto **CONTENIDO**. ![](./media/app-service-logic-enterprise-integration-xml/xml-1-5.png)
7. Seleccione la etiqueta Cuerpo como el contenido que quiere validar. ![](./media/app-service-logic-enterprise-integration-xml/xml-3.png)
8. Seleccione el cuadro de lista **NOMBRE DE ESQUEMA** y elija el esquema que quiera utilizar para validar la entrada de *Contenido* anterior. ![](./media/app-service-logic-enterprise-integration-xml/xml-4.png)
9. Guarde el trabajo. ![](./media/app-service-logic-enterprise-integration-xml/xml-5.png)

En este momento, ya ha terminado de configurar el conector de validación. En una aplicación real, puede almacenar los datos validados en una aplicación LOB como SalesForce. Puede agregar fácilmente una acción para enviar el resultado de la acción de validación a Salesforce.

Ahora puede probar la acción de validación realizando una solicitud al punto de conexión HTTP.

## Pasos siguientes

[Más información sobre Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack")

<!---HONumber=AcomDC_0713_2016-->
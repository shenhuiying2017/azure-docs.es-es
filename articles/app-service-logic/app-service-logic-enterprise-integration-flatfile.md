<properties 
	pageTitle="Información general sobre Enterprise Integration Pack | Servicio de aplicaciones de Microsoft Azure" 
	description="Utilice las características de Enterprise Integration Pack para posibilitar escenarios de integración y proceso empresariales mediante el Servicio de aplicaciones de Microsoft Azure." 
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
	ms.date="06/30/2016" 
	ms.author="deonhe"/>

# Integración de empresas con archivos sin formato

## Información general

Utilizará el conector de codificación de archivos sin formato desde una Aplicación lógica que codifique el contenido XML. Es posible que quiera codificar el contenido XML antes de enviarlo a un socio comercial en un escenario B2B. La Aplicación lógica que cree puede obtener su contenido XML desde distintos orígenes; entre ellos, un desencadenador de solicitud HTTP u otra aplicación, o incluso uno de los numerosos [conectores](../connectors/apis-list.md). Consulte la [documentación de Aplicaciones lógicas](./app-service-logic-what-are-logic-apps.md "Más información sobre Aplicaciones lógicas") para obtener más información sobre la eficacia de las Aplicaciones lógicas.

## Procedimiento para crear el conector de codificación de archivos sin formato

Siga estos pasos para crear una Aplicación lógica y agregar un conector de codificación a la Aplicación lógica:

1. Cree una Aplicación lógica y [vincúlela a la cuenta de integración](./app-service-logic-enterprise-integration-accounts.md "Aprenda a vincular una cuenta de integración a una Aplicación lógica.") que contenga el esquema que utilizará para codificar los datos XML.
2. Agregue un desencadenador **Request - When an HTTP request is received** (Solicitar: cuando se reciba una solicitud HTTP) a la Aplicación lógica. ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-1.png)
3. Agregue la acción de codificación de archivos sin formato realizando este procedimiento:
-  Seleccione el signo **más**.
-  Haga clic en el vínculo **Agregar una acción** que aparece después de haber seleccionado el signo más.
-  Escriba *Flat* en el cuadro de búsqueda para filtrar todas las acciones que quiera usar.
-  Seleccione la acción **Flat File Encoding** (Codificación de archivos sin formato) de la lista. ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-2.png)
6. Haga clic en el cuadro de texto **CONTENIDO** del control de codificación de archivos sin formato que aparece en una ventana emergente. ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-3.png)
7. Elija la etiqueta Cuerpo como el contenido que quiere codificar. Esta etiqueta rellenará el campo de contenido. ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-4.png)
8. Seleccione el cuadro de lista **NOMBRE DE ESQUEMA** y elija el esquema que quiera utilizar para codificar la entrada de *Contenido* anterior. ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-5.png)
9. Guarde el trabajo. ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-6.png)

En este momento, ya ha terminado de configurar su conector de codificación de archivos sin formato. En una aplicación real, puede almacenar los datos codificados en una aplicación LOB, como Salesforce, o enviarlos a un socio comercial. Puede agregar fácilmente una acción para enviar el resultado de la acción de codificación a Salesforce o a su socio comercial utilizando uno de los demás conectores que se proporcionan.

Ahora puede probar el conector realizando una solicitud al punto de conexión HTTP e incluyendo el contenido XML en el cuerpo de la solicitud.

## Procedimiento para crear el conector de descodificación de archivos sin formato

### Requisito previo
Debe tener un archivo de esquema ya cargado en la cuenta de integración para completar estos pasos.

1. Agregue un desencadenador **Request - When an HTTP request is received** (Solicitar: cuando se reciba una solicitud HTTP) a la Aplicación lógica. ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-1.png)
2. Agregue la acción de codificación de archivos sin formato realizando este procedimiento:
-  Seleccione el signo **más**.
-  Haga clic en el vínculo **Agregar una acción** que aparece después de haber seleccionado el signo más.
-  Escriba *Flat* en el cuadro de búsqueda para filtrar todas las acciones que quiera usar.
-  Seleccione la acción **Flat File Decoding** (Descodificación de archivos sin formato) de la lista. ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-2.png)
- Seleccione el control **CONTENIDO**. Tras esto, se creará una lista del contenido de los pasos anteriores que puede utilizar como contenido para descodificar. Observará que la etiqueta *Cuerpo* de la solicitud HTTP entrante está disponible para usarse como el contenido que se descodificará. Tenga en cuenta que también puede especificar el contenido que descodificar directamente en el control **CONTENIDO**. En este ejemplo, seleccionamos *Cuerpo* para utilizar el cuerpo de la solicitud HTTP entrante del paso 1 de los pasos de descodificación.
- Seleccione la etiqueta *Cuerpo*. Observe que la etiqueta Cuerpo ahora se encuentra en el control CONTENIDO.
- Seleccione el nombre del esquema que quiera utilizar para descodificar el contenido. En este ejemplo, seleccionamos *OrderFile*. Tenga en cuenta que OrderFile es el nombre del esquema que cargamos en la cuenta de integración anteriormente. ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-decode-1.png)
- Haga clic en el vínculo **Guardar** del menú para guardar el trabajo. ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-6.png)

En este momento, ya ha terminado de configurar su conector de descodificación de archivos sin formato. En una aplicación real, puede almacenar los datos descodificados en una aplicación LOB como SalesForce. Puede agregar fácilmente una acción para enviar el resultado de la acción de descodificación a Salesforce.

Ahora puede probar el conector realizando una solicitud al punto de conexión HTTP e incluyendo el contenido XML que quiera descodificar en el cuerpo de la solicitud.

## Más información
- [Más información sobre Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack")

<!---HONumber=AcomDC_0706_2016-->
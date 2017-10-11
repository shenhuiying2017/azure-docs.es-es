---
title: "Codificación o descodificación de archivos sin formato en Azure Logic Apps | Microsoft Docs"
description: Uso del codificador o el descodificador de archivos sin formato de Enterprise Integration Pack en Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 82152dab-c7ad-43df-b721-596559703be8
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; mandia
ms.openlocfilehash: bc3430624844cdeb92958433fba295f67a8ae0ff
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="overview-of-enterprise-integration-with-flat-files"></a>Información general sobre Enterprise Integration Pack con archivos sin formato

Es posible que quiera codificar el contenido XML antes de enviarlo a un socio comercial en un escenario de negocio a negocio (B2B). Para ello, en las aplicaciones lógicas, puede utilizar el conector de codificación de archivos planos. La aplicación lógica que cree puede obtener el contenido XML desde distintos orígenes; entre ellos, un desencadenador de solicitud HTTP u otra aplicación, o incluso uno de los numerosos [conectores](../connectors/apis-list.md). Para más información sobre Logic Apps, consulte la [documentación de Logic Apps](logic-apps-what-are-logic-apps.md "Más información sobre Logic Apps").  

## <a name="create-the-flat-file-encoding-connector"></a>Creación del conector de codificación de archivos sin formato
Siga estos pasos para agregar un conector de codificación de archivos planos a la aplicación lógica.

1. Cree una aplicación lógica y [vincúlela a su cuenta de integración](logic-apps-enterprise-integration-accounts.md "Aprenda a vincular una cuenta de integración a una Aplicación lógica"). Esta cuenta contiene el esquema que se va a utilizar para codificar los datos XML.  
2. Agregue un desencadenador **Request - When an HTTP request is received** (Solicitar: cuando se reciba una solicitud HTTP) a la aplicación lógica.  
   ![Captura de pantalla del desencadenador que va a seleccionar](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)    
3. Agregue la acción de codificación de archivos planos de la siguiente manera:
   
    a. Seleccione el signo **más** .
   
    b. Haga clic en el vínculo **Agregar una acción** (que aparece después de haber seleccionado el signo más).
   
    c. Escriba *Flat* en el cuadro de búsqueda para filtrar todas las acciones que quiera usar.
   
    d. Seleccione la acción **Codificación de archivo plano** de la lista.   
   ![Captura de pantalla de la opción Codificación de archivo plano](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   
4. En el cuadro de diálogo **Codificación de archivo plano**, seleccione el cuadro de texto **Contenido**.  
   ![Captura de pantalla del cuadro de texto Contenido](media/logic-apps-enterprise-integration-flatfile/flatfile-3.png)  
5. Elija la etiqueta Cuerpo como el contenido que quiere codificar. Esta etiqueta rellenará el campo de contenido.     
   ![Captura de pantalla de la etiqueta Cuerpo](media/logic-apps-enterprise-integration-flatfile/flatfile-4.png)  
6. Seleccione el cuadro de lista **Nombre de esquema** y elija el esquema que quiera utilizar para codificar el contenido de entrada.    
   ![Captura de pantalla del cuadro de lista Nombre de esquema](media/logic-apps-enterprise-integration-flatfile/flatfile-5.png)  
7. Guarde el trabajo.   
   ![Captura de pantalla del icono Guardar](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)  

En este momento, ya ha terminado de configurar su conector de codificación de archivos sin formato. En una aplicación real, puede almacenar los datos codificados en una aplicación de línea de negocios como SalesForce. O bien puede enviar los datos codificados a un socio comercial. Puede agregar fácilmente una acción para enviar el resultado de la acción de codificación a Salesforce o a su socio comercial utilizando uno de los demás conectores que se proporcionan.

Ahora puede probar el conector realizando una solicitud al punto de conexión HTTP e incluyendo el contenido XML en el cuerpo de la solicitud.  

## <a name="create-the-flat-file-decoding-connector"></a>Creación del conector de descodificación de archivos sin formato

> [!NOTE]
> Para completar estos pasos, debe tener un archivo de esquema ya cargado en la cuenta de integración.

1. Agregue un desencadenador **Request - When an HTTP request is received** (Solicitar: cuando se reciba una solicitud HTTP) a la aplicación lógica.  
   ![Captura de pantalla del desencadenador que va a seleccionar](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)    
2. Agregue la acción de descodificación de archivos planos de la siguiente manera:
   
    a. Seleccione el signo **más** .
   
    b. Haga clic en el vínculo **Agregar una acción** (que aparece después de haber seleccionado el signo más).
   
    c. Escriba *Flat* en el cuadro de búsqueda para filtrar todas las acciones que quiera usar.
   
    d. Seleccione la acción **Descodificación de archivo plano** de la lista.   
   ![Captura de pantalla de la opción Descodificación de archivo plano](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   
3. Seleccione el control **Contenido** . Tras esto, se creará una lista del contenido de los pasos anteriores que puede utilizar como contenido para descodificar. Observe que la etiqueta *Cuerpo* de la solicitud HTTP entrante está disponible para usarse como el contenido que se descodificará. También puede especificar el contenido que descodificar directamente en el control **Contenido** .     
4. Seleccione la etiqueta *Cuerpo* . Observe que la etiqueta Cuerpo ahora se encuentra en el control **Contenido** .
5. Seleccione el nombre del esquema que quiera utilizar para descodificar el contenido. La captura de pantalla siguiente muestra que *OrderFile* es el nombre del esquema seleccionado. Este nombre de esquema se había cargado previamente en la cuenta de integración.
   
   ![Captura de pantalla del cuadro de diálogo Descodificación de archivo plano](media/logic-apps-enterprise-integration-flatfile/flatfile-decode-1.png)    
6. Guarde el trabajo.  
   ![Captura de pantalla del icono Guardar](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)    

En este momento, ya ha terminado de configurar su conector de descodificación de archivos sin formato. En una aplicación real, puede almacenar los datos descodificados en una aplicación de línea de negocios como SalesForce. Puede agregar fácilmente una acción para enviar el resultado de la acción de descodificación a Salesforce.

Ahora puede probar el conector realizando una solicitud al punto de conexión HTTP e incluyendo el contenido XML que quiera descodificar en el cuerpo de la solicitud.  

## <a name="next-steps"></a>Pasos siguientes
* [Más información sobre Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack").  


---
title: "Información general sobre los esquemas y Enterprise Integration Pack | Microsoft Docs"
description: "Información sobre cómo usar los esquemas con Logic Apps y Enterprise Integration Pack"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2e0419c46e9380eb7e1848524a99501b4961d35c


---
# <a name="learn-about-schemas-and-the-enterprise-integration-pack"></a>Información sobre los esquemas y Enterprise Integration Pack
## <a name="why-use-a-schema"></a>¿Por qué se utilizan los esquemas?
Utilice los esquemas para confirmar que los documentos XML recibidos son válidos; que contienen los datos esperados en un formato predefinido. Los esquemas se utilizan para validar los mensajes que se intercambian en un escenario B2B.

## <a name="add-a-schema"></a>Agregar un esquema
En el Portal de Azure:  

1. Seleccione **Más servicios**.  
   ![Captura de Azure Portal, con "Más servicios" resaltado](./media/app-service-logic-enterprise-integration-overview/overview-11.png)    
2. En el cuadro de búsqueda, especifique **integración** y seleccione **Integration Accounts** (Cuentas de integración) en la lista de resultados.     
   ![Captura de pantalla del cuadro de búsqueda del filtro](./media/app-service-logic-enterprise-integration-overview/overview-21.png)  
3. Seleccione la **cuenta de integración** en la que va a agregar el esquema.    
   ![Captura de pantalla de lista de cuentas de integración](./media/app-service-logic-enterprise-integration-overview/overview-31.png)  
4. Seleccione el icono de **Esquemas**.  
   ![Captura de pantalla de la cuenta de integración EIP con "Esquemas" resaltado](./media/app-service-logic-enterprise-integration-schemas/schema-11.png)  

### <a name="add-a-schema-file-less-than-2-mb"></a>Agregar un archivo de esquema inferior a 2 MB
1. En la hoja **Esquemas** que se abre (de los pasos anteriores), seleccione **Agregar**.  
   ![Captura de pantalla de la hoja Esquemas con el botón "Agregar" resaltado](./media/app-service-logic-enterprise-integration-schemas/schema-21.png)  
2. Escriba un nombre para el esquema. Luego, para cargar el archivo de esquema, seleccione el icono de carpeta junto al cuadro de texto **Esquema**. Cuando termine el proceso de carga, seleccione **Aceptar**.    
   ![Captura de pantalla de "Agregar esquema" con "Archivo pequeño" resaltado](./media/app-service-logic-enterprise-integration-schemas/schema-31.png)  

### <a name="add-a-schema-file-larger-than-2-mb-up-to-a-maximum-of-8-mb"></a>Agregar un archivo de esquema superior a 2 MB (hasta un máximo de 8 MB)
El proceso para esto depende en el nivel de acceso del contenedor de blob: **Público** o **Sin acceso anónimo**. Para determinar este nivel de acceso en **Azure Storage Explorer**, en **Contenedores de blobs**, seleccione el contenedor de blob que desee. Seleccione **Seguridad** y la pestaña **Nivel de acceso**.

1. Si el nivel de acceso de seguridad de blob es **Público**, siga estos pasos.  
   ![Captura de pantalla de Azure Storage Explorer, con "Contenedores de blob", "Seguridad" y "Público" resaltados](./media/app-service-logic-enterprise-integration-schemas/blob-public.png)  
   
    a. Cargue el esquema en el almacenamiento y copie el URI.  
    ![Captura de pantalla de cuenta de almacenamiento con URI resaltado](./media/app-service-logic-enterprise-integration-schemas/schema-blob.png)  
   
    b. En **Agregar esquema**, seleccione **Archivo grande** y proporcione el URI en el cuadro de texto **URI de contenido**.  
    ![Captura de pantalla de esquemas con el botón "Agregar" y "Archivo grande" resaltados](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)  
2. Si el nivel de acceso de seguridad de blob es **Sin acceso anónimo**, siga estos pasos.  
   ![Captura de pantalla de Azure Storage Explorer, con "Contenedores de blob", "Seguridad" y "Sin acceso anónimo" resaltados](./media/app-service-logic-enterprise-integration-schemas/blob-1.png)  
   
    a. Cargue el esquema en el almacenamiento.  
    ![Captura de pantalla de cuenta de almacenamiento](./media/app-service-logic-enterprise-integration-schemas/blob-3.png)
   
    b. Genere una firma de acceso compartido para el esquema.  
    ![Captura de pantalla de la cuenta de almacenamiento con la ficha firmas de acceso compartido resaltada](./media/app-service-logic-enterprise-integration-schemas/blob-2.png)
   
    c. En **Agregar esquema**, seleccione **Archivo grande** y proporcione el URI de firma de acceso compartido en el cuadro de texto **URI de contenido**.  
    ![Captura de pantalla de esquemas con el botón "Agregar" y "Archivo grande" resaltados](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)  
3. En la hoja **Esquemas** de la cuenta de integración EIP debería ver ahora el esquema recién agregado.  
   ![Captura de pantalla de la cuenta de integración EIP con "Esquemas" y el nuevo esquema resaltados](./media/app-service-logic-enterprise-integration-schemas/schema-41.png)

## <a name="edit-schemas"></a>Editar esquemas
1. Seleccione el icono de **Esquemas**.  
2. Seleccione el esquema que quiera editar en la hoja **Esquemas** que se abre.
3. En la hoja **Esquemas** hoja, seleccione **Editar**.  
   ![Captura de pantalla de la hoja Esquemas](./media/app-service-logic-enterprise-integration-schemas/edit-12.png)    
4. Seleccione el archivo de esquema que quiera editar mediante el cuadro de diálogo del selector de archivos que se abre.
5. Haga clic en la opción **Abrir** del selector de archivos.  
   ![Captura de pantalla del selector de archivos](./media/app-service-logic-enterprise-integration-schemas/edit-31.png)  
6. Recibirá una notificación que indica que la carga se ha realizado correctamente.  

## <a name="delete-schemas"></a>Eliminar esquemas
1. Seleccione el icono de **Esquemas**.  
2. Elija el esquema que quiera eliminar de la hoja **Esquemas** que se abre.  
3. En la hoja **Esquemas** hoja, seleccione **Eliminar**.
   ![Captura de pantalla de la hoja Esquemas](./media/app-service-logic-enterprise-integration-schemas/delete-12.png)  
4. Para confirmar su elección, seleccione **Sí**.  
   ![Captura de pantalla del mensaje de confirmación "Eliminar esquema"](./media/app-service-logic-enterprise-integration-schemas/delete-21.png)  
5. Finalmente, observe que la lista de esquemas de la hoja **Esquemas** se actualiza y que el esquema que ha eliminado ya no aparece.  
   ![Captura de pantalla de la cuenta de integración EIP con "Esquemas" resaltado](./media/app-service-logic-enterprise-integration-schemas/delete-31.png)    

## <a name="next-steps"></a>Pasos siguientes
* [Más información sobre Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack").  




<!--HONumber=Nov16_HO3-->



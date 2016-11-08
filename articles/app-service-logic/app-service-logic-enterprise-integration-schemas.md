---
title: Información general sobre los esquemas y Enterprise Integration Pack | Microsoft Docs
description: Sepa cómo usar los esquemas con las Aplicaciones lógicas y Enterprise Integration Pack.
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun

ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: deonhe

---
# Información sobre los esquemas y Enterprise Integration Pack
## ¿Por qué se utilizan los esquemas?
Utilice los esquemas para confirmar que los documentos XML recibidos son válidos; es decir, que contienen los datos esperados en un formato predefinido.

## ¿Cómo se agregan esquemas?
En el Portal de Azure:

1. Seleccione **Más servicios**.  
   ![](./media/app-service-logic-enterprise-integration-overview/overview-11.png)    
2. Escriba **integration** en el cuadro de búsqueda del filtro y seleccione **Integration Accounts** (Cuentas de integración) en la lista de resultados.    
   ![](./media/app-service-logic-enterprise-integration-overview/overview-21.png)
3. Seleccione la **cuenta integración** en la que va a agregar el esquema.    
   ![](./media/app-service-logic-enterprise-integration-overview/overview-31.png)  
4. Seleccione el icono de **Esquemas**.  
   ![](./media/app-service-logic-enterprise-integration-schemas/schema-11.png)  

#### Para agregar un archivo de esquema de menos de 2 MB
1. Haga clic en el botón **Agregar** de la hoja Esquemas que se abre.  
   ![](./media/app-service-logic-enterprise-integration-schemas/schema-21.png)  
2. Escriba un **nombre** para el esquema. Después, cargue el archivo de esquema y seleccione el icono de carpeta en el lado derecho del cuadro de texto **Esquema**. Cuando termine el proceso de carga, haga clic en el botón **Aceptar**.    
   ![](./media/app-service-logic-enterprise-integration-schemas/schema-31.png)  

#### Para agregar un archivo de esquema de más de 2 MB (máximo de 8 MB)
1. Si el nivel de acceso de seguridad de blob es **público**:  
   ![](./media/app-service-logic-enterprise-integration-schemas/blob-public.png)  
   
   * Cargue el esquema en el almacenamiento y copie el URI.  
     ![](./media/app-service-logic-enterprise-integration-schemas/schema-blob.png)
   * Seleccione Archivos grandes en Agregar esquema y proporcione el URI en URI de contenido.  
     ![](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)
2. Si el nivel de acceso de seguridad de blob es **Sin acceso anónimo**.  
   ![](./media/app-service-logic-enterprise-integration-schemas/blob-1.png)  
   
   * Cargue el esquema en el almacenamiento.  
     ![](./media/app-service-logic-enterprise-integration-schemas/blob-3.png)
   * Genere una Firma de acceso compartido para el esquema.  
     ![](./media/app-service-logic-enterprise-integration-schemas/blob-2.png)
   * Seleccione Archivos grandes en Agregar esquema y proporcione el URI de Firma de acceso compartido en URI de contenido.  
     ![](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)
3. Debería ver el esquema que acaba de agregar.  
   ![](./media/app-service-logic-enterprise-integration-schemas/schema-41.png)

## ¿Cómo se utilizan esquemas?
* Los esquemas se utilizan para validar los mensajes que se intercambian en un escenario B2B.

## ¿Cómo se editan esquemas?
1. Seleccione el icono de **Esquemas**.
2. Elija el esquema que quiera modificar en la hoja Esquemas que se abre.
3. Haga clic en el vínculo **Cargar** de la hoja Esquemas.  
   ![](./media/app-service-logic-enterprise-integration-schemas/edit-12.png)    
4. Seleccione el archivo de esquema que quiera cargar mediante el cuadro de diálogo selector de archivos que se abre.
5. Haga clic en la opción **Abrir** del selector de archivos.  
   ![](./media/app-service-logic-enterprise-integration-schemas/edit-31.png)  
6. Recibirá una notificación que indica que la carga se ha realizado correctamente.

## ¿Cómo se eliminan esquemas?
1. Seleccione el icono de **Esquemas**.
2. Elija el esquema que quiera eliminar en la hoja Esquemas que se abre.
3. Seleccione el vínculo **Eliminar** de la barra de menús de la hoja Esquemas.
   ![](./media/app-service-logic-enterprise-integration-schemas/delete-12.png)  
4. Si realmente quiere eliminar el esquema seleccionado, seleccione **Sí** en el cuadro de diálogo Eliminar esquema para confirmar la acción.  
   ![](./media/app-service-logic-enterprise-integration-schemas/delete-21.png)  
5. Finalmente, observe que la lista de esquemas de la hoja Esquemas se actualiza y que el esquema que ha eliminado ya no aparece.  
   ![](./media/app-service-logic-enterprise-integration-schemas/delete-31.png)    

## Pasos siguientes
* [Más información sobre Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack")

<!---HONumber=AcomDC_0921_2016-->
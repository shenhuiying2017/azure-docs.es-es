---
title: "Información general sobre las asignaciones y Enterprise Integration Pack | Microsoft Docs"
description: "Sepa cómo usar las asignaciones con las Aplicaciones lógicas y Enterprise Integration Pack."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 90f5cfc4-46b2-4ef7-8ac4-486bb0e3f289
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: 7e95b4da32aee892bf08cbcbe23a67d110911bf6


---
# <a name="learn-about-maps-and-the-enterprise-integration-pack"></a>Información sobre las asignaciones y Enterprise Integration Pack
## <a name="overview"></a>Información general
Enterprise Integration Pack utiliza las asignaciones para transformar datos XML de un formato a otro. 

## <a name="what-is-a-map"></a>¿Qué son las asignaciones?
Una asignación es un documento XML que define qué datos de un documento deben transformarse en otro formato. 

## <a name="why-use-maps"></a>¿Por qué se utilizan las asignaciones?
Supongamos que recibe periódicamente pedidos o facturas B2B de clientes que usen el formato de fecha AAAMMDD. Sin embargo, en su organización, las fechas se almacenan en el formato MMDDAAA. Puede usar una asignación para *transformar* el formato de fecha AAAMMDD en MMDDAAA antes de almacenar los detalles de los pedidos o las facturas en la base de datos de actividades de clientes.

## <a name="how-do-i-create-a-map"></a>¿Cómo se crea una asignación?
[Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack") para Visual Studio 2015 permite crear proyectos de integración de Biztalk.  Al crear un archivo de asignación de integración, podrá asignar visualmente elementos entre dos archivos de esquema XML.  Después de compilar este proyecto, se genera un documento XSLT.

## <a name="how-to-upload-a-map"></a>¿Cómo se cargan asignaciones?
En el Portal de Azure:  

1. Seleccione **Examinar**  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. Escriba **integration** en el cuadro de búsqueda del filtro y seleccione **Integration Accounts** (Cuentas de integración) en la lista de resultados     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. Seleccione la **cuenta integración** en la que va a agregar la asignación  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. Seleccione el icono de **Asignaciones**  
   ![](./media/logic-apps-enterprise-integration-maps/map-1.png)  
5. Haga clic en el botón **Agregar** de la hoja Asignaciones que se abre  
   ![](./media/logic-apps-enterprise-integration-maps/map-2.png)  
6. Escriba el **nombre** de la asignación y, después, para cargar el archivo de asignación, seleccione el icono de carpeta de la derecha del cuadro de texto **Asignación**. Cuando se complete el proceso de carga, haga clic en el botón **Aceptar**.  
   ![](./media/logic-apps-enterprise-integration-maps/map-3.png)  
7. Ahora, la asignación se agrega a la cuenta de integración. Recibirá una notificación en la pantalla que indica que el proceso de agregar el archivo de asignación se ha realizado correctamente o con errores. Cuando reciba la notificación, seleccione el icono de **Asignaciones** y verá la asignación que acaba de agregar en la hoja Asignaciones:    
   ![](./media/logic-apps-enterprise-integration-maps/map-4.png)  

## <a name="how-to-edit-a-map"></a>¿Cómo se editan asignaciones?
Para editar una asignación, debe cargar un nuevo archivo de asignación con los cambios que desee. En primer lugar, puede descargar la asignación y editarla. 

Siga estos pasos para cargar una nueva asignación que reemplace una que ya exista:  

1. Seleccione el icono de **Asignaciones** .  
2. Elija la asignación que quiera editar cuando se abra la hoja Asignaciones.  
3. En la hoja **Asignaciones**, haga clic en el vínculo **Actualizar**  
   ![](./media/logic-apps-enterprise-integration-maps/edit-1.png)   
4. Seleccione el archivo de asignaciones que desea cargar, para lo que debe usar el cuadro de diálogo del selector de archivos que se abre y, luego, seleccione **Abrir** en el selector de archivos   
   ![](./media/logic-apps-enterprise-integration-maps/edit-2.png)   
5. Recibirá una notificación emergente cuando se cargue la asignación.    

## <a name="how-to-delete-a-map"></a>¿Cómo se eliminan asignaciones?
1. Seleccione el icono de **Asignaciones** .  
2. Elija la asignación que quiera eliminar cuando se abra la hoja Asignaciones.  
3. Haga clic en el vínculo **Eliminar**    
   ![](./media/logic-apps-enterprise-integration-maps/delete.png)   
4. Confirme que quiere eliminar realmente la asignación.  
   ![](./media/logic-apps-enterprise-integration-maps/delete-confirmation-1.png)   

## <a name="next-steps"></a>Pasos siguientes
* [Más información sobre Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack")  
* [Más información sobre los contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md "Información sobre los contratos de integración de empresas")  
* [Más información sobre las transformaciones](logic-apps-enterprise-integration-transform.md "Información sobre las transformaciones de integración empresarial")  




<!--HONumber=Jan17_HO3-->



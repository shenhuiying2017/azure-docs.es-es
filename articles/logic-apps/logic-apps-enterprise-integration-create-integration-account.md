---
title: "Creación, vinculación, eliminación o movimiento de una cuenta de integración en Azure Logic Apps | Microsoft Docs"
description: "Creación de una cuenta de integración y vinculación de la misma a Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: dca762854f22721de76f69cbc28c0377d563fe28
ms.openlocfilehash: e26271a1291fc76e3ea8f93a7aa9ab8c811ff604


---

# <a name="what-is-an-integration-account"></a>¿Qué es una cuenta de integración?
Una cuenta de integración permite a las aplicaciones de integración empresarial administrar artefactos, incluidos esquemas, asignaciones, certificados, asociados y contratos. Cualquier aplicación de integración que se crea, utiliza una cuenta de integración para tener acceso a estos esquemas, mapas, certificados, etc.

## <a name="create-an-integration-account"></a>Creación de una cuenta de integración
1. Seleccione **Examinar**:   
   ![](./media/logic-apps-enterprise-integration-accounts/account-1.png)  
2. Escriba **integración** en el cuadro de búsqueda del filtro y seleccione **Cuentas de integración** en la lista de resultados:     
   ![](./media/logic-apps-enterprise-integration-accounts/account-2.png)  
3. En el menú de la parte superior de la página, haga clic en el botón *Agregar*:      
   ![](./media/logic-apps-enterprise-integration-accounts/account-3.png)  
4. Escriba el **nombre**, seleccione la **suscripción** que desea usar, cree una nuevo **grupo de recursos** o seleccione uno existente, seleccione la **ubicación** en que se hospedará la cuenta de integración, seleccione un **plan de tarifa** y, luego, haga clic en el botón **Crear**.   
   
   En este momento, la cuenta de integración se aprovisionará en la ubicación seleccionada. Este proceso debería tardar un 1 minuto como máximo.    
   ![](./media/logic-apps-enterprise-integration-accounts/account-4.png)  
5. Actualice la página. Verá que la nueva cuenta de integración aparece en la lista:  
   ![](./media/logic-apps-enterprise-integration-accounts/account-5.png) 

A continuación, vincule la cuenta de integración que acaba de crear a la aplicación lógica. 

## <a name="link-an-integration-account-to-a-logic-app"></a>Vinculación de una cuenta de integración a una aplicación lógica
Para que las aplicaciones lógicas puedan acceder a las asignaciones, los esquemas, los contratos y otros artefactos de la cuenta de integración, vincule esta a la aplicación lógica.

#### <a name="prereqs"></a>Requisitos previos
* Tener una cuenta de integración
* Tener una aplicación lógica

> [!NOTE] 
> Antes de empezar, asegúrese de que la cuenta de integración y la aplicación lógica se encuentran en la **misma ubicación de Azure**.

1. Seleccione el vínculo **Configuración** en el menú de la aplicación lógica:  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-1.png)   
2. Seleccione el elemento **Cuenta de integración** en la hoja Configuración:  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-2.png)   
3. Seleccione la cuenta de integración que desee vincular a la aplicación lógica en el cuadro de lista desplegable **Seleccione una cuenta de integración**:  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-3.png)   
4. Guarde el trabajo:  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-4.png)   
5. Verá una notificación que indica que la cuenta de integración se ha vinculado a la aplicación lógica y que todos los artefactos de la cuenta de integración están ahora disponibles en dicha aplicación:  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-5.png)   

Ahora que la cuenta de integración está vinculada a la aplicación lógica, puede usar los conectores de B2B dentro de las aplicaciones lógicas. Algunos conectores B2B comunes incluyen validación XML y codificación y descodificación de archivos planos.  

## <a name="how-to-delete-an-integration-account"></a>¿Cómo se elimina una cuenta de integración?
1. Seleccione **Examinar**:  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. Escriba **integración** en el cuadro de búsqueda del filtro y seleccione **Cuentas de integración** en la lista de resultados:     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. Seleccione la **cuenta de integración** que desee eliminar:  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. Haga clic en el vínculo **Eliminar** del menú:   
   ![](./media/logic-apps-enterprise-integration-accounts/delete.png)  
5. Confirme la selección.    

## <a name="how-to-move-an-integration-account"></a>¿Cómo se mueve una cuenta de integración?
Puede mover fácilmente una cuenta de integración a una nueva suscripción y a un grupo de recursos. Siga estos pasos si tiene que mover su cuenta de integración:

> [!IMPORTANT]
> Tiene que actualizar todos los scripts para utilizar los nuevos identificadores de recurso después de mover una cuenta de integración.

1. Seleccione **Examinar**:  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. Escriba **integración** en el cuadro de búsqueda del filtro y seleccione **Cuentas de integración** en la lista de resultados:     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. Seleccione la **cuenta de integración** que desee eliminar:  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. Haga clic en el vínculo **Mover** del menú:   
   ![](./media/logic-apps-enterprise-integration-accounts/move.png)  
5. Confirme la selección.    

## <a name="next-steps"></a>Pasos siguientes
* [Más información sobre los contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md "Información sobre los contratos de integración de empresas")  




<!--HONumber=Jan17_HO5-->



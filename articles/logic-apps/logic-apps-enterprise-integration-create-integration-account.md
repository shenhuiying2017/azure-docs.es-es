---
title: "Información general sobre las cuentas de integración y Enterprise Integration Pack | Microsoft Docs"
description: "Aprenda todo lo que necesita saber sobre las cuentas de integración, Enterprise Integration Pack y las Aplicaciones lógicas."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: ce2f01045195b9881bc1b3c337c453d10cd39540


---
# <a name="overview-of-integration-accounts"></a>Información general sobre las cuentas de integración
## <a name="what-is-an-integration-account"></a>¿Qué es una cuenta de integración?
Una cuenta de integración es una cuenta de Azure que permite a las aplicaciones de Enterprise Integration Pack administrar artefactos, incluidos esquemas, asignaciones, certificados, partners y contratos. Todas las aplicaciones de integración que cree tendrán que utilizar una cuenta de integración para acceder, por ejemplo, a un esquema, una asignación o un certificado.

## <a name="create-an-integration-account"></a>Creación de una cuenta de integración
1. Seleccione **Examinar**   
   ![](./media/logic-apps-enterprise-integration-accounts/account-1.png)  
2. Escriba **integration** en el cuadro de búsqueda del filtro y seleccione **Integration Accounts** (Cuentas de integración) en la lista de resultados     
   ![](./media/logic-apps-enterprise-integration-accounts/account-2.png)  
3. En el menú de la parte superior de la página, haga clic en el botón *Agregar*      
   ![](./media/logic-apps-enterprise-integration-accounts/account-3.png)  
4. Escriba el **nombre**, seleccione la **suscripción** que desea usar, cree una nuevo **grupo de recursos** o seleccione uno existente, seleccione la **ubicación** en que se hospedará la cuenta de integración, seleccione un **plan de tarifa** y, luego, haga clic en el botón **Crear**.   
   
   En este momento, la cuenta de integración se aprovisionará en la ubicación seleccionada. Este proceso debería tardar un 1 minuto como máximo.    
   ![](./media/logic-apps-enterprise-integration-accounts/account-4.png)  
5. Actualice la página. Verá que la nueva cuenta de integración aparece en la lista. Felicidades.  
   ![](./media/logic-apps-enterprise-integration-accounts/account-5.png) 

## <a name="how-to-link-an-integration-account-to-a-logic-app"></a>¿Cómo se vincula una cuenta de integración a una Aplicación lógica?
Para que las Aplicaciones lógicas puedan acceder a las asignaciones, los esquemas, los contratos y otros artefactos de su cuenta de integración, antes debe vincularla a su Aplicación lógica.

### <a name="here-are-the-steps-to-link-an-integration-account-to-a-logic-app"></a>A continuación, figuran los pasos para vincular una cuenta de integración a una Aplicación lógica.
#### <a name="prerequisites"></a>Requisitos previos
* Tener una cuenta de integración
* Disponer de una Aplicación lógica

> [!NOTE]
> Antes de empezar, asegúrese de que la cuenta de la integración y la Aplicación lógica se encuentran en la **misma ubicación de Azure** .
> 
> 

1. Seleccione el vínculo **Settings** (Configuración) del menú de la aplicación lógica  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-1.png)   
2. Seleccione el elemento **Integration Account** (Cuenta de integración) en la hoja Settings (Configuración)  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-2.png)   
3. Seleccione la cuenta de integración que desee vincular a su aplicación lógica en el cuadro de lista desplegable **Select an Integration account** (Seleccione una cuenta de integración)  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-3.png)   
4. Guarde el trabajo  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-4.png)   
5. Verá una notificación que indica que la cuenta de integración se ha vinculado a su Aplicación lógica y que todos los artefactos de la cuenta de integración están ahora disponibles en la Aplicación lógica.  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-5.png)   

Ahora que la cuenta de integración se ha vinculado a la Aplicación lógica, puede acceder a esta última y usar los conectores B2B, como los de validación XML, codificación y descodificación de archivo sin formato o transformación, para crear aplicaciones con características B2B.  

## <a name="how-to-delete-an-integration-account"></a>¿Cómo se elimina una cuenta de integración?
1. Seleccione **Examinar**  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. Escriba **integration** en el cuadro de búsqueda del filtro y seleccione **Integration Accounts** (Cuentas de integración) en la lista de resultados     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. Seleccione la **cuenta de integración** que desee eliminar  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. Haga clic en el vínculo **Delete** (Eliminar) del menú   
   ![](./media/logic-apps-enterprise-integration-accounts/delete.png)  
5. Confirme la selección.    

## <a name="how-to-move-an-integration-account"></a>¿Cómo se mueve una cuenta de integración?
Puede mover fácilmente una cuenta de integración a una nueva suscripción y a un grupo de recursos. Siga estos pasos si tiene que mover su cuenta de integración:

> [!IMPORTANT]
> Tendrá que actualizar todos los scripts para utilizar los nuevos id. de recurso después de mover una cuenta de integración.
> 
> 

1. Seleccione **Examinar**  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. Escriba **integration** en el cuadro de búsqueda del filtro y seleccione **Integration Accounts** (Cuentas de integración) en la lista de resultados     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. Seleccione la **cuenta de integración** que desee eliminar  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. Haga clic en el vínculo **Move** (Mover) del menú   
   ![](./media/logic-apps-enterprise-integration-accounts/move.png)  
5. Confirme la selección.    

## <a name="next-steps"></a>Pasos siguientes
* [Más información sobre los contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md "Información sobre los contratos de integración de empresas")  




<!--HONumber=Jan17_HO3-->



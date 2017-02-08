---
title: "Información acerca de asociados y Enterprise Integration Pack | Microsoft Docs"
description: "Sepa cómo usar partners con las Aplicaciones lógicas y Enterprise Integration Pack"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: 549dd716df6ee64892ef33ffe4d42751d8662ccb


---
# <a name="learn-about-partners-and-enterprise-integration-pack"></a>Información sobre partners y Enterprise Integration Pack
## <a name="overview"></a>Información general
Antes de crear a un partner, tanto usted como la organización con la que vaya a hacer negocios deben compartir la información que lo ayude a identificar y validar los mensajes que se envían entre sí. Tras estos intercambios y cuando esté preparado para iniciar la relación empresarial, podrá crear un *partner* en la cuenta de integración.

## <a name="what-is-a-partner"></a>¿Qué es un partner?
Los partners son las entidades que participan en las transacciones y los intercambios de mensajes de negocio a negocio (B2B). 

## <a name="how-are-partners-used"></a>¿Cómo se usan los partners?
Los partners se utilizan para crear contratos. Los contratos definen los detalles de los mensajes que se intercambiarán entre los asociados. 

Antes de crear uno, hay que agregar, como mínimo, dos partners a la cuenta de integración. Además, uno de ellos uno debe ser su organización. El partner que la representa se conoce como el " **partner anfitrión**". El segundo partner representaría la otra organización con la que su organización intercambia mensajes. A este se le conoce como " **partner invitado**". El partner invitado puede ser otra compañía o incluso un departamento de su organización.  

Cuando haya agregado los partners, los usaría para crear un contrato. 

La configuración de recepción y de envío afecta exclusivamente al partner anfitrión. Por ejemplo, la configuración de recepción de un contrato determina cómo el partner anfitrión recibe los mensajes enviados de un partner invitado. Del mismo modo, la configuración de envío del contrato indica cómo el partner anfitrión envía mensajes al partner invitado.

## <a name="how-to-create-a-partner"></a>¿Cómo se crean partners?
En el Portal de Azure:  

1. Seleccione **Examinar**  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. Escriba **integration** en el cuadro de búsqueda del filtro y seleccione **Integration Accounts** (Cuentas de integración) en la lista de resultados     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. Seleccione la **cuenta integración** a la que va a agregar los asociados  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. Seleccione el icono de **Asociados** .  
   ![](./media/logic-apps-enterprise-integration-partners/partner-1.png)  
5. Haga clic en el botón **Agregar** de la hoja Asociados que se abre  
   ![](./media/logic-apps-enterprise-integration-partners/partner-2.png)  
6. Escriba el **nombre** del partner; después, seleccione el **calificador** y, por último, especifique un **valor**. El valor se utiliza para ayudar a identificar los documentos que se incluyen en las aplicaciones.  
   ![](./media/logic-apps-enterprise-integration-partners/partner-3.png)  
7. Seleccione el icono de notificación de *campana* para ver el progreso del proceso de creación de socios comerciales.  
   ![](./media/logic-apps-enterprise-integration-partners/partner-4.png)  
8. Seleccione el icono de **Asociados** . Con esto, se actualiza el icono y debería ver que el número de partners aumenta, lo que significa que se ha agregado correctamente el nuevo partner.    
   ![](./media/logic-apps-enterprise-integration-partners/partner-5.png)  
9. Cuando haya seleccionado el icono de Asociados, también verá que el partner que acaba de agregar aparece en la hoja Asociados.    
   ![](./media/logic-apps-enterprise-integration-partners/partner-6.png)  

## <a name="how-to-edit-a-partner"></a>¿Cómo se editan partners?
Siga estos pasos para editar un partner que ya exista en la cuenta de integración:  

1. Seleccione el icono de **Asociados** .  
2. Elija el partner que quiera editar cuando se abra la hoja Asociados.  
3. En el icono de **Update Partner** (Actualizar partner), realice los cambios necesarios.  
4. Si está satisfecho con los cambios, haga clic en el vínculo **Guardar**, o bien seleccione **Descartar** si desea deshacerlos.  
   ![](./media/logic-apps-enterprise-integration-partners/edit-1.png)  

## <a name="how-to-delete-a-partner"></a>¿Cómo se eliminan partners?
1. Seleccione el icono de **Asociados** .  
2. Elija el partner que quiera editar cuando se abra la hoja Asociados.  
3. Haga clic en el vínculo **Eliminar**    
   ![](./media/logic-apps-enterprise-integration-partners/delete-1.png)   

## <a name="next-steps"></a>Pasos siguientes
* [Más información sobre los contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md "Información sobre los contratos de integración de empresas")  




<!--HONumber=Jan17_HO3-->



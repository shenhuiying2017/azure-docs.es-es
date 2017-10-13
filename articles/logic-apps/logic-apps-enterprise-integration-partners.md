---
title: "Creación de asociados para mensajes de negocio a negocio (B2B): Azure Logic Apps | Microsoft Docs"
description: "Información sobre cómo agregar asociados a una cuenta de integración con Enterprise Integration Pack y Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 950cb449b53f400f0f0f860caf5415bbb5212269
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="add-or-update-partners-in-business-to-business-agreements-in-your-workflow"></a>Incorporación o actualización de asociados de contratos de negocio a negocio del flujo de trabajo

Los asociados son entidades que participan en transacciones de negocio a negocio (B2B) e intercambian mensajes entre ellos. Antes de poder crear asociados que le representen a usted y a otra organización en estas transacciones, debe compartir la información que identifica y valida los mensajes enviados por cada uno. Después de analizar estos detalles y prepararse para comenzar la relación de negocios, podrá crear asociados en la cuenta de integración para que los represente a ambos.

## <a name="what-roles-do-partners-have-in-your-integration-account"></a>¿Qué roles tienen los asociados en la cuenta de integración?

Para definir los detalles acerca de los mensajes intercambiados entre los asociados, debe crear contratos entre ellos. No obstante, antes de crear uno, hay que agregar, como mínimo, dos asociados a la cuenta de integración. La organización debe formar parte del contrato como **asociado del host**. El otro asociado, o **asociado invitado**, representa la organización que intercambia mensajes con su organización. El asociado invitado puede ser otra compañía o incluso un departamento de su organización.

Después de agregar a estos asociados, puede crear un contrato.

La configuración de recepción y de envío afecta exclusivamente al partner anfitrión. Por ejemplo, la configuración de recepción de un contrato determina cómo el partner anfitrión recibe los mensajes enviados de un partner invitado. Del mismo modo, la configuración de envío del contrato indica cómo el partner anfitrión envía mensajes al partner invitado.

## <a name="how-to-create-a-partner"></a>¿Cómo se crean partners?

1. En Azure Portal, seleccione **Examinar**.

    ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)

2. En el cuadro de búsqueda, especifique **integración** y seleccione **Integration Accounts** (Cuentas de integración) en la lista de resultados.

    ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)

3. Seleccione la cuenta integración en la que desea agregar a los asociados.

    ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Seleccione el icono de **Asociados** .

    ![](./media/logic-apps-enterprise-integration-partners/partner-1.png)

5. En la hoja de asociados, seleccione **Agregar**.

    ![](./media/logic-apps-enterprise-integration-partners/partner-2.png)

6. Escriba un nombre para el asociado y, a continuación, seleccione un **Calificador**. Finalmente, escriba un **valor** para ayudar a identificar los documentos que se incluyen en las aplicaciones.

    ![](./media/logic-apps-enterprise-integration-partners/partner-3.png)

7. Seleccione el icono de notificación de *campana* para ver el progreso del proceso de creación de asociados.

    ![](./media/logic-apps-enterprise-integration-partners/partner-4.png)

8. Para confirmar que los nuevos asociados se agregaron correctamente, seleccione el icono de **Asociados**.

    ![](./media/logic-apps-enterprise-integration-partners/partner-5.png)

    Cuando haya seleccionado el icono de Asociados, también verá que los asociados que acaba de agregar aparecen en la hoja Asociados.

    ![](./media/logic-apps-enterprise-integration-partners/partner-6.png)

## <a name="how-to-edit-existing-partners-in-your-integration-account"></a>Edición de asociados existentes en su cuenta de integración

1. Seleccione el icono de **Asociados** .
2. Seleccione el asociado que quiera editar cuando se abra la hoja Asociados.
3. En el icono **Actualizar asociado**, realice los cambios.
4. Cuando haya terminado, elija **Guardar** o, para cancelar los cambios, seleccione **Descartar**.

    ![](./media/logic-apps-enterprise-integration-partners/edit-1.png)

## <a name="how-to-delete-a-partner"></a>¿Cómo se eliminan partners?

1. Seleccione el icono de **Asociados** .
2. Seleccione el asociado que desea eliminar cuando se abra la hoja Asociados.
3. Elija **Eliminar**.

    ![](./media/logic-apps-enterprise-integration-partners/delete-1.png)

## <a name="next-steps"></a>Pasos siguientes
* [Más información sobre los contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md "Información sobre los contratos de integración de empresas")  


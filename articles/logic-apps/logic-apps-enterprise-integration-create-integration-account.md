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
ms.date: 02/23/2017
ms.author: LADocs; mandia
ms.openlocfilehash: b238ef8cf9d1328913334a92c042584334d81e3c
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="what-is-an-integration-account"></a>¿Qué es una cuenta de integración?

Una cuenta de integración proporciona un medio de que las aplicaciones de integración de la empresa, específicamente las aplicaciones lógicas, accedan y administren los artefactos B2B, por ejemplo, los asociados comerciales, los acuerdos, los mapas, los esquemas o los certificados, entre otros. Para proporcionar este acceso, vincule su cuenta de integración a la aplicación lógica, después de asegurarse de que tanto esta como la lógica de integración tienen la *misma ubicación de Azure*.

## <a name="create-an-integration-account"></a>Creación de una cuenta de integración

1. Inicie sesión en [Azure Portal](http://portal.azure.com "Azure Portal"). 

2. En el menú principal de Azure, seleccione **Todos los servicios**. En el cuadro de búsqueda, escriba "integración" y, después, seleccione **Cuentas de integración**.

   ![Creación de una cuenta de integración](./media/logic-apps-enterprise-integration-accounts/account-1.png)

3. En la parte superior de la página, elija **Agregar**.

   ![Elección de Agregar](./media/logic-apps-enterprise-integration-accounts/account-3.png)

4. Dele un nombre a la cuenta de integración y seleccione la suscripción de Azure que desee utilizar. Puede crear un **Grupo de recursos** nuevo o seleccionar uno existente. Seleccione la **ubicación** para hospedar la cuenta de integración y el **plan de tarifa**. Cuando esté listo, seleccione **Crear**.

   ![Incorporación de los detalles de la cuenta de integración](./media/logic-apps-enterprise-integration-accounts/account-4.png)

   Azure aprovisionará la cuenta de integración en la ubicación seleccionada, lo cual tardará un minuto como mucho.

5. Actualice la página. Verá la nueva cuenta de integración aparecer en la lista.

   ![La nueva cuenta de integración aparece en la lista](./media/logic-apps-enterprise-integration-accounts/account-5.png) 

A continuación, vincule la cuenta de integración que acaba de crear a la aplicación lógica. 

## <a name="link-an-integration-account-to-a-logic-app"></a>Vinculación de una cuenta de integración a una aplicación lógica

Para que las aplicaciones accedan a los artefactos B2B, tales como esquemas, acuerdos, mapas y esquemas en la cuenta de integración, vincule esta última a la aplicación lógica. 

1. En Azure Portal, seleccione la aplicación lógica y compruebe su ubicación.

   ![Selección de la aplicación lógica y comprobación de la ubicación](./media/logic-apps-enterprise-integration-accounts/linkaccount-1.png)

2. En **Configuración**, seleccione **Cuenta de integración**.

   ![Selección de la "Cuenta de integración"](./media/logic-apps-enterprise-integration-accounts/linkaccount-2.png)

3. Seleccione la cuenta de integración que desee vincular a la aplicación de la lista **Seleccione una cuenta de integración**. Para terminar de vinculación, elija **Guardar**.

   ![Selección de la cuenta de integración](./media/logic-apps-enterprise-integration-accounts/linkaccount-3.png)

   Recibirá una notificación de la vinculación de la cuenta de integración a la aplicación lógica y de que esta tiene ahora disponibles todos los artefactos de la cuenta de integración.

   ![Aplicación lógica vinculada a la cuenta de integración](./media/logic-apps-enterprise-integration-accounts/linkaccount-5.png)

Ahora que la cuenta de integración está vinculada a la aplicación lógica, puede usar los conectores de B2B en las aplicaciones lógicas. Algunos conectores de B2B comunes incluyen validación XML, y codificación y descodificación de archivos planos.  

## <a name="delete-your-integration-account"></a>Eliminación de la cuenta de integración

1. En el menú principal de Azure, seleccione **Todos los servicios**. En el cuadro de búsqueda, escriba "integración" y, después, seleccione **Cuentas de integración**.

   ![Búsqueda de la cuenta de integración](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. Seleccione la cuenta de integración que desee eliminar.

    ![Selección de la cuenta de integración que se va a eliminar](./media/logic-apps-enterprise-integration-accounts/account-5.png)

3. En el menú, elija **Eliminar**.

    ![Selección de "Eliminar"](./media/logic-apps-enterprise-integration-accounts/delete.png)

4. Confirme que desea eliminar la cuenta de integración.

## <a name="move-your-integration-account"></a>Movimiento de la cuenta de integración

Para mover una cuenta de integración a otro grupo de recursos o suscripción de Azure, siga estos pasos. Después de mover la cuenta de integración, asegúrese de actualizar todos los scripts para utilizar los nuevos identificadores de recurso.

1. En el menú principal de Azure, seleccione **Todos los servicios**. En el cuadro de búsqueda, escriba "integración" y, después, seleccione **Cuentas de integración**.

   ![Búsqueda de la cuenta de integración](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. Seleccione la cuenta de integración que desee mover. En **Configuración**, elija **Propiedades**.

   ![Seleccione de la cuenta de integración que va a mover. En Configuración, elija Propiedades.](./media/logic-apps-enterprise-integration-accounts/move.png)

3. Cambie el grupo de recursos o la suscripción de Azure asociados a la cuenta de integración.

   ![Elija Cambiar el grupo de recursos o Cambiar suscripción](./media/logic-apps-enterprise-integration-accounts/move-2.png)

## <a name="next-steps"></a>pasos siguientes

* [Más información sobre los contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md "Información sobre los contratos de integración de empresas")  


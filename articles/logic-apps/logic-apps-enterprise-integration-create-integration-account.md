---
title: 'Crear y administrar cuentas de integración para soluciones de B2B: Azure Logic Apps | Microsoft Docs'
description: Crear, vincular, mover y eliminar cuentas de integración para la integración empresarial y las soluciones B2B con Azure Logic Apps
services: logic-apps
documentationcenter: ''
author: ecfan
manager: cfowler
editor: ''
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/30/2018
ms.author: estfan
ms.openlocfilehash: e661920974c2b0d28200d4c3d82bd644a7a55395
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166253"
---
# <a name="create-and-manage-integration-accounts-for-b2b-solutions-with-logic-apps"></a>Crear y administrar cuentas de integración para soluciones de B2B con Logic Apps

Para poder generar [integración empresarial y soluciones de B2B](../logic-apps/logic-apps-enterprise-integration-overview.md) con [Azure Logic Apps](../logic-apps/logic-apps-overview.md), antes debe tener una cuenta de integración, que es donde crear, almacenar y administrar artefactos de B2B, como socios comerciales, acuerdos, asignaciones, esquemas, certificados, etc. Para que la aplicación lógica pueda trabajar con los artefactos de la cuenta de integración y usar los conectores de Logic Apps B2B, como la validación XML, debe [vincular su cuenta de integración](#link-account) a la aplicación lógica. Para vincularlos, tanto la cuenta de integración como la aplicación lógica deben tener la *misma* ubicación de Azure o región.

En este artículo se muestra cómo realizar estas tareas:

* Crear la cuenta de integración.
* Vincular la cuenta de integración a una aplicación lógica
* Mover la cuenta de integración a otra suscripción o grupo de recursos de Azure.
* Eliminar la cuenta de integración.

Si no tiene una suscripción de Azure, <a href="https://azure.microsoft.com/free/" target="_blank">regístrese para obtener una cuenta gratuita de Azure</a>.

## <a name="sign-in-to-the-azure-portal"></a>Inicie sesión en el Portal de Azure.

Inicie sesión en <a href="https://portal.azure.com" target="_blank">Azure Portal</a> con sus credenciales de su cuenta de Azure.

## <a name="create-integration-account"></a>Creación de una cuenta de integración

1. En el menú principal de Azure, seleccione **Todos los servicios**. En el cuadro de búsqueda, especifique "cuentas de integración" como filtro y seleccione **Cuentas de integración**.

   ![Buscar cuentas de integración](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. En **Cuentas de integración**, elija **Agregar**.

   ![Elija "Agregar" para crear una cuenta de integración](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

3. Proporcione información sobre la cuenta de integración: 

   ![Incorporación de los detalles de la cuenta de integración](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Propiedad | Obligatorio | Valor de ejemplo | DESCRIPCIÓN | 
   |----------|----------|---------------|-------------|
   | NOMBRE | Sí | test-integration-account | Nombre para la cuenta de integración. En este ejemplo, utilice el nombre especificado. | 
   | La suscripción | Sí | <*Azure-subscription-name*> | Nombre de la suscripción a Azure que se va a usar | 
   | Grupos de recursos | Sí | test-integration-account-rg | Nombre del [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md) que se utiliza para organizar recursos relacionados. Para este ejemplo, cree un nuevo grupo de recursos con el nombre especificado. | 
   | Nivel de precios | Sí | Gratuito | El plan de tarifa que quiere usar. En este ejemplo, seleccione **Gratuito**, pero, para obtener más información, consulte [Límites y configuración de Logic Apps](../logic-apps/logic-apps-limits-and-config.md) y [Precios de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/). | 
   | Ubicación | Sí | Oeste de EE. UU | Región en la que quiere almacenar la información de la cuenta de integración. Seleccione la misma ubicación que la aplicación lógica o cree una aplicación lógica en la misma ubicación que la cuenta de integración. En este ejemplo, | 
   | Log Analytics | Sin  | Off | Mantenga el valor **Off** para el registro de diagnóstico. | 
   ||||| 

4. Cuando esté listo, elija **Anclar al panel** y elija **Crear**.

   Después de que Azure implemente su cuenta de integración en la ubicación seleccionada, proceso que suele durar un minuto, Azure abre su cuenta de integración.

   ![Azure abre su cuenta de integración](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

Ahora, para que la aplicación lógica pueda usar su cuenta de integración, debe vincular la cuenta de integración a la aplicación lógica.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Vincular a la aplicación lógica

Para que las aplicaciones lógicas accedan a la cuenta de integración que contiene los artefactos de B2B, tales como socios comerciales, esquemas, acuerdos y mapas, debe vincular la cuenta de integración a la aplicación lógica. 

> [!NOTE]
> La cuenta de integración y la aplicación lógica deben estar en la misma región.

1. En Azure Portal, busque y abra la aplicación lógica.

2. En el menú de la aplicación lógica, en **Configuración**, seleccione **Configuración de flujo de trabajo**. En la lista **Seleccione una cuenta de integración**, seleccione la cuenta de integración que quiera vincular con la aplicación lógica.

   ![Selección de la cuenta de integración](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-2.png)

3. Para terminar de vinculación, elija **Guardar**.

   ![Selección de la cuenta de integración](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-3.png)

   Cuando la cuenta de integración se haya vinculado correctamente, Azure muestra un mensaje de confirmación. 

   ![Azure confirma el vínculo correcto](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-5.png)

Ahora, la aplicación lógica puede usar cualquier artefacto de la cuenta de integración, además de conectores de B2B, como la validación XML, y la codificación o descodificación de archivos sin formato.  

## <a name="unlink-from-logic-app"></a>Desvinculación de aplicación lógica

Para vincular la aplicación lógica a otra cuenta de integración o dejar de usar una cuenta de integración con la aplicación lógica, puede eliminar el vínculo a través de Azure Resource Explorer.

1. En el explorador, vaya a <a href="https://resources.azure.com" target="_blank">Azure Resource Explorer (https://resources.azure.com)</a>. Asegúrese de que ha iniciado sesión con las mismas credenciales de Azure.

   ![Explorador de recursos de Azure](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

2. En el cuadro de búsqueda, escriba el nombre de la aplicación lógica y, a continuación, busque y seleccione la aplicación lógica.

   ![Buscar y seleccionar la aplicación lógica](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

3. En la barra de título del explorador, elija **lectura/escritura**.

   ![Activar el modo de "lectura/escritura"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-read-write-mode.png)

4. En la pestaña **Datos**, seleccione **Editar**.

   ![En la pestaña Datos, seleccione "Editar"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-edit.png)

5. En el editor, busque la propiedad `integrationAccount` para la cuenta de integración y elimine la propiedad, que tiene este formato:

   ```json
   "integrationAccount": {
      "name": "<integration-account-name>",
      "id": "<integration-account-resource-ID>",
      "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Por ejemplo: 

   ![Buscar la definición de la propiedad "integrationAccount"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

6. En la pestaña **Datos**, seleccione **Put** para guardar los cambios. 

   ![Elegir "Put" para guardar los cambios](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

7. En Azure Portal, en la **configuración de flujo de trabajo** de la aplicación lógica, compruebe que la propiedad **Cuenta de integración** esté vacía.

   ![Comprobar que la cuenta de integración no está vinculada](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Mover la cuenta de integración

Puede mover la cuenta de integración a otra suscripción o grupo de recursos de Azure.

1. En el menú principal de Azure, seleccione **Todos los servicios**. En el cuadro de búsqueda, especifique "cuentas de integración" como filtro y seleccione **Cuentas de integración**.

   ![Búsqueda de la cuenta de integración](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. En **Cuentas de integración**, seleccione la cuenta de integración que quiera mover. En el menú de la cuenta de integración, en **Configuración**, elija **Propiedades**.

   ![En "Configuración", elija "Propiedades".](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-properties.png)

3. Cambie la suscripción o el grupo de recursos de Azure para la cuenta de integración.

   ![Elegir "Cambiar el grupo de recursos" o "Cambiar suscripción"](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

4. Al acabar, asegúrese de actualizar todos los scripts con los nuevos identificadores de recursos para los artefactos.  

## <a name="delete-integration-account"></a>Eliminar una cuenta de integración

1. En el menú principal de Azure, seleccione **Todos los servicios**. En el cuadro de búsqueda, especifique "cuentas de integración" como filtro y seleccione **Cuentas de integración**.

   ![Búsqueda de la cuenta de integración](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. En **Cuentas de integración**, seleccione la cuenta de integración que quiera eliminar. En el menú de cuenta de integración, elija **Introducción** y, a continuación, **Eliminar**. 

   ![Seleccione la cuenta de integración. En la página "Información general", elija "Eliminar"](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

3. Para confirmar que quiere eliminar la cuenta de integración, elija **Sí**.

   ![Para confirmar la eliminación, elija "Sí"](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Pasos siguientes

* [Crear socios comerciales](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Crear acuerdos](../logic-apps/logic-apps-enterprise-integration-agreements.md)
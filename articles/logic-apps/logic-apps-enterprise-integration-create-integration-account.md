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
ms.openlocfilehash: 716e7b5bab8725dea0fd2b760d0e46e8e892c5b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-an-integration-account"></a>¿Qué es una cuenta de integración?

Una cuenta de integración permite a las aplicaciones de integración empresarial administrar artefactos, incluidos esquemas, asignaciones, certificados, asociados y contratos. Cualquier aplicación de integración que se crea, utiliza una cuenta de integración para tener acceso a estos esquemas, mapas, certificados, etc.

## <a name="create-an-integration-account"></a>Creación de una cuenta de integración

1.  Inicie sesión en [Azure Portal](http://portal.azure.com "Azure Portal"). En el menú izquierdo, seleccione **Más servicios**.

    ![Selección de "Más servicios"](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. En el cuadro de búsqueda, escriba "integración" como filtro. En la lista de resultados, seleccione **Cuentas de integración**.

    ![Filtre por "integración", seleccione "Cuentas de integración"](./media/logic-apps-enterprise-integration-accounts/account-2.png)  

3. En la parte superior de la página, elija **Agregar**.

    ![Elección de Agregar](./media/logic-apps-enterprise-integration-accounts/account-3.png)

4. Dele un nombre a la cuenta de integración y seleccione la suscripción de Azure que desee utilizar. Puede crear un **Grupo de recursos** nuevo o seleccionar uno existente. A continuación, seleccione la **Ubicación** para hospedar la cuenta de integración y el **Plan de tarifa**. 

    Cuando esté listo, seleccione **Crear**.

    ![Incorporación de los detalles de la cuenta de integración](./media/logic-apps-enterprise-integration-accounts/account-4.png)

    Azure aprovisionará la cuenta de integración en la ubicación seleccionada, lo cual tardará un minuto como mucho.

5. Actualice la página. Verá la nueva cuenta de integración aparecer en la lista.

    ![La nueva cuenta de integración aparece en la lista](./media/logic-apps-enterprise-integration-accounts/account-5.png) 

A continuación, vincule la cuenta de integración que acaba de crear a la aplicación lógica. 

## <a name="link-an-integration-account-to-a-logic-app"></a>Vinculación de una cuenta de integración a una aplicación lógica

Para que las aplicaciones lógicas puedan acceder a las asignaciones, los esquemas, los contratos y otros artefactos de la cuenta de integración, vincule esta última a la aplicación lógica.

### <a name="prerequisites"></a>Requisitos previos

* Tener una cuenta de integración
* Tener una aplicación lógica

> [!NOTE] 
> Antes de empezar, asegúrese de que la cuenta de integración y la aplicación lógica se encuentran en la *misma ubicación de Azure*.


1. En Azure Portal, seleccione la aplicación lógica y compruebe su ubicación.

    ![Selección de la aplicación lógica y comprobación de la ubicación](./media/logic-apps-enterprise-integration-accounts/linkaccount-1.png)

2. En **Configuración**, seleccione **Cuenta de integración**.

    ![Selección de la "Cuenta de integración"](./media/logic-apps-enterprise-integration-accounts/linkaccount-2.png)

3. Seleccione la cuenta de integración que desee vincular a la aplicación de la lista **Seleccione una cuenta de integración**. Para terminar de vinculación, elija **Guardar**.

    ![Selección de la cuenta de integración](./media/logic-apps-enterprise-integration-accounts/linkaccount-3.png)

    Recibirá una notificación de la vinculación de la cuenta de integración a la aplicación lógica y de que esta tiene ahora todos los artefactos de la cuenta de integración disponibles.

    ![Aplicación lógica vinculada a la cuenta de integración](./media/logic-apps-enterprise-integration-accounts/linkaccount-5.png)

Ahora que la cuenta de integración está vinculada a la aplicación lógica, puede usar los conectores de B2B en las aplicaciones lógicas. Algunos conectores de B2B comunes incluyen validación XML, y codificación y descodificación de archivos planos.  

## <a name="delete-your-integration-account"></a>Eliminación de la cuenta de integración

1. Seleccione **Más servicios**.

    ![Selección de "Más servicios"](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. En el cuadro de búsqueda, escriba "integración" como filtro. En la lista de resultados, seleccione **Cuentas de integración**.

    ![Filtre por "integración", seleccione "Cuentas de integración"](./media/logic-apps-enterprise-integration-accounts/account-2.png)  

3. Seleccione la cuenta de integración que desee eliminar.

    ![Selección de la cuenta de integración que se va a eliminar](./media/logic-apps-enterprise-integration-accounts/account-5.png)

4. En el menú, elija **Eliminar**.

    ![Selección de "Eliminar"](./media/logic-apps-enterprise-integration-accounts/delete.png)

5. Confirme que desea eliminar la cuenta de integración.

## <a name="move-your-integration-account"></a>Movimiento de la cuenta de integración

Para mover una cuenta de integración a otro grupo de recursos o suscripción de Azure, siga estos pasos.

> [!IMPORTANT]
> Después de mover una cuenta de integración tiene que actualizar todos los scripts para utilizar los nuevos identificadores de recurso.

1. Seleccione **Más servicios**.

    ![Selección de "Más servicios"](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. En el cuadro de búsqueda, escriba "integración" como filtro. En la lista de resultados, seleccione **Cuentas de integración**.

    ![Filtre por "integración", seleccione "Cuentas de integración"](./media/logic-apps-enterprise-integration-accounts/account-2.png)

3. Seleccione la cuenta de integración que desee mover. En **Configuración**, elija **Propiedades**.

    ![Seleccione de la cuenta de integración que va a mover. En Configuración, elija Propiedades.](./media/logic-apps-enterprise-integration-accounts/move.png)

5. Cambie el grupo de recursos o la suscripción de Azure asociados a la cuenta de integración.

    ![Elija Cambiar el grupo de recursos o Cambiar suscripción](./media/logic-apps-enterprise-integration-accounts/move-2.png)

## <a name="next-steps"></a>Pasos siguientes
* [Más información sobre los contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md "Información sobre los contratos de integración de empresas")  


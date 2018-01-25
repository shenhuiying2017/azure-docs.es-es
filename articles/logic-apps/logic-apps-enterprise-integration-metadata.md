---
title: "Administración de metadatos de artefactos de la cuenta de integración: Azure Logic Apps | Microsoft Docs"
description: "Agregue o recupere metadatos de artefactos de cuentas de integración para Azure Logic Apps"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 11/21/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 5eebae624ea024f2ff8c1fa4764027c05220a15f
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="manage-artifact-metadata-in-integration-accounts-for-logic-apps"></a>Administración de metadatos de artefactos en cuentas de integración para aplicaciones lógicas

Puede definir metadatos personalizados para artefactos en cuentas de integración y recuperar metadatos durante el tiempo de ejecución para su aplicación lógica. Por ejemplo, puede especificar metadatos para artefactos como asociados, acuerdos, esquemas y asignaciones: todos almacenan metadatos usando pares de clave-valor. Actualmente, los artefactos no pueden crear metadatos a través de la interfaz de usuario, pero puede usar API de REST para crear metadatos. Para agregar metadatos al crear o seleccionar un asociado, acuerdo o esquema en Azure Portal, elija **Editar**. Para recuperar metadatos de artefactos en aplicaciones lógicas, puede usar la característica	 Búsqueda de artefactos de la cuenta de integración.

## <a name="add-metadata-to-artifacts-in-integration-accounts"></a>Adición de metadatos a artefactos en cuentas de integración

1. Cree una [cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md).

2. Agregue un artefacto a su cuenta de integración, por ejemplo, un [asociado](logic-apps-enterprise-integration-partners.md#how-to-create-a-partner), un [acuerdo](logic-apps-enterprise-integration-agreements.md#how-to-create-agreements) o un [esquema](logic-apps-enterprise-integration-schemas.md).

3.  Seleccione el artefacto, elija **Editar** y escriba los detalles de los metadatos.

    ![Introducción de los metadatos](media/logic-apps-enterprise-integration-metadata/image1.png)

## <a name="retrieve-metadata-from-artifacts-for-logic-apps"></a>Recuperación de metadatos de artefactos para aplicaciones lógicas

1. Cree una [aplicación lógica](quickstart-create-first-logic-app-workflow.md).

2. Cree un [vínculo desde su aplicación lógica a su cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md#link-an-integration-account-to-a-logic-app). 

3. En Diseñador de aplicación lógica, agregue un desencadenador como *Solicitud* o *HTTP* a su aplicación lógica.

4.  Elija **Paso siguiente** > **Agregar una acción**. Busque una *integración* para poder encontrar y seleccionar **Búsqueda de artefactos de la cuenta de integración**.

    ![Seleccione Búsqueda de artefactos de la cuenta de integración.](media/logic-apps-enterprise-integration-metadata/image2.png)

5. Seleccione **Tipo de artefacto** y proporcione el **nombre del artefacto**.

    ![Seleccione el tipo de artefacto y proporcione el nombre del artefacto.](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="example-retrieve-partner-metadata"></a>Ejemplo: recuperación de metadatos de asociados

Los metadatos del asociado tienen estos detalles de `routingUrl`:

![Búsqueda de metadatos de "routingURL" de asociado](media/logic-apps-enterprise-integration-metadata/image6.png)

1. En su aplicación lógica, agregue su desencadenador, una acción **Cuenta de integración: Búsqueda de artefactos de la cuenta de integración** para su asociado y un **HTTP**.

    ![Adición de un desencadenador, una búsqueda de artefactos y un "HTTP" a su aplicación lógica](media/logic-apps-enterprise-integration-metadata/image4.png)

2. Para recuperar el identificador URI, vaya a la vista de código de la aplicación lógica. La definición de su aplicación lógica debería ser similar a la de este ejemplo:

    ![Buscar lookup](media/logic-apps-enterprise-integration-metadata/image5.png)


## <a name="next-steps"></a>pasos siguientes
* [Más información sobre los contratos](logic-apps-enterprise-integration-agreements.md "Información sobre los contratos de integración de empresas")  

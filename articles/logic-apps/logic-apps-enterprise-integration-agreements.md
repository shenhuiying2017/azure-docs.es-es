---
title: "Contratos de comunicación B2B: Azure Logic Apps | Microsoft Docs"
description: "Creación de contratos para que los asociados se puedan comunicar en escenarios B2B de Azure Logic Apps y Enterprise Integration Pack"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 447ffb8e-3e91-4403-872b-2f496495899d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2016
ms.author: LADocs
ms.openlocfilehash: 1f939c626be11a358b0f8bece8840992a4362e1f
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="partner-agreements-for-b2b-communication-with-azure-logic-apps-and-enterprise-integration-pack"></a>Contratos de asociados para la comunicación B2B con Azure Logic Apps y Enterprise Integration Pack

Los contratos son la piedra angular de las comunicaciones de negocio a negocio (B2B) que permiten a las entidades empresariales comunicarse perfectamente mediante protocolos estándar del sector. Al habilitar escenarios B2B para aplicaciones lógicas con Enterprise Integration Pack, un contrato es una disposición de las comunicaciones entre los socios comerciales B2B. Este contrato se basa en la comunicación que los asociados desean establecer y es específico del protocolo o transporte.

La integración de Enterprise admite estos estándares de protocolo o transporte:

* [AS2](logic-apps-enterprise-integration-as2.md)
* [X12](logic-apps-enterprise-integration-x12.md)
* [EDIFACT](logic-apps-enterprise-integration-edifact.md)

## <a name="why-use-agreements"></a>Por qué usar contratos

Estas son algunas de las ventajas más comunes de usar contratos:

* Permite que distintas organizaciones y empresas puedan intercambiar información en un formato conocido.
* Mejora la eficacia al realizar transacciones B2B
* Fácil de crearlos, administrarlos y utilizarlos al crear aplicaciones de Enterprise Integration

## <a name="how-to-create-agreements"></a>Creación de contratos

* [Creación de un contrato AS2](logic-apps-enterprise-integration-as2.md)
* [Creación de un contrato X12](logic-apps-enterprise-integration-x12.md)
* [Creación de un contrato EDIFACT](logic-apps-enterprise-integration-edifact.md)

## <a name="how-to-use-an-agreement"></a>Uso de contratos

Puede crear [aplicaciones lógicas](logic-apps-overview.md "Más información sobre Logic Apps") con funcionalidades de B2B mediante el uso de un contrato que hay creado.

## <a name="how-to-edit-an-agreement"></a>Edición de contratos

Puede editar cualquier acuerdo siguiendo estos pasos:

1. Seleccione la cuenta de integración que contiene el contrato que desea actualizar.

2. Seleccione el icono de **contratos**.

3. En la hoja **Contratos**, seleccione el contrato.

4. Elija **Editar**. Realice los cambios.

5. Elija **Aceptar** para guardar los cambios.

## <a name="how-to-delete-an-agreement"></a>Eliminación de una cuenta

Puede eliminar cualquier acuerdo siguiendo estos pasos:

1. Seleccione la cuenta de integración que contiene el contrato que desea eliminar.
2. Seleccione el icono de **contratos**.
3. En la hoja **Contratos**, seleccione el contrato.
4. Elija **Eliminar**.
5. Confirme que desea eliminar el contrato seleccionado.

    La hoja de contratos ya no muestra el contrato que se eliminó.

## <a name="next-steps"></a>pasos siguientes
* [Creación de un contrato AS2](logic-apps-enterprise-integration-as2.md)

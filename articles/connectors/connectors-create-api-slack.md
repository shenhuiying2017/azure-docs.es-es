---
title: Uso del conector de Slack en Azure Logic Apps | Microsoft Docs
description: "Conéctese a Slack en las aplicaciones lógicas"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 234cad64-b13d-4494-ae78-18b17119ba24
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: fc5fc128efe01bd0727e3ff30d8938918e89ac3a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-slack-connector"></a>Introducción al conector de Slack
Slack es una herramienta de comunicación de equipo, que reúne todas las comunicaciones del equipo en un solo lugar, inmediatamente localizables y disponibles dondequiera que vaya. 

Empiece por crear una aplicación lógica ahora. Para ello, consulte [Creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="create-a-connection-to-slack"></a>Creación de una conexión a Slack
Para usar el conector de Slack, cree primero una **conexión** y, después, especifique los detalles de las siguientes propiedades: 

| Propiedad | Obligatorio | Descripción |
| --- | --- | --- |
| Se necesita el cifrado de tokens |Sí |Proporcionar credenciales de Slack |

Siga estos pasos para iniciar sesión en Slack y completar la configuración de la **conexión** de Slack en la aplicación lógica:

1. Seleccione **Periodicidad**
2. Seleccione un valor para **Frequency** (Frecuencia) y especifique el correspondiente a **Interval** (Intervalo)
3. Seleccione **Add an action**(Agregar una acción)  
   ![Configurar Slack][1]  
4. Escriba Slack en el cuadro de búsqueda y espere a que la búsqueda devuelva todas las entradas que incluyan Slack en el nombre.
5. Seleccione **Slack - exponer mensaje**
6. Seleccione **Sign in to Slack** (Iniciar sesión en Slack):  
   ![Configurar Slack][2]
7. Especifique sus credenciales de Slack para iniciar sesión y autorizar la aplicación    
   ![Configurar Slack][3]  
8. Se le redirigirá a la página de inicio de sesión de su organización. **Autorice** la interacción de Slack con la aplicación lógica:      
   ![Configurar Slack][5] 
9. Una vez completada la autorización se le redirigirá a la aplicación lógica para terminar mediante la configuración de la sección **Slack - obtener todos los mensajes** . Agregue otros desencadenadores y acciones que necesite.  
   ![Configurar Slack][6]
10. Guarde el trabajo seleccionando **Guardar** en la barra de menús superior.

## <a name="connector-specific-details"></a>Detalles específicos del conector

Vea los desencadenadores y las acciones definidos en Swagger y vea también todos los límites en los [detalles del conector](/connectors/slack/).

## <a name="more-connectors"></a>Más conectores
Volver a la [lista de API](apis-list.md).

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png 
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png

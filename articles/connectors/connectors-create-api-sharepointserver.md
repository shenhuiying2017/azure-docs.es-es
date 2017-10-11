---
title: Uso del conector de SharePoint Server en Logic Apps | Microsoft Docs
description: "Introducción al uso del conector de SharePoint Server en Logic Apps"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 0238a060-d592-4719-b7a2-26064c437a1a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 0f3274816e279a1aa57febaa2f8294914900799a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-the-sharepoint-connector"></a>Introducción al conector de SharePoint
El conector de SharePoint ofrece una manera de trabajar con listas en SharePoint.

Para empezar, cree una aplicación lógica; consulte [Creación de su primer flujo de trabajo de aplicación lógica para automatizar los procesos entre aplicaciones de nube y servicios en la nube](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="create-a-connection-to-sharepoint"></a>Creación de una conexión a SharePoint
Para usar el conector de SharePoint, cree primero una **conexión** y, después, especifique los detalles de las siguientes propiedades: 

| Propiedad | Obligatorio | Descripción |
| --- | --- | --- |
| Se necesita el cifrado de tokens |Sí |Proporcionar credenciales de SharePoint |

Para poder establecer la conexión con **SharePoint**, especifique su identidad (nombre de usuario y contraseña, credenciales de tarjeta inteligente, etc.) a SharePoint. Después de autenticarse, podrá usar el conector de SharePoint en la aplicación lógica. 

Mientras se encuentra en el diseñador de la aplicación lógica, siga estos pasos para iniciar sesión en SharePoint y crear la **conexión** para su uso en la aplicación lógica:

1. Escriba SharePoint en el cuadro de búsqueda y espere a que la búsqueda devuelva todas las entradas que incluyan SharePoint en el nombre:   
   ![Configurar SharePoint][1]  
2. Seleccione **SharePoint - Cuando se crea un archivo**   
3. Seleccione **Sign in to SharePoint** (Iniciar sesión en SharePoint):   
   ![Configurar SharePoint][2]    
4. Proporcione sus credenciales de SharePoint para iniciar sesión para autenticarse con SharePoint    
   ![Configurar SharePoint][3]     
5. Una vez completada la autenticación se le redirigirá a la aplicación lógica para terminar mediante la configuración del diálogo de SharePoint **Cuando se crea un archivo** .          
   ![Configurar SharePoint][4]  
6. A continuación, puede agregar otros desencadenadores y acciones que necesita para completar la aplicación lógica.   
7. Guarde el trabajo seleccionando **Guardar** en la barra de menús superior.  

## <a name="connector-specific-details"></a>Detalles específicos del conector

Vea los desencadenadores y las acciones definidos en Swagger y vea también todos los límites en los [detalles del conector](/connectors/sharepoint/).

## <a name="more-connectors"></a>Más conectores
Volver a la [lista de API](apis-list.md).

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png  
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png

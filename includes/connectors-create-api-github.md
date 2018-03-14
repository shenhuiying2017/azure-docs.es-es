---
title: "archivo de inclusión"
description: "archivo de inclusión"
services: logic-apps
author: MandiOhlinger
ms.service: logic-apps
ms.topic: include
ms.date: 03/02/2018
ms.author: mandia
ms.custom: include file
ms.openlocfilehash: ec5b3ca9ccd139cbdf17768056eb1d835336e7a7
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2018
---
1. En [Azure Portal](https://portal.azure.com), cree una aplicación lógica en blanco. 

2. En el Diseñador de aplicaciones lógicas, escriba "github" como filtro. 

3. Seleccione el conector y el desencadenador de GitHub que desea usar.

   ![Selección del conector y el desencadenador de GitHub](./media/connectors-create-api-github/github-connector.png)

   > [!NOTE]
   > Todos los flujos de trabajo de aplicaciones lógicas deben comenzar con un desencadenador. Puede seleccionar acciones solo cuando el flujo de trabajo de lógica ya se inicia con un desencadenador. 

4. Si anteriormente no creó una conexión, elija **Iniciar sesión** para proporcionar sus credenciales de GitHub cuando se le soliciten.  

   ![Inicio de sesión con sus credenciales de GitHub](./media/connectors-create-api-github/github-connector-sign-in-credentials.png)

   La aplicación lógica usa estas credenciales para autorizar la conexión y el acceso a los datos a la cuenta de GitHub. 

5. Indique su nombre de usuario y contraseña de GitHub y luego confirme su autorización.

   ![Provisión de credenciales y confirmación de la autorización](./media/connectors-create-api-github/github-connector-authorize.png)   

   Ya se ha creado la conexión en Azure Portal y está lista para su uso.

6. Continúe definiendo el flujo de trabajo de aplicación lógica.

   ![Adición de más acciones al flujo de trabajo de la aplicación lógica](./media/connectors-create-api-github/github-connector-logic-app.png)


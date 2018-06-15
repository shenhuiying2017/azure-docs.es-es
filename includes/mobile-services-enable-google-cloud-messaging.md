---
title: archivo de inclusión
description: archivo de inclusión
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/06/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ae84b5fd5647ef6c54006c0411e334b14173392e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33836585"
---
1. Navegue a la [consola de Google Cloud](https://console.developers.google.com/cloud-resource-manager)e inicie sesión con las credenciales de su cuenta de Google+. 
2. Seleccione **Create Project** (Crear proyecto) en la barra de herramientas. 
   
    ![Creación de un proyecto](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)   
3. En **Project name** (Nombre del proyecto), escriba un nombre para su proyecto y haga clic en **Create** (Crear).
4. Seleccione el botón **Alerts** (Alertas) de la barra de herramientas y seleccione su proyecto de la lista. Verá el panel de su proyecto. También puede ir directamente al panel mediante la dirección URL: https://console.developers.google.com/home/dashboard?project=<YOUR PROJECT NAME>

    ![Selección del proyecto en las alertas](./media/mobile-services-enable-google-cloud-messaging/alert-new-project.png)
5. Anote el **número de proyecto** que se muestra en el icono **Project info** (Información del proyecto) del panel. 

    ![Identificador del proyecto](./media/mobile-services-enable-google-cloud-messaging/project-number.png)
6. En el icono **API** del panel, seleccione **Go to APIs overview** (Ir a información general de API). 

    ![Vínculo a información general de API](./media/mobile-services-enable-google-cloud-messaging/go-to-api-overview.png)
7. En la página **API**, seleccione **ENABLE APIS AND SERVICES** (HABILITAR API Y SERVICIOS). 

    ![Botón para habilitar API y servicios](./media/mobile-services-enable-google-cloud-messaging/enable-api-services-button.png)
8. Busque y seleccione **Google Cloud Messaging**. 

    ![Búsqueda y selección de Google Cloud Messaging](./media/mobile-services-enable-google-cloud-messaging/search-select-gcm.png)
9. Para habilitar Google Cloud Messaging en el proyecto, seleccione **ENABLE** (HABILITAR).

    ![Habilitación de Google Cloud Messaging](./media/mobile-services-enable-google-cloud-messaging/enable-gcm-button.png)
10. Seleccione **Create credentials** (Crear credenciales) en la barra de herramientas. 

    ![Botón para la creación de credenciales](./media/mobile-services-enable-google-cloud-messaging/create-credentials-button.png)
11. En la página **Add credentials to your project** (Agregar credenciales al proyecto), seleccione el vínculo **API key** (Clave de API). 

    ![Botón para la creación de credenciales](./media/mobile-services-enable-google-cloud-messaging/api-key-button.png)    
12. En la página **API key** (Clave de API), seleccione **Create/Save** (Crear o guardar). En el ejemplo siguiente, se selecciona la opción **IP addresses** (Direcciones IP) y se escribe **0.0.0.0/0** como direcciones IP permitidas. Debe restringir la clave de API de la manera adecuada. 

    ![Clave de API: botón de creación](./media/mobile-services-enable-google-cloud-messaging/api-key-create-button.png)
13. Copie la **clave de API** en el Portapapeles y guárdela en alguna parte. 

    ![Copia de la clave de API](./media/mobile-services-enable-google-cloud-messaging/copy-api-key.png)
   
    Usará este valor de clave de API para permitir que Azure lleve a cabo la autenticación con GCM y envíe notificaciones de inserción en nombre de su aplicación. Para volver al panel del proyecto, use la dirección URL: https://console.developers.google.com/home/dashboard?project=<YOUR PROJECT NAME>


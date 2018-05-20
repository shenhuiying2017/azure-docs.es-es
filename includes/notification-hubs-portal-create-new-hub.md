---
title: archivo de inclusión
description: archivo de inclusión
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/28/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c718fa972b9d6cba13a13edd3c7e8df29fa63207
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
1. Inicie sesión en el [Azure Portal](https://portal.azure.com).

2. Seleccione **Crear un recurso** > **Web y móvil** > **Centro de notificaciones**.
   
      ![Azure Portal: creación de centros de notificaciones](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-create.png)
      
3. En el cuadro **Centro de notificaciones**, escriba un nombre único. Seleccione el elemento **Región**, **Suscripción** y **Grupo de recursos** (si dispone ya de uno). 
   
      Si aún no tiene un espacio de nombres de Service Bus, puede usar el predeterminado, que se crea en función del nombre del centro (si está disponible el espacio de nombres).
    
      Si ya tiene un espacio de nombres de Service Bus donde crear el centro, siga estos pasos:

    a. En el área **Espacio de nombres**, seleccione el vínculo **Seleccionar uno existente**. 
   
    b. Seleccione **Crear**.
   
      ![Azure Portal: establecimiento de las propiedades del centro de notificaciones](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

4. Seleccione **Notificaciones** (icono de campana) y, luego, **Go to resource** (Ir al recurso). 

      ![Azure Portal: notificaciones -> Go to resource (Ir al recurso)](./media/notification-hubs-portal-create-new-hub/notification-go-to-resource.png)    
5. Seleccione **Directivas de acceso** en la lista. Anote las dos cadenas de conexión disponibles. Las necesitará para gestionar las notificaciones push más tarde.

      >[!IMPORTANT]
      >**NO** use la clave DefaultFullSharedAccessSignature en su aplicación. Está pensada para usarse solamente en el back-end.
      >
   
      ![Azure Portal: cadenas de conexión del centro de notificaciones](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)


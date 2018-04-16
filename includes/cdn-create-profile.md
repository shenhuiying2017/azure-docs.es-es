---
title: archivo de inclusión
description: archivo de inclusión
services: cdn
author: dksimpson
ms.service: cdn
ms.topic: include
ms.date: 04/04/2018
ms.author: rli; v-deasim
ms.custom: include file
ms.openlocfilehash: 692364e9b2e78b3bd1f63137148dfbc680364737
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2018
---
## <a name="create-a-new-cdn-profile"></a>Crear un nuevo perfil de CDN

Un perfil de CDN es un contenedor para los puntos de conexión de CDN y especifica un plan de tarifa.

1. En Azure Portal, en la parte superior izquierda, seleccione **Crear un recurso**.
    
    Aparece el panel **Nuevos**.
   
2. Seleccione **Web y móvil**, y después **CDN**.
   
    ![Seleccionar recurso de CDN](./media/cdn-create-profile/cdn-new-resource.png)

    Aparece el panel **Perfil de CDN**.

    Use la configuración especificada en la tabla que aparece después de la imagen.
   
    ![Nuevo perfil de CDN](./media/cdn-create-profile/cdn-new-profile.png)

    | Configuración  | Valor |
    | -------- | ----- |
    | **Name** | Escriba *my-cdn-profile-123* como nombre de perfil. Este nombre debe ser único globalmente; si ya está en uso, puede especificar otro diferente. |
    | **Suscripción** | Seleccione una suscripción de Azure en la lista desplegable.|
    | **Grupos de recursos** | Seleccione **Crear nuevo** y escriba *my-resource-group-123* y un nombre para el nuevo grupo de recursos. Este nombre debe ser único globalmente; si ya está en uso, puede especificar otro diferente. | 
    | **Ubicación del grupo de recursos** | Seleccione **Centro de EE. UU.** en la lista desplegable. |
    | **Plan de tarifa** | Seleccione **Verizon estándar** en la lista desplegable. |
    | **Crear un punto de conexión de CDN ahora** | Déjelo sin seleccionar. |  
   
3. Seleccione **Anclar al panel** para guardar el perfil en el panel después de crearlo.
    
4. Seleccione **Crear** para crear el perfil. 


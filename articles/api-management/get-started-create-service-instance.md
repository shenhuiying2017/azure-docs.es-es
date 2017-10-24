---
title: "Creación de una instancia de Azure API Management | Microsoft Docs"
description: Siga los pasos de este tutorial para crear una nueva instancia de API Management.
services: api-management
documentationcenter: 
author: vladvino
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/17/2017
ms.author: apimpm
ms.openlocfilehash: 6433ea1f0eb6ad375402b998b4dfa80bded35c4b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-new-azure-api-management-service-instance"></a>Creación de una nueva instancia del servicio Azure API Management

En este tutorial, se describen los pasos que deben seguirse para crear una nueva instancia de API Management mediante [Azure Portal](https://portal.azure.com/).

## <a name="prerequisites"></a>Requisitos previos

+ Una suscripción de Azure activa.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-new-service"></a>Creación de un nuevo servicio

1. En [Azure Portal](https://portal.azure.com/), seleccione **Nuevo** > **Enterprise Integration** > **API Management**.

    Si lo desea, también puede elegir **nuevo**, escribir `API management` en el cuadro de búsqueda y presionar Intro. Haga clic en **Crear**.

2. En la ventana **Servicio API Management**, especifique un **nombre** único para el servicio API Management. Este nombre no se podrá modificar más adelante.

    > [!TIP]
    > El nombre del servicio se usa para generar un nombre de dominio predeterminado con el formato *{nombre}.azure-api.net.* Si desea utilizar un nombre de dominio personalizado, consulte [Configure a custom domain](configure-custom-domain.md) (Configuración de un dominio personalizado). <br/>
    > El nombre del servicio se utiliza para hacer referencia al servicio y al recurso de Azure correspondiente.

5. Seleccione una **suscripción** entre las diferentes suscripciones de Azure a las que tiene acceso.
6. En **Grupo de recursos**seleccione el recurso nuevo o uno ya existente.  Un grupo de recursos es una colección de recursos que comparten ciclos de vida, permisos y directivas. Obtenga más información [aquí](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. En **Ubicación**, seleccione la región geográfica en la que se va a crear el servicio API Management. En el cuadro de lista desplegable, solo aparecerán las regiones del servicio API Management. 
9. Especifique un nombre en **Nombre de la organización**. Este nombre se va a utilizar en varios lugares; por ejemplo, como el nombre del portal del desarrollador y como el remitente de los correos electrónicos de notificación.
10. En **Correo electrónico del administrador**, especifique la dirección de correo electrónico a la que se van a enviar todas las notificaciones de **API Management**.
11. En **Plan de tarifa**, especifique el nivel **Desarrollador** para evaluar el servicio. Este nivel no puede utilizarse en producción. Para más información sobre el escalado de los niveles de API Management, consulte [Actualización y escalado](upgrade-and-scale.md).
12. Seleccione **Create**.

    > [!TIP]
    > Normalmente, se tarda entre 20 y 30 minutos en crear el servicio API Management. Si selecciona **Anclar al panel**, podrá encontrar el servicio que acaba de crear con más facilidad.

## <a name="next-steps"></a>Pasos siguientes

[Publish an API with Azure API Management](#api-management-getstarted-publish-api.md) (Publicación de una API con Azure API Management)
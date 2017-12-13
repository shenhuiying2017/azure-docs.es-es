---
title: "Creación de una instancia de Azure API Management | Microsoft Docs"
description: Siga los pasos de este tutorial para crear una nueva instancia de API Management.
services: api-management
documentationcenter: 
author: juliako
manager: cflower
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: b7ea43419cbbb5f5d38f8ad721d564650c3681a6
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2017
---
# <a name="create-a-new-azure-api-management-service-instance"></a>Creación de una nueva instancia del servicio Azure API Management

Azure API Management (APIM) ayuda a las organizaciones a publicar API para desarrolladores externos, asociados e internos a fin de desbloquear el potencial de sus datos y servicios. API Management proporciona las competencias esenciales para garantizar un programa de API de éxito mediante compromisos con desarrolladores, información detallada empresarial, análisis, seguridad y protección. APIM le permite crear y administrar modernas puertas de enlace de API para los servicios back-end existentes hospedados en cualquier lugar. Para más información, consulte el tema de [introducción](api-management-key-concepts.md).

Esta guía de inicio rápido describe los pasos que deben seguirse para crear una nueva instancia de API Management mediante Azure Portal.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

![nueva instancia](./media/get-started-create-service-instance/get-started-create-service-instance-created.png)

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en Azure Portal: http://portal.azure.com/.

## <a name="create-a-new-service"></a>Creación de un nuevo servicio

1. En [Azure Portal](https://portal.azure.com/), seleccione **Nuevo** > **Enterprise Integration** > **API Management**.

    Si lo desea, también puede elegir **nuevo**, escribir `API management` en el cuadro de búsqueda y presionar Intro. Haga clic en **Crear**.

2. En la ventana **servicio API Management**, escriba los valores.

    ![nueva instancia](./media/get-started-create-service-instance/get-started-create-service-instance-create-new.png)

    | Configuración      | Valor sugerido  | Descripción              |
    | ------------ |  ------- | ---------------------------------|
    |**Name**|Un nombre único para el servicio API Management| Este nombre no se podrá modificar más adelante. El nombre del servicio se usa para generar un nombre de dominio predeterminado con el formato *{nombre}.azure-api.net.* Si desea utilizar un nombre de dominio personalizado, consulte [Configure a custom domain](configure-custom-domain.md) (Configuración de un dominio personalizado). <br/> El nombre del servicio se utiliza para hacer referencia al servicio y al recurso de Azure correspondiente.|
    |**Suscripción**|Su suscripción | La suscripción en que se creará esta nueva instancia de servicio. Seleccione una suscripción entre las diferentes suscripciones de Azure a las que tiene acceso.|
    |**Grupo de recursos**|*apimResourceGroup*|Seleccione un nuevo recurso o uno ya existente. Un grupo de recursos es una colección de recursos que comparten ciclos de vida, permisos y directivas. Obtenga más información [aquí](../azure-resource-manager/resource-group-overview.md#resource-groups).|
    |**Ubicación**|*Oeste de EE. UU.*|Seleccione la región geográfica más próxima. En el cuadro de lista desplegable, solo aparecerán las regiones del servicio API Management. |
    |**Nombre de la organización**|El nombre de su organización|Este nombre se usa en varios lugares, incluido el título del portal para desarrolladores y el remitente de correos electrónicos de notificación.|
    |**Correo electrónico del administrador**|*admin@org.com*|Especifique la dirección de correo electrónico a la que se enviarán todas las notificaciones de **API Management**.|
    |**Plan de tarifa**|*Developer*|Especifique el nivel de **Desarrollador** para evaluar el servicio. Este nivel no puede utilizarse en producción. Para más información sobre el escalado de los niveles de API Management, consulte [Actualización y escalado](upgrade-and-scale.md).|
3. Seleccione **Create**.

    > [!TIP]
    > Normalmente, se tarda entre 20 y 30 minutos en crear el servicio API Management. Si selecciona **Anclar al panel**, podrá encontrar el servicio que acaba de crear con más facilidad.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no los necesite, puede quitar el grupo de recursos y todos los recursos relacionados siguiendo los pasos a continuación:


1. En Azure Portal, seleccione ![flecha](./media/get-started-create-service-instance/arrow.png).
2. Seleccione **Grupos de recursos**.
3. Encuentre el grupo de recursos.
4. Haga clic en ". . ." y elimine el grupo.

![cleanup](./media/get-started-create-service-instance/cleanup.png)

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Importación y publicación de la primera API](import-and-publish.md)

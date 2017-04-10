---
title: "Conexión de una aplicación web a una instancia de Content Delivery Network | Microsoft Docs"
description: "Conexión de una aplicación web a una instancia de Content Delivery Network para entregar los archivos estáticos desde nodos perimetrales."
services: app-service
author: syntaxc4
ms.author: cfowler
ms.date: 04/03/2017
ms.topic: hero-article
ms.service: app-service-web
manager: erikre
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: d616ca49da7a909a1a104409c5845f39c4a5377a
ms.lasthandoff: 04/04/2017

---
# <a name="connect-a-web-app-to-a-content-delivery-network"></a>Conexión de una aplicación web a una instancia de Content Delivery Network

En este tutorial va a crear un perfil y un punto de conexión de la red CDN de Azure para ofrecer los archivos estáticos desde la aplicación web a través de las ubicaciones POP de Azure CDN.

> [!TIP]
> Revise la actualización a la lista de fechas de [Ubicaciones POP de la red CDN de Azure](https://docs.microsoft.com/en-us/azure/cdn/cdn-pop-locations).
>

## <a name="step-1---login-to-azure-portal"></a>Paso 1: Inicie sesión en Azure Portal

En primer lugar, abra su explorador preferido y vaya a Azure [Portal](https://portal.azure.com).

## <a name="step-2---create-a-cdn-profile"></a>Paso 2: Cree un nuevo perfil de CDN

Haga clic en el botón `+ New` situado en el panel de navegación izquierdo y haga clic en **Web y móvil**. En la categoría Web y móvil, seleccione **CDN**.

Especifique el **Nombre**, la **Ubicación**, el **Grupo de recursos** y el **Plan de tarifa** y luego haga clic en **Crear**.

Abra el centro de grupos de recursos de la barra de navegación izquierda y seleccione **myResourceGroup**. En la lista de recursos, seleccione **myCDNProfile**.

![azure-cdn-profile-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-profile-created.png)

## <a name="step-3---create-a-cdn-endpoint"></a>Paso 3: Cree un punto de conexión de CDN

Haga clic en `+ Endpoint` en los comandos situados junto al cuadro de búsqueda y se iniciará la hoja de creación del punto de conexión.

Especifique el **Nombre**, **Tipo de origen**, **Nombre de host de origen** y, a continuación, haga clic en **Agregar**.

Se creará el punto de conexión. Una vez creado el punto de conexión de la instancia de Content Delivery Network, se actualizará el estado a **En ejecución**.

![azure-cdn-endpoint-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-endpoint-created.png)

## <a name="step-4---leveraging-cdn"></a>Paso 4: Aprovechamiento de la instancia de CDN

Ahora que se está ejecutando el punto de conexión, vamos a comprobar que el contenido está disponible en el servidor POP navegando hasta un archivo estático del servidor web y cambiando, a continuación, el nombre de host de `http://<app_name>.azurewebsites.net/path/to-static-file` a `http://<endpoint_name>.azureedge.net/path/to-static-file`.

![app-service-web-url-to-cdn-endpoint-url](media/app-service-web-tutorial-content-delivery-network/app-service-web-url-to-cdn-endpoint-url.png)

## <a name="next-steps"></a>Pasos siguientes



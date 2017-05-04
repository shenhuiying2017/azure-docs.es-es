---
title: "Adición de una instancia de Content Delivery Network en Azure App Service | Microsoft Docs"
description: "Agregue una instancia de Content Delivery Network en Azure App Service para entregar los archivos estáticos desde nodos perimetrales."
services: app-service
author: syntaxc4
ms.author: cfowler
ms.date: 04/03/2017
ms.topic: hero-article
ms.service: app-service-web
manager: erikre
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 7ba3737566401152a3171e8926beca188045230c
ms.lasthandoff: 04/22/2017

---
# <a name="add-a-content-deliver-network-on-an-azure-app-service"></a>Adición de una instancia de Content Delivery Network en Azure App Service

En este tutorial, agregará una instancia de Content Delivery Network (CDN) a Azure App Service para exponer el contenido estático en un servidor perimetral. Va a crear un perfil de CDN, que es una colección de hasta 10 puntos de conexión de red CDN.

La instancia de Content Delivery Network almacena en caché contenido web estático en ubicaciones colocadas estratégicamente para proporcionar el máximo rendimiento a la hora de proporcionar contenido a los usuarios. Entre las ventajas de utilizar la red CDN para almacenar en memoria caché los recursos de sitios web se incluyen:

* Mejor rendimiento y experiencia del usuario para los usuarios finales sobre todo a la hora de utilizar aplicaciones donde son necesarios varios recorridos de ida y vuelta para cargar el contenido
* Gran escalado para mejorar la administración de cargas instantáneas pesadas, por ejemplo, al comienzo de un evento de lanzamiento de un producto.
* Al distribuir solicitudes de usuarios y ofrecer contenido desde servidores perimetrales, se envía menos tráfico al origen.

> [!TIP]
> Revise la lista actualizada de [Ubicaciones POP de la red CDN de Azure](https://docs.microsoft.com/en-us/azure/cdn/cdn-pop-locations).
>

## <a name="deploy-the-sample"></a>Implementación del ejemplo

Para completar este tutorial, necesitará una aplicación implementada en la aplicación web. Siga la [guía de inicio rápido de HTML estático](app-service-web-get-started-html.md) para tener una base para este tutorial.

## <a name="step-1---login-to-azure-portal"></a>Paso 1: Inicie sesión en Azure Portal

En primer lugar, abra su explorador preferido y vaya a Azure [Portal](https://portal.azure.com).

## <a name="step-2---create-a-cdn-profile"></a>Paso 2: Cree un nuevo perfil de CDN

Haga clic en el botón `+ New` situado en el panel de navegación izquierdo y haga clic en **Web y móvil**. En la categoría Web y móvil, seleccione **CDN**.

Especifique los siguientes campos:

| Campo | Valor de ejemplo | Descripción |
|---|---|---|
| Nombre | myCDNProfile | Un nombre para el perfil de CDN. |
| Ubicación | Europa occidental | Esta es la ubicación de Azure en la que se almacenará la información de su perfil de red CDN. No tiene ningún impacto en las ubicaciones de puntos de conexión de CDN. |
| Grupos de recursos | myResourceGroup | Para más información sobre los grupos de recursos, consulte [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups). |
| Plan de tarifa  | Estándar de Akamai | Consulte en [Información general de la red de entrega de contenido (CDN) de Azure](../cdn/cdn-overview.md#azure-cdn-features) una comparación de los planes de tarifa. |

Haga clic en **Crear**.

Abra el centro de grupos de recursos de la barra de navegación izquierda y seleccione **myResourceGroup**. En la lista de recursos, seleccione **myCDNProfile**.

![azure-cdn-profile-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-profile-created.png)

## <a name="step-3---create-a-cdn-endpoint"></a>Paso 3: Cree un punto de conexión de CDN

Haga clic en **+ Punto de conexión** en los comandos situados junto al cuadro de búsqueda y se iniciará la hoja de creación del punto de conexión.

Especifique los siguientes campos:

| Campo | Valor de ejemplo | Descripción |
|---|---|
| Nombre |  | Este nombre se usará para obtener acceso a sus recursos almacenados en caché en el dominio `<endpointname>.azureedge.net`. |
| Tipo de origen | Aplicación web | Al seleccionar un tipo de origen proporciona menús contextuales para los campos restantes. Seleccionar un origen personalizado le proporcionará un campo de texto para el nombre de host de origen. |
| Nombre de host de origen | |  En la lista desplegable se muestran todos los orígenes disponibles del tipo que especificó. Si seleccionó Origen personalizado como su Tipo de origen, tendrá que escribir el dominio de su origen personalizado.  |

Haga clic en **Agregar**.

Se creará el punto de conexión. Una vez creado el punto de conexión de la instancia de Content Delivery Network, se actualizará el estado a **En ejecución**.

![azure-cdn-endpoint-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-endpoint-created.png)

## <a name="step-4---serve-from-azure-cdn"></a>Paso 4: Atienda las solicitudes desde la red CDN de Azure

Ahora que el punto de conexión de la red CDN se está **ejecutando**, podrá acceder al contenido desde el punto de conexión de esta.

Teniendo en cuenta que hemos usado la [guía de inicio rápido de HTML estático](app-service-web-get-started-html.md) como punto de partida para este tutorial, debemos tener las siguientes carpetas disponibles en la red CDN: `css`, `img`, `js`.

Las rutas de acceso de contenido entre la dirección URL de la aplicación web `http://<app_name>.azurewebsites.net/img/` y la dirección URL del punto de conexión de la red CDN `http://<endpointname>.azureedge.net/img/` son las mismas. Esto significa simplemente que puede sustituir el dominio del punto de conexión de la red CDN de cualquier contenido estático para poder atender las solicitudes desde la red CDN.

Vamos a extraer la primera imagen del punto de conexión de la red CDN. Para ello, vaya a la siguiente dirección URL en el explorador web que prefiera:

```bash
http://<endpointname>.azureedge.net/img/03-enterprise.png
```

Ahora que el contenido estático está disponible en la red CDN, puede actualizar la aplicación para que use el punto de conexión de la red CDN para entregar el contenido al usuario final.

En función del lenguaje en el que se haya compilado el sitio web, puede haber muchos marcos de trabajo para ayudar con la reserva de la red CDN. Por ejemplo, ASP.NET proporciona compatibilidad con la [unión y minificación](https://docs.microsoft.com/en-us/aspnet/mvc/overview/performance/bundling-and-minification#using-a-cdn) lo cual habilita también funcionalidades de reserva de red CDN.

Si su lenguaje no tiene integrada una biblioteca de compatibilidad de reserva de red CDN, puede usar un marco de javascript como [FallbackJS](http://fallback.io/), que admite la carga de [scripts](https://github.com/dolox/fallback/tree/master/examples/loading-scripts), [hojas de estilos](https://github.com/dolox/fallback/tree/master/examples/loading-stylesheets) e [imágenes](https://github.com/dolox/fallback/tree/master/examples/loading-images).

## <a name="step-5---purge-the-cdn"></a>Paso 5: Purge la red CDN

A veces es necesario forzar una purga de la red CDN, si desea que expire el contenido antes de que termine el período de vida (TTL).

Es posible purgar manualmente la red CDN de Azure, desde la hoja Perfil de CDN o la hoja Punto de conexión de CDN. Si decide purgar desde la página de perfil, será necesario seleccionar qué punto de conexión desea purgar.

Para purgar el contenido, escriba las rutas de acceso al contenido que desea purgar. Puede pasar una ruta de acceso completa del archivo para purgar un archivo individual o un segmento de ruta de acceso para purgar y actualizar el contenido de una carpeta particular.

Una vez que haya proporcionado todas las rutas de acceso de contenido que desea purgar, haga clic en **Purgar**.

![app-service-web-purge-cdn](media/app-service-web-tutorial-content-delivery-network/app-service-web-purge-cdn.png)

## <a name="step-6---map-a-custom-domain"></a>Paso 6: Asigne un dominio personalizado

Asignar un dominio personalizado al punto de conexión de la red CDN proporciona un dominio uniforme para la aplicación web.

Para asignar un dominio personalizado al punto de conexión de la red CDN, cree un registro CNAME con el registrador de dominio.

> [!NOTE]
> Un registro CNAME es una característica DNS que asigna un dominio de origen, como `www.contosocdn.com` o `static.contosocdn.com`, a un dominio de destino.

En este caso, vamos a agregar un dominio de origen `static.contosocdn.com` que señalará al dominio de destino que está en el punto de conexión de la red CDN.

| dominio de origen | dominio de destino |
|---|---|
| static.contosocdn.com | &lt;endpointname&gt;.azureedge.net |

En la hoja de información general sobre el punto de conexión de la red CDN, haga clic en el botón `+ Custom domain`.

En la hoja Agregar un dominio personalizado, escriba su dominio personalizado, incluido el subdominio, en el cuadro de diálogo. Por ejemplo, escriba el nombre del dominio con el formato `static.contosocdn.com`.

Haga clic en **Agregar**.

## <a name="step-7---version-content"></a>Paso 7: Contenido de la versión

En el panel de navegación izquierdo del punto de conexión de la red CDN, seleccione **Caché** en el encabezado de configuración.

La hoja **Caché** le permite configurar la forma en que la red CDN controla las cadenas de consulta de la solicitud.

> [!NOTE]
> Para obtener una descripción de las opciones de comportamiento de almacenamiento en caché de la cadena de consulta, lea el tema [Control del comportamiento del almacenamiento en caché de la red CDN de Azure con cadenas de consulta](../cdn/cdn-query-string.md).

Seleccione **Almacenar en caché todas las URL únicas** en la lista desplegable para ver el comportamiento de almacenamiento en caché de la cadena de consulta.

Haga clic en **guardar**.

## <a name="next-steps"></a>Pasos siguientes

* [¿Qué es la red CDN de Azure?](../best-practices-cdn.md?toc=%2fazure%2fcdn%2ftoc.json)
* [Habilitación de HTTPS en un dominio personalizado de la red CDN de Azure](../cdn/cdn-custom-ssl.md)
* [Mejora del rendimiento comprimiendo archivos en la red CDN de Azure](../cdn/cdn-improve-performance.md)
* [Carga previa de activos en un punto de conexión de CDN de Azure](../cdn/cdn-preload-endpoint.md)


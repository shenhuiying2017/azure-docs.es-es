---
title: "Adición de una red CDN a una instancia de Azure App Service | Microsoft Docs"
description: "Agregue una red de entrega de contenido a Azure App Service para almacenar en memoria caché y entregar archivos estáticos desde servidores próximos a sus clientes en todo el mundo."
services: app-service\web
author: syntaxc4
ms.author: cfowler
ms.date: 05/31/2017
ms.topic: tutorial
ms.service: app-service-web
manager: erikre
ms.workload: web
ms.custom: mvc
ms.openlocfilehash: 257b75d01f3904661c1a188a2d53ffcb74f48f06
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="add-a-content-delivery-network-cdn-to-an-azure-app-service"></a>Incorporación de una red de entrega de contenido a Azure App Service

[Azure Content Delivery Network](../cdn/cdn-overview.md) almacena en caché contenido web estático en ubicaciones colocadas estratégicamente para obtener el máximo rendimiento a la hora de proporcionar contenido a los usuarios. CDN también reduce la carga del servidor en la aplicación web. Este tutorial muestra cómo agregar Azure CDN a una [aplicación web de Azure App Service](app-service-web-overview.md). 

Esta es la página de inicio del ejemplo del sitio HTML estático que se utilizará:

![Página de inicio de la aplicación de ejemplo](media/app-service-web-tutorial-content-delivery-network/sample-app-home-page.png)

Temas que se abordarán:

> [!div class="checklist"]
> * Crear un punto de conexión de CDN.
> * Actualizar los recursos en caché.
> * Utilizar cadenas de consulta para controlar las versiones en caché.
> * Utilizar un dominio personalizado para el punto de conexión de CDN.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial:

- [Instalación de Git](https://git-scm.com/)
- [Instalación de la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-web-app"></a>Creación de la aplicación web

Para crear la aplicación web con la que va a trabajar, siga el [tutorial de inicio rápido de HTML estático](app-service-web-get-started-html.md) mediante el paso de **búsqueda de la aplicación**.

### <a name="have-a-custom-domain-ready"></a>Tenga listo un dominio personalizado

Para realizar el paso de dominio personalizado de este tutorial, debe tener un dominio personalizado y tener acceso al Registro DNS del proveedor de dominios (por ejemplo, GoDaddy). Por ejemplo, para agregar entradas DNS para `contoso.com` y `www.contoso.com`, debe tener acceso para configurar las opciones de configuración DNS del dominio raíz `contoso.com`.

Si aún no tiene un nombre de dominio, puede seguir el tutorial [Comprar y configurar un nombre de dominio personalizado en Azure App Service](custom-dns-web-site-buydomains-web-app.md) para comprar un dominio mediante Azure Portal. 

## <a name="log-in-to-the-azure-portal"></a>Iniciar sesión en el portal de Azure

Abra un explorador y vaya a [Azure Portal](https://portal.azure.com).

## <a name="create-a-cdn-profile-and-endpoint"></a>Creación de un perfil y un punto de conexión de CDN

En el panel de navegación izquierdo, seleccione **App Services** y, a continuación, seleccione la aplicación que creó en el tutorial [Creación de una aplicación web HTML estática en Azure en cinco minutos](app-service-web-get-started-html.md).

![Selección de la aplicación de App Service en el portal](media/app-service-web-tutorial-content-delivery-network/portal-select-app-services.png)

En la página **App Service**, en la sección **Configuración**, seleccione **Redes > Configurar Azure CDN para la aplicación**.

![Selección de la instancia de CDN en el portal](media/app-service-web-tutorial-content-delivery-network/portal-select-cdn.png)

En la página **Azure Content Delivery Network**, proporcione la configuración del **Nuevo punto de conexión** tal y como se especifica en la tabla.

![Creación del perfil y el punto de conexión en el portal](media/app-service-web-tutorial-content-delivery-network/portal-new-endpoint.png)

| Configuración | Valor sugerido | Descripción |
| ------- | --------------- | ----------- |
| **Perfil de CDN** | myCDNProfile | Seleccione **Crear nuevo** para crear un nuevo perfil de red CDN. Un perfil de CDN es una colección de puntos de conexión de CDN con el mismo plan de tarifa. |
| **Plan de tarifa** | Estándar de Akamai | El [plan de tarifa](../cdn/cdn-overview.md#azure-cdn-features) especifica el proveedor y las características disponibles. En este tutorial, usamos Akamai estándar. |
| **Nombre del punto de conexión de CDN** | Cualquier nombre que sea único en el dominio azureedge.net | Accederá a los recursos en caché en el dominio  *\<nombrePuntoConexión>.azureedge.net*.

Seleccione **Crear**.

Azure crea el perfil y el punto de conexión. El nuevo punto de conexión aparece en la lista **Puntos de conexión** de la misma página y, una vez aprovisionado, el estado es **En ejecución**.

![Nuevo punto de conexión en la lista](media/app-service-web-tutorial-content-delivery-network/portal-new-endpoint-in-list.png)

### <a name="test-the-cdn-endpoint"></a>Probar el punto de conexión de CDN

Si ha seleccionado el plan de tarifa de Verizon, la propagación del punto de conexión suele tardar unos 90 minutos. En Akamai, la propagación tarda unos minutos

La aplicación de ejemplo tiene un archivo `index.html` y las carpetas *css*, *img* y *js*, que contienen otros recursos estáticos. Las rutas de acceso de contenido para todos estos archivos son las mismas en el punto de conexión de CDN. Por ejemplo, las siguientes direcciones URL acceden ambas al archivo *bootstrap.css* en la carpeta *css*:

```
http://<appname>.azurewebsites.net/css/bootstrap.css
```

```
http://<endpointname>.azureedge.net/css/bootstrap.css
```

Mediante un explorador, vaya la dirección URL siguiente:

```
http://<endpointname>.azureedge.net/index.html
```

![Página de inicio de la aplicación de ejemplo servida desde la red CDN](media/app-service-web-tutorial-content-delivery-network/sample-app-home-page-cdn.png)

 Verá la misma página que ejecutó anteriormente en una aplicación web de Azure. Azure CDN ha recuperado los recursos de la aplicación web original y los sirve desde el punto de conexión de CDN.

Para asegurarse de que esta página se almacena en caché en la red CDN, actualice la página. En ocasiones se necesitan dos solicitudes del mismo recurso para que la red CDN almacene en memoria caché el contenido solicitado.

Para más información acerca de cómo crear perfiles y puntos de conexión de Azure CDN, consulte [Introducción a Azure CDN](../cdn/cdn-create-new-endpoint.md).

## <a name="purge-the-cdn"></a>Purga de la red CDN

La red CDN actualiza periódicamente los recursos desde la aplicación web original en función de la configuración del período de vida (TTL). El TTL predeterminado es siete días.

En ocasiones podría necesitar actualizar la red CDN antes de la expiración del TTL; por ejemplo, al implementar contenido actualizado en la aplicación web. Para desencadenar una actualización, puede purgar manualmente los recursos de la red CDN. 

En esta sección del tutorial, se implementará un cambio en la aplicación web y se purgará la red CDN para desencadenar la actualización de su caché.

### <a name="deploy-a-change-to-the-web-app"></a>Implementación de cambios en la aplicación web

Abra el archivo `index.html` y agregue "- V2" al encabezado H1, como se muestra en el ejemplo siguiente: 

```
<h1>Azure App Service - Sample Static HTML Site - V2</h1>
```

Confirme el cambio e impleméntelo en la aplicación web.

```bash
git commit -am "version 2"
git push azure master
```

Una vez finalizada la implementación, vaya a la dirección URL de la aplicación web y podrá ver el cambio.

```
http://<appname>.azurewebsites.net/index.html
```

![V2 en el título de la aplicación web](media/app-service-web-tutorial-content-delivery-network/v2-in-web-app-title.png)

Vaya a la dirección URL de la página de inicio del punto de conexión de CDN y no verá el cambio porque la versión almacenada en caché en la red CDN no ha expirado todavía. 

```
http://<endpointname>.azureedge.net/index.html
```

![Sin título V2 en la red CDN](media/app-service-web-tutorial-content-delivery-network/no-v2-in-cdn-title.png)

### <a name="purge-the-cdn-in-the-portal"></a>Purga de la red CDN en el portal

Para hacer que la red CDN actualice la versión almacenada en caché, purgue la red CDN.

En el área de navegación izquierda del portal, seleccione **Grupos de recursos** y, a continuación, seleccione el grupo de recursos que ha creado para la aplicación web (myResourceGroup).

![Selección de un grupo de recursos](media/app-service-web-tutorial-content-delivery-network/portal-select-group.png)

En la lista de recursos, seleccione el punto de conexión de CDN.

![Selección del punto de conexión](media/app-service-web-tutorial-content-delivery-network/portal-select-endpoint.png)

En la parte superior de la página **Punto de conexión**, haga clic en **Purgar**.

![Seleccionar Purgar](media/app-service-web-tutorial-content-delivery-network/portal-select-purge.png)

Escriba las rutas de acceso al contenido que desea purgar. Puede pasar una ruta de acceso de archivo completa para purgar un archivo individual, o un segmento de ruta de acceso para purgar y actualizar todo el contenido de una carpeta. Puesto que ha cambiado `index.html`, asegúrese de que es una de las rutas de acceso.

En la parte inferior de la página, seleccione **Purgar**.

![Página Purgar](media/app-service-web-tutorial-content-delivery-network/app-service-web-purge-cdn.png)

### <a name="verify-that-the-cdn-is-updated"></a>Compruebe que se ha actualizado la red CDN

Espere hasta que la solicitud de purga finalice el procesamiento; por lo general, un par de minutos. Para ver el estado actual, seleccione el icono de campana en la parte superior de la página. 

![Notificación de purga](media/app-service-web-tutorial-content-delivery-network/portal-purge-notification.png)

Vaya a la dirección URL del punto de conexión de CDN de `index.html` y ahora verá el texto V2 que agregó al título de la página principal. Esto muestra que se ha actualizado la caché de la red CDN.

```
http://<endpointname>.azureedge.net/index.html
```

![V2 en el título en la red CDN](media/app-service-web-tutorial-content-delivery-network/v2-in-cdn-title.png)

Para más información, consulte [Purgar un punto de conexión de Azure CDN](../cdn/cdn-purge-endpoint.md). 

## <a name="use-query-strings-to-version-content"></a>Utilizar cadenas de consulta para versiones del contenido

Azure CDN ofrece las siguientes opciones de comportamiento de almacenamiento en caché:

* Pasar por alto las cadenas de consulta
* Omitir el almacenamiento en caché de cadenas de consulta
* Almacenar en caché todas las URL únicas 

El primero de ellos es el comportamiento predeterminado, lo que significa que solo hay una versión almacenada en caché de un recurso, independientemente de la cadena de consulta utilizada en la dirección URL. 

En esta sección del tutorial, cambiará el comportamiento de almacenamiento en caché para almacenar en caché todas las direcciones URL únicas.

### <a name="change-the-cache-behavior"></a>Cambio del comportamiento de la memoria caché

En la página **Punto de conexión de CDN** de Azure Portal, seleccione **Caché**.

Seleccione **Almacenar en caché todas las URL únicas** en la lista desplegable **Comportamiento del almacenamiento en caché de cadenas de consulta**.

Seleccione **Guardar**.

![Selección del comportamiento de almacenamiento de cadenas de consulta en caché](media/app-service-web-tutorial-content-delivery-network/portal-select-caching-behavior.png)

### <a name="verify-that-unique-urls-are-cached-separately"></a>Compruebe que las direcciones URL únicas se almacenan en caché por separado

En un explorador, vaya a la página de inicio del punto de conexión de CDN, pero incluya una cadena de consulta: 

```
http://<endpointname>.azureedge.net/index.html?q=1
```

La red CDN devuelve el contenido de la aplicación web actual, que incluye "V2" en el encabezado. 

Para asegurarse de que esta página se almacena en caché en la red CDN, actualice la página. 

Abra `index.html`, cambie "V2" por "V3" e implemente el cambio. 

```bash
git commit -am "version 3"
git push azure master
```

En un explorador, vaya a la dirección URL del punto de conexión CDN con una nueva cadena de consulta como `q=2`. La red CDN obtiene el archivo `index.html` actual y muestra "V3".  Sin embargo, si navega hasta el punto de conexión de CDN con la cadena de consulta `q=1`, verá "V2".

```
http://<endpointname>.azureedge.net/index.html?q=2
```

![V3 en el título en la red CDN, cadena de consulta 2](media/app-service-web-tutorial-content-delivery-network/v3-in-cdn-title-qs2.png)

```
http://<endpointname>.azureedge.net/index.html?q=1
```

![V2 en el título en la red CDN, cadena de consulta 1](media/app-service-web-tutorial-content-delivery-network/v2-in-cdn-title-qs1.png)

Este resultado muestra que cada cadena de consulta se trata de manera diferente:

* q = 1 se usó antes, por lo que se devuelve contenido almacenado en caché (V2).
* q = 2 es nuevo, por lo que se recupera y se devuelve el contenido de aplicación web más reciente (V3).

Para más información, consulte [Control del comportamiento del almacenamiento en caché de Azure CDN con cadenas de consulta](../cdn/cdn-query-string.md).

## <a name="map-a-custom-domain-to-a-cdn-endpoint"></a>Asignación de un dominio personalizado a un punto de conexión de CDN

Se asigna el dominio personalizado al punto de conexión de CDN mediante la creación de un registro CNAME. Un registro CNAME es una característica DNS que asigna un dominio de origen a un dominio de destino. Por ejemplo, podría asignar `cdn.contoso.com` o `static.contoso.com` a `contoso.azureedge.net`.

Si no tiene un dominio personalizado, puede seguir el tutorial [Comprar y configurar un nombre de dominio personalizado en Azure App Service](custom-dns-web-site-buydomains-web-app.md) para comprar un dominio mediante Azure Portal. 

### <a name="find-the-hostname-to-use-with-the-cname"></a>Búsqueda del nombre de host que se utilizará con el registro CNAME

En la página **Punto de conexión** de Azure Portal, asegúrese de que **Introducción** está seleccionado en el panel de navegación izquierdo y, a continuación, seleccione el botón **+ Dominio personalizado**, en la parte superior de la página.

![Seleccionar Agregar dominio personalizado](media/app-service-web-tutorial-content-delivery-network/portal-select-add-domain.png)

En la página **Agregar un dominio personalizado**, verá el nombre de host del punto de conexión que debe utilizar para crear un registro CNAME. El nombre de host se deriva de la dirección URL del punto de conexión de CDN: **&lt;NombrePuntoConexión>.azureedge.net**. 

![Página Agregar dominio](media/app-service-web-tutorial-content-delivery-network/portal-add-domain.png)

### <a name="configure-the-cname-with-your-domain-registrar"></a>Configuración del registro CNAME con su registrador de dominios

Navegue al sitio web del registrador y localice la sección para crear registros DNS. Podría encontrarlo en una sección como **Nombre de dominio**, **DNS** o **Administración del servidor de nombres**.

Busque la sección para la administración de CNAME. Tiene que dirigirse a la página de configuración avanzada y buscar las palabras CNAME, Alias o Subdomains.

Cree un nuevo registro CNAME que asigne el subdominio elegido (por ejemplo, **static** o **cdn**) al **nombre de host del punto de conexión** que se mostró anteriormente en el portal. 

### <a name="enter-the-custom-domain-in-azure"></a>Escriba el dominio personalizado en Azure

Vuelva a la página **Agregar un dominio personalizado** y escriba su dominio personalizado, incluido el subdominio, en el cuadro de diálogo. Por ejemplo, escriba: `cdn.contoso.com`.   
   
Azure comprobará que el registro CNAME existe para el nombre de dominio que ha escrito. Si el registro CNAME es correcto, el dominio personalizado se valida.

La propagación del registro CNAME en los servidores de nombres de Internet puede llevar cierto tiempo. Si el dominio no se valida inmediatamente, espere unos minutos e inténtelo de nuevo.

### <a name="test-the-custom-domain"></a>Prueba del dominio personalizado

En un explorador, vaya hasta el archivo `index.html` utilizando su dominio personalizado (por ejemplo, `cdn.contoso.com/index.html`) para comprobar que el resultado es el mismo que cuando va directamente a `<endpointname>azureedge.net/index.html`.

![Página de inicio de la aplicación de ejemplo utilizando la dirección URL de dominio personalizado](media/app-service-web-tutorial-content-delivery-network/home-page-custom-domain.png)

Para más información, consulte [Asignación del contenido de la red CDN de Azure a un dominio personalizado](../cdn/cdn-map-content-to-custom-domain.md).

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Pasos siguientes

¿Qué ha aprendido?

> [!div class="checklist"]
> * Crear un punto de conexión de CDN.
> * Actualizar los recursos en caché.
> * Utilizar cadenas de consulta para controlar las versiones en caché.
> * Utilizar un dominio personalizado para el punto de conexión de CDN.

Aprenda cómo optimizar el rendimiento de CDN en los siguientes artículos:

> [!div class="nextstepaction"]
> [Mejora del rendimiento comprimiendo archivos en la red CDN de Azure](../cdn/cdn-improve-performance.md)

> [!div class="nextstepaction"]
> [Carga previa de activos en un punto de conexión de CDN de Azure](../cdn/cdn-preload-endpoint.md)

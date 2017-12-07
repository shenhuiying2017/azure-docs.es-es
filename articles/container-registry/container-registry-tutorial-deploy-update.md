---
title: "Tutorial de Azure Container Registry: inserción una imagen actualizada para implementaciones regionales"
description: "Inserte una imagen de Docker en Azure Container Registry con replicación geográfica y, luego, vea los cambios implementados automáticamente en aplicaciones web que se ejecutan en varias regiones. Tercera parte de una serie de tres partes."
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 359fdcabd579d277e40f02eba2d4603ebd9f5f1f
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2017
---
# <a name="push-an-updated-image-to-regional-deployments"></a>Inserción de una imagen actualizada en implementaciones regionales

Esta es la tercera parte de una serie de tutoriales de tres partes. En el [tutorial anterior](container-registry-tutorial-deploy-app.md), la replicación geográfica se configuró para dos implementaciones regionales de aplicaciones web diferentes. En este tutorial, primero modifique la aplicación, luego cree una nueva imagen de contenedor e insértela en el registro con replicación geográfica. Por último, vea el campo implementado automáticamente por los webhooks de Azure Container Registry en ambas instancias de aplicaciones web.

En este tutorial, parte final de la serie, se realizan las siguientes operaciones:

> [!div class="checklist"]
> * Modificación del HTML de la aplicación web
> * Creación y etiquetado de la imagen de Docker
> * Inserción del cambio en Azure Container Registry
> * Visualización de la aplicación actualizada en dos regiones diferentes

Si aún no ha configurado las dos implementaciones regionales de *Web App for Containers*, vuelva al tutorial anterior de la serie, [Implementación de la aplicación web desde Azure Container Registry](container-registry-tutorial-deploy-app.md).

## <a name="modify-the-web-application"></a>Modificación de la aplicación web

En este paso, realice un cambio en la aplicación web que tenga una alta visibilidad después de insertar la imagen de contenedor actualizada en Azure Container Registry.

Busque el archivo `AcrHelloworld/Views/Home/Index.cshtml` en el origen de la aplicación que [clonó a partir de GitHub](container-registry-tutorial-prepare-registry.md#get-application-code) en un tutorial anterior y ábralo en el editor de texto que prefiera. Agregue la siguiente línea a continuación de la línea `<h1>` existente:

```html
<h1>MODIFIED</h1>
```

El `Index.cshtml` modificado debería tener el mismo aspecto para:

```html
@{
    ViewData["Title"] = "Azure Container Registry :: Geo-replication";
}
<style>
    body {
        background-image: url('images/azure-regions.png');
        background-size: cover;
    }
    .footer {
        position: fixed;
        bottom: 0px;
        width: 100%;
    }
</style>

<h1 style="text-align:center;color:blue">Hello World from:  @ViewData["REGION"]</h1>
<h1>MODIFIED</h1>
<div class="footer">
    <ul>
        <li>Registry URL: @ViewData["REGISTRYURL"]</li>
        <li>Registry IP: @ViewData["REGISTRYIP"]</li>
        <li>Registry Region: @ViewData["REGION"]</li>
    </ul>
</div>
```

## <a name="rebuild-the-image"></a>Recompilación de la imagen

Ahora que ha actualizado la aplicación web, recompile la imagen de contenedor. Al igual que antes, use el nombre de imagen completo, incluida la URL del servidor de inicio de sesión, para la etiqueta:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

## <a name="push-image-to-azure-container-registry"></a>Inserción de imágenes en Azure Container Registry

Ahora, inserte la imagen del contenedor *acr helloworld* actualizada en el registro con replicación geográfica. Aquí está ejecutando un comando `docker push` único para implementar la imagen actualizada en las réplicas del registro en las regiones *Oeste de EE. UU.* y *Este de EE. UU.*

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

La salida debe ser similar a la siguiente:

```bash
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
5b9454e91555: Pushed
d6803756744a: Layer already exists
b7b1f3a15779: Layer already exists
a89567dff12d: Layer already exists
59c7b561ff56: Layer already exists
9a2f9413d9e4: Layer already exists
a75caa09eb1f: Layer already exists
v1: digest: sha256:4c3f2211569346fbe2d1006c18cbea2a4a9dcc1eb3a078608cef70d3a186ec7a size: 1792
```

## <a name="view-the-webhook-logs"></a>Visualización de los registros de webhook

Mientras se está replicando la imagen, puede ver el webhook de Azure Container Registry que se desencadena.

Para ver los webhooks regionales que se crearon al implementar el contenedor en *Web Apps for Containers* en un tutorial anterior, vaya hasta el registro de contenedor en Azure Portal y, luego, seleccione **Webhooks** en **SERVICIOS**.

![Webhooks de registro de contenedor en Azure Portal][tutorial-portal-01]

Seleccione cada Webhook para ver el historial de sus llamadas y respuestas. Debería ver una fila para la acción de **inserción** en los registros de ambos webhooks. En este caso, el registro para el webhook que se encuentra en la región *Oeste de EE. UU.*  se muestra en la acción de **inserción** desencadenada por `docker push` en el paso anterior:

![Webhooks de registro de contenedor en Azure portal (Oeste de EE. UU.)][tutorial-portal-02]

## <a name="view-the-updated-web-app"></a>Visualización de la aplicación web actualizada

Los webhooks notifican a las aplicaciones web que se ha insertado una nueva imagen en el registro, que implementa automáticamente el contenedor actualizado en las dos aplicaciones web regionales.

Compruebe que la aplicación se ha actualizado en ambas implementaciones. Para ello, vaya a ambas implementaciones de aplicaciones web regionales en el explorador web. Como recordatorio, puede buscar la dirección URL de la aplicación web implementada en la parte superior derecha de cada pestaña de información general de App Service.

![Información general de App Service en Azure Portal][tutorial-portal-03]

Para ver la aplicación actualizada, seleccione el vínculo en la descripción general de App Service. Esta es una vista de ejemplo de la aplicación en ejecución en el *Oeste de EE. UU.*:

![Vista del explorador de la aplicación web modificada que se ejecuta en la región del Oeste de EE. UU.][deployed-app-westus-modified]

Compruebe que la imagen de contenedor actualizada también se implementó en la implementación del *Este de EE. UU.*  viéndola en el explorador.

![Vista del explorador de la aplicación web modificada que se ejecuta en la región del Este de EE. UU.][deployed-app-eastus-modified]

Con un solo `docker push`, ha actualizado ambas implementaciones de aplicaciones web regionales y Azure Container Registry ofreció las imágenes de contenedor desde repositorios de cierre de la red.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, se actualiza y se inserta una nueva versión del contenedor de aplicación web en el registro con replicación geográfica. Los webhooks en Azure Container Registry notificaron a Web App for Containers la actualización, que activó una extracción local de replicas del registro.

En este tutorial, parte final de la serie, se realizan las siguientes operaciones:

> [!div class="checklist"]
> * Actualización del HTML de la aplicación web
> * Generación y etiquetado de la imagen de Docker
> * Inserción del cambio en Azure Container Registry
> * Visualización de la aplicación actualizada en dos regiones diferentes

<!-- IMAGES -->
[deployed-app-eastus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-eastus-modified.png
[deployed-app-westus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-westus-modified.png
[local-container-01]: ./media/container-registry-tutorial-deploy-update/local-container-01.png
[tutorial-portal-01]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-01.png
[tutorial-portal-02]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-02.png
[tutorial-portal-03]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-03.png
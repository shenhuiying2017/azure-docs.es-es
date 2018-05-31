---
title: Creación de un entorno de desarrollo en la nube de Node.js para Kubernetes con VS Code | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: Desarrollo rápido de Kubernetes con contenedores y microservicios en Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contenedores
manager: douge
ms.openlocfilehash: deb651170b0fd58f8c89b591f3e42b5b629f4095
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361480"
---
# <a name="get-started-on-azure-dev-spaces-with-nodejs"></a>Introducción a Azure Dev Spaces con Node.js

[!INCLUDE[](includes/learning-objectives.md)]

[!INCLUDE[](includes/see-troubleshooting.md)]

Ahora está preparado para crear un entorno de desarrollo basado en Kubernetes en Azure.

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="install-the-azure-cli"></a>Instalación de la CLI de Azure
Azure Dev Spaces requiere una configuración mínima de la máquina local. La mayor parte de la configuración del entorno de desarrollo se almacena en la nube y se puede compartir con otros usuarios. Para comenzar, descargue y ejecute la [CLI de Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

> [!IMPORTANT]
> Si ya tiene instalada la CLI de Azure, asegúrese de que usa la versión 2.0.32 o superior.

[!INCLUDE[](includes/sign-into-azure.md)]

[!INCLUDE[](includes/use-dev-spaces.md)]

[!INCLUDE[](includes/install-vscode-extension.md)]

Mientras espera a que se cree el clúster, puede empezar a escribir código.

## <a name="create-a-nodejs-container-in-kubernetes"></a>Creación de un contenedor de Node.js en Kubernetes

En esta sección, va a crear una aplicación web de Node.js y a ejecutarla en un contenedor de Kubernetes.

### <a name="create-a-nodejs-web-app"></a>Creación de una aplicación web de Node.js
Descargue el código de GitHub en https://github.com/Azure/dev-spaces y seleccione **Clone or Download** (Clonar o descargar) para descargar el repositorio de GitHub en su entorno local. El código de esta guía se encuentra en `samples/nodejs/getting-started/webfrontend`.

[!INCLUDE[](includes/azds-prep.md)]

[!INCLUDE[](includes/build-run-k8s-cli.md)]

### <a name="update-a-content-file"></a>Actualización de un archivo de contenido
Con Azure Dev Spaces no se trata solo de conseguir que el código se ejecute en Kubernetes, sino de permitirle ver rápida e iterativamente que los cambios de código surtan efecto en un entorno de Kubernetes en la nube.

1. Busque el archivo `./public/index.html` y realice una edición en el código HTML. Por ejemplo, cambie el color de fondo de la página por un tono azul:

    ```html
    <body style="background-color: #95B9C7; margin-left:10px; margin-right:10px;">
    ```

2. Guarde el archivo. Momentos después, en la ventana de terminal verá un mensaje que indica que se ha actualizado un archivo en el contenedor en ejecución.
1. Regrese al explorador y actualice la página. Verá la actualización del color.

¿Qué ha ocurrido? Las ediciones en los archivos de contenido, como HTML y CSS, no requieren que se reinicie el proceso Node.js, por lo que un comando `azds up` activo sincronizará automáticamente cualquier archivo de contenido modificado directamente en el contenedor en ejecución en Azure, proporcionando así una forma rápida de ver las ediciones del contenido.

### <a name="test-from-a-mobile-device"></a>Prueba en un dispositivo móvil
Si abre la aplicación web en un dispositivo móvil, notará que la interfaz de usuario no se muestra correctamente en un dispositivo pequeño.

Para solucionar este problema, va a agregar una etiqueta META `viewport`:
1. Abra el archivo `./public/index.html`.
1. Agregue la etiqueta META `viewport` al elemento existente `head`:

    ```html
    <head>
        <!-- Add this line -->
        <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    ```

1. Guarde el archivo.
1. Actualice el explorador del dispositivo. Ahora verá la aplicación web correctamente representada. 

Este es un ejemplo de cómo algunos problemas no se encuentran hasta que se realiza una prueba en los dispositivos en los que se debe usar una aplicación. Con Azure Dev Spaces de Visual Studio, puede iterar rápidamente su código y validar cualquier cambio en los dispositivos de destino.

### <a name="update-a-code-file"></a>Actualización de un archivo de código
Actualizar los archivos de código del lado servidor requiere un poco más de trabajo, porque hay que reiniciar la aplicación Node.js.

1. En la ventana de terminal, presione `Ctrl+C` (para detener `azds up`).
1. Abra el archivo de código denominado `server.js` y modifique el mensaje de saludo del servicio: 

    ```javascript
    res.send('Hello from webfrontend running in Azure!');
    ```

3. Guarde el archivo.
1. Ejecute `azds up` en la ventana de terminal. 

Se vuelve a crear la imagen del contenedor y se vuelve a implementar el gráfico de Helm. Vuelva a cargar la página del explorador para ver que los cambios de código surten efecto.

Pero hay un método *más rápido* para desarrollar código, que se verá en la siguiente sección. 

## <a name="debug-a-container-in-kubernetes"></a>Depuración de un contenedor en Kubernetes

[!INCLUDE[](includes/debug-intro.md)]

[!INCLUDE[](includes/init-debug-assets-vscode.md)]

### <a name="select-the-azds-debug-configuration"></a>Selección de la configuración de depuración de AZDS
1. Para abrir la vista de depuración, haga clic en el icono de depuración en la **barra de actividad** en el lateral de VS Code.
1. Seleccione **Launch Program (AZDS)** [Iniciar programa (AZDS)] como la configuración de depuración activa.

![](media/get-started-node/debug-configuration-nodejs.png)

> [!Note]
> Si no ve ningún comando de Azure Dev Spaces en la paleta de comandos, asegúrese de haber [instalado la extensión de Visual Studio Code para Azure Dev Spaces](get-started-nodejs.md#get-kubernetes-debugging-for-vs-code).

### <a name="debug-the-container-in-kubernetes"></a>Depuración del contenedor en Kubernetes
Presione **F5**  para depurar el código en Kubernetes.

De forma similar al comando `up`, el código se sincroniza con el entorno de desarrollo cuando comienza la depuración, y se crea un contenedor que se implementa en Kubernetes. En esta ocasión, el depurador se asocia al contenedor remoto.

[!INCLUDE[](includes/tip-vscode-status-bar-url.md)]

Establezca un punto de interrupción en un archivo de código del lado servidor, por ejemplo, dentro de `app.get('/api'...` en `server.js`. Actualice la página del explorador o presione el botón "Say It Again" (Repetir); llegará al punto de interrupción y podrá ejecutar el código paso a paso.

Tiene acceso completo a la información de depuración, igual que si el código se ejecutara localmente, como la pila de llamadas, las variables locales o la información de excepción, por ejemplo.

### <a name="edit-code-and-refresh-the-debug-session"></a>Edición del código y actualización de la sesión de depuración
Con el depurador activo, realice una edición de código; por ejemplo, vuelva a modificar el mensaje de saludo:

```javascript
app.get('/api', function (req, res) {
    res.send('**** Hello from webfrontend running in Azure! ****');
});
```

Guarde el archivo y, en el **panel de acciones de depuración**, haga clic en el botón **Actualizar**. 

![](media/get-started-node/debug-action-refresh-nodejs.png)

En lugar de volver a crear e implementar una nueva imagen de contenedor cada vez que se realizan ediciones en el código, lo que a menudo lleva un tiempo considerable, Azure Dev Spaces reinicia el proceso Node.js entre sesiones de depuración para proporcionar un bucle de edición/depuración más rápido.

Actualice la aplicación web en el explorador o presione el botón *Say It Again* (Repetir). Verá que aparece el mensaje personalizado en la interfaz de usuario.

### <a name="use-nodemon-to-develop-even-faster"></a>Uso de NodeMon para acelerar el desarrollo
*Nodemon* es una conocida herramienta que usan los desarrolladores de Node.js para el desarrollo rápido. En lugar de reiniciar manualmente el proceso de Node cada vez que se realiza una edición del código en el lado servidor, los desarrolladores a menudo configuran su proyecto de Node para que *nodemon* supervise los cambios en el archivo y reinicie automáticamente el proceso del servidor. En este estilo de trabajo, el desarrollador solo actualiza el explorador después de realizar una edición del código.

Con Azure Dev Spaces, puede utilizar muchos de los mismos flujos de trabajo de desarrollo que emplea con el desarrollo local. Para ilustrar esto, el proyecto `webfrontend` de ejemplo se ha configurado para usar *nodemon* (está configurado como una dependencia de desarrollo en `package.json`).

Realice estos pasos:
1. Detenga el depurador de VS Code.
1. Haga clic en el icono de depuración en la **barra de actividad** en el lateral de VS Code. 
1. Seleccione **Attach (AZDS)** [Asociar (AZDS)] como la configuración de depuración activa.
1. Presione F5.

En esta configuración, el contenedor está configurado para iniciar *nodemon*. Cuando se realizan ediciones del código de servidor, *nodemon* reinicia automáticamente el proceso de Node, tal y como lo hace en el desarrollo local. 
1. Edite el mensaje de saludo de nuevo en `server.js` y guarde el archivo.
1. Actualice el explorador o haga clic en el botón *Say It Again* (Repetir) para que los cambios surtan efecto.

**Ahora tiene un método para iterar rápidamente el código y depurarlo directamente en Kubernetes.** A continuación, verá cómo puede crear y llamar a un segundo contenedor.

## <a name="call-a-service-running-in-a-separate-container"></a>Llamada a un servicio que se ejecuta en un contenedor diferente

En esta sección va a crear un segundo servicio `mywebapi` y a hacer que `webfrontend` lo llame. Cada servicio se ejecutará en contenedores diferentes. A continuación, realizará la depuración entre los dos contenedores.

![](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>Abra el código de ejemplo de *mywebapi*.
Ya debería tener el código de ejemplo de `mywebapi` necesario en esta guía, en una carpeta llamada `samples` (si no es así, vaya a https://github.com/Azure/dev-spaces y seleccione **Clone or Download** (Clonar o descargar) para descargar el repositorio de GitHub). El código de esta sección se encuentra en `samples/nodejs/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Ejecución de *mywebapi*
1. Abra la carpeta `mywebapi` en una *ventana independiente de VS Code*.
1. Presione F5 y espere a que el servicio se compile e implemente. Sabrá que está listo cuando aparezca la barra de depuración de VS Code.
1. Anote la dirección URL del punto de conexión; se parecerá a esta http://localhost:\<portnumber\>. **Sugerencia: La barra de estado de VS Code mostrará una dirección URL en la que se puede hacer clic.** Puede parecer que el contenedor se ejecuta localmente, pero en realidad se ejecuta en el entorno de desarrollo de Azure. La razón de la dirección de localhost es que `mywebapi` no ha definido ningún punto de conexión público y solo se puede acceder a él desde la instancia de Kubernetes. Para mayor comodidad, y para facilitar la interacción con el servicio privado desde la máquina local, Azure Dev Spaces crea un túnel SSH temporal al contenedor que se ejecuta en Azure.
1. Cuando `mywebapi` esté listo, abra el explorador en la dirección de localhost. Verá una respuesta del servicio `mywebapi` ("Hello from mywebapi").


### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Realización de una solicitud de *webfrontend* a *mywebapi*
Vamos ahora a escribir código en `webfrontend` que realiza una solicitud a `mywebapi`.
1. Cambie a la ventana de VS Code para `webfrontend`.
1. Agregue estas líneas de código en la parte superior de `server.js`:
    ```javascript
    var request = require('request');
    var propagateHeaders = require('./propagateHeaders');
    ```

3. *Reemplace* el código por el controlador GET `/api`. Al administrar una solicitud, se realiza a su vez una llamada a `mywebapi` y se devuelven los resultados de ambos servicios.

    ```javascript
    app.get('/api', function (req, res) {
        request({
            uri: 'http://mywebapi',
            headers: propagateHeaders.from(req) // propagate headers to outgoing requests
        }, function (error, response, body) {
            res.send('Hello from webfrontend and ' + body);
        });
    });
    ```

Observe cómo se emplea la detección de servicios DNS de Kubernetes para hacer referencia al servicio como `http://mywebapi`. **El código del entorno de desarrollo se ejecuta del mismo modo que lo hará en producción**.

En el ejemplo de código anterior se usa un módulo auxiliar denominado `propagateHeaders`. Este módulo auxiliar se agregó a la carpeta de código en el momento de ejecutar `azds prep`. La función `propagateHeaders.from()` propaga encabezados específicos desde un objeto http.IncomingMessage existente hasta un objeto de encabezados en una solicitud saliente. Más adelante verá cómo esta función ayuda a los equipos con el desarrollo en colaboración.

### <a name="debug-across-multiple-services"></a>Depuración entre varios servicios
1. En este momento, `mywebapi` aún se estará ejecutando con el depurador asociado. Si no es así, presione F5 en el proyecto `mywebapi`.
1. Establezca un punto de interrupción en el controlador GET `/` predeterminado.
1. En el proyecto `webfrontend`, establezca un punto de interrupción justo antes de que se envíe una solicitud GET a `http://mywebapi`.
1. Presione F5 en el proyecto `webfrontend`.
1. Abra la aplicación web y recorra el código de ambos servicios. La aplicación web debe mostrar un mensaje concatenado por los dos servicios: "Hello from webfrontend and Hello from mywebapi".

¡Listo! Ahora tiene una aplicación de varios contenedores donde cada uno se puede desarrollar e implementar por separado.

## <a name="learn-about-team-development"></a>Aprenda sobre el desarrollo en equipo.

[!INCLUDE[](includes/team-development-1.md)]

Véalo ahora en acción:
1. Vaya a la ventana de VS Code para `mywebapi` y realice una edición de código para el controlador GET `/` predeterminado, por ejemplo:

    ```javascript
    app.get('/', function (req, res) {
        res.send('mywebapi now says something new');
    });
    ```

[!INCLUDE[](includes/team-development-2.md)]

[!INCLUDE[](includes/well-done.md)]

[!INCLUDE[](includes/clean-up.md)]





---
title: Creación de un entorno de desarrollo de Kubernetes en la nube mediante .NET Core y VS Code | Microsoft Docs
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
ms.openlocfilehash: a57118feb85a010e38d73b758ebfb84d1cc463fa
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361260"
---
# <a name="get-started-on-azure-dev-spaces-with-net-core"></a>Introducción a Azure Dev Spaces con .NET Core

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

Mientras espera a que se cree el clúster, puede empezar a desarrollar código.

## <a name="create-an-aspnet-core-web-app"></a>Cree una aplicación web ASP.NET Core
Si tiene [.NET Core](https://www.microsoft.com/net) instalado, puede crear rápidamente una aplicación web de ASP.NET Core en una carpeta llamada `webfrontend`.
    
```cmd
dotnet new mvc --name webfrontend
```

O bien **descargue el código de ejemplo de GitHub** en https://github.com/Azure/dev-spaces y seleccione **Clone or Download** (Clonar o descargar) para descargar el repositorio GitHub a su entorno local. El código de esta guía se encuentra en `samples/dotnetcore/getting-started/webfrontend`.

[!INCLUDE[](includes/azds-prep.md)]

[!INCLUDE[](includes/build-run-k8s-cli.md)]

## <a name="update-a-content-file"></a>Actualización de un archivo de contenido
Azure Dev Spaces no se trata solo de conseguir que el código se ejecute en Kubernetes, se trata de permitirle ver rápida e iterativamente que los cambios de código surtan efecto en un entorno de Kubernetes en la nube.

1. Busque el archivo `./Views/Home/Index.cshtml` y realice una edición en el código HTML. Por ejemplo, cambie la línea 70 que dice `<h2>Application uses</h2>` a algo como: `<h2>Hello k8s in Azure!</h2>`
1. Guarde el archivo. Momentos después, en la ventana de terminal verá un mensaje que indica que se ha actualizado un archivo en el contenedor en ejecución.
1. Regrese al explorador y actualice la página. Debería ver la página web que muestra el código HTML actualizado.

¿Qué ha ocurrido? Las ediciones de archivos de contenido, como HTML y CSS, no requieren compilarlos de nuevo en una aplicación web de .NET Core, por lo que un comando `azds up` activo sincroniza automáticamente cualquier archivo de contenido modificado en el contenedor que se está ejecutando en Azure, para que pueda ver sus ediciones de contenido de inmediato.

## <a name="update-a-code-file"></a>Actualización de un archivo de código
Actualizar archivos de código requiere un poco más de trabajo, porque una aplicación .NET Core necesita volver a crear y producir archivos binarios de aplicaciones actualizados.

1. En la ventana de terminal, presione `Ctrl+C` (para detener `azds up`).
1. Abra el archivo de código denominado `Controllers/HomeController.cs` y edite el mensaje que se mostrará en la página Acerca de: `ViewData["Message"] = "Your application description page.";`.
1. Guarde el archivo.
1. Ejecute `azds up` en la ventana de terminal. 

Este comando vuelve a crear la imagen del contenedor y vuelve a implementar el gráfico de Helm. Para que los cambios de código surtan efecto en la aplicación en ejecución, vaya al menú Acerca de la aplicación web.


Pero hay un método *más rápido* para desarrollar código, que se verá en la siguiente sección. 

## <a name="debug-a-container-in-kubernetes"></a>Depuración de un contenedor en Kubernetes

[!INCLUDE[](includes/debug-intro.md)]

[!INCLUDE[](includes/init-debug-assets-vscode.md)]


### <a name="select-the-azds-debug-configuration"></a>Selección de la configuración de depuración de AZDS
1. Para abrir la vista de depuración, haga clic en el icono de depuración en la **barra de actividad** en el lateral de VS Code.
1. Seleccione **.NET Core Launch (AZDS)** [Inicio de .NET Core (AZDS)] como la configuración de depuración activa.

![](media/get-started-netcore/debug-configuration.png)

> [!Note]
> Si no ve ningún comando de Azure Dev Spaces en la paleta de comandos, asegúrese de haber instalado la extensión de Visual Studio Code para Azure Dev Spaces. Asegúrese de que el área de trabajo que abre en VS Code es la carpeta que contiene azds.yaml.


### <a name="debug-the-container-in-kubernetes"></a>Depuración del contenedor en Kubernetes
Presione **F5**  para depurar el código en Kubernetes.

De forma similar al comando `up`, el código se sincroniza con el entorno de desarrollo y se crea un contenedor que se implementa en Kubernetes. En esta ocasión, por supuesto, el depurador se asocia al contenedor remoto.

[!INCLUDE[](includes/tip-vscode-status-bar-url.md)]

Establezca un punto de interrupción en un archivo de código del lado servidor, por ejemplo en la función `Index()` del archivo de origen `Controllers/HomeController.cs`. Al actualizar la página del navegador, el punto de interrupción se activa.

Tiene acceso completo a la información de depuración, tal como lo haría si el código se ejecutara localmente, como la pila de llamadas, las variables locales o la información de excepción, por ejemplo.

### <a name="edit-code-and-refresh"></a>Edición del código y actualización
Con el depurador activo, realice una edición del código. Por ejemplo, modifique el mensaje de la página Acerca de en `Controllers/HomeController.cs`. 

```csharp
public IActionResult About()
{
    ViewData["Message"] = "My custom message in the About page.";
    return View();
}
```

Guarde el archivo y, en el **panel de acciones de depuración**, haga clic en el botón **Actualizar**. 

![](media/get-started-netcore/debug-action-refresh.png)

En lugar de volver a crear e implementar una nueva imagen de contenedor cada vez que se realizan modificaciones en el código, lo que a menudo lleva un tiempo considerable, Azure Dev Spaces volverá a compilar el código de manera incremental dentro del contenedor existente para proporcionar un bucle de modificación/depuración más rápido.

Actualice la aplicación web en el explorador y vaya a la página Acerca de. Debería ver aparecer el mensaje personalizado en la interfaz de usuario.

**Ahora tiene un método para iterar rápidamente el código y depurarlo directamente en Kubernetes.** A continuación, verá cómo puede crear y llamar a un segundo contenedor.

## <a name="call-a-service-running-in-a-separate-container"></a>Llamada a un servicio que se ejecuta en un contenedor diferente

En esta sección, puede crear un segundo servicio `mywebapi` y hacer que `webfrontend` lo llame. Cada servicio se ejecutará en contenedores diferentes. A continuación, realizará la depuración entre los dos contenedores.

![Varios contenedores](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Descargue el código de ejemplo para *mywebapi*.
Por motivos de tiempo, vamos a descargar código de ejemplo desde un repositorio de GitHub. Vaya a https://github.com/Azure/dev-spaces y seleccione **Clone or Download** (Clonar o descargar) para descargar el repositorio de GitHub. El código de esta sección se encuentra en `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Ejecución de *mywebapi*
1. Abra la carpeta `mywebapi` en una *ventana independiente de VS Code*.
1. Presione F5 y espere a que el servicio se compile e implemente. Sabrá que está listo cuando aparezca la barra de depuración de VS Code.
1. Anote la dirección URL del punto de conexión; se parecerá a esta http://localhost:\<portnumber\>. **Sugerencia: La barra de estado de VS Code mostrará una dirección URL en la que se puede hacer clic.** Puede parecer que el contenedor se está ejecutando localmente, pero en realidad se ejecuta en el entorno de desarrollo de Azure. La razón de la dirección de localhost es que `mywebapi` no ha definido ningún punto de conexión público y solo se puede acceder a él desde la instancia de Kubernetes. Para mayor comodidad, y para facilitar la interacción con el servicio privado desde la máquina local, Azure Dev Spaces crea un túnel SSH temporal al contenedor que se ejecuta en Azure.
1. Cuando `mywebapi` esté listo, abra el explorador en la dirección de localhost. Anexe `/api/values` a la dirección URL para invocar la GET API predeterminada para `ValuesController`. 
1. Si todos los pasos se realizaron correctamente, verá una respuesta del servicio `mywebapi`.

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Realización de una solicitud de *webfrontend* a *mywebapi*
Vamos ahora a escribir código en `webfrontend` que realiza una solicitud a `mywebapi`.
1. Cambie a la ventana de VS Code para `webfrontend`.
1. *Reemplace* el código por el método About:

    ```csharp
    public async Task<IActionResult> About()
    {
        ViewData["Message"] = "Hello from webfrontend";
        
        // Use HeaderPropagatingHttpClient instead of HttpClient so we can propagate
        // headers in the incoming request to any outgoing requests
        using (var client = new HeaderPropagatingHttpClient(this.Request))
        {
            // Call *mywebapi*, and display its response in the page
            var response = await client.GetAsync("http://mywebapi/api/values/1");
            ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
        }

        return View();
    }
    ```

Observe cómo se emplea la detección de servicios DNS de Kubernetes para hacer referencia al servicio como `http://mywebapi`. **El código del entorno de desarrollo se ejecuta del mismo modo que lo hará en producción**.

El ejemplo de código anterior también usa la clase `HeaderPropagatingHttpClient`. Esta clase auxiliar se agregó a la carpeta de código en el momento de ejecutar `azds prep`. `HeaderPropagatingHttpClient` se deriva de la conocida clase `HttpClient` y agrega una funcionalidad para propagar los encabezados específicos de un objeto HttpRequest ya existente de ASP.NET en un objeto HttpRequestMessage de salida. Más adelante veremos como el uso de esta clase derivada facilita una experiencia de desarrollo más productiva en escenarios con equipos.


### <a name="debug-across-multiple-services"></a>Depuración entre varios servicios
1. En este momento, `mywebapi` aún se estará ejecutando con el depurador asociado. Si no es así, presione F5 en el proyecto `mywebapi`.
1. Establezca un punto de interrupción en el método `Get(int id)` que controla las solicitudes GET `api/values/{id}`.
1. En el proyecto `webfrontend`, establezca un punto de interrupción justo antes de que se envíe una solicitud GET a `mywebapi/api/values`.
1. Presione F5 en el proyecto `webfrontend`.
1. Invoque la aplicación web y recorra el código de ambos servicios.
1. En la aplicación web, la página Acerca de muestra un mensaje que han concatenado los dos servicios: "Hello from webfrontend and Hello from mywebapi".


¡Listo! Ahora tiene una aplicación de varios contenedores donde cada uno se puede desarrollar e implementar por separado.

## <a name="learn-about-team-development"></a>Aprenda sobre el desarrollo en equipo.

[!INCLUDE[](includes/team-development-1.md)]

Vamos a verlo en acción. Vaya a la ventana de VS Code para `mywebapi` y realice una edición de código para el método `string Get(int id)`, por ejemplo:

    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

[!INCLUDE[](includes/team-development-2.md)]

[!INCLUDE[](includes/well-done.md)]

[!INCLUDE[](includes/clean-up.md)]

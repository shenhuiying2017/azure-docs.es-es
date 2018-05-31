---
title: Creación de un entorno de desarrollo de Kubernetes en la nube mediante .NET Core y Visual Studio | Microsoft Docs
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
ms.openlocfilehash: 012efcbd3fa87268f3a68fdac524ce8310d10120
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34362063"
---
# <a name="get-started-on-azure-dev-spaces-with-net-core-and-visual-studio"></a>Introducción a Azure Dev Spaces con .NET Core y Visual Studio

En esta guía, aprenderá a:

- Crear un entorno basado en Kubernetes en Azure que esté optimizado para el desarrollo
- Desarrollar código de forma iterativa en contenedores con Visual Studio
- Desarrollar de forma independiente dos servicios distintos y usar la detección de servicios DNS de Kubernetes para realizar una llamada a otro servicio
- Desarrollar y probar de forma productiva el código en un entorno de equipo

[!INCLUDE[](includes/see-troubleshooting.md)]

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="get-the-visual-studio-tools"></a>Obtención de las herramientas de Visual Studio
1. Instale la versión más reciente de [Visual Studio 2017](https://www.visualstudio.com/vs/).
1. En el instalador de Visual Studio, asegúrese de que está seleccionada la carga de trabajo siguiente:
    * ASP.NET y desarrollo web
1. Instale la [extensión de Visual Studio para Azure Dev Spaces](https://aka.ms/get-azds-visualstudio).

Ahora está preparado para crear una aplicación web ASP.NET con Visual Studio.

## <a name="create-an-aspnet-web-app"></a>Creación de una aplicación web de ASP.NET

En Visual Studio 2017, cree un nuevo proyecto. Actualmente, el proyecto debe ser una **aplicación web de ASP.NET Core**. Llame al proyecto "**webfrontend**".

![](media/get-started-netcore-visualstudio/NewProjectDialog1.png)

Seleccione la plantilla **Aplicación web (controlador de vista de modelos)** y asegúrese de que apunta a **.NET Core** y **ASP.NET Core 2.0** en los dos menús desplegables en la parte superior del cuadro de diálogo. Haga clic en **Aceptar** para crear el proyecto.

![](media/get-started-netcore-visualstudio/NewProjectDialog2.png)


## <a name="create-a-dev-environment-in-azure"></a>Creación de un entorno de desarrollo en Azure

Con Azure Dev Spaces, puede crear entornos de desarrollo basados en Kubernetes que estén totalmente administrados por Azure y optimizados para el desarrollo. Con el proyecto que acaba de crear abierto, seleccione **Azure Dev Spaces**  en el menú desplegable de configuración de inicio, como se muestra a continuación.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

En el cuadro de diálogo que se muestra a continuación, asegúrese de que ha iniciado sesión con la cuenta adecuada y, a continuación, seleccione un clúster de Kubernetes existente.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.PNG)

Por ahora, deje el menú desplegable **Espacio** con un valor predeterminado de `default`. Más adelante, aprenderá más acerca de esta opción. Active la casilla **Publicly Accessible** (Acceso público) para que la aplicación web esté accesible mediante un punto de conexión público. Esta configuración no es necesaria, pero será útil mostrar algunos conceptos más adelante en este tutorial. Pero no se preocupe, en cualquier caso podrá depurar el sitio web con Visual Studio.

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog2.png)

Haga clic en **Aceptar** para seleccionar o crear el clúster.

Si elige un clúster que no ha sido habilitado para trabajar con Azure Dev Spaces, verá un mensaje que le pregunta si desea configurarlo.

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

Elija **Aceptar**.

 Se inicia una tarea en segundo plano para realizar esto. Esta operación tarda unos minutos en completarse. Para ver si se ha creado, sitúe el puntero sobre el icono **Tareas en segundo plano**  en la esquina inferior izquierda de la barra de estado, como se muestra en la siguiente imagen.

![](media/get-started-netcore-visualstudio/BackgroundTasks.PNG)

> [!Note]
> Hasta que el entorno de desarrollo no se haya creado correctamente, no se puede depurar la aplicación.

## <a name="look-at-the-files-added-to-project"></a>Examen de los archivos agregados al proyecto
Mientras espera a que se cree el entorno de desarrollo, mire los archivos que se han agregado al proyecto cuando eligió usar un entorno de desarrollo.

Primero, puede ver que se ha agregado una carpeta con el nombre `charts` y, dentro de ella, un [gráfico Helm](https://docs.helm.sh) para la aplicación a la que se ha aplicado la técnica scaffolding. Estos archivos se usan para implementar la aplicación en el entorno de desarrollo.

Verá que se ha agregado un archivo denominado `Dockerfile`. Este archivo contiene la información necesaria para empaquetar la aplicación en el formato estándar de Docker. También se crea un archivo `HeaderPropagation.cs`, que se verá más adelante en el tutorial. 

Por último, verá un archivo denominado `azds.yaml`, que contiene la información de configuración que necesita el entorno de desarrollo, como por ejemplo si la aplicación debe ser accesible mediante un punto de conexión público.

![](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>Depuración de un contenedor en Kubernetes
Cuando el entorno de desarrollo se haya creado correctamente, puede depurar la aplicación. Establezca un punto de interrupción en el código, por ejemplo, en la línea 20 del archivo `HomeController.cs`, donde se establece la variable `Message`. Pulse **F5** para iniciar la depuración. 

Visual Studio se comunica con el entorno de desarrollo para compilar e implementar la aplicación y, después, abre un explorador con la aplicación web en ejecución. Puede parecer que el contenedor se ejecuta localmente, pero en realidad lo hace en el entorno de desarrollo de Azure. La razón de la dirección de localhost es porque Azure Dev Spaces crea un túnel SSH temporal al contenedor que se ejecuta en Azure.

Haga clic en el vínculo **Acerca de** en la parte superior de la página para desencadenar el punto de interrupción. Tiene acceso completo a la información de depuración, tal como lo haría si el código se ejecutara localmente, como la pila de llamadas, las variables locales o la información de excepción, por ejemplo.

## <a name="call-another-container"></a>Llamada a otro contenedor
En esta sección va a crear un segundo servicio `mywebapi` y a hacer que `webfrontend` lo llame. Cada servicio se ejecutará en contenedores diferentes. A continuación, realizará la depuración entre los dos contenedores.

![](media/common/multi-container.png)

## <a name="download-sample-code-for-mywebapi"></a>Descargue el código de ejemplo para *mywebapi*.
Por motivos de tiempo, vamos a descargar código de ejemplo desde un repositorio de GitHub. Vaya a https://github.com/Azure/dev-spaces y seleccione **Clone or Download** (Clonar o descargar) para descargar el repositorio de GitHub. El código de esta sección se encuentra en `samples/dotnetcore/getting-started/mywebapi`.

## <a name="run-mywebapi"></a>Ejecución de *mywebapi*
1. Abra el proyecto `mywebapi` en *otra ventana de Visual Studio*.
1. Seleccione **Azure Dev Spaces** en la lista desplegable de configuración de inicio como hizo anteriormente con el proyecto `webfrontend`. En lugar de crear un nuevo entorno de desarrollo en este momento, seleccione el mismo que ya ha creado. Al igual que antes, deje el espacio con el valor predeterminado `default` y haga clic en **Aceptar**. En la ventana de salida, puede observar que Visual Studio comienza a preparar este nuevo servicio en el entorno de desarrollo con el fin de acelerar las cosas al comenzar la depuración.
1. Presione F5 y espere a que el servicio se compile e implemente. Sabrá que está listo cuando la barra de estado de Visual Studio se vuelva de color naranja
1. Anote la dirección URL del punto de conexión que se muestra en el panel **Azure Dev Spaces para AKS** en la ventana **Salida**. Se parecerá a esta http://localhost:\<portnumber\>. Puede parecer que el contenedor se ejecuta localmente, pero en realidad lo hace en el entorno de desarrollo de Azure.
2. Cuando `mywebapi` esté listo, abra el explorador en la dirección de localhost y anexe `/api/values` a la dirección URL para invocar la API de GET predeterminada de `ValuesController`. 
3. Si todos los pasos se realizaron correctamente, podrá ver una respuesta del servicio `mywebapi` parecida a esta.

    ![](media/get-started-netcore-visualstudio/WebAPIResponse.png)

## <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Realización de una solicitud de *webfrontend* a *mywebapi*
Vamos ahora a escribir código en `webfrontend` que realiza una solicitud a `mywebapi`. Cambie a la ventana de Visual Studio que tiene el proyecto `webfrontend`. En el archivo `HomeController.cs` *reemplace* el código en el método About por el código siguiente:

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

El ejemplo de código anterior también usa la clase `HeaderPropagatingHttpClient`. Esta clase auxiliar es el archivo `HeaderPropagation.cs` que se agregó al proyecto cuando se configuró para usar Azure Dev Spaces. `HeaderPropagatingHttpClient` se deriva de la conocida clase `HttpClient` y agrega funcionalidad para propagar encabezados específicos desde un objeto HttpRequest existente de ASP .NET hasta un objeto HttpRequestMessage saliente. Más adelante se verá como el uso de esta clase facilita una experiencia de desarrollo más productiva en escenarios de trabajo en equipo.

## <a name="debug-across-multiple-services"></a>Depuración entre varios servicios
1. En este momento, `mywebapi` aún se estará ejecutando con el depurador asociado. Si no es así, presione F5 en el proyecto `mywebapi`.
1. Establezca un punto de interrupción en el método `Get(int id)` en el archivo `ValuesController.cs` que controla las solicitudes GET `api/values/{id}`.
1. En el proyecto `webfrontend` donde pegó el código anterior, establezca un punto de interrupción justo antes de que se envíe una solicitud GET a `mywebapi/api/values`.
1. Presione F5 en el proyecto `webfrontend`. Visual Studio abre de nuevo un explorador en el puerto localhost adecuado y se muestra la aplicación web.
1. Haga clic en el vínculo "**Acerca de**" en la parte superior de la página para desencadenar el punto de interrupción del proyecto `webfrontend`. 
1. Presione F10 para continuar. Ahora se desencadena el punto de interrupción del proyecto `mywebapi`.
1. Presione F5 para continuar; volverá al código del proyecto `webfrontend`.
1. Al presionar F5 una vez más, se completa la solicitud y se devuelve una página del explorador. En la aplicación web, la página Acerca de muestra un mensaje que han concatenado los dos servicios: "Hello from webfrontend and Hello from mywebapi".

¡Listo! Ahora tiene una aplicación de varios contenedores donde cada uno se puede desarrollar e implementar por separado.

## <a name="learn-about-team-development"></a>Aprenda sobre el desarrollo en equipo.

Hasta ahora, ha ejecutado el código de la aplicación como si fuera el único desarrollador que trabaja en la aplicación. En esta sección, aprenderá cómo Azure Dev Spaces simplifica el desarrollo en equipo:
* Permitirá que un equipo de desarrolladores trabaje en el mismo entorno de desarrollo
* Permitirá que cada desarrollador efectúe una iteración en su código de forma aislada y sin peligro de interrumpir a otros desarrolladores
* Probará el código de un extremo a otro, antes de confirmarlo, sin tener que crear bocetos o dependencias simuladas

### <a name="challenges-with-developing-microservices"></a>Dificultades con el desarrollo de microservicios
Por el momento, la aplicación de ejemplo no es muy compleja. Pero en el trabajo de desarrollo del mundo real, las dificultades pronto surgen a medida que se agregan más servicios y crece el equipo de desarrollo.

Imagínese trabajando en un servicio que interactúa con docenas de otros servicios.

- Puede ser poco realista ejecutar todo localmente para el desarrollo. Es posible que la máquina de desarrollo no tenga recursos suficientes para ejecutar la aplicación entera. O bien, quizás la aplicación tenga puntos de conexión que deben estar accesibles públicamente (por ejemplo, la aplicación responde a un webhook desde una aplicación SaaS).
- Puede intentar ejecutar solo los servicios de los que depende, pero esto significa que debe conocer el cierre completo de dependencias (por ejemplo, las dependencias de dependencias). O bien sea cuestión de no saber fácilmente cómo compilar y ejecutar las dependencias porque no ha trabajado en ellas.
- Algunos desarrolladores lo que hacen es simular o realizar bocetos de muchas de sus dependencias de servicios. Este sistema puede funcionar a veces, pero administrar esos bocetos puede ocuparles pronto todo el trabajo de desarrollo. Además, puede ocurrir que el entorno de desarrollo no se parezca al de producción, y que aparezcan errores imperceptibles.
- A eso se suma que hacer cualquier tipo de prueba de un extremo a otro se hace difícil. Las pruebas de integración solo pueden suceder de forma realista tras una confirmación, lo que significa que verá problemas más adelante en el ciclo de desarrollo.

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-development-environment"></a>Trabajar en un entorno de desarrollo compartido
Con Azure Dev Spaces, puede configurar un entorno de desarrollo *compartido* en Azure. Cada desarrollador puede centrarse en su parte de la aplicación y desarrollar de forma iterativa *código de confirmación previa* en un entorno que ya contiene todos los demás servicios y recursos en la nube de los que dependen sus escenarios. Las dependencias siempre están actualizadas y los desarrolladores trabajan de una manera que refleja el entorno de producción.

### <a name="work-in-your-own-space"></a>Trabajar en su propio espacio de trabajo
A medida que desarrolla código para su servicio, y antes de estar preparado para insertarlo en el repositorio, el código estará normalmente en buenas condiciones. Aún estará dándole forma, probándolo y experimentando con soluciones de manera iterativa. Azure Dev Spaces proporciona el concepto de un **espacio**, que le permite trabajar de forma aislada sin miedo a que le interrumpan los miembros de su equipo.

Para asegurarse de que los servicios `webfrontend` y `mywebapi` se ejecutan en el entorno de desarrollo  **y en el `default`espacio**, siga estos pasos.
1. Cierre las sesiones de depuración o de F5 en ambos servicios, pero mantenga abierto el proyecto en sus ventanas de Visual Studio.
2. Cambie a la ventana de Visual Studio con el proyecto `mywebapi` y presione Ctrl+F5 para ejecutar el servicio sin el depurador asociado.
3. Cambie a la ventana de Visual Studio con el proyecto `webfrontend` y presione Ctrl+F5 para ejecutarlo también.

> [!Note]
> En ocasiones es necesario actualizar el explorador después de que la página web se muestra inicialmente a continuación de una operación Ctrl+F5.

Cualquiera que abra la dirección URL pública y vaya a la aplicación web, invocará la ruta de acceso al código que ha escrito, que recorrerá ambos servicios mediante el espacio predeterminado `default`. Ahora, suponga que quiere seguir desarrollando `mywebapi`; ¿cómo podría hacerlo sin interrumpir a otros desarrolladores que usan el entorno de desarrollo? Para ello, configurará su propio espacio.

### <a name="create-a-new-space"></a>Creación de un nuevo espacio
En Visual Studio, puede crear espacios adicionales que se usarán al presionar F5 o Ctrl+F5 para su servicio. Puede llamar a un espacio como quiera y puede ser flexible sobre lo que significa (p. ej. `sprint4` o `demo`).

Siga estos pasos para crear un nuevo espacio:
1. Cambie a la ventana de Visual Studio con el proyecto `mywebapi`.
2. Haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** y seleccione **Propiedades**.
3. Seleccione la pestaña **Depurar** a la izquierda para mostrar la configuración de Azure Dev Spaces.
4. Desde aquí, puede cambiar o crear el clúster o el espacio que se usará al presionar F5 o Ctrl+F5. *Asegúrese de que el espacio de Azure Dev Spaces que creó anteriormente está seleccionado*.
5. En la lista desplegable de espacios, seleccione **<Create New Space…>** (Crear nuevo espacio).

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. En el cuadro de diálogo **Add Space** (Agregar espacio), escriba un nombre para el espacio y haga clic en **Aceptar**. Puede usar su nombre (por ejemplo, "scott") para el nuevo espacio de forma que sus compañeros puedan identificar fácilmente el espacio en el que está trabajando.

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. Ahora verá el entorno de desarrollo y el nuevo espacio seleccionados en la página de propiedades del proyecto.

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>Actualización del código de *mywebapi*

1. En el proyecto `mywebapi`, realice un cambio de código al método `string Get(int id)` en el archivo `ValuesController.cs` de la manera siguiente:
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. Establezca un punto de interrupción en este bloque de código actualizado (quizás ya tenga uno establecido desde antes).
3. Presione F5 para iniciar el servicio `mywebapi`. El servicio se inicia en su entorno de desarrollo mediante el espacio seleccionado, que en este caso es `scott`.

Este es un diagrama que le ayudará a comprender cómo funcionan los distintos espacios. La ruta de acceso azul muestra una solicitud a través del espacio `default`, que es la ruta de acceso predeterminada que se usa si no hay ningún espacio antepuesto a la dirección URL. La ruta de acceso verde muestra una solicitud a través del espacio `scott`.

![](media/common/Space-Routing.png)

Esta funcionalidad integrada de Azure Dev Spaces le permite probar el código de un extremo a otro de un entorno compartido sin necesidad de que cada desarrollador vuelva a crear la pila completa de servicios en su espacio. Este enrutamiento requiere el reenvío de encabezados de propagación en el código de aplicación, como se ilustra en el paso anterior de esta guía.

### <a name="test-code-running-in-the-scott-space"></a>Prueba del código que se ejecuta en el espacio `scott`
Para probar la nueva versión de `mywebapi` junto con `webfrontend`, abra el explorador en la dirección URL del punto de acceso público de `webfrontend` (por ejemplo, http://webfrontend-teamenv.123456abcdef.eastus.aksapp.io) y vaya a la página Acerca de. Verá el mensaje original "Hello from webfrontend and Hello from mywebapi".

Ahora, agregue la parte "scott.s." a la dirección URL para que ponga algo parecido a http://scott.s.webfrontend-teamenv.123456abcdef.eastus.aksapp.io y actualice el explorador. Se llegará al punto de interrupción establecido en el proyecto `mywebapi`. Presione F5 para continuar; en el explorador debería ver el nuevo mensaje "Hello from webfrontend and mywebapi now says something new". El motivo es que la ruta de acceso al código actualizado en `mywebapi` se ejecuta en el espacio `scott`.

[!INCLUDE[](includes/well-done.md)]

[!INCLUDE[](includes/clean-up.md)]

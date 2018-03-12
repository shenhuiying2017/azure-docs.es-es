---
title: "Creación de una aplicación .NET para Service Fabric | Microsoft Docs"
description: "En este tutorial, aprenderá a crear una aplicación con un front-end de ASP.NET Core y un back-end de servicio confiable con estado, así como a implementar la aplicación en un clúster."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/29/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: f8d9733b7dad4c6beeed9bcc950910ed6b426585
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/02/2018
---
# <a name="tutorial-create-and-deploy-an-application-with-an-aspnet-core-web-api-front-end-service-and-a-stateful-back-end-service"></a>Tutorial: Creación e implementación de una aplicación con un servicio de front-end de ASP.NET Core Web API y un servicio back-end con estado
Este tutorial es la primera parte de una serie.  Aprenda a crear una aplicación de Azure Service Fabric con un front-end de ASP.NET Core Web API y un servicio back-end con estado para almacenar los datos. Cuando termine, tendrá una aplicación de votación con un front-end web de ASP.NET Core que guarda los resultados de una votación en un servicio back-end con estado en el clúster. Si no desea crear manualmente la aplicación de votación, puede [descargar el código fuente](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) de la aplicación terminada y pasar directamente al [Tutorial de la aplicación de ejemplo de votación](#walkthrough_anchor).

![Diagrama de la aplicación](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

En la primera parte de la serie, se aprende a:

> [!div class="checklist"]
> * Crear un servicio de ASP.NET Core Web API como un servicio de confianza con estado
> * Crear un servicio de ASP.NET Core Web Application como un servicio de confianza sin estado
> * Usar el proxy inverso para comunicarse con el servicio con estado

En esta serie de tutoriales, se aprende a:
> [!div class="checklist"]
> * Crear una aplicación de .NET Service Fabric
> * [Implementar la aplicación en un clúster remoto](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Configurar CI/CD con Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Configurar la supervisión y el diagnóstico para la aplicación](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>requisitos previos
Antes de empezar este tutorial:
- Si no tiene ninguna suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Instale Visual Studio 2017](https://www.visualstudio.com/) versión 15.5 o posteriores con las cargas de trabajo de **Desarrollo de Azure** y de **Desarrollo de ASP.NET y web**.
- [Instale el SDK de Service Fabric](service-fabric-get-started.md).

## <a name="create-an-aspnet-web-api-service-as-a-reliable-service"></a>Creación de un servicio de ASP.NET Web API como un servicio de confianza
En primer lugar, cree el front-end web de la aplicación de votación mediante ASP.NET Core. ASP.NET Core es un marco de desarrollo web ligero multiplataforma, que puede usar para crear modernas interfaces de usuario web y API web. Para obtener una descripción completa de cómo se integra ASP.NET Core con Service Fabric, se recomienda fehacientemente leer el artículo [ASP.NET Core en Reliable Services de Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md). De momento, puede seguir este tutorial para empezar a trabajar rápidamente. Para más información sobre ASP.NET Core, vea [Documentación de ASP.NET Core](https://docs.microsoft.com/aspnet/core/).

1. Inicie Visual Studio como **administrador**.

2. Cree un proyecto con **Archivo**->**Nuevo**->**Proyecto**.

3. En el cuadro de diálogo **Nuevo proyecto**, elija **Nube > Aplicación de Service Fabric**.

4. Asigne el nombre **Voting** a la aplicación y presione **Aceptar**.

   ![Cuadro de diálogo de proyecto nuevo en Visual Studio.](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog.png)

5. En la página **Nuevo servicio de Service Fabric**, elija **ASP.NET Core sin estado** y asigne el nombre de **VotingWeb** a su servicio.
   
   ![Selección del servicio web ASP.NET en el cuadro de diálogo de nuevo servicio](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog-2.png) 

6. En la página siguiente se proporciona un conjunto de plantillas de proyecto ASP.NET Core. Para este tutorial, elija **Aplicación web (controlador de vista de modelos)**. 
   
   ![Elección del tipo de proyecto ASP.NET](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog.png)

   Visual Studio crea una aplicación y un proyecto de servicio, y los muestra en el Explorador de soluciones.

   ![Explorador de soluciones después de la creación de una aplicación con el servicio ASP.NET Core Web API]( ./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="add-angularjs-to-the-votingweb-service"></a>Incorporación de AngularJS al servicio VotingWeb
Agregue [AngularJS](http://angularjs.org/) al servicio con la [compatibilidad de Bower](/aspnet/core/client-side/bower). En primer lugar, agregue un archivo de configuración de Bower al proyecto.  En el Explorador de soluciones, haga clic con el botón derecho en **VotingWeb** y seleccione **Agregar->Nuevo elemento**. Seleccione **Web** y **Archivo de configuración de Bower**.  Se creará el archivo *bower.json*.

Abra *bower.json* y agregue las entradas angular y angular-bootstrap, y guarde los cambios.

```json
{
  "name": "asp.net",
  "private": true,
  "dependencies": {
    "bootstrap": "3.3.7",
    "jquery": "3.2.1",
    "jquery-validation": "1.16.0",
    "jquery-validation-unobtrusive": "3.2.6",
    "angular": "v1.6.8",
    "angular-bootstrap": "v1.1.0"
  }
}
```
Al guardar el archivo *bower.json*, Angular se instala en la carpeta *wwwroot/lib* del proyecto. Además, aparece dentro de la carpeta *Dependencies/Bower*.

### <a name="update-the-sitejs-file"></a>Actualización del archivo site.js
Abra el archivo *wwwroot/js/site.js*.  Reemplace el contenido por el código de JavaScript que usan las vistas de inicio:

```javascript
var app = angular.module('VotingApp', ['ui.bootstrap']);
app.run(function () { });

app.controller('VotingAppController', ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {

    $scope.refresh = function () {
        $http.get('api/Votes?c=' + new Date().getTime())
            .then(function (data, status) {
                $scope.votes = data;
            }, function (data, status) {
                $scope.votes = undefined;
            });
    };

    $scope.remove = function (item) {
        $http.delete('api/Votes/' + item)
            .then(function (data, status) {
                $scope.refresh();
            })
    };

    $scope.add = function (item) {
        var fd = new FormData();
        fd.append('item', item);
        $http.put('api/Votes/' + item, fd, {
            transformRequest: angular.identity,
            headers: { 'Content-Type': undefined }
        })
            .then(function (data, status) {
                $scope.refresh();
                $scope.item = undefined;
            })
    };
}]);
```

### <a name="update-the-indexcshtml-file"></a>Actualización del archivo Index.cshtml
Abra el archivo *Views/Home/Index.cshtml*, la vista específica para el controlador de inicio.  Reemplace su contenido por el siguiente y guarde los cambios.

```html
@{
    ViewData["Title"] = "Service Fabric Voting Sample";
}

<div ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <form class="col-xs-12 center-block">
                    <div class="col-xs-6 form-group">
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item"/>
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr/>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <div class="row">
                    <div class="col-xs-4">
                        Click to vote
                    </div>
                </div>
                <div class="row top-buffer" ng-repeat="vote in votes.data">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(vote.key)">
                            <span class="pull-left">
                                {{vote.key}}
                            </span>
                            <span class="badge pull-right">
                                {{vote.value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(vote.key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
```

### <a name="update-the-layoutcshtml-file"></a>Actualización del archivo _Layout.cshtml
Abra el archivo *Views/Shared/_Layout.cshtml*, el diseño predeterminado para la aplicación ASP.NET.  Reemplace su contenido por el siguiente y guarde los cambios.

```html
<!DOCTYPE html>
<html ng-app="VotingApp" xmlns:ng="http://angularjs.org">
<head>
    <meta charset="utf-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>@ViewData["Title"]</title>

    <link href="~/lib/bootstrap/dist/css/bootstrap.min.css" rel="stylesheet"/>
    <link href="~/css/site.css" rel="stylesheet"/>

</head>
<body>
<div class="container body-content">
    @RenderBody()
</div>

<script src="~/lib/jquery/dist/jquery.js"></script>
<script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
<script src="~/lib/angular/angular.js"></script>
<script src="~/lib/angular-bootstrap/ui-bootstrap-tpls.js"></script>
<script src="~/js/site.js"></script>

@RenderSection("Scripts", required: false)
</body>
</html>
```

### <a name="update-the-votingwebcs-file"></a>Actualización del archivo VotingWeb.cs
Abra el archivo *VotingWeb.cs*, que permite crear el elemento WebHost de ASP.NET Core dentro del servicio sin estado con el servidor web de WebListener.  

Agregue la directiva `using System.Net.Http;` en la parte superior del archivo.  

Reemplace la función `CreateServiceInstanceListeners()` por lo siguiente y guarde los cambios.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(
            serviceContext =>
                new KestrelCommunicationListener(
                    serviceContext,
                    "ServiceEndpoint",
                    (url, listener) =>
                    {
                        ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

                        return new WebHostBuilder()
                            .UseKestrel()
                            .ConfigureServices(
                                services => services
                                    .AddSingleton<HttpClient>(new HttpClient())
                                    .AddSingleton<FabricClient>(new FabricClient())
                                    .AddSingleton<StatelessServiceContext>(serviceContext))
                            .UseContentRoot(Directory.GetCurrentDirectory())
                            .UseStartup<Startup>()
                            .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                            .UseUrls(url)
                            .Build();
                    }))
    };
}
```

Agregue también el método `GetVotingDataServiceName`, que devuelve el nombre del servicio cuando sondea:

```csharp
internal static Uri GetVotingDataServiceName(ServiceContext context)
{
    return new Uri($"{context.CodePackageActivationContext.ApplicationName}/VotingData");
}
```

### <a name="add-the-votescontrollercs-file"></a>Adición del archivo VotesController.cs
Agregue un controlador para definir las acciones de votación. Haga clic con el botón derecho en la carpeta **Controladores** y seleccione **Agregar->Nuevo elemento->Clase**.  Asigne al archivo el nombre "VotesController.cs" y haga clic en **Agregar**.  

Reemplace el contenido del archivo por el siguiente y guarde los cambios.  Más adelante, en [Actualización del archivo VotesController.cs](#updatevotecontroller_anchor), este archivo se modifica para leer y escribir los datos de votación desde el servicio back-end.  Por ahora, el controlador devuelve datos de cadena estática a la vista.

```csharp
namespace VotingWeb.Controllers
{
    using System;
    using System.Collections.Generic;
    using System.Fabric;
    using System.Fabric.Query;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Newtonsoft.Json;

    [Produces("application/json")]
    [Route("api/Votes")]
    public class VotesController : Controller
    {
        private readonly HttpClient httpClient;

        public VotesController(HttpClient httpClient)
        {
            this.httpClient = httpClient;
        }

        // GET: api/Votes
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            List<KeyValuePair<string, int>> votes= new List<KeyValuePair<string, int>>();
            votes.Add(new KeyValuePair<string, int>("Pizza", 3));
            votes.Add(new KeyValuePair<string, int>("Ice cream", 4));

            return Json(votes);
        }
     }
}
```

### <a name="configure-the-listening-port"></a>Configuración del puerto de escucha
Cuando se crea el servicio front-end VotingWeb, Visual Studio selecciona aleatoriamente un puerto en el que el servicio realiza la escucha.  Dado que el servicio VotingWeb actúa como front-end para esta aplicación y acepta tráfico externo, queremos enlazar dicho servicio a un puerto fijo y conocido.  El [manifiesto del servicio](service-fabric-application-and-service-manifests.md) declara los puntos de conexión de servicio. En el Explorador de soluciones, abra *VotingWeb/PackageRoot/ServiceManifest.xml*.  Busque el recurso **Endpoint** en la sección de **recursos** y cambie el valor del **puerto** a 80 o a otro puerto. Para implementar y ejecutar la aplicación localmente, el puerto de escucha de la aplicación debe estar abierto y disponible en el equipo.

```xml
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="80" />
    </Endpoints>
  </Resources>
```

Actualice también el valor de la propiedad de dirección URL de la aplicación en el proyecto Voting para que un explorador web se abra en el puerto correcto al depurar mediante "F5".  En el Explorador de soluciones, seleccione el proyecto **Voting** y actualice la propiedad **dirección URL de la aplicación**.

![Dirección URL de la aplicación](./media/service-fabric-tutorial-deploy-app-to-party-cluster/application-url.png)

### <a name="deploy-and-run-the-application-locally"></a>Implementación y ejecución local de la aplicación
Ya puede continuar y ejecutar la aplicación. Presione `F5` en Visual Studio para implementar la aplicación, con el fin de depurarla. `F5` produce un error si previamente no ha abierto Visual Studio como **administrador**.

> [!NOTE]
> La primera vez que ejecute e implemente la aplicación localmente, Visual Studio creará un clúster local para la depuración.  Es posible que la creación del clúster tarde un tiempo. El estado de creación del clúster se muestra en la ventana de salida de Visual Studio.

En este momento, la aplicación web se debe parecer a esta:

![Front-end de ASP.NET Core](./media/service-fabric-tutorial-create-dotnet-app/debug-front-end.png)

Para detener la depuración de la aplicación, vuelva a Visual Studio y presione **Mayús+F5**.

## <a name="add-a-stateful-back-end-service-to-your-application"></a>Adición de un servicio back-end con estado a la aplicación
Ahora que se ejecuta un servicio de ASP.NET Web API en la aplicación, agreguemos un servicio de confianza con estado para almacenar datos en la aplicación.

Service Fabric permite almacenar de forma coherente y confiable su estado justo dentro de su servicio mediante Reliable Collections. Reliable Collections es un conjunto de clases de colecciones de alta disponibilidad y confiabilidad que le resultará familiar a cualquiera que haya usado colecciones de C#.

En este tutorial creará un servicio, que almacena un valor de contador en una colección de confianza.

1. En el Explorador de soluciones, haga clic con el botón derecho en **Servicios**, en el proyecto de aplicación y seleccione **Agregar > Nuevo servicio de Service Fabric**.
    
2. En el cuadro de diálogo **Nuevo servicio de Service Fabric**, elija **ASP.NET Core con estado** y asigne el nombre de **VotingData** al servicio y presione **Aceptar**.

    ![Cuadro de diálogo de servicio nuevo en Visual Studio.](./media/service-fabric-tutorial-create-dotnet-app/add-stateful-service.png)

    Una vez creado el proyecto de servicio, tendrá dos servicios en la aplicación. Mientras continúa la creación de la aplicación, puede agregar más servicios de la misma forma. Cada uno puede tener versiones y actualizaciones independientes.

3. En la página siguiente se proporciona un conjunto de plantillas de proyecto ASP.NET Core. Para este tutorial, elija **API Web**.

    ![Elección del tipo de proyecto ASP.NET](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog2.png)

    Visual Studio crea un proyecto de servicio y lo muestra en el Explorador de soluciones.

    ![Explorador de soluciones](./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-webapi-service.png)

### <a name="add-the-votedatacontrollercs-file"></a>Adición del archivo VoteDataController.cs

En el proyecto **VotingData** haga clic con el botón derecho en la carpeta **Controladores** y seleccione **Agregar->Nuevo elemento->Clase**. Asigne al archivo el nombre "VoteDataController.cs" y haga clic en **Agregar**. Reemplace el contenido del archivo por el siguiente y guarde los cambios.

```csharp
namespace VotingData.Controllers
{
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.ServiceFabric.Data;
    using Microsoft.ServiceFabric.Data.Collections;

    [Route("api/[controller]")]
    public class VoteDataController : Controller
    {
        private readonly IReliableStateManager stateManager;

        public VoteDataController(IReliableStateManager stateManager)
        {
            this.stateManager = stateManager;
        }

        // GET api/VoteData
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            CancellationToken ct = new CancellationToken();

            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                Microsoft.ServiceFabric.Data.IAsyncEnumerable<KeyValuePair<string, int>> list = await votesDictionary.CreateEnumerableAsync(tx);

                Microsoft.ServiceFabric.Data.IAsyncEnumerator<KeyValuePair<string, int>> enumerator = list.GetAsyncEnumerator();

                List<KeyValuePair<string, int>> result = new List<KeyValuePair<string, int>>();

                while (await enumerator.MoveNextAsync(ct))
                {
                    result.Add(enumerator.Current);
                }

                return this.Json(result);
            }
        }

        // PUT api/VoteData/name
        [HttpPut("{name}")]
        public async Task<IActionResult> Put(string name)
        {
            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
                await tx.CommitAsync();
            }

            return new OkResult();
        }

        // DELETE api/VoteData/name
        [HttpDelete("{name}")]
        public async Task<IActionResult> Delete(string name)
        {
            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                if (await votesDictionary.ContainsKeyAsync(tx, name))
                {
                    await votesDictionary.TryRemoveAsync(tx, name);
                    await tx.CommitAsync();
                    return new OkResult();
                }
                else
                {
                    return new NotFoundResult();
                }
            }
        }
    }
}
```


## <a name="connect-the-services"></a>Conexión de los servicios
En este paso se conectan los dos servicios y se hace que la aplicación web de front-end obtenga y configure la información de votación del servicio back-end.

Service Fabric proporciona una flexibilidad completa en el modo de comunicación con los servicios confiables. Dentro de una única aplicación, es posible que tenga servicios que sean accesibles a través de TCP. Puede haber también otros servicios que podrían estar accesibles a través de una API de REST de HTTP e incluso otros servicios que pueden estar accesibles a través de sockets web. Para más información sobre las opciones disponibles y sus inconvenientes, consulte [Conexión y comunicación con servicios en Service Fabric](service-fabric-connect-and-communicate-with-services.md).

En este tutorial se utiliza [ASP.NET Core Web API](service-fabric-reliable-services-communication-aspnetcore.md).

<a id="updatevotecontroller" name="updatevotecontroller_anchor"></a>

### <a name="update-the-votescontrollercs-file"></a>Actualización del archivo VotesController.cs
En el proyecto **VotingWeb**, abra el archivo *Controllers/VotesController.cs*.  Reemplace el contenido de la definición de clase `VotesController` por el siguiente y guarde los cambios.

```csharp
public class VotesController : Controller
{
    private readonly HttpClient httpClient;
    private readonly FabricClient fabricClient;
    private readonly StatelessServiceContext serviceContext;

    public VotesController(HttpClient httpClient, StatelessServiceContext context, FabricClient fabricClient)
    {
        this.fabricClient = fabricClient;
        this.httpClient = httpClient;
        this.serviceContext = context;
    }

    // GET: api/Votes
    [HttpGet("")]
    public async Task<IActionResult> Get()
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);

        ServicePartitionList partitions = await this.fabricClient.QueryManager.GetPartitionListAsync(serviceName);

        List<KeyValuePair<string, int>> result = new List<KeyValuePair<string, int>>();

        foreach (Partition partition in partitions)
        {
            string proxyUrl =
                $"{proxyAddress}/api/VoteData?PartitionKey={((Int64RangePartitionInformation) partition.PartitionInformation).LowKey}&PartitionKind=Int64Range";

            using (HttpResponseMessage response = await this.httpClient.GetAsync(proxyUrl))
            {
                if (response.StatusCode != System.Net.HttpStatusCode.OK)
                {
                    continue;
                }

                result.AddRange(JsonConvert.DeserializeObject<List<KeyValuePair<string, int>>>(await response.Content.ReadAsStringAsync()));
            }
        }

        return this.Json(result);
    }

    // PUT: api/Votes/name
    [HttpPut("{name}")]
    public async Task<IActionResult> Put(string name)
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);
        long partitionKey = this.GetPartitionKey(name);
        string proxyUrl = $"{proxyAddress}/api/VoteData/{name}?PartitionKey={partitionKey}&PartitionKind=Int64Range";

        StringContent putContent = new StringContent($"{{ 'name' : '{name}' }}", Encoding.UTF8, "application/json");
        putContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");

        using (HttpResponseMessage response = await this.httpClient.PutAsync(proxyUrl, putContent))
        {
            return new ContentResult()
            {
                StatusCode = (int) response.StatusCode,
                Content = await response.Content.ReadAsStringAsync()
            };
        }
    }

    // DELETE: api/Votes/name
    [HttpDelete("{name}")]
    public async Task<IActionResult> Delete(string name)
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);
        long partitionKey = this.GetPartitionKey(name);
        string proxyUrl = $"{proxyAddress}/api/VoteData/{name}?PartitionKey={partitionKey}&PartitionKind=Int64Range";

        using (HttpResponseMessage response = await this.httpClient.DeleteAsync(proxyUrl))
        {
            if (response.StatusCode != System.Net.HttpStatusCode.OK)
            {
                return this.StatusCode((int) response.StatusCode);
            }
        }

        return new OkResult();
    }


    /// <summary>
    /// Constructs a reverse proxy URL for a given service.
    /// Example: http://localhost:19081/VotingApplication/VotingData/
    /// </summary>
    /// <param name="serviceName"></param>
    /// <returns></returns>
    private Uri GetProxyAddress(Uri serviceName)
    {
        return new Uri($"http://localhost:19081{serviceName.AbsolutePath}");
    }

    /// <summary>
    /// Creates a partition key from the given name.
    /// Uses the zero-based numeric position in the alphabet of the first letter of the name (0-25).
    /// </summary>
    /// <param name="name"></param>
    /// <returns></returns>
    private long GetPartitionKey(string name)
    {
        return Char.ToUpper(name.First()) - 'A';
    }
}
```
<a id="walkthrough" name="walkthrough_anchor"></a>

## <a name="walk-through-the-voting-sample-application"></a>Tutorial de la aplicación de ejemplo de votación
La aplicación de votación consta de dos servicios:
- Servicio front-end web (VotingWeb): front-end web de ASP.NET Core, que ofrece servicio a la página web y expone API web para comunicarse con el servicio back-end.
- Servicio back-end (VotingData): servicio web de ASP.NET Core, que expone una API para almacenar los resultados de una votación en un diccionario confiable que se conserva en el disco.

![Diagrama de la aplicación](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

Al votar en la aplicación, se producen los eventos siguientes:
1. JavaScript envía la solicitud de votación a la API web del servicio front-end web como una solicitud HTTP PUT.

2. El servicio front-end web usa un proxy para localizar y reenviar una solicitud HTTP PUT al servicio back-end.

3. El servicio back-end recibe la solicitud entrante y almacena el resultado actualizado en un diccionario confiable, que se replica en varios nodos del clúster y se conserva en el disco. Todos los datos de la aplicación se almacenan en el clúster, por lo que no se necesita una base de datos.

## <a name="debug-in-visual-studio"></a>Depurar en Visual Studio
Cuando se depura una aplicación en Visual Studio, se usa un clúster de desarrollo de Service Fabric local. Tiene la opción de ajustar la experiencia de depuración a su escenario. En esta aplicación, los datos se almacenan en el servicio back-end mediante un diccionario confiable. Visual Studio quita la aplicación de forma predeterminada cuando se detiene el depurador. Cuando se quita la aplicación, los datos del servicio back-end también se quitan. Para conservar los datos entre sesiones de depuración, puede cambiar el **modo de depuración de la aplicación** como una propiedad del proyecto **Voting** en Visual Studio.

Para ver lo que ocurre en el código, siga estos pasos:
1. Abra el archivo **VotesController.cs** y establezca un punto de interrupción en el método **Put** (línea 63) de la API web. Puede buscar el archivo en el Explorador de soluciones de Visual Studio.

2. Abra el archivo **VoteDataController.cs** y establezca un punto de interrupción en el método **Put** (línea 53) de esta API web.

3. Vuelva al explorador y haga clic en una opción de votación o agregue una nueva opción de votación. Alcanzará el primer punto de interrupción del controlador de API del front-end web.
    
    1. Aquí es donde el código JavaScript del explorador envía una solicitud al controlador de API web del servicio front-end.
    
    ![Incorporación del servicio front-end de voto](./media/service-fabric-tutorial-create-dotnet-app/addvote-frontend.png)

    2. Primero, construya la dirección URL para el valor de ReverseProxy del servicio back-end **(1)**.
    3. A continuación, envíe la solicitud PUT de HTTP a ReverseProxy **(2)**.
    4. Por último, se devuelve la respuesta del servicio back-end al cliente **(3)**.

4. Pulse **F5** para continuar.
    1. Ya está en el punto de interrupción del servicio back-end.
    
    ![Incorporación del servicio back-end de voto](./media/service-fabric-tutorial-create-dotnet-app/addvote-backend.png)

    2. En la primera línea del método **(1)** se usa `StateManager` para obtener o agregar un diccionario confiable denominado `counts`.
    3. Todas las interacciones con valores de un diccionario confiable requieren una transacción. Esta instrucción using **(2)** crea dicha transacción.
    4. Después, en la transacción, se actualiza el valor de la tecla correspondiente para la opción de votación y se confirma la operación **(3)**. Una vez que se devuelve el método Commit, los datos se actualizan en el diccionario y se replican en otros nodos del clúster. Los datos ahora están almacenados de forma segura en el clúster y el servicio back-end puede conmutar por error a otros nodos, mientras sigue teniendo los datos disponibles.
5. Pulse **F5** para continuar.

Para detener la sesión de depuración, pulse **Maýus+F5**.


## <a name="next-steps"></a>Pasos siguientes
En esta parte del tutorial, ha aprendido a:

> [!div class="checklist"]
> * Crear un servicio de ASP.NET Core Web API como un servicio de confianza con estado
> * Crear un servicio de ASP.NET Core Web Application como un servicio de confianza sin estado
> * Usar el proxy inverso para comunicarse con el servicio con estado

Avance hasta el siguiente tutorial:
> [!div class="nextstepaction"]
> [Implementación de la aplicación en Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md)

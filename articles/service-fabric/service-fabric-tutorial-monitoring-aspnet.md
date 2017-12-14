---
title: "Supervisión y diagnóstico de servicios ASP.NET Core en Azure Service Fabric | Microsoft Docs"
description: "Aprenda a configurar la supervisión y el diagnóstico para una aplicación ASP.NET Core en Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/14/2017
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: ce854a3dc41dec69c3f8de245a03d55a2354335f
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="monitor-and-diagnose-an-aspnet-core-application-on-service-fabric"></a>Supervisar y diagnosticar una aplicación ASP.NET Core en Service Fabric
Este tutorial es la cuarta parte de una serie. Describe los pasos necesarios para configurar la supervisión y el diagnóstico de una aplicación ASP.NET Core que se ejecuta en un clúster de Service Fabric con Application Insights. Recopilaremos datos de telemetría de la aplicación desarrollada en la primera parte del tutorial, [Crear una aplicación de .NET Service Fabric](service-fabric-tutorial-create-dotnet-app.md). 

En la cuarta parte de la serie de tutoriales, aprenderá a:
> [!div class="checklist"]
> * Configurar Application Insights para la aplicación
> * Recopilar datos de telemetría de respuesta para hacer un seguimiento de la comunicación basada en HTTP entre servicios
> * Usar la característica Mapa de aplicación en Application Insights
> * Agregar eventos personalizados con la API de Application Insights

En esta serie de tutoriales, se aprende a:
> [!div class="checklist"]
> * [Crear una aplicación de .NET Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
> * [Implementar la aplicación en un clúster remoto](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Configurar CI/CD con Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * Configurar la supervisión y el diagnóstico para la aplicación

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial:
- Si no tiene ninguna suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Instale Visual Studio 2017](https://www.visualstudio.com/) y las cargas de trabajo de **desarrollo de Azure** y de **desarrollo web y de ASP.NET**.
- [Instale el SDK de Service Fabric](service-fabric-get-started.md).

## <a name="download-the-voting-sample-application"></a>Descarga de la aplicación de ejemplo de votación
Si no compiló la aplicación de ejemplo de votación en la [primera parte de esta serie de tutoriales](service-fabric-tutorial-create-dotnet-app.md), puede descargarla. En un terminal o ventana Comandos, ejecute el comando siguiente para clonar el repositorio de la aplicación de ejemplo en la máquina local.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="set-up-an-application-insights-resource"></a>Configurar un recurso de Application Insights
Application Insights es la plataforma de administración del rendimiento de aplicaciones de Azure y la plataforma recomendada de Service Fabric para la supervisión y el diagnóstico de aplicaciones. Para crear un recurso de Application Insights, vaya a [Azure Portal](https://portal.azure.com). Haga clic en **Nuevo** en el menú de navegación izquierdo para abrir Azure Marketplace. Haga clic en **Supervisión y administración** y, a continuación, en **Application Insights**.

![Crear un recurso nuevo de AI](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource.png)

Ahora deberá rellenar la información necesaria acerca de los atributos del recurso que se va a crear. Especifique un *Nombre*, *Grupo de recursos*, y *Suscripción* adecuados. Defina la *Ubicación* donde quiera implementar el clúster de Service Fabric en el futuro. En este tutorial, se implementará la aplicación en un clúster local, de modo que el campo *Ubicación* es irrelevante. El *Tipo de aplicación* debe dejarse como "Aplicación web ASP.NET." 

![Atributos de recurso de AI](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource-attrib.png)

Una vez completada la información necesaria, haga clic en **Crear** para aprovisionar el recurso. Esto tardará, aproximadamente, un minuto. 
<!-- When completed, navigate to the newly deployed resource, and find the "Instrumentation Key" (visible in the "Essentials" drop down section). Copy it to clipboard, since we will need it in the next step. -->

## <a name="add-application-insights-to-the-applications-services"></a>Agregar Application Insights a los servicios de la aplicación

Inicie Visual Studio de 2017 con privilegios elevados. Para hacerlo, haga clic con el botón derecho en el icono de Visual Studio del menú Inicio y seleccione **Ejecutar como administrador**. Haga clic en **Archivo** > **Abrir** > **Proyecto o solución** y desplácese hasta la aplicación Voting (creada en la primera parte del tutorial o clonada de GIT). Abra *Voting.sln* y, si se le pide que restaure los paquetes NuGet de la aplicación, haga clic en **Sí**.

Siga estos pasos para configurar Application Insights para los servicios VotingWeb y VotingData:
1. Haga clic con el botón derecho en el nombre del servicio y, a continuación, en **Configurar Application Insights...** .

    ![Configurar AI](./media/service-fabric-tutorial-monitoring-aspnet/configure-ai.png)

2. Haga clic en **Comenzar gratis**.
3. Inicie sesión en su cuenta (con la que configuró la suscripción a Azure) y seleccione la suscripción en la que creó el recurso de Application Insights. Busque el recurso en la sección *Recurso existente de Application Insights*, en la lista desplegable "Recurso". Haga clic en **Registrar** para agregar Application Insights al servicio.

    ![Registrar AI](./media/service-fabric-tutorial-monitoring-aspnet/register-ai.png)

4. Haga clic en **Finalizar** cuando se haya completado la acción en el cuadro de diálogo que aparece.
    
Asegúrese de seguir los pasos anteriores para **ambos** servicios de la aplicación para finalizar la configuración de Application Insights para la aplicación. Se utiliza el mismo recurso de Application Insights para ambos servicios para ver las solicitudes entrantes y salientes, y la comunicación entre servicios.

## <a name="add-the-microsoftapplicationinsightsservicefabricnative-nuget-to-the-services"></a>Agregue el paquete NuGet Microsoft.ApplicationInsights.ServiceFabric.Native a los servicios

Application Insights tiene dos paquetes NuGet específicos de Service Fabric que se pueden usar, en función del escenario. Uno se usa con los servicios nativos de Service Fabric y el otro, con contenedores y archivos ejecutables invitados. En este caso, usaremos el paquete NuGet Microsoft.ApplicationInsights.ServiceFabric.Native para aprovechar la información del contexto de servicio que aporta. Para obtener más información sobre el SDK de Application Insights y los paquetes NuGet específicos de Service Fabric, consulte [Microsoft Application Insights for Service Fabric](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/develop/README.md) (Microsoft Application Insights para Service Fabric). 

Estos son los pasos necesarios para configurar el paquete NuGet:
1. Haga clic con el botón derecho en la **solución 'Voting'**, en la parte superior del Explorador de soluciones, y, a continuación, en **Administrar paquetes NuGet para la solución...**.
2. Haga clic en **Examinar** en el menú de navegación superior de la ventana "NuGet - Solución" y active la casilla **Incluir versión preliminar** junto a la barra de búsqueda.
3. Busque `Microsoft.ApplicationInsights.ServiceFabric.Native` y haga clic en el paquete NuGet adecuado.
4. A la derecha, haga clic en las dos casillas de verificación situadas junto a los dos servicios de la aplicación, **VotingWeb** y **VotingData**, y haga clic en **Instalar**.
    ![Registro de AI completado](./media/service-fabric-tutorial-monitoring-aspnet/aisdk-sf-nuget.png)
5. Haga clic en **Aceptar** en el cuadro de diálogo *Revisar cambios* que aparece y apruebe la *Aceptación de licencia*. Esto completará la adición del paquete NuGet a los servicios.
6. Ahora debe configurar el inicializador de telemetría en los dos servicios. Para hacerlo, abra *VotingWeb.cs* y *VotingData.cs*. Para ambos, siga estos dos pasos:
    1. Agréguelos *mediante* instrucciones en la parte superior de cada archivo  *\<NombreServicio> .cs*:
    
    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.ServiceFabric;
    ```
    
    2. En las instrucciones *return* anidadas de *CreateServiceInstanceListeners()* o *CreateServiceReplicaListeners()*, en *ConfigureServices*  >  *services*, entre los dos servicios Singleton declarados, agregue: `.AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))`.
    Esto agregará el *Contexto de servicio* a la telemetría, lo que le permitirá comprender mejor el origen de la telemetría de Application Insights. La instrucción *return* anidada de *VotingWeb.cs* debería tener este aspecto:
    
    ```csharp
    return new WebHostBuilder().UseWebListener()
        .ConfigureServices(
            services => services
                .AddSingleton<StatelessServiceContext>(serviceContext)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))
                .AddSingleton<HttpClient>())
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
        .UseUrls(url)
        .Build();
    ```

    Del mismo modo, en *VotingData.cs*, debe tener:

    ```csharp
    return new WebHostBuilder()
        .UseKestrel()
        .ConfigureServices(
            services => services
                .AddSingleton<StatefulServiceContext>(serviceContext)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))
                .AddSingleton<IReliableStateManager>(this.StateManager))
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
        .UseUrls(url)
        .Build();
    ```

En este punto, ya está listo para implementar la aplicación. Haga clic en **Iniciar** en la parte superior (o en **F5**) y Visual Studio compilará y empaquetará la aplicación, configurará el clúster local e implementará en este la aplicación. 

Cuando la aplicación finalice la implementación, diríjase a [localhost: 8080](localhost:8080), donde podrá ver la aplicación de ejemplo de una sola página Voting. Vote distintos elementos de su elección para crear datos de ejemplo y telemetría. Yo elegí el tema de los postres.

![Todos los votos de ejemplo](./media/service-fabric-tutorial-monitoring-aspnet/vote-sample.png)

Si quiere, puede seleccionar *Remove* (Quitar) para eliminar algunas opciones de voto. Así mismo, cuando acabe, puede agregar algunos votos.

## <a name="view-telemetry-and-the-app-map-in-application-insights"></a>Ver datos de telemetría y Mapa de la aplicación en Application Insights 

Vaya al recurso de Application Insights de Azure Portal y, en la barra de navegación izquierda del recurso, haga clic en **Versiones preliminares**, en la sección *Configurar*. **Active** la *Asignación de aplicaciones de varios roles* en la lista de versiones preliminares disponibles.

![Habilitar Mapa de la aplicación en AI](./media/service-fabric-tutorial-monitoring-aspnet/ai-appmap-enable.png)

Haga clic en **Introducción** para volver a la página principal del recurso. A continuación, haga clic en **Buscar** en la parte superior para ver los seguimientos entrantes. Los seguimientos tardan unos minutos en aparecer en Application Insights. En caso de no ver ninguno, espere un momento y seleccione el botón **Actualizar** en la parte superior.
![Ver los seguimientos de AI](./media/service-fabric-tutorial-monitoring-aspnet/ai-search.png)

Al desplazarse hacia abajo desde la ventana *Buscar*, se mostrarán todos los datos de telemetría entrantes que obtiene de forma predeterminada Application Insights. Para cada acción que haya realizado en la aplicación Voting, debería haber una solicitud PUT saliente desde *VotingWeb* (PUT Votes/Put [nombre]) y una solicitud PUT entrante desde *VotingData* (PUT VoteData/Put [nombre]), seguidas de un par de solicitudes GET para actualizar los datos que se muestran. También habrá un seguimiento de la dependencia de HTTP en localhost, puesto que se trata de solicitudes HTTP. A continuación se incluye un ejemplo de lo que verá sobre cómo se agrega un voto: ![seguimiento de solicitud de ejemplo de AI](./media/service-fabric-tutorial-monitoring-aspnet/sample-request.png)

Puede hacer clic en uno de los seguimientos para ver más detalles sobre este. Application Insights proporciona información útil acerca de la solicitud, como el *Tiempo de respuesta* y la *URL de solicitud*. Además, puesto que agregó un paquete de NuGet específico de Service Fabric, también obtendrá datos sobre la aplicación en el contexto de un clúster de Service Fabric en la sección *Datos personalizados* más adelante. Esto incluye el contexto de servicio, para que pueda ver los datos de *PartitionID* y *ReplicaId* del origen de la solicitud y localizar mejor problemas al diagnosticar errores en la aplicación.

![Detalles de seguimiento de AI](./media/service-fabric-tutorial-monitoring-aspnet/trace-details.png)

Además, dado que habilitamos el Mapa de la aplicación, en la página *Introducción*, al hacer clic en el icono **Mapa de la aplicación**, se muestran los dos servicios conectados.

![Detalles de seguimiento de AI](./media/service-fabric-tutorial-monitoring-aspnet/app-map.png)

El Mapa de la aplicación le puede ayudar a entender mejor la topología de la aplicación, especialmente a medida que empiece a agregar distintos servicios que funcionan juntos. También proporciona datos básicos sobre los índices de éxito de las solicitudes, y puede ayudarle a diagnosticar solicitudes con error y a comprender qué salió mal. Para obtener más información sobre el Mapa de la aplicación, consulte [Mapa de aplicación en Application Insights](../application-insights/app-insights-app-map.md).

## <a name="add-custom-instrumentation-to-your-application"></a>Agregar instrumentación personalizada a la aplicación

Aunque Application Insights proporciona una gran cantidad de datos de telemetría de forma predeterminada, puede agregar una instrumentación más personalizada. Se puede basar en las necesidades empresariales o en mejorar el diagnóstico cuando hay algún problema en la aplicación. Application Insights tiene una API para ingerir métricas y eventos personalizados. Puede obtener más información [aquí](../application-insights/app-insights-api-custom-events-metrics.md).

A continuación, agregaremos algunos eventos personalizados a *VoteDataController.cs* (en *VotingData* > *Controllers*) para realizar el seguimiento de cuándo se agregan y eliminan votos del elemento *votesDictionary* subyacente. 
1. Agregue `using Microsoft.ApplicationInsights;` al final de otras instrucciones using.
2. Declare un nuevo elemento *TelemetryClient* al principio de la clase, bajo la creación de *IReliableStateManager*: `private TelemetryClient telemetry = new TelemetryClient();`.
3. En la función *Put()*, agregue un evento que confirme que se ha agregado un voto. Agregue `telemetry.TrackEvent($"Added a vote for {name}");` después de completar la transacción, justo antes de la instrucción return *OkResult*.
4. En *Delete()*, hay un elemento "if/else" basado en la condición de que *votesDictionary* contenga votos para una opción de voto determinada. 
    1. Agregue un evento que confirme la eliminación de un voto en la instrucción *if*, después de *await tx.CommitAsync()*:`telemetry.TrackEvent($"Deleted votes for {name}");`
    2. Agregue un evento para mostrar que la eliminación no tuvo lugar en la instrucción *else*, antes de la instrucción return:`telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");`

Este es un ejemplo del aspecto que podrían tener las funciones *Put()* y *Delete()* después de agregar los eventos:

```csharp
// PUT api/VoteData/name
[HttpPut("{name}")]
public async Task<IActionResult> Put(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
        await tx.CommitAsync();
    }

    telemetry.TrackEvent($"Added a vote for {name}");
    return new OkResult();
}

// DELETE api/VoteData/name
[HttpDelete("{name}")]
public async Task<IActionResult> Delete(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        if (await votesDictionary.ContainsKeyAsync(tx, name))
        {
            await votesDictionary.TryRemoveAsync(tx, name);
            await tx.CommitAsync();
            telemetry.TrackEvent($"Deleted votes for {name}");
            return new OkResult();
        }
        else
        {
            telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");
            return new NotFoundResult();
        }
    }
}
```

Cuando haya finalizado de realizar estos cambios, **inicie** la aplicación para compilar e implementar su versión más reciente. Cuando finalice la implementación de la aplicación, vaya a [localhost: 8080](localhost:8080), y agregue y elimine algunas opciones de voto. A continuación, vuelva al recurso de Application Insights para ver los seguimientos de la última ejecución (como antes, los seguimientos pueden tardar de 1 a 2 minutos en aparecer en Application Insights). Para todos los votos que agregó y eliminó, ahora debería ver "Custom event" junto con la telemetría de respuesta. 

![eventos personalizados](./media/service-fabric-tutorial-monitoring-aspnet/custom-events.png)

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha aprendido cómo:
> [!div class="checklist"]
> * Configurar Application Insights para la aplicación
> * Recopilar datos de telemetría de respuesta para hacer un seguimiento de la comunicación basada en HTTP entre servicios
> * Usar la característica Mapa de aplicación en Application Insights
> * Agregar eventos personalizados con la API de Application Insights

Ahora que ha completado la configuración de la supervisión y el diagnóstico para la aplicación ASP.NET, pruebe lo siguiente:
- [Explorar la supervisión y el diagnóstico en Service Fabric](service-fabric-diagnostics-overview.md)
- [Análisis de eventos de Service Fabric con Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
- Para obtener más información acerca de Application Insights, consulte [Documentación de Application Insights](https://docs.microsoft.com/azure/application-insights/)

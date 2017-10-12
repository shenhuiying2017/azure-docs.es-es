---
title: "Anotaciones de la versión de Application Insights | Microsoft Docs"
description: "Agregue marcadores de implementación o compilación a sus gráficos del Explorador de métricas en Application Insights."
services: application-insights
documentationcenter: .net
author: CFreemanwa
manager: carmonm
ms.assetid: 23173e33-d4f2-4528-a730-913a8fd5f02e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: bwren
ms.openlocfilehash: f7eb2f3cba535eb64db5544c498289c9e895987a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Anotaciones sobre gráficos de métricas en Application Insights
En las anotaciones sobre gráficos del [Explorador de métricas](app-insights-metrics-explorer.md) se muestra donde ha implementado una nueva compilación u otros eventos importantes. Permiten ver fácilmente si los cambios tuvieron algún efecto en el rendimiento de la aplicación. Se pueden crear automáticamente con el [sistema de compilación de Visual Studio Team Services](https://www.visualstudio.com/en-us/get-started/build/build-your-app-vs). También puede crear anotaciones para marcar los eventos que prefiera si los [crea desde PowerShell](#create-annotations-from-powershell).

![Ejemplo de anotaciones con correlación visible con el tiempo de respuesta del servidor](./media/app-insights-annotations/00.png)



## <a name="release-annotations-with-vsts-build"></a>Publicar anotaciones con una compilación de VSTS

Las anotaciones de versión son una característica del servicio de versiones y compilaciones basado en la nube de Visual Studio Team Services. 

### <a name="install-the-annotations-extension-one-time"></a>Instalación de la extensión Anotaciones (una vez)
Para poder crear anotaciones de versión, debe instalar una de las muchas extensiones de Team Service disponibles en Visual Studio Marketplace.

1. Inicie sesión en su proyecto de [Visual Studio Team Services](https://www.visualstudio.com/en-us/get-started/setup/sign-up-for-visual-studio-online).
2. En Visual Studio Marketplace, [obtenga la extensión de anotaciones de la versión](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)y agréguela a su cuenta de Team Services.

![En la parte superior derecha de página web de Team Services, abra Marketplace. Seleccione Visual Studio Team Services y después, en Generar y versión, elija Ver más.](./media/app-insights-annotations/10.png)

Esto solo lo tiene que hacer una vez en su cuenta de Visual Studio Team Services. Las anotaciones de versión se pueden configurar ahora para cualquier proyecto de su cuenta. 

### <a name="configure-release-annotations"></a>Configurar anotaciones de versión

Necesita obtener una clave de API separada para cada plantilla de versión de VSTS.

1. Inicie sesión en el [Portal de Microsoft Azure](https://portal.azure.com) y abra el recurso de Application Insights que supervisa su aplicación. (O bien, [cree uno ahora](app-insights-overview.md), si aún no lo ha hecho).
2. Abra **Acceso a la API**, **Id. de Application Insights**.
   
    ![En portal.azure.com, abra el recurso de Application Insights y elija Configuración. Abra el Acceso a la API. Copie el identificador de aplicación.](./media/app-insights-annotations/20.png)

4. En una ventana de explorador independiente, abra (o cree) la plantilla de versión que administra sus implementaciones de Visual Studio Team Services. 
   
    Agregue una tarea y seleccione la tarea de anotación de versión de Application Insights en el menú.
   
    Pegue el **Id. de la aplicación** que ha copiado de la hoja Acceso de API.
   
    ![En Visual Studio Team Services, abra Versión, seleccione una definición de versión y elija Editar. Haga clic en Agregar tarea y seleccione Anotación de versión de Application Insights. Pegue el Id. de Application Insights.](./media/app-insights-annotations/30.png)
4. Establezca el campo **APIKey** en una variable `$(ApiKey)`.

5. Vuelva a la ventana de Azure, cree una clave de API y realice una copia de esta.
   
    ![En la hoja Acceso a la API de la ventana de Azure, haga clic en Crear clave de API. Proporcione un comentario, compruebe las anotaciones de escritura y haga clic en Generar clave. Copie la clave nueva.](./media/app-insights-annotations/40.png)

6. Abra la pestaña Configuración de la plantilla de la versión.
   
    Cree una definición de variable para `ApiKey`.
   
    Pegue su clave de API a la definición de la variable de ApiKey.
   
    ![En la ventana de Team Services, seleccione la pestaña Configuración y haga clic en Agregar variable. Establezca el nombre en ApiKey y en el Valor, pegue la clave que acaba de generar y haga clic en el icono de bloqueo.](./media/app-insights-annotations/50.png)
7. Por último, haga clic en **Guardar** para guardar la definición de versión.


## <a name="view-annotations"></a>Ver anotaciones
Ahora, cuando se utilice la plantilla de versión para implementar una nueva versión, se enviará una anotación a Application Insights. Las anotaciones se mostrarán en los gráficos del Explorador de métricas.

Haga clic en cualquier marcador de anotación para abrir los detalles de la versión, incluidos el solicitante, la bifurcación de control de código fuente, la definición de la versión, el entorno y mucho más.

![Haga clic en cualquier marcador de anotación de la versión.](./media/app-insights-annotations/60.png)

## <a name="create-custom-annotations-from-powershell"></a>Crear anotaciones personalizadas desde PowerShell
También puede crear anotaciones desde cualquier proceso que desee (sin usar VS Team System). 


1. Realice una copia local del [script de PowerShell en GitHub](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).

2. Obtenga el id. de la aplicación y cree una clave de API desde la hoja Acceso a la API.

3. Llame al script de esta forma:

```PS

     .\CreateReleaseAnnotation.ps1 `
      -applicationId "<applicationId>" `
      -apiKey "<apiKey>" `
      -releaseName "<myReleaseName>" `
      -releaseProperties @{
          "ReleaseDescription"="a description";
          "TriggerBy"="My Name" }
```

El script se puede modificar fácilmente (por ejemplo, para crear anotaciones para el pasado).

## <a name="next-steps"></a>Pasos siguientes

* [Crear elementos de trabajo](app-insights-diagnostic-search.md#create-work-item)
* [Automation con PowerShell](app-insights-powershell.md)

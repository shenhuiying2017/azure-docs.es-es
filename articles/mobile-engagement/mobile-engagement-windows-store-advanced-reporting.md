---
title: Informes avanzados de aplicaciones universales de Windows con Mobile Engagement
description: "Cómo integrar Azure Mobile Engagement con aplicaciones Windows Universal"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: ea5030bf-73ac-49b7-bc3e-c25fc10e945a
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: feac309db1ffce0945012e293bfc1df417aed876
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="advanced-reporting-with-the-windows-universal-apps-engagement-sdk"></a>Informes avanzados con el SDK de Engagement para aplicaciones la contratación de aplicaciones universales de Windows
> [!div class="op_single_selector"]
> * [Windows universal](mobile-engagement-windows-store-advanced-reporting.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-reporting.md)
> 
> 

Este tema describe los escenarios de informes adicionales en la aplicación universal de Windows. Estos escenarios incluyen opciones que puede aplicar a la aplicación creada en la [Introducción a Azure Mobile Engagement para aplicaciones universales de Windows](mobile-engagement-windows-store-dotnet-get-started.md) .

## <a name="prerequisites"></a>Requisitos previos
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

Antes de comenzar este tutorial, primero debe realizar el tutorial [Introducción a Azure Mobile Engagement para aplicaciones universales de Windows](mobile-engagement-windows-store-dotnet-get-started.md) . Este tutorial explica las opciones adicionales que puede elegir.

## <a name="specifying-engagement-configuration-at-runtime"></a>Especificación de la configuración de Engagement en tiempo de ejecución
La configuración de Engagement se centraliza en el archivo `Resources\EngagementConfiguration.xml` del proyecto, que es donde se especificó en el tema [Introducción a Azure Mobile Engagement para aplicaciones universales de Windows](mobile-engagement-windows-store-dotnet-get-started.md) .

Sin embargo, también puede especificarlo en tiempo de ejecución llamando al método siguiente antes de inicializar el agente de Engagement:

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);



## <a name="recommended-method-overload-your-page-classes"></a>Método recomendado: sobrecargar las clases `Page`
Para activar el informe de todos los registros que Engagement necesita para calcular las estadísticas de usuarios, sesiones, actividades, bloqueos y aspectos técnicos, puede hacer que todas las subclases `Page` hereden de las clases `EngagementPage`.

Este es un ejemplo para una página de la aplicación. Puede hacer lo mismo para todas las páginas de la aplicación.

### <a name="c-source-file"></a>Archivo de código fuente C#
Modifique el archivo `.xaml.cs` de página:

* Agregue las instrucciones `using`:
  
      using Microsoft.Azure.Engagement;
* Reemplace `Page` por `EngagementPage`:

**Sin Engagement:**

        namespace Example
        {
          public sealed partial class ExamplePage : Page
          {
            [...]
          }
        }

**Con Engagement:**

        using Microsoft.Azure.Engagement;

        namespace Example
        {
          public sealed partial class ExamplePage : EngagementPage
          {
            [...]
          }
        }

> [!IMPORTANT]
> Si la página invalida el método `OnNavigatedTo`, no olvide llamar a `base.OnNavigatedTo(e)`. De lo contrario, no se informará de la actividad (`EngagementPage` llama a `StartActivity` en su método `OnNavigatedTo`).
> 
> 

### <a name="xaml-file"></a>Archivo XAML
Modifique el archivo `.xaml` de página:

* Agregue a las declaraciones de espacios de nombres:
  
      xmlns:engagement="using:Microsoft.Azure.Engagement"
* Reemplace `Page` por `engagement:EngagementPage`:

**Sin Engagement:**

        <Page>
            <!-- layout -->
            ...
        </Page>

**Con Engagement:**

        <engagement:EngagementPage
            xmlns:engagement="using:Microsoft.Azure.Engagement">
            <!-- layout -->
            ...
        </engagement:EngagementPage >

### <a name="override-the-default-behaviour"></a>Invalidar el comportamiento predeterminado
De forma predeterminada, el nombre de clase de la página se informa como el nombre de actividad, sin más. Si la clase usa el sufijo "Page", Engagement lo quitará.

Para invalidar el comportamiento predeterminado del nombre, agregue este código:

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

Para notificar información adicional con su actividad, agregue este código:

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
          /* your code */
          return extra;
        }

Estos métodos se invocan desde el método `OnNavigatedTo` de la página.

### <a name="alternate-method-call-startactivity-manually"></a>Método alternativo: llamar a `StartActivity()` manualmente
Si no puede o no quiere sobrecargar las clases `Page`, en su lugar, puede iniciar las actividades mediante una llamada directa a los métodos `EngagementAgent`.

Se recomienda llamar a `StartActivity` dentro del método `OnNavigatedTo` de su página.

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [!IMPORTANT]
> Asegúrese de finalizar la sesión correctamente.
> 
> El SDK de Windows Universal llama automáticamente al método `EndActivity` cuando se cierra la aplicación. Por lo tanto, es **MUY** recomendable llamar al método `StartActivity` cada vez que cambie la actividad del usuario y no llamar **NUNCA** al método `EndActivity`. Este método notifica al servidor de Engagement que el usuario actual ha salido de la aplicación, lo cual afecta a todos los registros de la aplicación.
> 
> 

## <a name="advanced-reporting"></a>Informes avanzados
Opcionalmente, es aconsejable informar de eventos, errores y trabajos de aplicación específicos. Para ello, use los otros métodos que se encuentran en la clase `EngagementAgent`. La API de Engagement permite usar todas las funcionalidades avanzadas de Engagement.

Para obtener más información, consulte [Cómo usar la API de etiquetado avanzado de Mobile Engagement en la aplicación Windows Universal](mobile-engagement-windows-store-use-engagement-api.md).


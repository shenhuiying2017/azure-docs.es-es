<properties urlDisplayName="Get Started with Mobile Analytics" pageTitle="Introducción al análisis móvil | Centro de desarrollo móvil" metaKeywords="" description="Get Started with Mobile Analytics." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get Started with Mobile Analytics" authors="mahender" manager="dwrede"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="10/10/2014" ms.author="mahender" />

# Introducción al análisis móvil (Capptain)

<div class="dev-center-tutorial-selector sublanding">
<a href="/en-us/documentation/articles/mobile-services-ios-get-started-mobile-analytics" title="iOS" class="current">iOS</a>
</div>

En este tutorial, agregará funciones de análisis móvil a su aplicación mediante [Capptain]. Los análisis móviles le permiten determinar el modo en que los usuarios interactúan con su aplicación y cuáles son las secciones que están impulsando la mayor actividad. Para comenzar a obtener estos datos, instrumentaremos su aplicación con el SDK de Capptain.


>[AZURE.NOTE] Capptain.com, propiedad de Microsoft, ofrece análisis para aplicaciones móviles, gratuitos para un máximo de 100.000 usuarios activos, para los clientes de nivel estándar de Servicios móviles de Azure. Para aprovechar esta oferta, póngase en contacto con nosotros en mobileservices@microsoft.com y le daremos más información. En el siguiente tutorial se resumen las características y funciones de Capptain.com y se proporcionan instrucciones sobre cómo usarlas.


Este tutorial le guiará a través de estos pasos básicos:

1. [Inicialización del SDK de Capptain]
2. [Sobrecarga de UIViewController]

Este tutorial requiere lo siguiente:

* Una cuenta de [Capptain]
* Una aplicación de [nivel estándar de Servicios móviles]

## <a name="initialize"></a>Inicialización del SDK de Capptain

1. Vaya a la página **Detalles de la aplicación** de su aplicación registrada en Capptain. Seleccione la pestaña SDK y descargue el paquete.

2. En XCode, agregue el SDK de Capptain a su proyecto; para ello, haga clic con el botón secundario en el proyecto y seleccione "Agregar archivos a..." Elija la carpeta CapptainSDK.

3. Seleccione su proyecto. En la pestaña **Fases de compilación**, seleccione **Vincular binarios con bibliotecas** y agregue los siguientes marcos de trabajo:
    * AdSupport.framework: establezca el vínculo como Opcional.
    * SystemConfiguration.framework
    * CoreTelephony.framework
    * CFNetwork.framework
    * CoreLocation.framework
    * libxml2.dylib

    >[AZURE.NOTE] El marco de trabajo AdSupport se puede quitar. Capptain necesita este marco para recopilar el IDFA. No obstante, la recopilación de IDFA se puede deshabilitar para cumplir la directiva de Apple con respecto a este Id.

4. En el archivo de implementación de delegación de la aplicación, importe el agente de Capptain.


        #import "CapptainAgent.h"


5. En "applicationDidFinishLaunching:" o "application:didFinishLaunchingWithOptions:", inicialice el agente Capptain pasando "registerapp:identifiedby:" con el identificador de la aplicación y la clave del SDK.

         - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
          [...]
          [CapptainAgent registerApp:@"YOUR_APPID" identifiedBy:@"YOUR_SDK_KEY"];
          [...]
        }

## <a name="instrument"></a>Sobrecarga de UIViewController

1. Encuentre cada elemento secundario de "UIViewController" en su proyecto y asegúrese de que cada uno herede de "CapptainViewController".

        #import <UIKit/UIKit.h>
        #import "CapptainViewController.h"

        // formerly @interface Tab1ViewController : UIViewController<UITextFieldDelegate>
        @interface Tab1ViewController : CapptainViewController<UITextFieldDelegate> {
          UITextField* myTextField1;
          UITextField* myTextField2;
        }

        @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
        @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

2. Encuentre cada elemento secundario de "UITableViewController" en su proyecto y asegúrese de que cada uno herede de "CapptainTableViewController".

    Su aplicación está ya configurada para enviar datos de análisis a Capptain.

## Pasos siguientes
Puede obtener más información sobre lo que Capptain puede hacer por su aplicación en [http://www.capptain.com](http://www.capptain.com)

<!-- Anchors. -->
[Inicialización del SDK de Capptain]: #initialize
[Sobrecarga de UIViewController]: #instrument


<!-- URLs. -->
[Capptain]: http://www.capptain.com
[Nivel estándar de Servicios móviles]: /en-us/pricing/details/mobile-services/

<!--HONumber=35_1-->

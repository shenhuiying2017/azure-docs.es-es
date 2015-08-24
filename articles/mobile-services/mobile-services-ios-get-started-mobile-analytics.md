<properties 
	pageTitle="Introducción al análisis móvil | Microsoft Azure" 
	description="Introducción al análisis móvil" 
	documentationCenter="ios" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="mahender"/>

# Introducción al análisis móvil (Capptain)

En este tutorial, agregará funciones de análisis móvil a su aplicación mediante [Capptain]. Los análisis móviles le permiten determinar el modo en que los usuarios interactúan con su aplicación y cuáles son las secciones que están impulsando la mayor actividad. Para comenzar a obtener estos datos, instrumentaremos su aplicación con el SDK de Capptain.


>[AZURE.NOTE]Capptain.com, propiedad de Microsoft, ofrece análisis para aplicaciones móviles, gratuitos para un máximo de 100.000 usuarios activos, para los clientes de nivel estándar de Servicios móviles de Azure. Para aprovechar esta oferta, póngase en contacto con nosotros en mobileservices@microsoft.com y le daremos más información. En el siguiente tutorial se resumen las características y funciones de Capptain.com y se proporcionan instrucciones sobre cómo usarlas.


Este tutorial le guiará a través de estos pasos básicos:

1. [Inicialización del SDK de Capptain]
2. [Sobrecarga de UIViewController]

Este tutorial requiere lo siguiente:

* Una cuenta de [Capptain]
* Una aplicación de [nivel estándar de Servicios móviles]

## <a name="initialize"></a>Inicialización del SDK de Capptain

1. Vaya a la página **Detalles de la aplicación** de su aplicación registrada en Capptain. Seleccione la pestaña SDK y descargue el paquete.

2. En XCode, agregue el SDK de Capptain a su proyecto; para ello, haga clic con el botón derecho en el proyecto y seleccione "Agregar archivos a..." Elija la carpeta CapptainSDK.

3. Seleccione su proyecto. En la pestaña **Build Phases** (Generar fases), seleccione **Link Binary With Libraries** (Vincular binario con bibliotecas) y agregue los marcos siguientes:
    * AdSupport.framework: establezca el vínculo como Opcional.
    * SystemConfiguration.framework
    * CoreTelephony.framework
    * CFNetwork.framework
    * CoreLocation.framework
    * libxml2.dylib

    >[AZURE.NOTE]El marco de trabajo AdSupport se puede quitar. Capptain necesita este marco para recopilar el IDFA. No obstante, la recopilación de IDFA se puede deshabilitar para cumplir la directiva de Apple con respecto a este Id.

4. En el archivo de implementación de delegación de la aplicación, importe el agente de Capptain.


        #import "CapptainAgent.h"


5. Dentro de `applicationDidFinishLaunching:` o `application:didFinishLaunchingWithOptions:`, inicialice el agente de Capptain pasando `registerapp:identifiedby:` con su Id. de Apple y clave de SDK.

         - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
          [...]
          [CapptainAgent registerApp:@"YOUR_APPID" identifiedBy:@"YOUR_SDK_KEY"];
          [...]
        }

## <a name="instrument"></a>Sobrecarga de UIViewController

1. Busque cada elemento secundario de `UIViewController` en su proyecto y asegúrese de que cada uno herede de `CapptainViewController`.

        #import <UIKit/UIKit.h>
        #import "CapptainViewController.h"

        // formerly @interface Tab1ViewController : UIViewController<UITextFieldDelegate>
        @interface Tab1ViewController : CapptainViewController<UITextFieldDelegate> {
          UITextField* myTextField1;
          UITextField* myTextField2;
        }

        @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
        @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

2. Busque cada elemento secundario de `UITableViewController` en su proyecto y asegúrese de que cada uno herede de `CapptainTableViewController`.

    Su aplicación está ya configurada para enviar datos de análisis a Capptain.

## Pasos siguientes
Puede obtener más información sobre lo que Capptain puede hacer por su aplicación en [http://www.capptain.com](http://www.capptain.com)

<!-- Anchors. -->
[Inicialización del SDK de Capptain]: #initialize
[Sobrecarga de UIViewController]: #instrument


<!-- URLs. -->
[Capptain]: http://www.capptain.com
[nivel estándar de Servicios móviles]: /pricing/details/mobile-services/
 

<!---HONumber=August15_HO7-->
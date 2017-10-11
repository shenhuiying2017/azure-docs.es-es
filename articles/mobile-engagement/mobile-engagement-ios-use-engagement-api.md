---
title: "Cómo usar la API de Engagement en iOS"
description: "Último SDK de iOS: cómo usar la API de Engagement en iOS"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 1fb4509e-3804-46c1-949f-1cf727f91f9f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: a31424da98205e97bdf57010cccfd044360f03dd
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-use-the-engagement-api-on-ios"></a>Cómo usar la API de Engagement en iOS
Este documento es un complemento al documento Cómo integrar Engagement en iOS: en él se proporciona información detallada acerca de cómo usar la API de Engagement para informar de las estadísticas de la aplicación.

Tenga en cuenta que si solo desea que Engagement informe de las sesiones, las actividades, bloqueos e información técnica de la aplicación, a continuación, la manera más sencilla es hacer que todos los objetos `UIViewController` personalizados hereden de la clase `EngagementViewController` correspondiente.

Si desea hacer más, por ejemplo, si necesita informar de eventos, errores y trabajos específicos de la aplicación, o si debe informar de las actividades de la aplicación de manera diferente de la que se implementa en las clases `EngagementViewController`, deberá usar la API de Engagement.

La API de Engagement la proporciona la clase `EngagementAgent` . Para recuperar una instancia de esta clase puede llamarse al método estático `[EngagementAgent shared]` (tenga en cuenta que el objeto `EngagementAgent` que se devuelve es un singleton).

Antes de las llamadas a una API, el objeto `EngagementAgent` debe inicializarse llamando al método `[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];`

## <a name="engagement-concepts"></a>Conceptos de Engagement
En las siguientes secciones se detallan los [conceptos de Mobile Engagement](mobile-engagement-concepts.md) para la plataforma iOS.

### <a name="session-and-activity"></a>`Session` y `Activity`
Una *actividad* suele asociarse con una pantalla de la aplicación (es decir, la *actividad* se inicia cuando la pantalla se muestra y se detiene cuando se cierra la pantalla, como cuando se integra el SDK de Engagement con el uso de las clases `EngagementViewController`).

Sin embargo, las *actividades* también se pueden controlar manualmente mediante la API de Engagement. Esto permite dividir una pantalla dada en varias subpartes para obtener más detalles sobre el uso de esta pantalla (por ejemplo, para saber con qué frecuencia y durante cuánto tiempo se utilizan los cuadros de diálogo dentro de esta pantalla).

## <a name="reporting-activities"></a>Informes sobre actividades
### <a name="user-starts-a-new-activity"></a>El usuario inicia una nueva actividad
            [[EngagementAgent shared] startActivity:@"MyUserActivity" extras:nil];

Debe llamar a `startActivity()` cada vez que cambie la actividad de usuario. La primera llamada a esta función inicia una nueva sesión de usuario.

### <a name="user-ends-his-current-activity"></a>El usuario finaliza su actividad actual
            [[EngagementAgent shared] endActivity];

> [!WARNING]
> **NUNCA** realice una llamada a esta función, excepto si quiere dividir un uso de la aplicación en varias sesiones: una llamada a esta función terminaría la sesión actual inmediatamente, pero puede realizar una llamada posterior a `startActivity()` para iniciar una nueva sesión. Esta función es invocada automáticamente por el SDK cuando se cierra la aplicación.
> 
> 

## <a name="reporting-events"></a>Informes de eventos
### <a name="session-events"></a>Eventos de sesión
Los eventos de sesión se suelen usar para notificar las acciones que realiza el usuario durante su sesión.

**Ejemplo sin datos adicionales:**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:nil];
            ...
       }
       [...]
    }

**Ejemplo con datos adicionales:**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        NSMutableDictionary* extras = [NSMutableDictionary dictionary];
        [extras setObject:[NSNumber numberWithInt:toInterfaceOrientation] forKey:@"to_orientation_id"];
        [extras setObject:[NSNumber numberWithDouble:duration] forKey:@"duration"];
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:extras];
            ...
       }
       [...]
    }

### <a name="standalone-events"></a>Eventos independientes
Al contrario de los eventos de sesión, los eventos independientes pueden utilizarse fuera del contexto de una sesión.

**Ejemplo:**

    [[EngagementAgent shared] sendEvent:@"received_notification" extras:nil];

## <a name="reporting-errors"></a>Informes de errores
### <a name="session-errors"></a>Errores de sesión
Los errores de sesión suelen usarse para notificar los errores que afectan al usuario durante su sesión.

**Ejemplo:**

    /** The user has entered invalid data in a form */
    @implementation MyViewController {
      [...]
      -(void)onMyFormSubmitted:(MyForm*)form {
        [...]
        /* The user has entered an invalid email address */
        [[EngagementAgent shared] sendSessionError:@"sign_up_email" extras:nil]
        [...]
      }
      [...]
    }

### <a name="standalone-errors"></a>Errores independientes
Al contrario de los errores de la sesión, los errores independientes pueden utilizarse fuera del contexto de una sesión.

**Ejemplo:**

    [[EngagementAgent shared] sendError:@"something_failed" extras:nil];

## <a name="reporting-jobs"></a>Informes de trabajos
**Ejemplo:**

Supongamos que desea notificar la duración del proceso de inicio de sesión:

    [...]
    -(void)signIn
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      [... sign in ...]

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    }
    [...]

### <a name="report-errors-during-a-job"></a>Informe de errores durante un trabajo
Los errores pueden estar relacionados con un trabajo en ejecución en lugar de la sesión del usuario actual.

**Ejemplo:**

Suponga que desea notificar un error durante el proceso de inicio de sesión:

    [...]
    -(void)signin
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      BOOL success = NO;
      while (!success) {
        /* Try to sign in */
        NSError* error = nil;
        [self trySigin:&error];
        success = error == nil;

        /* If an error occured report it */
        if(!success)
        {
          [[EngagementAgent shared] sendJobError:@"sign_in_error"
                         jobName:@"sign_in"
                          extras:[NSDictionary dictionaryWithObject:[error localizedDescription] forKey:@"error"]];

          /* Retry after a moment */
          [NSThread sleepForTimeInterval:20];
        }
      }

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    };
    [...]

### <a name="events-during-a-job"></a>Eventos durante un trabajo
Los errores pueden estar relacionados con un trabajo en ejecución en lugar de la sesión del usuario actual.

**Ejemplo:**

Supongamos que tenemos una red social y utilizamos un trabajo de informe del tiempo total durante el cual el usuario está conectado al servidor. El usuario puede recibir mensajes de sus amigos, este es un evento de trabajo.

    [...]
    - (void) signin
    {
      [...Sign in code...]
      [[EngagementAgent shared] startJob:@"connection" extras:nil];
    }
    [...]
    - (void) signout
    {
      [...Sign out code...]
      [[EngagementAgent shared] endJob:@"connection"];
    }
    [...]
    - (void) onMessageReceived
    {
      [...Notify user...]
      [[EngagementAgent shared] sendJobEvent:@"connection" jobName:@"message_received" extras:nil];
    }
    [...]

## <a name="extra-parameters"></a>Parámetros adicionales
Se pueden adjuntar datos arbitrarios en eventos, errores, actividades y trabajos.

Estos datos pueden estructurarse, utilizan la clase de NSDictionary de iOS.

Tenga en cuenta que los extras pueden contener `arrays(NSArray, NSMutableArray)`, `numbers(NSNumber class)`, `strings(NSString, NSMutableString)`, `urls(NSURL)`, `data(NSData, NSMutableData)` u otras `NSDictionary` instancias.

> [!NOTE]
> El parámetro adicional se serializa en JSON. Si desea pasar objetos distintos de los descritos anteriormente, debe implementar el método siguiente en la clase:
> 
> -(NSString*)JSONRepresentation;
> 
> El método debe devolver una representación JSON del objeto.
> 
> 

### <a name="example"></a>Ejemplo
    NSMutableDictionary* extras = [NSMutableDictionary dictionaryWithCapacity:2];
    [extras setObject:[NSNumber numberWithInt:123] forKey:@"video_id"];
    [extras setObject:@"http://foobar.com/blog" forKey:@"ref_click"];
    [[EngagementAgent shared] sendEvent:@"video_clicked" extras:extras];

### <a name="limits"></a>Límites
#### <a name="keys"></a>simétricas
Cada clave de la `NSDictionary` debe coincidir con la siguiente expresión regular:

`^[a-zA-Z][a-zA-Z_0-9]*`

Esto significa que las claves tienen que empezar como mínimo con una letra, seguida de letras, dígitos o caracteres de subrayado (\_).

#### <a name="size"></a>Tamaño
Los extras están limitados a **1024** caracteres por llamada (una vez codificados en JSON por el agente de Engagement).

En el ejemplo anterior, el JSON que se envía al servidor tiene una longitud de 58 caracteres:

    {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

## <a name="reporting-application-information"></a>Información de la aplicación de informes
Puede notificar manualmente la información de seguimiento (o cualquier otro tipo de información específica de la aplicación) mediante la función `sendAppInfo:` .

Tenga en cuenta que esta información se puede enviar de forma incremental: para un dispositivo dado solo se conservará el último valor de una clave determinada.

Al igual que los extras de evento, la clase `NSDictionary` se usa para resumir la información de la aplicación. Tenga en cuenta que las matrices o diccionarios secundarios se tratarán como cadenas sin formato (con la serialización de JSON).

**Ejemplo:**

    NSMutableDictionary* appInfo = [NSMutableDictionary dictionaryWithCapacity:2];
    [appInfo setObject:@"female" forKey:@"gender"];
    [appInfo setObject:@"1983-12-07" forKey:@"birthdate"]; // December 7th 1983
    [[EngagementAgent shared] sendAppInfo:appInfo];

### <a name="limits"></a>Límites
#### <a name="keys"></a>simétricas
Cada clave de la `NSDictionary` debe coincidir con la siguiente expresión regular:

`^[a-zA-Z][a-zA-Z_0-9]*`

Esto significa que las claves tienen que empezar como mínimo con una letra, seguida de letras, dígitos o caracteres de subrayado (\_).

#### <a name="size"></a>Tamaño
La información de la aplicación está limitada a **1024** caracteres por llamada (una vez codificados en JSON por el agente de Engagement).

En el ejemplo anterior, el JSON que se envía al servidor tiene una longitud de 44 caracteres:

    {"birthdate":"1983-12-07","gender":"female"}

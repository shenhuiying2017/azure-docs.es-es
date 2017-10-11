---
title: "Cómo usar la API de Engagement en Windows Phone Silverlight"
description: "Cómo usar la API de Engagement en Windows Phone Silverlight"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: ae2ba2e8-f75b-4dee-a164-a7dd65d35a23
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: ec8b6c13ea052c8063dfde4321cdd286ab6cb817
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-use-the-engagement-api-on-windows-phone-silverlight"></a>Cómo usar la API de Engagement en Windows Phone Silverlight
Este documento es un complemento al documento [Cómo integrar Mobile Engagement en su aplicación de Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md). En él se proporciona información detallada acerca de cómo usar la API de Engagement para informar de las estadísticas de la aplicación.

Si solo desea que Engagement informe sobre las sesiones, las actividades, los bloqueos y la información técnica de la aplicación, la manera más sencilla consiste en hacer que las subclases `PhoneApplicationPage` hereden de la clase `EngagementPage`.

Si desea hacer más, por ejemplo, si necesita informar de eventos, errores y trabajos específicos de la aplicación, o si debe informar de las actividades de la aplicación de manera diferente de la que se implementa en las clases `EngagementPage`, deberá usar la API de Engagement.

La API de Engagement la proporciona la clase `EngagementAgent` . Puede acceder a esos métodos a través de `EngagementAgent.Instance`.

Incluso si no se ha inicializado el módulo de agente, las llamadas a la API se aplazan y se ejecutará de nuevo cuando el agente está disponible.

## <a name="engagement-concepts"></a>Conceptos de Engagement
En las siguientes secciones se detallan los conceptos de Mobile Engagement para la plataforma Windows Phone.

### <a name="session-and-activity"></a>`Session` y `Activity`
Una *actividad* suele estar asociada con una página de la aplicación. Es decir, la *actividad* se inicia cuando la página se muestra y se detiene cuando se cierra la página. Este es el caso cuando la integración del SDK de Engagement se realiza mediante la clase `EngagementPage`.

Sin embargo, las *actividades* también se pueden controlar manualmente mediante la API de Engagement. Esto permite dividir una página determinada en varias subpartes para obtener más detalles sobre el uso de esta página (por ejemplo, la frecuencia y la duración con las que se usan los cuadros de diálogo en esta página).

## <a name="reporting-activities"></a>Informes sobre actividades
### <a name="user-starts-a-new-activity"></a>El usuario inicia una nueva actividad
#### <a name="reference"></a>Referencia
            void StartActivity(string name, Dictionary<object, object> extras = null)

Debe llamar a `StartActivity()` cada vez que cambie la actividad de usuario. La primera llamada a esta función inicia una nueva sesión de usuario.

> [!IMPORTANT]
> El SDK llaman automáticamente al método EndActivity cuando se cierra la aplicación. Por lo tanto, es MUY recomendable llamar al método StartActivity cada vez que cambie la actividad del usuario y NUNCA llamar al método EndActivity, ya que esto obliga la finalización de la sesión actual.
> 
> 

#### <a name="example"></a>Ejemplo
            EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### <a name="user-ends-his-current-activity"></a>El usuario finaliza su actividad actual
#### <a name="reference"></a>Referencia
            void EndActivity()

Cuando el usuario finaliza su última actividad, se debe llamar a `EndActivity()` al menos una vez. De esta manera se informa al SDK de Engagement de que el usuario está inactivo y que la sesión del usuario se debe cerrar cuando expire el tiempo de espera de la misma (si se llama a `StartActivity()` antes de que expire dicho tiempo de espera, la sesión simplemente continúa).

#### <a name="example"></a>Ejemplo
            EngagementAgent.Instance.EndActivity();

## <a name="reporting-jobs"></a>Informes de trabajos
### <a name="start-a-job"></a>Iniciar un trabajo
#### <a name="reference"></a>Referencia
            void StartJob(string name, Dictionary<object, object> extras = null)

Puede usar el trabajo para hace un seguimiento de tareas determinadas durante un período de tiempo.

#### <a name="example"></a>Ejemplo
            // An upload begins...

            // Set the extras
            var extras = new Dictionary<object, object>();
            extras.Add("title", "avatar");
            extras.Add("type", "image");

            EngagementAgent.Instance.StartJob("uploadData", extras);

### <a name="end-a-job"></a>Finalizar un trabajo
#### <a name="reference"></a>Referencia
            void EndJob(string name)

Cuando haya finalizado una tarea de la que un trabajo realiza el seguimiento, se debe llamar al método EndJob para este trabajo al proporcionar su nombre.

#### <a name="example"></a>Ejemplo
            // In the previous section, we started an upload tracking with a job
            // Then, the upload ends

            EngagementAgent.Instance.EndJob("uploadData");

## <a name="reporting-events"></a>Informes de eventos
Hay tres tipos de eventos:

* Eventos independientes
* Eventos de sesión
* Eventos de trabajo

### <a name="standalone-events"></a>Eventos independientes
#### <a name="reference"></a>Referencia
            void SendEvent(string name, Dictionary<object, object> extras = null)

Los eventos independientes se pueden producir fuera del contexto de una sesión.

#### <a name="example"></a>Ejemplo
            EngagementAgent.Instance.SendEvent("event", extra);

### <a name="session-events"></a>Eventos de sesión
#### <a name="reference"></a>Referencia
            void SendSessionEvent(string name, Dictionary<object, object> extras = null)

Los eventos de sesión se suelen usar para notificar las acciones que realiza el usuario durante su sesión.

#### <a name="example"></a>Ejemplo
**Sin datos:**

            EngagementAgent.Instance.SendSessionEvent("sessionEvent");

            // or

            EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**Con datos:**

            Dictionary<object, object> extras = new Dictionary<object,object>();
            extras.Add("name", "data");
            EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### <a name="job-events"></a>Eventos de trabajo
#### <a name="reference"></a>Referencia
            void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

Los eventos de trabajo se suelen usar para notificar las acciones que realiza un usuario durante un trabajo.

#### <a name="example"></a>Ejemplo
            EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

## <a name="reporting-errors"></a>Informes de errores
Hay tres tipos de errores:

* Errores independientes
* Errores de sesión
* Errores de trabajo

### <a name="standalone-errors"></a>Errores independientes
#### <a name="reference"></a>Referencia
            void SendError(string name, Dictionary<object, object> extras = null)

Al contrario de los errores de sesión, los errores independientes se pueden producir fuera del contexto de una sesión.

#### <a name="example"></a>Ejemplo
            EngagementAgent.Instance.SendError("errorName", extras);

### <a name="session-errors"></a>Errores de sesión
#### <a name="reference"></a>Referencia
            void SendSessionError(string name, Dictionary<object, object> extras = null)

Los errores de sesión suelen usarse para notificar los errores que afectan al usuario durante su sesión.

#### <a name="example"></a>Ejemplo
            EngagementAgent.Instance.SendSessionError("errorName", extra);

### <a name="job-errors"></a>Errores de trabajo
#### <a name="reference"></a>Referencia
            void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

Los errores pueden estar relacionados con un trabajo en ejecución en lugar de la sesión del usuario actual.

#### <a name="example"></a>Ejemplo
            EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

## <a name="reporting-crashes"></a>Informes de bloqueos
El agente proporciona dos métodos para tratar los bloqueos.

### <a name="send-an-exception"></a>Enviar una excepción
#### <a name="reference"></a>Referencia
            void SendCrash(Exception e, bool terminateSession = false)

#### <a name="example"></a>Ejemplo
Puede enviar una excepción en cualquier momento al llamar a:

            EngagementAgent.Instance.SendCrash(aCatchedException);

También puede usar un parámetro opcional para finalizar la sesión de Engagement al mismo tiempo que se envía el bloqueo. Para ello, llame a:

            EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

Si lo hace, la sesión y los trabajos se cerrarán después de enviar el bloqueo.

### <a name="send-an-unhandled-exception"></a>Enviar una excepción no controlada
#### <a name="reference"></a>Referencia
            void SendCrash(ApplicationUnhandledExceptionEventArgs e)

Engagement también proporciona un método para enviar excepciones no controladas. Esto es especialmente útil cuando se usa dentro del controlador de eventos UnhandledException de Silverlight.

Este método **SIEMPRE** finalizará la sesión y los trabajos de Engagement después de su invocación.

#### <a name="example"></a>Ejemplo
Puede usarlo para implementar su propio controlador UnhandledException (especialmente si deshabilitó la característica de informes de errores automáticos de Engagement). Por ejemplo, en el método `Application_UnhandledException` del archivo `App.xaml.cs`:

            // In your App.xaml.cs file

            // Code to execute on Unhandled Exceptions
            private void Application_UnhandledException(object sender, ApplicationUnhandledExceptionEventArgs e)
            {
              // your own code

              EngagementAgent.Instance.SendCrash(e);
            }

## <a name="onactivated"></a>OnActivated
### <a name="reference"></a>Referencia
            void OnActivated(ActivatedEventArgs e)

Cuando el usuario se desplaza hacia delante, alejándose de una aplicación, después de que se genere el evento Desactivado, el sistema operativo intentará poner la aplicación en un estado inactivo. A continuación, la aplicación se encuentra en un estado conocido como "extinción". En este proceso, la aplicación se finaliza, pero se conservan algunos datos sobre su estado, así como las páginas individuales dentro de la aplicación.

Tiene que insertar `EngagementAgent.Instance.OnActivated(e)` en el método `Application_Activated` desde el archivo App.xaml.cs para restablecer el agente de Engagement cuando la aplicación se haya puesto en estado de extinción.

### <a name="example"></a>Ejemplo
            // Inside your App.xaml.cs file

            // Code to execute when the application is activated (brought to foreground)
            // This code will not execute when the application is first launched
            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
              EngagementAgent.Instance.OnActivated(e);
            }

## <a name="device-id"></a>Identificador de dispositivo
            String GetDeviceId()

Puede obtener el identificador del dispositivo de Engagement mediante una llamada a este método.

## <a name="extras-parameters"></a>Parámetros adicionales
Es posible adjuntar datos arbitrarios a un evento, un error, una actividad o un trabajo. Estos datos se pueden estructurar mediante un diccionario. Las claves y los valores pueden ser de cualquier tipo.

Los datos adicionales se serializan, de modo que si desea insertar su propio tipo de datos adicionales, tendrá que agregar un contrato de datos para este tipo.

### <a name="example"></a>Ejemplo
Creamos una nueva clase "Person".

            using System.Runtime.Serialization;

            namespace Engagement.Agent
            {
              [DataContract]
              public class Person
              {
                public Person(string name, int age)
                {
                  Age = age;
                  Name = name;
                }

                // Properties

                [DataMember]
                public int Age
                {
                  get;
                  set;
                }

                [DataMember]
                public string Name
                {
                  get;
                  set; 
                }
              }
            }

A continuación, agregaremos una instancia de `Person` a un dato adicional.

            Person person = new Person("Engagement Haddock", 51);
            var extras = new Dictionary<object, object>();
            extras.Add("people", person);

            EngagementAgent.Instance.SendEvent("Event", extras);

> [!WARNING]
> Si coloca otros tipos de objetos, asegúrese de que el método ToString() se implementa para devolver una cadena legible.
> 
> 

### <a name="limits"></a>Límites
#### <a name="keys"></a>simétricas
Cada clave del objeto debe coincidir con la siguiente expresión regular:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Esto significa que las claves deben empezar con al menos una letra, seguida de letras, dígitos o caracteres de subrayado (\_).

#### <a name="size"></a>Tamaño
Los datos adicionales están limitados a **1024** caracteres por llamada.

## <a name="reporting-application-information"></a>Información de la aplicación de informes
### <a name="reference"></a>Referencia
            void SendAppInfo(Dictionary<object, object> appInfos)

Puede notificar manualmente la información de seguimiento (o cualquier otro tipo de información específica de la aplicación) mediante la función SendAppInfo().

Tenga en cuenta que esta información se puede enviar de forma incremental: para un dispositivo dado solo se conservará el último valor de una clave determinada. Al igual que los datos adicionales de evento, use un elemento de Dictionary \<object, object\> para adjuntar información.

### <a name="example"></a>Ejemplo
            Dictionary<object, object> appInfo = new Dictionary<object, object>()
            {
               {"subscription", "2013-12-07"},
               {"premium", "true"}
            };

            EngagementAgent.Instance.SendAppInfo(appInfo);

### <a name="limits"></a>Límites
#### <a name="keys"></a>simétricas
Cada clave del objeto debe coincidir con la siguiente expresión regular:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Esto significa que las claves deben empezar con al menos una letra, seguida de letras, dígitos o caracteres de subrayado (\_).

#### <a name="size"></a>Tamaño
La información de la aplicación está limitada a **1024** caracteres por llamada.

En el ejemplo anterior, el JSON que se envía al servidor tiene una longitud de 44 caracteres:

            {"subscription":"2013-12-07","premium":"true"}

## <a name="logging"></a>Registro
### <a name="enable-logging"></a>Habilitación del registro
El SDK puede configurarse para generar registros de prueba en la consola del IDE.
Estos registros no están activados de forma predeterminada. Para personalizar esto, actualice la propiedad `EngagementAgent.Instance.TestLogEnabled` a uno de los valores disponibles en la enumeración `EngagementTestLogLevel`, por ejemplo:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

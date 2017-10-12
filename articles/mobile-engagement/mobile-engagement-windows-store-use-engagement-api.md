---
title: "Cómo usar la API de Engagement en Windows Universal"
description: "Cómo usar la API de Engagement en Windows Universal"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: bb501fca-9cfe-4495-81df-b5efd6e0137b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 75fc134a5535e6113331470cf61df9c06eb8e2ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-engagement-api-on-windows-universal"></a>Cómo usar la API de Engagement en Windows Universal
Este documento es un complemento al documento [Cómo integrar Engagement en Windows Universal](mobile-engagement-windows-store-integrate-engagement.md): en él se proporciona información detallada acerca de cómo usar la API de Engagement para informar de las estadísticas de la aplicación.

Tenga en cuenta que si solo desea que Engagement informe sobre las sesiones, las actividades, los bloqueos y la información técnica de la aplicación, la manera más sencilla consiste en hacer que las subclases `Page` hereden de la clase `EngagementPage`.

Si desea hacer más, por ejemplo, si necesita informar de eventos, errores y trabajos específicos de la aplicación, o si debe informar de las actividades de la aplicación de manera diferente de la que se implementa en las clases `EngagementPage`, deberá usar la API de Engagement.

La API de Engagement la proporciona la clase `EngagementAgent` . Puede acceder a esos métodos a través de `EngagementAgent.Instance`.

Incluso si no se ha inicializado el módulo de agente, las llamadas a la API se aplazan y se ejecutará de nuevo cuando el agente está disponible.

## <a name="engagement-concepts"></a>Conceptos de Engagement
En las siguientes secciones se detallan los [conceptos de Mobile Engagement](mobile-engagement-concepts.md) para la plataforma Windows Universal.

### <a name="session-and-activity"></a>`Session` y `Activity`
Una *actividad* suele estar asociada con una página de la aplicación. Es decir, la *actividad* se inicia cuando la página se muestra y se detiene cuando se cierra la página. Este es el caso cuando la integración del SDK de Engagement se realiza mediante la clase `EngagementPage`.

Sin embargo, las *actividades* también se pueden controlar manualmente mediante la API de Engagement. Esto permite dividir una página determinada en varias subpartes para obtener más detalles sobre el uso de esta página (por ejemplo, la frecuencia y la duración con las que se usan los cuadros de diálogo en esta página).

## <a name="reporting-activities"></a>Informes sobre actividades
### <a name="user-starts-a-new-activity"></a>El usuario inicia una nueva actividad
#### <a name="reference"></a>Referencia
            void StartActivity(string name, Dictionary<object, object> extras = null)

Debe llamar a `StartActivity()` cada vez que cambie la actividad de usuario. La primera llamada a esta función inicia una nueva sesión de usuario.

> [!IMPORTANT]
> El SDK llama automáticamente al método EndActivity cuando se cierra la aplicación. Por lo tanto, es MUY recomendable llamar al método StartActivity cada vez que cambie la actividad del usuario y NUNCA llamar al método EndActivity, ya que esto obliga la finalización de la sesión actual.
> 
> 

#### <a name="example"></a>Ejemplo
            EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### <a name="user-ends-his-current-activity"></a>El usuario finaliza su actividad actual
#### <a name="reference"></a>Referencia
            void EndActivity()

Esto finaliza la actividad y la sesión. No debe llamar a este método, a menos que realmente sepa lo que está haciendo.

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
Existen tres tipos de errores:

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
            void SendCrash(Exception e)

Engagement también proporciona un método para enviar excepciones no controladas si ha **DESHABILITADO** los informes de **bloqueo** automáticos de Engagement. Esto es especialmente útil cuando se usa dentro del controlador de eventos UnhandledException.

Este método **SIEMPRE** finalizará la sesión y los trabajos de Engagement después de su invocación.

#### <a name="example"></a>Ejemplo
Puede usarlo para implementar su propio controlador UnhandledExceptionEventArgs. Por ejemplo, agregue el método `Current_UnhandledException` del archivo `App.xaml.cs`:

            // In your App.xaml.cs file

            // Code to execute on Unhandled Exceptions
            void Current_UnhandledException(object sender, UnhandledExceptionEventArgs e)
            {
               EngagementAgent.Instance.SendCrash(e.Exception,false);
            }

En App.xaml.cs, en "Public App(){}", agregue lo siguiente:

            Application.Current.UnhandledException += Current_UnhandledException;

## <a name="device-id"></a>Identificador de dispositivo
            String EngagementAgent.Instance.GetDeviceId()

Puede obtener el identificador del dispositivo de Engagement mediante una llamada a este método.

## <a name="extras-parameters"></a>Parámetros adicionales
Es posible adjuntar datos arbitrarios a un evento, un error, una actividad o un trabajo. Estos datos se pueden estructurar mediante un diccionario. Las claves y los valores pueden ser de cualquier tipo.

Los datos adicionales se serializan, de modo que si desea insertar su propio tipo de datos adicionales, tendrá que agregar un contrato de datos para este tipo.

### <a name="example"></a>Ejemplo
Creamos una nueva clase "Person".

            using System.Runtime.Serialization;

            namespace Microsoft.Azure.Engagement
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

Tenga en cuenta que estos datos se pueden enviar de forma incremental: para un dispositivo concreto solo se conservará el último valor de una clave determinada. Al igual que los datos adicionales de evento, use un elemento Dictionary \<object, object\> para adjuntar datos.

### <a name="example"></a>Ejemplo
            Dictionary<object, object> appInfo = new Dictionary<object, object>()
              {
                {"birthdate", "1983-12-07"},
                {"gender", "female"}
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

            {"birthdate":"1983-12-07","gender":"female"}

## <a name="logging"></a>Registro
### <a name="enable-logging"></a>Habilitación del registro
El SDK puede configurarse para generar registros de prueba en la consola del IDE.
Estos registros no están activados de forma predeterminada. Para personalizar esto, actualice la propiedad `EngagementAgent.Instance.TestLogEnabled` a uno de los valores disponibles en la enumeración `EngagementTestLogLevel`, por ejemplo:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();


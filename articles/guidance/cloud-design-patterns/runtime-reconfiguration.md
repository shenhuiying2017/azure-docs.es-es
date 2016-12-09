---
title: "Patrón de reconfiguración en tiempo de ejecución | Azure | Microsoft Docs"
description: "Diseñe una aplicación de forma que pueda volver a configurarse sin necesidad de volver a implementarla o reiniciarla."
categories:
- design-implementation
- management-monitoring
keywords: "Patrón de diseño"
author: dragon119
manager: bennage
ms.service: guidance
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.author: mwasson
ms.date: 11/14/2016
translationtype: Human Translation
ms.sourcegitcommit: c4963489ca53a90cab480116ec0a044501ccb5e9
ms.openlocfilehash: 2a7c30eca77809e08fd8621027daef60146408a0

---
   
# <a name="runtime-reconfiguration"></a>Reconfiguración en tiempo de ejecución

Diseñe una aplicación de forma que pueda volver a configurarse sin necesidad de volver a implementarla o reiniciarla. Esto ayuda a mantener la disponibilidad y a minimizar el tiempo de inactividad. 

## <a name="context-and-problem"></a>Contexto y problema

Un objetivo principal de las aplicaciones, como los sitios web comerciales y empresariales, es minimizar el tiempo de inactividad y la interrupción para clientes y usuarios. A veces, es necesario reconfigurar la aplicación para cambiar el comportamiento específico o la configuración mientras se implementa y está en uso. Por lo tanto, es una ventaja para la aplicación diseñarse para permitir que estos cambios de configuración se apliquen mientras se está ejecutando, y para los componentes de la aplicación para que detecten los cambios y los apliquen tan pronto como sea posible.

Ejemplos de los tipos de cambios de configuración que se van a aplicar pueden ser el ajuste de la granularidad de registro para ayudar en la depuración de un problema con la aplicación, el intercambio de cadenas de conexión para usar un almacén de datos diferente o bien, activar o desactivar secciones específicas o la funcionalidad de la aplicación.

## <a name="solution"></a>Solución

La solución para implementar este patrón depende de las características disponibles en el entorno de hospedaje de la aplicación. Normalmente, el código de aplicación responderá a uno o más eventos que ha generado la infraestructura de hospedaje cuando detecta un cambio en la configuración de la aplicación. Este es normalmente el resultado de cargar un nuevo archivo de configuración, o en respuesta a cambios en la configuración mediante el portal de administración o teniendo acceso a una API.

El código que controla los eventos de cambio de la configuración puede examinar los cambios y aplicarlos a los componentes de la aplicación. Estos componentes tienen que detectar y reaccionar ante los cambios y, de este modo, los valores que usan se expondrán normalmente como propiedades o métodos grabables que el código del controlador de eventos puede establecer en valores nuevos o ejecutarlos. Desde este punto, los componentes deben usar los valores nuevos de forma que se produzcan los cambios necesarios en el comportamiento de la aplicación.

Si no es posible para los componentes aplicar los cambios en tiempo de ejecución, será necesario reiniciar la aplicación para que estos cambios se apliquen cuando la aplicación se inicie de nuevo. En algunos entornos de hospedaje es posible detectar estos tipos de cambios, e indicar al entorno que la aplicación debe reiniciarse. En otros casos, puede que sea necesario implementar un código que analice los cambios de la configuración y fuerce un reinicio de la aplicación cuando sea necesario.

En la figura se muestra información general de este patrón.

![Figura 1: información general básica de este patrón](images/runtime-reconfiguration-pattern.png)


La mayoría de los entornos exponen eventos que surgen en respuesta a cambios de configuración. En los que no, será necesario tener un mecanismo de sondeo que compruebe regularmente la existencia de cambios en la configuración y aplique estos cambios. Puede que también sea necesario reiniciar la aplicación si los cambios no pueden aplicarse en tiempo de ejecución. Por ejemplo, es posible comparar la fecha y la hora de un archivo de configuración en intervalos predefinidos, y ejecutar código para aplicar los cambios cuando se detecte una versión más reciente. Otro enfoque consiste en incorporar un control en la interfaz de usuario de la administración de la aplicación, o exponer un punto de conexión seguro al que se pueda tener acceso desde fuera de la aplicación, que ejecute código que lea y aplique la configuración actualizada. 

De manera alternativa, la aplicación puede reaccionar ante algún otro cambio del entorno. Por ejemplo, las apariciones de un error en tiempo de ejecución determinado pueden cambiar la configuración de registro para recopilar información adicional automáticamente, o el código puede usar la fecha actual para leer y aplicar un tema que refleje la estación del año o un evento especial. 

## <a name="issues-and-considerations"></a>Problemas y consideraciones

Tenga en cuenta los puntos siguientes al decidir cómo implementar este patrón:

Las opciones de configuración deben almacenarse fuera de la aplicación que se ha implementado, de forma que puedan actualizarse sin necesidad de que el paquete completo se vuelva a implementar. Normalmente, la configuración se almacena en un archivo de configuración, o en un repositorio externo como una base de datos o un almacenamiento en línea. El acceso al mecanismo de configuración en tiempo de ejecución debe estar controlado estrictamente, así como estrictamente auditado cuando se use.

Si la infraestructura de hospedaje no detecta automáticamente eventos de cambio en la configuración, y expone estos eventos al código de la aplicación, debe implementar un mecanismo alternativo para detectar y aplicar los cambios. Esto puede realizarse a través de un mecanismo de sondeo, o exponiendo un control interactivo o un punto de conexión que inicie el proceso de actualización. 

Si necesita implementar un mecanismo de sondeo, considere la frecuencia con la que deben realizarse comprobaciones de actualizaciones en la configuración. Un intervalo largo de sondeo significa que los cambios puede que no se apliquen durante algún tiempo. Un intervalo corto puede afectar negativamente a la operación al absorber recursos de E/S y de proceso disponibles.

Si existe más de una instancia de la aplicación, deben tenerse en cuenta factores adicionales, dependiendo de cómo se detecten los cambios. Si los cambios se detectan automáticamente mediante eventos que se han generado por la infraestructura de hospedaje, puede que todas las instancias de aplicación no los detecten a la vez. Esto significa que algunas instancias estarán usando la configuración original durante un tiempo mientras que otras usarán la configuración nueva. Si la actualización se detecta mediante un mecanismo de sondeo, este debe comunicar el cambio a todas las instancias para mantener la coherencia.

Algunos cambios de configuración necesitan que la aplicación se reinicie, o incluso necesitan que el servidor de hospedaje se reinicie. Debe identificar estos tipos de opciones de configuración y realizar la acción adecuada para cada una. Por ejemplo, un cambio que necesite que la aplicación se reinicie puede hacer esto automáticamente, o puede ser responsabilidad del administrador iniciar el reinicio cuando la aplicación no se encuentre bajo una carga excesiva y otras instancias de la aplicación puedan controlar la carga. 

Antes de aplicar la actualización en todas las instancias, planee una implementación en fases de las actualizaciones y confirme que son correctas y que las instancias de aplicación actualizadas funcionan correctamente. Esto puede evitar una interrupción total de la aplicación en caso de producirse un error. Donde la actualización necesite un reinicio de la aplicación, particularmente donde la aplicación tarde un tiempo considerable en el inicio o en la preparación, use una implementación en fases para evitar que varias instancias se encuentren sin conexión a la vez.

Tenga en cuenta cómo revertir los cambios de configuración que provocan problemas o errores en la aplicación. Por ejemplo, debe ser posible revertir un cambio inmediatamente en lugar de esperar un intervalo de sondeo para detectar el cambio. 

Tenga en cuenta cómo puede afectar la ubicación de las opciones de configuración al rendimiento de la aplicación. Por ejemplo, controlar cualquier error que pueda producirse si el almacenamiento externo no está disponible cuando la aplicación se inicie, o cuando los cambios de la configuración se apliquen. Puede hacer esto mediante una configuración predeterminada o almacenando la configuración en caché localmente en el servidor y volver a usar estos valores al volver a intentar acceder al almacén de datos remoto.

El almacenamiento en caché puede ser útil para reducir los retrasos si un componente necesita tener acceso repetidamente a las opciones de configuración. En cambio, cuando la configuración cambia, el código de la aplicación tiene que invalidar la configuración que se ha almacenado en caché, y el componente debe usar la configuración actualizada. 

## <a name="when-to-use-this-pattern"></a>Cuándo usar este patrón

Este patrón es útil para:

- Aplicaciones que tienen que evitar el tiempo de inactividad innecesario, mientras todavía pueden aplicar cambios en la configuración de la aplicación.

- Entornos que exponen eventos que se han generado automáticamente cuando cambia la configuración principal. Normalmente, esto se produce cuando se detecta un nuevo archivo de configuración, o cuando los cambios se realizan en un archivo de configuración existente. 

- Aplicaciones donde la configuración cambia a menudo y los cambios pueden aplicarse a los componentes sin necesidad de que la aplicación se reinicie, o sin necesidad de que el servidor de hospedaje se reinicie.

Este patrón puede que no sea útil si los componentes en tiempo de ejecución están diseñados de forma que solo puedan configurarse en el tiempo de inicialización, y el esfuerzo de actualización de esos componentes no puede justificarse en comparación con el reinicio de la aplicación y la experiencia de un breve tiempo de inactividad. 

## <a name="example"></a>Ejemplo

Los roles de Microsoft Azure Cloud Services detectan y exponen dos eventos que se generan cuando el entorno de hospedaje detecta un cambio en los archivos ServiceConfiguration.cscfg:

- **RoleEnvironment.Changing**. Este evento se genera después de que se detecte un cambio de configuración, pero antes de que se aplique a la aplicación. Puede controlar el evento para consultar los cambios y para cancelar la reconfiguración en tiempo de ejecución. Si cancela el cambio, el rol web o de trabajo se reiniciará automáticamente de forma que la aplicación use la nueva configuración.
- **RoleEnvironment.Changed**. Este evento se genera después de que la configuración de la aplicación se haya aplicado. Puede controlar el evento para consultar los cambios que se han aplicado.

Cuando cancela un cambio en el evento `RoleEnvironment.Changing`, está indicando a Azure que no puede aplicarse una configuración nueva mientras que la aplicación se esté ejecutando, y que debe reiniciarse para usar el valor nuevo. De hecho, cancelará un cambio solo si su aplicación o componente no puede reaccionar ante el cambio en tiempo de ejecución, y necesita reiniciarse para usar el valor nuevo.

>  Para obtener más información, consulte [RoleEnvironment.Changing Event](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx). 

Para controlar los eventos `RoleEnvironment.Changing` y `RoleEnvironment.Changed`, normalmente agregará un controlador personalizado al evento. Por ejemplo, el siguiente código de la clase `Global.asax.cs` (en la solución de reconfiguración en tiempo de ejecución disponible en [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/samples/runtime-reconfiguration)) muestra cómo agregar una función personalizada denominada `RoleEnvironment_Changed` a la cadena del controlador de eventos. Esto procede del archivo Global.asax.cs del ejemplo.

>  Los ejemplos de este patrón se encuentran en el proyecto RuntimeReconfiguration.Web de la solución RuntimeReconfiguration. 

```csharp
protected void Application_Start(object sender, EventArgs e)
{
  ConfigureFromSetting(CustomSettingName);
  RoleEnvironment.Changed += this.RoleEnvironment_Changed;
}
```

En un rol web o de trabajo puede usar un código similar en el controlador de eventos `OnStart` del rol para controlar el evento `RoleEnvironment.Changing`. Esto procede del archivo WebRole.cs del ejemplo.

```csharp
public override bool OnStart()
{
  // Add the trace listener. The web role process isn't configured by web.config.
  Trace.Listeners.Add(new DiagnosticMonitorTraceListener());

  RoleEnvironment.Changing +=   this.RoleEnvironment_Changing;
  return base.OnStart();
}
```

Tenga en cuenta que, en el caso de los roles web, el controlador de eventos `OnStart` se ejecuta en un proceso independiente del proceso de aplicación web. Esto se debe a que normalmente controla el controlador de eventos `RoleEnvironment.Changed` en el archivo Global.asax, de manera que puede actualizar la configuración en tiempo de ejecución de su aplicación web, y el evento `RoleEnvironment.Changing` en el rol. En el caso de un rol de trabajo, puede suscribirse a los eventos `RoleEnvironment.Changing` y `RoleEnvironment.Changed` en el controlador de eventos `OnStart`.

>  Puede almacenar las opciones de configuración personalizadas en el archivo de configuración de servicio, en un archivo de configuración personalizado, en una base de datos, como Azure SQL Database o SQL Server, de una máquina virtual, o en Azure Blob o Table Storage. Necesitará crear código que pueda tener acceso a las opciones de configuración personalizadas y aplicarlas a la aplicación&mdash;normalmente, estableciendo las propiedades de componentes en la aplicación.  

Por ejemplo, la siguiente función personalizada lee el valor de una configuración desde el archivo de configuración de servicio de Azure y, después, lo aplica a la instancia actual de un componente en tiempo de ejecución denominado `SomeRuntimeComponent`. Esto procede del archivo Global.asax.cs del ejemplo.

>  Algunas opciones de configuración, como las de Windows Identity Framework, no pueden almacenarse en el archivo de configuración de servicio de Azure y deben encontrarse en el archivo App.config o Web.config. 

```csharp
private static void ConfigureFromSetting(string settingName)
{
  var value = RoleEnvironment.GetConfigurationSettingValue(settingName);
  SomeRuntimeComponent.Instance.CurrentValue = value;
}
```

En Azure, algunos cambios de configuración se detectan y se aplican automáticamente. Esto incluye la configuración del sistema de diagnóstico de Azure en el archivo Diagnostics.wadcfg que especifica los tipos de información que se van a recopilar y cómo conservar los archivos de registro. Por lo tanto, solo es necesario para escribir código que controle la configuración personalizada que agrega al archivo de configuración de servicio. El código debe:

- Aplicar la configuración personalizada desde una configuración actualizada a los componentes adecuados de su aplicación en tiempo de ejecución, de manera que su comportamiento refleje la nueva configuración.

- Cancelar el cambio para indicar a Azure que el nuevo valor no puede aplicarse en tiempo de ejecución, y que la aplicación debe reiniciarse para que el cambio se aplique. 

Por ejemplo, en el siguiente código se muestra cómo puede usar el evento `RoleEnvironment.Changing` para cancelar la actualización de todas las configuraciones excepto las que pueden aplicarse en tiempo de ejecución sin necesidad de reiniciarse. Este ejemplo permite un cambio en "CustomSetting" que se va a aplicar en tiempo de ejecución sin reiniciar la aplicación. El componente que usa esta configuración podrá leer el valor nuevo y cambiar su comportamiento en consecuencia en tiempo de ejecución. Cualquier otro cambio en la configuración provocará automáticamente el reinicio del rol web o de trabajo. 

```csharp
private void RoleEnvironment_Changing(object sender,
                               RoleEnvironmentChangingEventArgs e)
{
  var changedSettings = e.Changes.OfType<RoleEnvironmentConfigurationSettingChange>()
                                 .Select(c => c.ConfigurationSettingName).ToList();
  Trace.TraceInformation("Changing notification. Settings being changed: "
                         + string.Join(", ", changedSettings));

  if (changedSettings
    .Any(settingName => !string.Equals(settingName, CustomSettingName,
                               StringComparison.Ordinal)))
  {
    Trace.TraceInformation("Cancelling dynamic configuration change (restarting).");

    // Setting this to true will restart the role gracefully. If Cancel isn't 
    // set to true, and the change isn't handled by the application, the 
    // application won't use the new value until it's restarted (either 
    // manually or for some other reason).
    e.Cancel = true; 
  }
  Else
  {
    Trace.TraceInformation("Handling configuration change without restarting. ");
  }
}
```

>  Este enfoque demuestra un procedimiento recomendado ya que garantiza que un cambio en cualquier configuración que el código de aplicación no reconozca (y no está seguro de que pueda aplicarse en tiempo de ejecución) provocará un reinicio. Si cualquiera de los cambios se cancela, el rol se reiniciará.  

Las actualizaciones que no se cancelan en el controlador de eventos `RoleEnvironment.Changing` pueden, por lo tanto, detectarse y aplicarse en los componentes de aplicación después de que la nueva configuración se haya aceptado por el marco de Azure. Por ejemplo, el siguiente código del archivo `Global.asax` de la solución de ejemplo controla el evento `RoleEnvironment.Changed`. Examina cada opción de configuración y, cuando encuentra "CustomSetting", llama a una función que aplica la nueva configuración en el componente apropiado de la aplicación. 

```csharp
private void RoleEnvironment_Changed(object sender, 
                               RoleEnvironmentChangedEventArgs e)
{
  Trace.TraceInformation("Updating instance with new configuration settings.");

  foreach (var settingChange in
           e.Changes.OfType<RoleEnvironmentConfigurationSettingChange>())
  {
    if (string.Equals(settingChange.ConfigurationSettingName, 
                      CustomSettingName, 
                      StringComparison.Ordinal))
    {
      // Execute a function to update the configuration of the component.
      ConfigureFromSetting(CustomSettingName );
    }
  }
}
```

Tenga en cuenta que si se produce un error al cancelar un cambio de configuración, pero no se aplica el valor nuevo en el componente de la aplicación, entonces el cambio no surtirá efecto hasta la próxima vez que se reinicie la aplicación. Esto puede provocar un comportamiento inesperado, especialmente si la instancia de rol de hospedaje se reinicia automáticamente por Azure como parte de sus operaciones regulares de mantenimiento&mdash;punto en el que se aplicará el nuevo valor de configuración.

## <a name="related-patterns-and-guidance"></a>Orientación y patrones relacionados

- Se encuentra disponible un ejemplo que demuestra este patrón en [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/samples/runtime-reconfiguration).
- Mover la información de configuración fuera del paquete de implementación de la aplicación a una ubicación centralizada puede proporcionar un control y una administración más sencilla de los datos de configuración, y permite compartirlos en aplicaciones e instancias de la aplicación. Para obtener más información, consulte [External Configuration Store pattern (patrón de almacén de configuración externo)](external-configuration-store.md). 



<!--HONumber=Nov16_HO3-->



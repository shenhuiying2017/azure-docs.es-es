<properties
   pageTitle="Paquetes de NuGet | Microsoft Azure"
   description="Guía sobre paquetes de NuGet para el trabajo de la directiva de reintento general."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/09/2015"
   ms.author="masashin"/>

# Paquetes de NuGet

<p class="lead">A medida que comienzan a comunicarse más componentes, se hace más importante que los errores transitorios se controlen de manera inteligente. El trabajo de tratamiento de errores transitorios administrado por el paquete NuGet de directivas de reintento puede ayudar a controlar los reintentos dentro de una sola instancia.</p>

> Este documento se basa en un borrador como prueba de concepto. No es la orientación revisada real.

El código de patrones y prácticas `TransientFaultHandling` se recomienda para el trabajo de directiva de reintento general.

```
Install-Package EnterpriseLibrary.WindowsAzure.TransientFaultHandling
```

## Configuración

En la sección se incluye la información de configuración para la característica de reintento:

Parámetro | Descripción
-------------------- | ----------------------
MaximumExecutionTime | Tiempo de ejecución máximo para la solicitud, incluidos todos los posibles intentos de reintentos.
ServerTimeOut | Intervalo de tiempo de espera del servidor para la solicitud
RetryPolicy | Directiva de reintento. Vea la sección de directivas siguiente

```csharp
/// <summary>
/// An interface required for request option types.
/// </summary>
public interface IRequestOptions
{
    IRetryPolicy RetryPolicy { get; set; }

    TimeSpan? ServerTimeout { get; set; }

    TimeSpan? MaximumExecutionTime { get; set; }
}
```

Mediante programación:

- Compatibilidad con la configuración en el cliente.
- Habilitar la invalidación en las operaciones proporcionadas por el cliente

Archivo de configuración:

```xml
<RetryPolicyConfiguration defaultRetryStrategy="Fixed Interval Retry Strategy">
    <linearInterval name="Fixed Interval Retry Strategy"
	retryInterval="00:00:01" maxRetryCount="10" />
    <exponentialBackoff name="Backoff Retry Strategy" minBackoff="00:00:01"
        maxBackoff="00:00:30" deltaBackoff="00:00:10" maxRetryCount="10"
        fastFirst="false"/>
</RetryPolicyConfiguration>
```

## Directivas

### Exponencial

Se usa para el espaciado de los intentos repetidos de las invocaciones de servicio exponencialmente para evitar la limitación del servicio.

__Enfoque:__

aumentar exponencialmente el intervalo de retroceso entre intentos posteriores. Agregar selección aleatoria a (+/-20 %) para que el intervalo de retroceso evite que todos los clientes reintenten simultáneamente

__Configuración:__

Parámetro | Descripción
-------------------- | -------------------------------------------------------
maxAttempt | Número de intentos de reintentos.
deltaBackoff | Intervalo de retroceso entre reintentos. Se usará múltiplos de este período de tiempo para los intentos de reintentos posteriores.
MinBackoff | Se agrega a todos los intervalos de reintentos calculados a partir de deltaBackoff.
FastFirst | Primer reintento inmediato
MaxBackoff | MaxBackoff se usa si el intervalo de reintento calculado es mayor que MaxBackoff. No se puede cambiar este valor.

__Lógica de implementación:__

```csharp
if(!ExponentialRetry.FastFirst){
    Random r = new Random();
    double increment = (Math.Pow(2, currentRetryCount) - 1) * r.Next((int)(this.deltaBackoff.TotalMilliseconds * 0.8), (int)(this.deltaBackoff.TotalMilliseconds * 1.2));
    retryInterval = (increment < 0) ? ExponentialRetry.MaxBackoff :
    TimeSpan.FromMilliseconds(Math.Min(ExponentialRetry.MaxBackoff.TotalMilliseconds, ExponentialRetry.MinBackoff.TotalMilliseconds + increment));
} else {
    retryInterval = TimeSpan.Zero;
}
```

## Lineal

Se usa para el espaciado de intentos repetidos de invocaciones de servicio de manera lineal para evitar la limitación del servicio.

__Enfoque:__

realizar un número especificado de reintentos, usando un intervalo de tiempo fijo especificado entre los reintentos. Agregue la selección aleatoria a (+/-20%) para el intervalo de retroceso para evitar que todos los clientes reintenten simultáneamente.

__Configuración:__

Parámetro | Descripción
-------------------- | -------------------------------------------------------
maxAttempt | Número de intentos de reintentos.
deltaBackoff | Intervalo de retroceso entre reintentos.
FastFirst | Primer reintento inmediato

__Lógica de implementación:__

```csharp
if(!ExponentialRetry.FastFirst) {
    Random r = new Random();
    retryInterval = TimeSpan.FromMilliseconds(r.Next((int)(
    this.deltaBackoff.TotalMilliseconds * 0.8), (int)(this.deltaBackoff.TotalMilliseconds * 1.2)));
} else {
    retryInterval = TimeSpan.Zero;
}
```

## Adaptable

Se usa para el espaciado de los intentos repetidos de las invocaciones de servicio basándose en el código de error/metadatos pasados por el servicio en el encabezado de respuesta.

__Enfoque:__

realizar un número especificado de reintentos usando un intervalo de retroceso calculado en función del código de error/metadatos pasados por el servicio en el encabezado de respuesta


__Configuración:__

no configurable

__Lógica de implementación:__

basada en el código de error/metadatos pasados por el servicio en el encabezado de respuesta

__Salto de circuito:__

basado en el [interruptor](http://msdn.microsoft.com/library/dn589784.aspx)

## Extensibilidad

Interfaz pública que se puede implementar para proporcionar la directiva de reintento personalizado

```csharp
public interface IRetryPolicy
{
    /// <summary>
    /// Generates a new retry policy for the current request attempt.
    /// </summary>
    IRetryPolicy CreateInstance();

    /// <summary>
    /// Determines whether the operation should be retried and the interval until the next retry.
    /// </summary>
    /// <param name="currentRetryCount">An integer specifying the number of retries for the given operation. A value of zero signifies this is the first error encountered.</param>
    /// <param name="statusCode">An integer containing the status code for the last operation.</param>
    /// <param name="retryInterval">A <see cref="TimeSpan"/> indicating the interval to wait until the next retry.</param>
    /// <returns><c>true</c> if the operation should be retried; otherwise, <c>false</c>.</returns>
    bool ShouldRetry(int currentRetryCount, int statusCode, out TimeSpan retryInterval);
}
```

## Telemetría

Reintentos de registro como eventos ETW mediante un EventSource. Estos son los campos que se deben registrar para cada intento de reintento.

Parámetro | Descripción
-------------------- | -------------------------------------------------------
requestId | ""
policyType | "RetryExponential"
operación | "Get:https://retry-guidance-tests.servicebus.windows.net/TestQueue/?api-version=2014-05"
operationStartTime | "5/9/2014 10:00:13 PM"
operationEndTime | "5/9/2014 10:00:14 PM"
iteration | "0"
iterationSleep | "00:00:00.1000000"
lastExceptionType | "Microsoft.ServiceBus.Messaging.MessagingCommunicationException"
exceptionMessage | "No se puede resolver el nombre remoto: 'retry-guidance-tests.servicebus.windows.net'.TrackingId:6a26f99c-dc6d-422e-8565-f89fdd0d4fe3,TimeStamp:9/5/2014 10:00:13 PM"

<!---HONumber=62-->
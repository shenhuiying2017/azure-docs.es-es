```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    }
}
```

|Propiedad  |Valor predeterminado | Descripción |
|---------|---------|---------| 
|routePrefix|api|Prefijo de ruta que se aplica a todas las rutas. Use una cadena vacía para quitar el prefijo predeterminado. |
|maxOutstandingRequests|-1|Número máximo de solicitudes pendientes que se mantendrán en un momento dado (-1 significa sin enlazar). El límite incluye las solicitudes que están en cola pero no han empezado a ejecutarse, así como todas las ejecuciones en curso. Se rechazan todas las solicitudes entrantes que superen este límite con una respuesta 429 "Too Busy" (demasiado ocupado). Los autores de las llamadas pueden utilizar esa respuesta para emplear estrategias de reintento basadas en el tiempo. Esta opción de configuración controla solo los movimiento de la cola que se producen dentro de la ruta de ejecución del host de trabajo. Otras colas, como la cola de solicitudes ASP.NET, no se ven afectadas por esta opción de configuración. |
|maxConcurrentRequests|-1|Número máximo de funciones HTTP que se ejecutarán en paralelo (-1 significa sin enlazar). Por ejemplo, podría establecer un límite si las funciones HTTP utilizan demasiados recursos del sistema cuando la simultaneidad es alta. O bien, si sus funciones realizan solicitudes de salida a un servicio de terceros, puede que haya que limitar la velocidad de dichas llamadas.|
|dynamicThrottlesEnabled|false|Hace que la canalización de procesamiento de solicitudes compruebe periódicamente los contadores de rendimiento del sistema. Los contadores incluyen conexiones, subprocesos, procesos, memoria y CPU. Si alguno de los contadores supera un umbral predefinido (80 %), las solicitudes se rechazan con una respuesta 429 "Too Busy" (demasiado ocupado) hasta que los contadores vuelvan a los niveles normales.|

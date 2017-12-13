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
|maxOutstandingRequests|-1|Número máximo de solicitudes pendientes que se mantienen en un momento dado. Este límite incluye las solicitudes que están en cola pero no han empezado a ejecutarse, así como todas las ejecuciones en curso. Se rechazan todas las solicitudes entrantes que superen este límite con una respuesta 429 "Too Busy" (demasiado ocupado). Esto permite que los llamadores empleen estrategias de reintento basadas en tiempo y también le ayuda a controlar las latencias de solicitud máximas. Únicamente se controlan los movimientos de la cola que se producen dentro de la ruta de ejecución del host del script. Otras colas, como la cola de solicitudes de ASP.NET, siguen en efecto y no se ven alteradas por esta opción de configuración. El valor predeterminado es ilimitado.|
|maxConcurrentRequests|-1|Número máximo de funciones HTTP que se ejecutarán en paralelo. Esto permite controlar la simultaneidad, que a su vez puede ayudar a administrar el uso de recursos. Por ejemplo, podría tener una función HTTP que utiliza una gran cantidad de recursos del sistema (memoria/cpu/sockets) y causa problemas cuando la simultaneidad es demasiado alta. O bien podría tener una función que realiza solicitudes de salida a un servicio de terceros y puede que haya que limitar la velocidad de dichas llamadas. En estos casos puede ayudar aplicar una limitación. El valor predeterminado es ilimitado.|
|dynamicThrottlesEnabled|false|Cuando se habilita, esta configuración hace que la canalización de procesamiento de la solicitud compruebe periódicamente contadores de rendimiento del sistema como conexiones, subprocesos, procesos, memoria o cpu y, si cualquiera de esos contadores superan un umbral alto integrado (80 %), las solicitudes se rechazarán con una respuesta 429 "Ocupado" hasta que los contadores vuelvan a niveles normales.|

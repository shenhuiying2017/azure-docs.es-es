```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|Propiedad  |Valor predeterminado | Descripción |
|---------|---------|---------| 
|maxConcurrentCalls|16|Número máximo de llamadas simultáneas a la devolución de llamada que el bombeo de mensajes debe iniciar. De forma predeterminada, el entorno de ejecución de Functions procesa simultáneamente varios mensajes. Para indicar al entorno de ejecución que procese solo los mensajes de una única cola o tema, establezca `maxConcurrentCalls` en 1. | 
|prefetchCount|N/D|Valor predeterminado de PrefetchCount que utilizará el receptor de mensajes subyacente.| 
|autoRenewTimeout|00:05:00|Duración máxima dentro de la cual el bloqueo de mensajes se renovará automáticamente.| 

```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|Propiedad  |Valor predeterminado | Descripción |
|---------|---------|---------| 
|maxPollingInterval|60000|Intervalo máximo, en milisegundos, entre sondeos de la cola.| 
|visibilityTimeout|0|Intervalo de tiempo entre los reintentos cuando se produce un error al procesar un mensaje.| 
|batchSize|16|Número de mensajes en cola para recuperar y procesar en paralelo. El valor máximo es 32.| 
|maxDequeueCount|5|Número de veces que se intenta procesar un mensaje antes de pasarlo a la cola de mensajes dudosos.| 
|newBatchThreshold|batchSize/2|Umbral en el que se debe capturar un nuevo lote de mensajes.| 

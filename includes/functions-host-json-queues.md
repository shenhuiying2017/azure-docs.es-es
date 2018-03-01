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

|Propiedad  |Valor predeterminado | DESCRIPCIÓN |
|---------|---------|---------| 
|maxPollingInterval|60000|Intervalo máximo, en milisegundos, entre sondeos de la cola.| 
|visibilityTimeout|0|Intervalo de tiempo entre los reintentos cuando se produce un error al procesar un mensaje.| 
|batchSize|16|El número de mensajes en cola que el runtime de Functions recupera simultáneamente y procesa en paralelo. Cuando el número que se está procesando llega a `newBatchThreshold` el runtime obtiene otro lote y empieza a procesar esos mensajes. Por lo tanto, el número máximo de mensajes simultáneos que se procesan por función es `batchSize` más `newBatchThreshold`. Este límite se aplica por separado a cada función desencadenada por la cola. <br><br>Si desea evitar la ejecución en paralelo de los mensajes de una cola, puede establecer `batchSize` en 1. Sin embargo, este valor solo elimina la simultaneidad siempre y cuando la aplicación de función se ejecute en una única máquina virtual (VM). Si la aplicación de función se escala horizontalmente a varias máquinas virtuales, cada una de ellas podría ejecutar una instancia de cada función desencadenada por la cola.<br><br>El valor máximo de `batchSize` es 32. | 
|maxDequeueCount|5|Número de veces que se intenta procesar un mensaje antes de pasarlo a la cola de mensajes dudosos.| 
|newBatchThreshold|batchSize/2|Siempre que el número de mensajes que se procesan simultáneamente llega a este número, el runtime recupera otro lote.| 




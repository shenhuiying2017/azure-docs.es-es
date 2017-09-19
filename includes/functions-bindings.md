| Tipo | Servicio | Trigger* | Entrada | Salida |  
| --- | --- | --- | --- | --- |  
| [Programación](../articles/azure-functions/functions-bindings-timer.md)  |Funciones de Azure |✔ | | |  
| [HTTP (REST o webhook)](../articles/azure-functions/functions-bindings-http-webhook.md) |Funciones de Azure |✔ |  |✔\** |  
| [Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md) |Almacenamiento de Azure |✔ |✔ |✔ |  
| [Eventos](../articles/azure-functions/functions-bindings-event-hubs.md) |Centros de eventos de Azure |✔ | |✔ |  
| [Colas](../articles/azure-functions/functions-bindings-storage-queue.md) |Almacenamiento de Azure |✔ | |✔ |  
| [Colas y temas](../articles/azure-functions/functions-bindings-service-bus.md) |Bus de servicio |✔ | |✔ |  
| [Tablas de almacenamiento](../articles/azure-functions/functions-bindings-storage-table.md) |Almacenamiento de Azure | |✔ |✔ |  
| [Tablas SQL](../articles/azure-functions/functions-bindings-mobile-apps.md) |Aplicaciones móviles de Azure | |✔ |✔ |  
| [NoSQL DB](../articles/azure-functions/functions-bindings-documentdb.md) | Azure Cosmos DB | |✔ |✔ |  
| [notificaciones push](../articles/azure-functions/functions-bindings-notification-hubs.md) |Centros de notificaciones de Azure | | |✔ |  
| [Texto de SMS de Twilio](../articles/azure-functions/functions-bindings-twilio.md) |Twilio | | |✔ |
| [Correo electrónico SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md) | SendGrid | | |✔ |

(\*: todos los desencadenadores tienen datos de entrada asociados)

(\**: el enlace de salida HTTP requiere un desencadenador de HTTP)



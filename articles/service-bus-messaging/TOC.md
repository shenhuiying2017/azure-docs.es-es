# Información general
## [¿Qué es la mensajería de Service Bus?](service-bus-messaging-overview.md)
## [Elementos fundamentales de Service Bus](service-bus-fundamentals-hybrid-solutions.md)
## [Arquitectura de Service Bus](service-bus-architecture.md)
## [P+F](service-bus-faq.md)

# Introducción
## [Creación de un espacio de nombres](service-bus-create-namespace-portal.md)
## Uso de colas
### [.NET](service-bus-dotnet-get-started-with-queues.md)
### [Java](service-bus-java-how-to-use-queues.md)
### [Node.js](service-bus-nodejs-how-to-use-queues.md)
### [PHP](service-bus-php-how-to-use-queues.md)
### [Python](service-bus-python-how-to-use-queues.md)
### [Ruby](service-bus-ruby-how-to-use-queues.md)
### [REST](/rest/api/servicebus/queues)
## Uso de temas y suscripciones
### [.NET](service-bus-dotnet-how-to-use-topics-subscriptions.md)
### [Java](service-bus-java-how-to-use-topics-subscriptions.md)
### [Node.js](service-bus-nodejs-how-to-use-topics-subscriptions.md)
### [PHP](service-bus-php-how-to-use-topics-subscriptions.md)
### [Python](service-bus-python-how-to-use-topics-subscriptions.md)
### [Ruby](service-bus-ruby-how-to-use-topics-subscriptions.md)

# Procedimientos
## Planeamiento y diseño
### [Identidad de servicio administrada (versión preliminar)](service-bus-managed-service-identity.md)
### [Control de acceso basado en rol (versión preliminar)](service-bus-role-based-access-control.md)
### [Mensajería premium](service-bus-premium-messaging.md)
### [Comparación de colas de Azure con colas de Service Bus](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
### [Optimización del rendimiento](service-bus-performance-improvements.md)
### [Recuperación ante desastres y replicación geográfica](service-bus-geo-dr.md)
### [Mensajería asincrónica y alta disponibilidad](service-bus-async-messaging.md)
### [Control de interrupciones y desastres](service-bus-outages-disasters.md)

## Desarrollo
### [Compilación de una aplicación de Service Bus de niveles múltiples](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)
### Administración de mensajes
#### [Colas, temas y suscripciones](service-bus-queues-topics-subscriptions.md)
#### [Mensajes, cargas y serialización](service-bus-messages-payloads.md)
#### [Transferencia, bloqueo y liquidación de mensajes](message-transfers-locks-settlement.md)
#### [Secuenciación y marcas de tiempo del mensaje](message-sequencing.md)
#### [Expiración de mensajes (período de vida)](message-expiration.md)
### [Autenticación y autorización](service-bus-authentication-and-authorization.md)
#### [Migración de ACS a SAS](service-bus-migrate-acs-sas.md)
#### [Autenticación con Firmas de acceso compartido](service-bus-sas.md)
### [Filtros y acciones de temas](topic-filters.md)
### [Temas y colas con particiones](service-bus-partitioning.md)
### [Sesiones de mensajes](message-sessions.md)
### AMQP
#### [Introducción a AMQP](service-bus-amqp-overview.md)
#### [.NET](service-bus-amqp-dotnet.md)
#### [Java](service-bus-amqp-java.md)
#### [Java Message Service y AMQP](service-bus-java-how-to-use-jms-api-amqp.md)
#### [Guía del protocolo AMQP](service-bus-amqp-protocol-guide.md)
#### [Operaciones basadas en solicitud/respuesta de AMQP](service-bus-amqp-request-response.md)
### Características avanzadas
#### [Colas de mensajes fallidos](service-bus-dead-letter-queues.md)
#### [Captura previa de mensajes](service-bus-prefetch.md)
#### [Detección de mensajes duplicados](duplicate-detection.md)
#### [Contadores de mensajes](message-counters.md)
#### [Aplazamiento de mensajes](message-deferral.md)
#### [Exploración de mensajes](message-browsing.md)
#### [Encadenamiento de entidades con reenvío automático](service-bus-auto-forwarding.md)
#### [Procesamiento de transacciones](service-bus-transactions.md)
#### [Implementación de los espacios de nombres emparejados](service-bus-paired-namespaces.md)
### [Seguimiento diagnóstico integral](service-bus-end-to-end-tracing.md)
## administración
### [Supervisión de Service Bus con Azure Monitor](service-bus-metrics-azure-monitor.md)
### [Bibliotecas de administración de Service Bus](service-bus-management-libraries.md)
### [Registros de diagnóstico](service-bus-diagnostic-logs.md)
### [Suspensión y reactivación de entidades de mensajería](entity-suspend.md)
### [Uso de plantillas de Azure Resource Manager](service-bus-resource-manager-overview.md)
#### [Creación de un espacio de nombres](service-bus-resource-manager-namespace.md)
#### [Creación de un espacio de nombres y una cola](service-bus-resource-manager-namespace-queue.md)
#### [Creación de un espacio de nombres con un tema y una suscripción](service-bus-resource-manager-namespace-topic.md)
#### [Creación de una regla de autorización para el espacio de nombres y la cola](service-bus-resource-manager-namespace-auth-rule.md)
#### [Creación de un espacio de nombres con un tema, una suscripción y una regla](service-bus-resource-manager-namespace-topic-with-rule.md)
#### 
### [Uso de Azure PowerShell para aprovisionar entidades](service-bus-manage-with-ps.md)

# Referencia
## .NET
### [Microsoft.ServiceBus.Messaging (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging)
### [Microsoft.Azure.ServiceBus (.NET Standard)](/dotnet/api/microsoft.azure.servicebus)
## [Java](/java/api/overview/azure/servicebus)
## [Azure PowerShell](/powershell/module/azurerm.servicebus)
## [REST](/rest/api/servicebus)
## [Excepciones](service-bus-messaging-exceptions.md)
## [Cuotas](service-bus-quotas.md)
## [Sintaxis de SQLFilter](service-bus-messaging-sql-filter.md)
## [Sintaxis de SQLRuleAction](service-bus-messaging-sql-rule-action.md)

# Recursos
## [Azure Roadmap](https://azure.microsoft.com/roadmap/?category=enterprise-integration)
## [Blog](https://blogs.msdn.microsoft.com/servicebus/)
## [Foros de MSDN](https://social.msdn.microsoft.com/forums/home?forum=servbus)
## [Precios](https://azure.microsoft.com/pricing/details/service-bus/)
## [Calculadora de precios](https://azure.microsoft.com/pricing/calculator/)
## [Detalles de precios](service-bus-pricing-billing.md)
## [Ejemplos](service-bus-samples.md)
## [ServiceBus360](https://www.servicebus360.com/)
## [Explorador de Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer)
## [Actualizaciones del servicio](https://azure.microsoft.com/updates/?product=service-bus)
## [Desbordamiento de la pila](http://stackoverflow.com/questions/tagged/azureservicebus)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=service-bus)



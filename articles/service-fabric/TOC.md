# [Documentación de Service Fabric](/azure/service-fabric)
# Información general
## [¿Qué es Service Fabric?](service-fabric-overview.md)

# Inicios rápidos
## [Creación de una aplicación .NET](service-fabric-quickstart-dotnet.md)

# Tutoriales
## Implementación de una aplicación .NET
### [1- Compilación de una aplicación .NET](service-fabric-tutorial-create-dotnet-app.md)
### [2- Implementación de la aplicación](service-fabric-tutorial-deploy-app-to-party-cluster.md)
### [3- Configuración de CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)

## Migración mediante lift-and-shift de una aplicación
### [1- Creación de un clúster seguro en Azure](service-fabric-tutorial-create-cluster-azure-ps.md)
### [2- Implementación de una aplicación de .NET con Docker Compose](service-fabric-host-app-in-a-container.md)

# Muestras
## [Ejemplos de código](https://azure.microsoft.com/en-us/resources/samples/?service=service-fabric)
## [PowerShell](service-fabric-powershell-samples.md)
## [CLI de Service Fabric](samples-cli.md)
### [Implementación del ejemplo](scripts/cli-deploy-application.md)
### [Eliminación del ejemplo](scripts/cli-remove-application.md)
# Conceptos
## [Descripción de los microservicios](service-fabric-overview-microservices.md)
## [Idea general](service-fabric-content-roadmap.md)
## [Escenarios de aplicación](service-fabric-application-scenarios.md)
## [Patrones y escenarios](service-fabric-patterns-and-scenarios.md)
## [Arquitectura](service-fabric-architecture.md)
## [Terminología](service-fabric-technical-overview.md)

## Compilación de aplicaciones y servicios
### Modelos de programación admitidos
#### [Información general](service-fabric-choose-framework.md)
#### Contenedores
##### [Información general](service-fabric-containers-overview.md)
##### [Docker Compose (versión preliminar)](service-fabric-docker-compose.md)
##### [Regulación de recursos](service-fabric-resource-governance.md)
#### Reliable Services
##### [Información general](service-fabric-reliable-services-introduction.md)
##### [Ciclo de vida de Reliable Services: C#](service-fabric-reliable-services-lifecycle.md)
##### [Ciclo de vida de Reliable Services: Java](service-fabric-reliable-services-lifecycle-java.md)
##### [Colecciones confiables](service-fabric-reliable-services-reliable-collections.md)
##### [Recomendaciones y directrices de colecciones confiables](service-fabric-reliable-services-reliable-collections-guidelines.md)
##### [Trabajo con Reliable Collections](service-fabric-work-with-reliable-collections.md)
##### [Transacciones y bloqueos](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
##### [Cola simultánea confiable](service-fabric-reliable-services-reliable-concurrent-queue.md)
##### [Serialización de colección confiable](service-fabric-reliable-services-reliable-collections-serialization.md)
##### [Reliable State Manager y Reliable Collection internos](service-fabric-reliable-services-reliable-collections-internals.md)
##### [Uso avanzado](service-fabric-reliable-services-advanced-usage.md)

#### Reliable Actors
##### [Información general](service-fabric-reliable-actors-introduction.md)
##### [Arquitectura](service-fabric-reliable-actors-platform.md)
##### [Ciclo de vida y recolección de elementos no utilizados](service-fabric-reliable-actors-lifecycle.md)
##### [Administración de estados](service-fabric-reliable-actors-state-management.md)
##### [Polimorfismo](service-fabric-reliable-actors-polymorphism.md)
##### [Reentrada](service-fabric-reliable-actors-reentrancy.md)
##### [Serialización del tipo](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

### [Modelo de aplicación](service-fabric-application-model.md)
### [Modelo de alojamiento](service-fabric-hosting-model.md)

### Services
#### [Recursos del servicio](service-fabric-service-manifest-resources.md)
#### [Estado de servicio](service-fabric-concepts-state.md)
#### [Partición de servicio](service-fabric-concepts-partitioning.md)
#### [Disponibilidad de servicios](service-fabric-availability-services.md)
#### Comunicación de servicio
##### [Información general](service-fabric-connect-and-communicate-with-services.md)
##### [Servicio DNS](service-fabric-dnsservice.md)
##### [Proxy inverso](service-fabric-reverseproxy.md)
##### [Configuración de proxy inverso para una comunicación segura](service-fabric-reverseproxy-configure-secure-communication.md)
##### [Diagnóstico de proxy inverso](service-fabric-reverse-proxy-diagnostics.md)
### [Escalabilidad de las aplicaciones](service-fabric-concepts-scalability.md)
### [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

### [Planeamiento de la capacidad de aplicación](service-fabric-capacity-planning.md)

## Administración de aplicaciones
### [Información general](service-fabric-application-lifecycle.md)
### [Configuración de ImageStoreConnectionString](service-fabric-image-store-connection-string.md)
### Actualización de aplicaciones
#### [Información general](service-fabric-application-upgrade.md)
#### [Configuración](service-fabric-visualstudio-configure-upgrade.md)
#### [Parámetros de actualización de la aplicación](service-fabric-application-upgrade-parameters.md)
#### [Serialización de datos en actualizaciones de aplicaciones](service-fabric-application-upgrade-data-serialization.md)
#### [Temas avanzados sobre actualización de aplicaciones](service-fabric-application-upgrade-advanced.md)
### [Introducción a los análisis de errores](service-fabric-testability-overview.md)

## Creación y administración de clústeres
### [Información general](service-fabric-deploy-anywhere.md)
### [Service Fabric en Linux](service-fabric-linux-overview.md)
### Planeación y preparación
#### [Planificación de capacidad](service-fabric-cluster-capacity.md)
#### [Recuperación ante desastres](service-fabric-disaster-recovery.md)
### [Descripción de un clúster](service-fabric-cluster-resource-manager-cluster-description.md)
### [Seguridad de clúster](service-fabric-cluster-security.md)
### [Diferencias de características entre Linux y Windows](service-fabric-linux-windows-differences.md)
### Clústeres en Azure
#### [Tipos de nodos y conjuntos de escalado de máquinas virtuales](service-fabric-cluster-nodetypes.md)
#### [Patrones de redes de clúster](service-fabric-patterns-networking.md)
### Administrador de recursos del clúster
#### [Información general](service-fabric-cluster-resource-manager-introduction.md)
#### [Arquitectura](service-fabric-cluster-resource-manager-architecture.md)
#### [Descripción de un clúster](service-fabric-cluster-resource-manager-cluster-description.md)
#### [Introducción a los grupos de aplicaciones](service-fabric-cluster-resource-manager-application-groups.md)
#### [Definición de la configuración de Resource Manager de clúster](service-fabric-cluster-resource-manager-configure-services.md)
#### [Métricas de consumo de recursos](service-fabric-cluster-resource-manager-metrics.md)
#### [Uso de afinidad de servicios](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
#### [Directivas de colocación de servicios](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
#### [Administración de un clúster](service-fabric-cluster-resource-manager-management-integration.md)
#### [Desfragmentación de clústeres](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
#### [Equilibrado de un clúster](service-fabric-cluster-resource-manager-balancing.md)
#### [Limitaciones](service-fabric-cluster-resource-manager-advanced-throttling.md)
#### [Movimiento de servicio](service-fabric-cluster-resource-manager-movement-cost.md)

## Supervisión y diagnóstico
### [Información general](service-fabric-diagnostics-overview.md)
### [Modelo de mantenimiento](service-fabric-health-introduction.md)
### [Diagnósticos de Reliable Services con estado](service-fabric-reliable-services-diagnostics.md)
### [Diagnósticos en Reliable Actors](service-fabric-reliable-actors-diagnostics.md)

# Guías de procedimientos
## Configuración de su entorno de desarrollo
### [Windows](service-fabric-get-started.md)
### [Linux](service-fabric-get-started-linux.md)
### [Mac OS](service-fabric-get-started-mac.md)

## Creación de una aplicación
### Compilación de un servicio ejecutable de invitado
#### [Hospedaje de una aplicación de Node.js en Windows](quickstart-guest-app.md)
#### [Implementación de una aplicación ejecutable de invitado](service-fabric-deploy-existing-app.md)
#### [Implementación de varios ejecutables invitados](service-fabric-deploy-multiple-apps.md)
### Compilación de un servicio de contenedor
#### [Creación de una aplicación contenedora de Windows](service-fabric-get-started-containers.md)
#### [Creación de una aplicación contenedora de Linux](service-fabric-get-started-containers-linux.md)
#### [Seguridad del contenedor](service-fabric-securing-containers.md)
#### [Docker Compose (versión preliminar)](service-fabric-docker-compose.md)
#### [Regulación de recursos de contenedores y servicios](service-fabric-resource-governance.md)
#### [Volumen y controladores de registro](service-fabric-containers-volume-logging-drivers.md)
#### [Servicios dentro de contenedores](service-fabric-services-inside-containers.md)
#### [Modos de redes de contenedor](service-fabric-networking-modes.md)

### Compilación de un servicio de Reliable Services
#### [Información general](service-fabric-reliable-services-introduction.md)
#### Conceptos
##### [Ciclo de vida de Reliable Services: C#](service-fabric-reliable-services-lifecycle.md)
##### [Ciclo de vida de Reliable Services: Java](service-fabric-reliable-services-lifecycle-java.md)

#### Reliable Collections
##### [Colecciones confiables](service-fabric-reliable-services-reliable-collections.md)
##### [Recomendaciones y directrices de colecciones confiables](service-fabric-reliable-services-reliable-collections-guidelines.md)
##### [Trabajo con Reliable Collections](service-fabric-work-with-reliable-collections.md)
##### [Transacciones y bloqueos](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
##### [Cola simultánea confiable](service-fabric-reliable-services-reliable-concurrent-queue.md)
##### [Serialización de colección confiable](service-fabric-reliable-services-reliable-collections-serialization.md)
##### [Reliable State Manager y Reliable Collection internos](service-fabric-reliable-services-reliable-collections-internals.md)

#### Introducción
##### [C# en Windows](service-fabric-reliable-services-quick-start.md)
##### [Java en Linux](service-fabric-reliable-services-quick-start-java.md)
##### [Creación de una aplicación de C# en Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
#### Comunicación con los servicios
##### [Comunicación con Reliable Services](service-fabric-reliable-services-communication.md)

##### [Comunicación remota del servicio: C#](service-fabric-reliable-services-communication-remoting.md)
##### [Comunicación remota del servicio: Java](service-fabric-reliable-services-communication-remoting-java.md)
##### [WCF](service-fabric-reliable-services-communication-wcf.md)
##### [Comunicaciones seguras: C#](service-fabric-reliable-services-secure-communication.md)
##### [Comunicaciones seguras: Java](service-fabric-reliable-services-secure-communication-java.md)

#### [Configuración](service-fabric-reliable-services-configuration.md)
#### [Envío de notificaciones](service-fabric-reliable-services-notifications.md)
#### [Copia de seguridad y restauración](service-fabric-reliable-services-backup-restore.md)

### Compilación de un servicio de Reliable Actors
#### Introducción
##### [C# en Windows](service-fabric-reliable-actors-get-started.md)
##### [Java en Linux](service-fabric-reliable-actors-get-started-java.md)
##### [Actor de Java en Linux](service-fabric-create-your-first-linux-application-with-java.md)
#### [Envío de notificaciones](service-fabric-reliable-actors-events.md)
#### [Configuración de recordatorios y temporizadores](service-fabric-reliable-actors-timers-reminders.md)
#### [Configuración de KvsActorStateProvider](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [Configuración de opciones de comunicación](service-fabric-reliable-actors-fabrictransportsettings.md)
#### [Configuración de ReliableDictionaryActorStateProvider](service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

### [Migración de la aplicación Java anterior para que sea compatible con Maven](service-fabric-migrate-old-javaapp-to-use-maven.md)

### [Configuración de proxy inverso para una comunicación segura](service-fabric-reverseproxy-configure-secure-communication.md)

### [Compilación de un servicio de ASP.NET Core](service-fabric-add-a-web-frontend.md)

### Configuración de seguridad
#### [Administración de secretos de aplicación](service-fabric-application-secret-management.md)  
#### [Configuración de directivas de seguridad para la aplicación](service-fabric-application-runas-security.md)

## Trabajo en un entorno de desarrollo de Windows
### [Administración de aplicaciones en Visual Studio](service-fabric-manage-application-in-visual-studio.md)
### [Configuración de conexiones seguras en Visual Studio](service-fabric-visualstudio-configure-secure-connections.md)
### [Configuración de la aplicación para varios entornos](service-fabric-manage-multiple-environment-app-configuration.md)
### [Depuración de un servicio .NET en VS](service-fabric-debugging-your-application.md)
### [Errores y excepciones habituales](service-fabric-errors-and-exceptions.md)
### [Supervisión y diagnóstico local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## Trabajo en un entorno de desarrollo de Linux
### [Introducción al complemento de Eclipse para el desarrollo de Java](service-fabric-get-started-eclipse.md)
### [Depuración de un servicio de Java en Eclipse](service-fabric-debugging-your-application-java.md)
### [Supervisión y diagnóstico local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

## Integración con API Management
### [Información general](service-fabric-api-management-overview.md)
### [Inicio rápido](service-fabric-api-management-quick-start.md)

## Migración desde Cloud Services
### [Comparación de Cloud Services con Service Fabric](service-fabric-cloud-services-migration-differences.md)
### [Migración a Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)
### [Procedimientos recomendados](/azure/architecture/service-fabric/migrate-from-cloud-services)

## Administración del ciclo de vida de las aplicaciones
### [Empaquetado de una aplicación](service-fabric-package-apps.md)

### Implementación o eliminación de aplicaciones
#### [Implementación de aplicaciones en un clúster local](service-fabric-get-started-with-a-local-cluster.md)
#### [PowerShell](service-fabric-deploy-remove-applications.md)
#### [CLI de Service Fabric](service-fabric-application-lifecycle-sfctl.md)
#### [Visual Studio](service-fabric-publish-app-remote-cluster.md)
#### [API de FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)

### Actualización de aplicaciones
#### [Actualización mediante PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
#### [Actualización mediante Visual Studio](service-fabric-application-upgrade-tutorial.md)
#### [Solución de problemas de actualizaciones de aplicaciones](service-fabric-application-upgrade-troubleshooting.md)

### Prueba de aplicaciones y servicios
#### [Prueba de la comunicación entre servicios](service-fabric-testability-scenarios-service-communication.md)
#### Simulación de errores
##### [Uso de caos controlado](service-fabric-controlled-chaos.md)
##### [Uso de acciones de prueba](service-fabric-testability-actions.md)
##### [Durante las cargas de trabajo](service-fabric-testability-workload-tests.md)
##### [Uso de escenarios de prueba](service-fabric-testability-scenarios.md)
##### [Uso de las API Node Transition](service-fabric-node-transition-apis.md)
#### [Prueba de carga de la aplicación](service-fabric-vso-load-test.md)

### Configuración de integración continua
#### [Configuración de integración continua con VSTS](service-fabric-set-up-continuous-integration.md)
#### [Implementación de una aplicación de Java para Linux con Jenkins](service-fabric-cicd-your-linux-java-application-with-jenkins.md)

## Creación y administración de clústeres
### Clústeres en Azure
#### Crear
##### [Creación del primer clúster en Azure](service-fabric-get-started-azure-cluster.md)
##### [Azure Portal](service-fabric-cluster-creation-via-portal.md)
##### [Administrador de recursos de Azure](service-fabric-cluster-creation-via-arm.md)
#### Escala
##### [Manualmente](service-fabric-cluster-scale-up-down.md)
##### [Mediante programación](service-fabric-cluster-programmatic-scaling.md)
#### [Actualización](service-fabric-cluster-upgrade.md)
#### [Establecer control de acceso](service-fabric-cluster-security-roles.md)
#### [Configuración](service-fabric-cluster-fabric-settings.md)
#### [Apertura de un puerto en el equilibrador de carga](create-load-balancer-rule.md)
#### [Administración de certificados del clúster](service-fabric-cluster-security-update-certs-azure.md)
#### [Eliminar](service-fabric-cluster-delete.md)

### Clústeres independientes
#### [Planeación y preparación para la implementación](service-fabric-cluster-standalone-deployment-preparation.md)
#### Crear
##### [Creación del primer clúster independiente](service-fabric-get-started-standalone-cluster.md)
##### [Creación local](service-fabric-cluster-creation-for-windows-server.md)
##### [Protección con certificados](service-fabric-windows-cluster-x509-security.md)  
##### [Protección mediante la seguridad de Windows](service-fabric-windows-cluster-windows-security.md)
##### [Contenido del paquete independiente](service-fabric-cluster-standalone-package-contents.md)
#### [Escala](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [Establecer control de acceso](service-fabric-cluster-security-roles.md)
#### [Configuración](service-fabric-cluster-manifest.md)
#### [Actualización](service-fabric-cluster-upgrade-windows-server.md)

### [Visualización de un clúster](service-fabric-visualizing-your-cluster.md)
### [Conexión a un clúster seguro](service-fabric-connect-to-secure-cluster.md)

### [Administración de un clúster mediante la CLI de Service Fabric](service-fabric-cli.md)
### [Aplicación de parches a nodos de clúster](service-fabric-patch-orchestration-application.md)

### Administración y orquestación de recursos de clúster
#### [Introducción a Resource Manager de clúster](service-fabric-cluster-resource-manager-introduction.md)
#### [Arquitectura de Resource Manager de clúster](service-fabric-cluster-resource-manager-architecture.md)
#### [Descripción de un clúster](service-fabric-cluster-resource-manager-cluster-description.md)
#### [Introducción a los grupos de aplicaciones](service-fabric-cluster-resource-manager-application-groups.md)
#### [Definición de la configuración de Resource Manager de clúster](service-fabric-cluster-resource-manager-configure-services.md)
#### [Métricas de consumo de recursos](service-fabric-cluster-resource-manager-metrics.md)
#### [Uso de afinidad de servicios](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
#### [Directivas de colocación de servicios](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
#### [Administración de un clúster](service-fabric-cluster-resource-manager-management-integration.md)
#### [Desfragmentación de clústeres](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
#### [Equilibrado de un clúster](service-fabric-cluster-resource-manager-balancing.md)
#### [Limitaciones](service-fabric-cluster-resource-manager-advanced-throttling.md)
#### [Movimiento de servicio](service-fabric-cluster-resource-manager-movement-cost.md)

## Supervisión y diagnóstico
### [Aplicaciones de supervisión y diagnóstico](service-fabric-diagnostics-overview.md)
### Generación de eventos
#### [Generación de eventos de nivel de plataforma](service-fabric-diagnostics-event-generation-infra.md)
##### [Canal operativo](service-fabric-diagnostics-event-generation-operational.md)
##### [Eventos de Reliable Services](service-fabric-reliable-services-diagnostics.md)
##### [Eventos de Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
##### [Métricas de rendimiento](service-fabric-diagnostics-event-generation-perf.md)
#### [Generación de eventos en el nivel de la aplicación](service-fabric-diagnostics-event-generation-app.md)
### Inspección del estado de un clúster y una aplicación
#### [Supervisión del estado de Service Fabric](service-fabric-health-introduction.md)
#### [Notificación y comprobación del estado del servicio](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
#### [Incorporación de informes de estado personalizados](service-fabric-report-health.md)
#### [Solución de problemas con informes de estado del sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
#### [Visualización de informes de estado](service-fabric-view-entities-aggregated-health.md)
#### [Supervisión de contenedores de Windows Server](service-fabric-diagnostics-containers-windowsserver.md)
### Agregación de eventos
#### [Agregación de eventos con EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)
#### Agregación de eventos con Azure Diagnostics
##### [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
##### [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
### Análisis de eventos
#### [Análisis de eventos con Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
#### [Análisis de eventos con OMS](service-fabric-diagnostics-event-analysis-oms.md)
### [Solución de problemas de un clúster local](service-fabric-troubleshoot-local-cluster-setup.md)

# Referencia
## [PowerShell (Azure)](/powershell/module/azurerm.servicefabric/)
## [PowerShell](/powershell/module/servicefabric/?view=azureservicefabricps)
## [CLI de Azure](/cli/azure/sf)
## [API de Java](/java/api/overview/azure/servicefabric)
## [.NET](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet)
## [REST](/rest/api/servicefabric)

# Recursos
## [Azure Roadmap](https://azure.microsoft.com/roadmap/)
## [Preguntas frecuentes](service-fabric-common-questions.md)
## [Ruta de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [Foro de MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)
## [Precios](https://azure.microsoft.com/pricing/details/service-fabric/)
## [Calculadora de precios](https://azure.microsoft.com/pricing/calculator/)
## [Código de ejemplo](http://aka.ms/servicefabricsamples)
## [Opciones de soporte técnico](service-fabric-support.md)
## [Actualizaciones del servicio](https://azure.microsoft.com/updates/?product=service-fabric)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)

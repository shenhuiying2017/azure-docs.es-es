
# Información general
## [¿Qué es Service Fabric?](service-fabric-overview.md)
## [Descripción de los microservicios](service-fabric-overview-microservices.md)
## [Escenarios de aplicación](service-fabric-application-scenarios.md)
## [Patrones y escenarios](service-fabric-patterns-and-scenarios.md)
## [Arquitectura](service-fabric-architecture.md)
## [Terminología](service-fabric-technical-overview.md)
## [Primer](service-fabric-content-roadmap.md)

# Introducción
## Configuración de su entorno de desarrollo
### [Windows](service-fabric-get-started.md)
### [Linux](service-fabric-get-started-linux.md)
### [Mac OS](service-fabric-get-started-mac.md)
## Crear su primera aplicación
### [C# en Windows](service-fabric-create-your-first-application-in-visual-studio.md)
### [Java en Linux](service-fabric-create-your-first-linux-application-with-java.md)
### [C# en Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
## [Implementación de aplicaciones en un clúster local](service-fabric-get-started-with-a-local-cluster.md)
## [Implementación de aplicaciones .NET en un contenedor](service-fabric-host-app-in-a-container.md)
## [Creación del primer clúster en Azure](service-fabric-get-started-azure-cluster.md)
## [Creación del primer clúster independiente](service-fabric-get-started-standalone-cluster.md)
## [Creación de la primera aplicación de contenedor](service-fabric-get-started-containers.md)

# Procedimientos
## Creación de una aplicación
  
### Conceptos
#### [Modelos de programación admitidos](service-fabric-choose-framework.md)
#### [Modelo de aplicación](service-fabric-application-model.md)
#### [Modelo de alojamiento](service-fabric-hosting-model.md)
#### [Recursos de manifiesto de servicio](service-fabric-service-manifest-resources.md)
#### [Estado de servicio](service-fabric-concepts-state.md)
#### [Partición de servicio](service-fabric-concepts-partitioning.md)
#### [Disponibilidad de servicios](service-fabric-availability-services.md)
#### [Escalabilidad de las aplicaciones](service-fabric-concepts-scalability.md)
#### [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

### [Planeamiento de la capacidad de las aplicaciones](service-fabric-capacity-planning.md)

### Compilación de un servicio ejecutable de invitado
#### [Implementación de una aplicación ejecutable de invitado](service-fabric-deploy-existing-app.md)
#### [Implementación de varios ejecutables invitados](service-fabric-deploy-multiple-apps.md)

### Compilación de un servicio de contenedor
#### [Información general](service-fabric-containers-overview.md)
#### [Implementación de un contenedor de Windows](service-fabric-deploy-container.md)
#### [Implementación de un contenedor de Linux](service-fabric-deploy-container-linux.md)
#### [Docker Compose (versión preliminar)](service-fabric-docker-compose.md)
#### [Regulación de recursos de contenedores y servicios](service-fabric-resource-governance.md)

### Compilación de un servicio con Reliable Services
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

#### Ciclo de vida de Reliable Services
#### [Configuración](service-fabric-reliable-services-configuration.md)
#### [Envío de notificaciones](service-fabric-reliable-services-notifications.md)
#### [Copia de seguridad y restauración](service-fabric-reliable-services-backup-restore.md)

#### Comunicación con los servicios
##### [Comunicación con Reliable Services](service-fabric-reliable-services-communication.md)
##### [Comunicación remota del servicio: C#](service-fabric-reliable-services-communication-remoting.md)
##### [Comunicación remota del servicio: Java](service-fabric-reliable-services-communication-remoting-java.md)
##### [WCF](service-fabric-reliable-services-communication-wcf.md)
##### [Comunicaciones seguras: C#](service-fabric-reliable-services-secure-communication.md)
##### [Comunicaciones seguras: Java](service-fabric-reliable-services-secure-communication-java.md)

#### [Uso avanzado](service-fabric-reliable-services-advanced-usage.md)

### Compilación de un servicio con Reliable Actors
#### [Información general](service-fabric-reliable-actors-introduction.md)
#### Conceptos
#### [Arquitectura](service-fabric-reliable-actors-platform.md)
#### [Ciclo de vida y recolección de elementos no utilizados](service-fabric-reliable-actors-lifecycle.md)
#### [Administración de estados](service-fabric-reliable-actors-state-management.md)
#### [Polimorfismo](service-fabric-reliable-actors-polymorphism.md)
#### [Reentrada](service-fabric-reliable-actors-reentrancy.md)
#### [Serialización del tipo](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

#### Introducción
##### [C# en Windows](service-fabric-reliable-actors-get-started.md)
##### [Java en Linux](service-fabric-reliable-actors-get-started-java.md)

#### [Envío de notificaciones](service-fabric-reliable-actors-events.md) 
#### [Configuración de recordatorios y temporizadores](service-fabric-reliable-actors-timers-reminders.md)
#### [Configuración de KvsActorStateProvider](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [Configuración de opciones de comunicación](service-fabric-reliable-actors-fabrictransportsettings.md) 
#### [Configuración de ReliableDictionaryActorStateProvider](service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

### Comunicación con los servicios
#### [Comunicación de servicio](service-fabric-connect-and-communicate-with-services.md)
#### [Servicio DNS](service-fabric-dnsservice.md)
#### [Proxy inverso](service-fabric-reverseproxy.md)

### [Adición de un front-end web](service-fabric-add-a-web-frontend.md)

### Trabajo en un IDE
#### [Introducción al complemento de Eclipse para el desarrollo de Java](service-fabric-get-started-eclipse.md)
#### [Administración de aplicaciones en Visual Studio](service-fabric-manage-application-in-visual-studio.md)
#### [Configuración de conexiones seguras en Visual Studio](service-fabric-visualstudio-configure-secure-connections.md)
#### [Configuración de la aplicación para varios entornos](service-fabric-manage-multiple-environment-app-configuration.md)

### Configuración de seguridad
#### [Administración de secretos de aplicación](service-fabric-application-secret-management.md)  
#### [Configuración de directivas de seguridad para la aplicación](service-fabric-application-runas-security.md)

### Depurar
#### [Depuración de un servicio de C# en VS](service-fabric-debugging-your-application.md)
#### [Depuración de un servicio de Java en Eclipse](service-fabric-debugging-your-application-java.md)
#### [Errores y excepciones habituales](service-fabric-errors-and-exceptions.md)

### Supervisión y diagnóstico local
#### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
#### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

### Migración desde Cloud Services
#### [Comparación de Cloud Services con Service Fabric](service-fabric-cloud-services-migration-differences.md)
#### [Migración a Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)
#### [Procedimientos recomendados](/azure/architecture/service-fabric/migrate-from-cloud-services)

## Administración del ciclo de vida de las aplicaciones
### [Información general](service-fabric-application-lifecycle.md)
### [Empaquetado de una aplicación](service-fabric-package-apps.md)
### [Descripción de la configuración de ImageStoreConnectionString](service-fabric-image-store-connection-string.md)
### Implementación o eliminación de aplicaciones
#### [PowerShell](service-fabric-deploy-remove-applications.md)
#### [Visual Studio](service-fabric-publish-app-remote-cluster.md)
#### [API de FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)
### Actualizar una aplicación
#### [Información general](service-fabric-application-upgrade.md)
#### [Configuración de la actualización de aplicaciones](service-fabric-visualstudio-configure-upgrade.md)
#### [Parámetros de actualización de la aplicación](service-fabric-application-upgrade-parameters.md)
#### Actualizar
##### [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
##### [Visual Studio](service-fabric-application-upgrade-tutorial.md)
#### [Solución de problemas de actualizaciones de aplicaciones](service-fabric-application-upgrade-troubleshooting.md)
#### [Serialización de datos en actualizaciones de aplicaciones](service-fabric-application-upgrade-data-serialization.md)
#### [Temas avanzados sobre actualización de aplicaciones](service-fabric-application-upgrade-advanced.md)

### Prueba de aplicaciones y servicios
#### [Introducción a los análisis de errores](service-fabric-testability-overview.md)
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
#### [Implementación de su aplicación de Linux Java con Jenkins](service-fabric-cicd-your-linux-java-application-with-jenkins.md)

## Creación y administración de clústeres

### [Información general](service-fabric-deploy-anywhere.md)
### Conceptos
#### [Descripción de un clúster](service-fabric-cluster-resource-manager-cluster-description.md)
#### [Seguridad de clúster](service-fabric-cluster-security.md)
#### [Diferencias de características entre Linux y Windows](service-fabric-linux-windows-differences.md)

### Planeación y preparación
#### [Planificación de capacidad](service-fabric-cluster-capacity.md)
#### [Recuperación ante desastres](service-fabric-disaster-recovery.md)

### Clústeres en Azure
#### Conceptos
##### [Tipos de nodos y conjuntos de escalado de máquinas virtuales](service-fabric-cluster-nodetypes.md)
##### [Patrones de redes de clúster](service-fabric-patterns-networking.md)
#### Crear 
##### [Azure Portal](service-fabric-cluster-creation-via-portal.md)
##### [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
##### [Visual Studio y Azure Resource Manager](service-fabric-cluster-creation-via-visual-studio.md)
#### Escala 
##### [Manualmente](service-fabric-cluster-scale-up-down.md)
##### [Mediante programación](service-fabric-cluster-programmatic-scaling.md)
#### [Actualización](service-fabric-cluster-upgrade.md)
#### [Establecer control de acceso](service-fabric-cluster-security-roles.md)
#### [Configuración](service-fabric-cluster-fabric-settings.md)
#### [Administración de certificados del clúster](service-fabric-cluster-security-update-certs-azure.md) 
#### [Eliminar](service-fabric-cluster-delete.md)

### Clústeres independientes
#### [Contenido del paquete independiente](service-fabric-cluster-standalone-package-contents.md)
#### [Planeación y preparación para la implementación](service-fabric-cluster-standalone-deployment-preparation.md)
#### Crear
##### [Creación local](service-fabric-cluster-creation-for-windows-server.md)
##### [Creación en máquinas virtuales de Azure](service-fabric-cluster-creation-with-windows-azure-vms.md)
##### [Protección con certificados](service-fabric-windows-cluster-x509-security.md)  
##### [Protección mediante la seguridad de Windows](service-fabric-windows-cluster-windows-security.md)
#### [Escala](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [Establecer control de acceso](service-fabric-cluster-security-roles.md)
#### [Configuración](service-fabric-cluster-manifest.md)
#### [Actualización](service-fabric-cluster-upgrade-windows-server.md) 

### [Visualización de un clúster](service-fabric-visualizing-your-cluster.md)
### [Conexión a un clúster seguro](service-fabric-connect-to-secure-cluster.md)

### [Administración de un clúster mediante la CLI de Azure](service-fabric-azure-cli.md)

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

### [Conexión a un clúster seguro](service-fabric-connect-to-secure-cluster.md)

## Inspección del estado de un clúster y una aplicación
### [Supervisión del estado de Service Fabric](service-fabric-health-introduction.md)
### [Notificación y comprobación del estado del servicio](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
### [Incorporación de informes de estado personalizados](service-fabric-report-health.md)
### [Solución de problemas con informes de estado del sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
### [Visualización de informes de estado](service-fabric-view-entities-aggregated-health.md)

## Supervisión y diagnóstico
### [Aplicaciones de supervisión y diagnóstico](service-fabric-diagnostics-overview.md)
### Supervisión y diagnóstico de los servicios localmente
#### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
#### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
### Registros de Diagnósticos de Azure
#### [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
#### [Linux](service-fabric-diagnostics-how-to-setup-lad.md)
### [Recopilación de registros de un proceso de servicio](service-fabric-diagnostic-collect-logs-without-an-agent.md)
### [Diagnósticos de Reliable Services con estado](service-fabric-reliable-services-diagnostics.md)
### [Diagnósticos en Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
### [Solución de problemas de un clúster local](service-fabric-troubleshoot-local-cluster-setup.md)

# Referencia
## [PowerShell](/powershell/azure/overview?view=azureservicefabricps)
## [API de Java](/java/api/)
## [.NET](/dotnet/api/)
## [REST](/rest/api/servicefabric)

# Recursos
## [Preguntas comunes acerca de Service Fabric](service-fabric-common-questions.md)
## [Opciones de soporte técnico de Service Fabric](service-fabric-support.md)
## [Código de ejemplo](http://aka.ms/servicefabricsamples)
## [Ruta de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [Precios](https://azure.microsoft.com/pricing/details/service-fabric/)
## [Actualizaciones del servicio](https://azure.microsoft.com/updates/?product=service-fabric)
## [Foro de MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)

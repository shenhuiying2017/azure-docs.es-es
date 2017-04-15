
# Información general
## [¿Qué es Service Fabric?](service-fabric-overview.md)
## [Descripción de los microservicios](service-fabric-overview-microservices.md)
## [Escenarios de aplicación](service-fabric-application-scenarios.md)
## [Arquitectura](service-fabric-architecture.md)
## [Terminología](service-fabric-technical-overview.md)
## [Guía básica de contenidos](service-fabric-content-roadmap.md)

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
## [Creación de un clúster independiente](service-fabric-get-started-standalone-cluster.md)

# Procedimientos
## Creación de una aplicación
### [Patrones y escenarios](service-fabric-patterns-and-scenarios.md)
### Aspectos básicos
#### [Modelo de aplicación](service-fabric-application-model.md)
#### [Empaquetado de una aplicación](service-fabric-package-apps.md)
#### [Modelo de programación admitido](service-fabric-choose-framework.md)
#### [Estado de servicio](service-fabric-concepts-state.md)
#### [Comunicación de servicio](service-fabric-connect-and-communicate-with-services.md)
#### [Adición de un front-end web](service-fabric-add-a-web-frontend.md)
#### [Recursos de manifiesto de servicio](service-fabric-service-manifest-resources.md)
#### [Introducción al complemento de Eclipse para el desarrollo de Java](service-fabric-get-started-eclipse.md)
#### [Administración de aplicaciones en Visual Studio](service-fabric-manage-application-in-visual-studio.md)
#### [Configuración de conexiones seguras en Visual Studio](service-fabric-visualstudio-configure-secure-connections.md)
#### Depurar
##### [Depuración de un servicio de C# en VS](service-fabric-debugging-your-application.md)
##### [Depuración de un servicio de Java en Eclipse](service-fabric-debugging-your-application-java.md)
#### Supervisión y diagnóstico
##### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
##### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
#### [Administración de secretos de aplicación](service-fabric-application-secret-management.md)  
#### [Configuración de directivas de seguridad para la aplicación](service-fabric-application-runas-security.md)  
#### [Configuración de la aplicación para varios entornos](service-fabric-manage-multiple-environment-app-configuration.md)  
#### [Errores y excepciones habituales](service-fabric-errors-and-exceptions.md) 

### Aplicación ejecutable de invitado
#### [Implementación de una aplicación ejecutable de invitado](service-fabric-deploy-existing-app.md)
#### [Implementación de varios ejecutables invitados](service-fabric-deploy-multiple-apps.md)

### Aplicación contenedora
#### [Información general](service-fabric-containers-overview.md)
#### [Implementación de un contenedor de Windows](service-fabric-deploy-container.md)
#### [Implementación de un contenedor de Docker](service-fabric-deploy-container-linux.md)

### Aplicación con Reliable Services
#### [Información general](service-fabric-reliable-services-introduction.md)
#### Introducción
##### [C# en Windows](service-fabric-reliable-services-quick-start.md)
##### [Java en Linux](service-fabric-reliable-services-quick-start-java.md)
#### [Ciclo de vida de Reliable Services: C#](service-fabric-reliable-services-lifecycle.md)
#### [Ciclo de vida de Reliable Services: Java](service-fabric-reliable-services-lifecycle-java.md)
#### [Colecciones confiables](service-fabric-reliable-services-reliable-collections.md)
#### [Uso de colecciones confiables](service-fabric-work-with-reliable-collections.md)
#### [Configuración](service-fabric-reliable-services-configuration.md)
#### [Notificaciones](service-fabric-reliable-services-notifications.md)
#### [Copia de seguridad y restauración](service-fabric-reliable-services-backup-restore.md)
#### [Comunicación con Reliable Services](service-fabric-reliable-services-communication.md)
#### [Comunicaciones seguras con Reliable Services: C#](service-fabric-reliable-services-secure-communication.md)
#### [Comunicaciones seguras con Reliable Services: Java](service-fabric-reliable-services-secure-communication-java.md)
##### [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)
##### [Comunicación remota del servicio: C#](service-fabric-reliable-services-communication-remoting.md)
##### [Comunicación remota del servicio: Java](service-fabric-reliable-services-communication-remoting-java.md)
##### [WCF](service-fabric-reliable-services-communication-wcf.md)
##### [Proxy inverso](service-fabric-reverseproxy.md)
#### [Uso avanzado](service-fabric-reliable-services-advanced-usage.md)

### Aplicación con Reliable Actors
#### [Información general](service-fabric-reliable-actors-introduction.md)
#### Introducción
##### [C# en Windows](service-fabric-reliable-actors-get-started.md)
##### [Java en Linux](service-fabric-reliable-actors-get-started-java.md)
#### [Arquitectura](service-fabric-reliable-actors-platform.md)
#### [Ciclo de vida y recolección de elementos no utilizados](service-fabric-reliable-actors-lifecycle.md)
#### [Polimorfismo](service-fabric-reliable-actors-polymorphism.md)
#### [Reentrada](service-fabric-reliable-actors-reentrancy.md)
#### [Eventos](service-fabric-reliable-actors-events.md) 
#### [Recordatorios y temporizadores](service-fabric-reliable-actors-timers-reminders.md)
#### [Administración de estados](service-fabric-reliable-actors-state-management.md)
#### [Configuración de KvsActorStateProvider](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [Serialización del tipo](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)
#### [Configuración de opciones de comunicación](service-fabric-reliable-actors-fabrictransportsettings.md) 
#### [Configuración de ReliableDictionaryActorStateProvider](service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

## Migración desde Cloud Services
### [Comparación de Cloud Services con Service Fabric](service-fabric-cloud-services-migration-differences.md)
### [Migración a Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)
### [Procedimientos recomendados](/azure/architecture/service-fabric/migrate-from-cloud-services)

## Creación y administración de clústeres

### Aspectos básicos
#### [Información general](service-fabric-deploy-anywhere.md)
#### [Descripción de un clúster](service-fabric-cluster-resource-manager-cluster-description.md)
#### [Planificación de capacidad](service-fabric-cluster-capacity.md)
#### [Visualización de un clúster](service-fabric-visualizing-your-cluster.md)
#### [Conexión a un clúster seguro](service-fabric-connect-to-secure-cluster.md)
#### [Administración de un clúster mediante la CLI de Azure](service-fabric-azure-cli.md) 
#### [Protección de un clúster](service-fabric-cluster-security.md)
#### [Recuperación ante desastres](service-fabric-disaster-recovery.md)
#### [Diferencias de características entre Linux y Windows](service-fabric-linux-windows-differences.md)

### Clústeres en Azure
#### Creación de un clúster en Azure
##### [Azure Portal](service-fabric-cluster-creation-via-portal.md)
##### [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
##### [Visual Studio y Azure Resource Manager](service-fabric-cluster-creation-via-visual-studio.md)
#### [Patrones de redes de clúster](service-fabric-patterns-networking.md)
#### [Tipos de nodos y conjuntos de escalado de máquinas virtuales](service-fabric-cluster-nodetypes.md)
#### [Escalado de un clúster](service-fabric-cluster-scale-up-down.md)
#### [Escalado de un clúster mediante programación](service-fabric-cluster-programmatic-scaling.md)
#### [Actualización de un clúster](service-fabric-cluster-upgrade.md)
#### [Eliminación de un clúster](service-fabric-cluster-delete.md)
#### [Control de acceso](service-fabric-cluster-security-roles.md)
#### [Configuración de un clúster](service-fabric-cluster-fabric-settings.md)
#### [Incorporación o sustitución de certificados de clúster](service-fabric-cluster-security-update-certs-azure.md) 
#### [Prueba de un Party Cluster gratis](http://aka.ms/tryservicefabric)

### Clústeres independientes
#### [Planeación y preparación para la implementación](service-fabric-cluster-standalone-deployment-preparation.md)
#### [Contenido del paquete independiente de Service Fabric](service-fabric-cluster-standalone-package-contents.md)
#### [Creación de un clúster independiente](service-fabric-cluster-creation-for-windows-server.md)
#### [Creación de un clúster independiente en Azure Virtual Machines](service-fabric-cluster-creation-with-windows-azure-vms.md)
#### [Escalado de un clúster](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [Actualización de un clúster](service-fabric-cluster-upgrade-windows-server.md)
#### [Control de acceso](service-fabric-cluster-security-roles.md)
#### [Configuración de un clúster](service-fabric-cluster-manifest.md)
#### [Protección de un clúster mediante certificados](service-fabric-windows-cluster-x509-security.md)  
#### [Protección de un clúster mediante la seguridad de Windows](service-fabric-windows-cluster-windows-security.md) 

## Administración del ciclo de vida de las aplicaciones
### [Información general](service-fabric-application-lifecycle.md)
### [Configuración de integración continua con VSTS](service-fabric-set-up-continuous-integration.md)
### [Implementación de su aplicación de Linux Java con Jenkins](service-fabric-cicd-your-linux-java-application-with-jenkins.md)
### [Descripción de la configuración de ImageStoreConnectionString](service-fabric-image-store-connection-string.md)
### Implementación o eliminación de aplicaciones
#### [PowerShell](service-fabric-deploy-remove-applications.md)
#### [Visual Studio](service-fabric-publish-app-remote-cluster.md)
#### [API de FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)
### [Introducción a la actualización de aplicaciones](service-fabric-application-upgrade.md)
### [Configuración de la actualización de aplicaciones](service-fabric-visualstudio-configure-upgrade.md)
### [Parámetros de actualización de la aplicación](service-fabric-application-upgrade-parameters.md)
### Actualizar una aplicación
#### [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
#### [Visual Studio](service-fabric-application-upgrade-tutorial.md)
### [Solución de problemas de actualizaciones de aplicaciones](service-fabric-application-upgrade-troubleshooting.md)
### [Serialización de datos en actualizaciones de aplicaciones](service-fabric-application-upgrade-data-serialization.md)
### [Temas avanzados sobre actualización de aplicaciones](service-fabric-application-upgrade-advanced.md)

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

## Escalado de aplicaciones
### [Reliable Services de partición](service-fabric-concepts-partitioning.md)
### [Disponibilidad de servicios](service-fabric-availability-services.md)
### [Escalado de aplicaciones](service-fabric-concepts-scalability.md)
### [Planeamiento de la capacidad de las aplicaciones](service-fabric-capacity-planning.md)

## Prueba de aplicaciones y servicios
### [Introducción a los análisis de errores](service-fabric-testability-overview.md)
### [Prueba de la comunicación entre servicios](service-fabric-testability-scenarios-service-communication.md)
### Simulación de errores
#### [Uso de caos controlado](service-fabric-controlled-chaos.md)
#### [Uso de acciones de prueba](service-fabric-testability-actions.md)
#### [Durante las cargas de trabajo](service-fabric-testability-workload-tests.md)
#### [Uso de escenarios de prueba](service-fabric-testability-scenarios.md)
#### [Uso de las API Node Transition](service-fabric-node-transition-apis.md)
### [Prueba de carga de la aplicación](service-fabric-vso-load-test.md)

## Administración y orquestación de recursos de clúster
### [Introducción a Resource Manager de clúster](service-fabric-cluster-resource-manager-introduction.md)
### [Arquitectura de Resource Manager de clúster](service-fabric-cluster-resource-manager-architecture.md)
### [Descripción de un clúster](service-fabric-cluster-resource-manager-cluster-description.md)
### [Introducción a los grupos de aplicaciones](service-fabric-cluster-resource-manager-application-groups.md)
### [Definición de la configuración de Resource Manager de clúster](service-fabric-cluster-resource-manager-configure-services.md)
### [Métricas de consumo de recursos](service-fabric-cluster-resource-manager-metrics.md)
### [Uso de afinidad de servicios](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
### [Directivas de colocación de servicios](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
### [Administración de un clúster](service-fabric-cluster-resource-manager-management-integration.md)
### [Desfragmentación de clústeres](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
### [Equilibrado de un clúster](service-fabric-cluster-resource-manager-balancing.md)
### [Limitaciones](service-fabric-cluster-resource-manager-advanced-throttling.md)
### [Movimiento de servicio](service-fabric-cluster-resource-manager-movement-cost.md)

# Referencia
## [PowerShell](/powershell/module/ServiceFabric/)
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

# Arquitectura

## Aspectos básicos de la nube

### [Diseño de aplicaciones resistentes](guidance-resiliency-overview.md)
#### [Lista de comprobación de resistencia](guidance-resiliency-checklist.md)
#### [Análisis del modo de error](guidance-resiliency-failure-mode-analysis.md)
#### [Recuperación ante desastres](..\resiliency\resiliency-disaster-recovery-azure-applications.md)
#### [Recuperación ante desastres y alta disponibilidad ](..\resiliency\resiliency-disaster-recovery-high-availability-azure-applications.md)
#### [Alta disponibilidad](..\resiliency\resiliency-high-availability-azure-applications.md)
#### [Lista de comprobación de alta disponibilidad](..\resiliency\resiliency-high-availability-checklist.md)
#### [Información orientativa acerca de la resistencia específica del servicio de Azure](..\resiliency\resiliency-service-guidance-index.md)
#### [Recuperación ante datos dañados o eliminación accidental](..\resiliency\resiliency-technical-guidance-recovery-data-corruption.md)
#### [Recuperación ante errores locales](..\resiliency\resiliency-technical-guidance-recovery-local-failures.md)
#### [Recuperación ante una interrupción del servicio en toda la región](..\resiliency\resiliency-technical-guidance-recovery-loss-azure-region.md)
#### [Recuperación desde un centro de datos local a Azure](..\resiliency\resiliency-technical-guidance-recovery-on-premises-azure.md)
#### [Guía técnica sobre resistencia en Azure](..\resiliency\resiliency-technical-guidance.md)


## Arquitecturas de referencia

### [Ejecución de cargas de trabajo de VM en Azure](guidance-ra-compute.md)
#### [Ejecución de una VM con Linux en Azure](guidance-compute-single-vm-linux.md)
#### [Ejecución de una VM con Windows en Azure](guidance-compute-single-vm.md)
#### [Ejecución de varias VM en Azure por motivos de escalabilidad y disponibilidad](guidance-compute-multi-vm.md)
#### [Ejecución de VM con Linux para una arquitectura de n niveles](guidance-compute-n-tier-vm-linux.md)
#### [Ejecución de VM con Windows para una arquitectura de n niveles](guidance-compute-n-tier-vm.md)
#### [Ejecución de VM con Linux en varias regiones para lograr alta disponibilidad](guidance-compute-multiple-datacenters-linux.md)
#### [Ejecución de VM con Windows en varias regiones para lograr alta disponibilidad](guidance-compute-multiple-datacenters.md)

### [Conexión de la red local a Azure](guidance-ra-hybrid-networking.md)
#### [Arquitectura de red híbrida con Azure ExpressRoute](guidance-hybrid-network-expressroute.md)
#### [Arquitectura de red híbrida con Azure y una VPN local](guidance-hybrid-network-vpn.md)
#### [Arquitectura de red híbrida de alta disponibilidad](guidance-hybrid-network-expressroute-vpn-failover.md)

### [Protección de los límites de la nube de Azure](guidance-ra-network-security.md)
#### [Arquitectura de red híbrida segura en Azure](guidance-iaas-ra-secure-vnet-hybrid.md)
#### [Red perimetral entre Internet y Azure](guidance-iaas-ra-secure-vnet-dmz.md)

### [Administración de identidades de Azure](guidance-ra-identity.md)
#### [Implementación de Azure Active Directory](guidance-identity-aad.md)
#### [Extensión de ADDS a Azure](guidance-identity-adds-extend-domain.md)
#### [Creación de un bosque de recursos ADDS en Azure](guidance-identity-adds-resource-forest.md)
#### [Implementación de ADFS en Azure](guidance-identity-adfs.md)

### [Arquitecturas de aplicación web para Azure App Service](guidance-ra-app-service.md)
#### [Aplicación web básica](guidance-web-apps-basic.md)
#### [Aplicación web con alta disponibilidad](guidance-web-apps-multi-region.md)
#### [Mejora de la escalabilidad en una aplicación web](guidance-web-apps-scalability.md)


## Procedimientos recomendados para aplicaciones en la nube

### [Guía de diseño de una API](..\best-practices-api-design.md)
### [Guía de implementación de API](..\best-practices-api-implementation.md)
### [Guía de escalado automático](..\best-practices-auto-scaling.md)
### [Lista de comprobación de disponibilidad](..\best-practices-availability-checklist.md)
### [Guía sobre los trabajos de segundo plano](..\best-practices-background-jobs.md)
### [¿Regiones emparejadas de Azure?](..\best-practices-availability-paired-regions.md)
### [Guía sobre el almacenamiento en caché](..\best-practices-caching.md)
### [Orientación sobre Content Delivery Network](..\best-practices-cdn.md)
### [Guía de creación de particiones de datos](..\best-practices-data-partitioning.md)
### [Guía de supervisión y diagnósticos](..\best-practices-monitoring.md)
### [Servicios en la nube de Microsoft y seguridad de red](..\best-practices-network-security.md)
### [Patrones para diseñar plantillas de Azure Resource Manager](..\best-practices-resource-manager-design-templates.md)
### [Convenciones de nomenclatura recomendadas para los recursos de Azure](guidance-naming-conventions.md)
### [Consideraciones de seguridad para Azure Resource Manager](..\best-practices-resource-manager-security.md)
### [Uso compartido del estado en las plantillas de Azure Resource Manager](..\best-practices-resource-manager-state.md)
### [Orientación general sobre reintentos](..\best-practices-retry-general.md)
### [Orientación específica sobre el servicio de reintentos](..\best-practices-retry-service-specific.md)
### [Lista de comprobación de escalabilidad](..\best-practices-scalability-checklist.md)


## Guías sobre escenarios

### [Guía de Elasticsearch en Azure](guidance-elasticsearch.md)
#### [Ejecución de Elasticsearch en Azure](guidance-elasticsearch-running-on-azure.md)
#### [Ajuste del rendimiento de ingesta de datos](guidance-elasticsearch-tuning-data-ingestion-performance.md)
#### [Ajuste del rendimiento de consulta y agregación de datos](guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md)
#### [Configuración de resistencia y recuperación](guidance-elasticsearch-configuring-resilience-and-recovery.md)
#### [Creación de un entorno de pruebas de rendimiento](guidance-elasticsearch-creating-performance-testing-environment.md)
#### [Implementación de un plan de pruebas de JMeter](guidance-elasticsearch-implementing-jmeter-test-plan.md)
#### [Implementación de una muestra de JMeter JUnit](guidance-elasticsearch-deploying-jmeter-junit-sampler.md)
#### [Ejecución de las pruebas de resistencia automatizadas](guidance-elasticsearch-running-automated-resilience-tests.md)
#### [Ejecución de las pruebas de rendimiento automatizadas](guidance-elasticsearch-running-automated-performance-tests.md)

### [Administración de identidades en aplicaciones multiinquilino](guidance-multitenant-identity.md)
#### [Información general](guidance-multitenant-identity-intro.md)
#### [Aplicación Tailspin Surveys](guidance-multitenant-identity-tailspin.md)
#### [Autenticación con Azure AD y OpenID Connect](guidance-multitenant-identity-authenticate.md)
#### [Identidades basadas en notificaciones](guidance-multitenant-identity-claims.md)
#### [Suscripción e incorporación de inquilinos](guidance-multitenant-identity-signup.md)
#### [Roles de la aplicación](guidance-multitenant-identity-app-roles.md)
#### [Autorización basada en roles y recursos](guidance-multitenant-identity-authorize.md)
#### [Protección de una API web de back-end](guidance-multitenant-identity-web-api.md)
#### [Almacenamiento en caché de los tokens de acceso](guidance-multitenant-identity-token-cache.md)
#### [Federación con el servicio AD FS de un cliente](guidance-multitenant-identity-adfs.md)
#### [Uso de aserción de cliente para obtener tokens de acceso](guidance-multitenant-identity-client-assertion.md)
#### [Uso de Azure Key Vault para proteger los secretos de la aplicación](guidance-multitenant-identity-keyvault.md)
#### [Implementación de dispositivos virtuales de alta disponibilidad](guidance-nva-ha.md)


<!--HONumber=Nov16_HO4-->



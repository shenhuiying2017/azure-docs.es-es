# Arquitectura

## Aspectos básicos de la nube

### [Diseño de aplicaciones resistentes de Azure](guidance-resiliency-overview.md)
#### [Lista de comprobación de resistencia](guidance-resiliency-checklist.md)
#### [Análisis del modo de error](guidance-resiliency-failure-mode-analysis.md)

#### [Recuperación ante desastres para aplicaciones generadas en Microsoft Azure](..\resiliency\resiliency-disaster-recovery-azure-applications.md)
#### [Recuperación ante desastres y alta disponibilidad para aplicaciones creadas en Microsoft Azure](..\resiliency\resiliency-disaster-recovery-high-availability-azure-applications.md)
#### [Alta disponibilidad para aplicaciones creadas en Microsoft Azure](..\resiliency\resiliency-high-availability-azure-applications.md)
#### [Lista de comprobación de alta disponibilidad](..\resiliency\resiliency-high-availability-checklist.md)
#### [Información orientativa acerca de la resistencia específica del servicio de Azure](..\resiliency\resiliency-service-guidance-index.md)
#### [Recuperación ante datos dañados o eliminación accidental](..\resiliency\resiliency-technical-guidance-recovery-data-corruption.md)
#### [Recuperación ante errores locales de Azure](..\resiliency\resiliency-technical-guidance-recovery-local-failures.md)
#### [Recuperación ante una interrupción del servicio en toda la región](..\resiliency\resiliency-technical-guidance-recovery-loss-azure-region.md)
#### [Recuperación desde un centro de datos local a Azure](..\resiliency\resiliency-technical-guidance-recovery-on-premises-azure.md)
#### [Guía técnica sobre resistencia en Azure](..\resiliency\resiliency-technical-guidance.md)


## Arquitecturas de referencia

### Arquitectura de referencia de Compute
#### [Ejecución de una máquina virtual Linux en Azure](guidance-compute-single-vm-linux.md)
#### [Ejecución de una máquina virtual Windows en Azure](guidance-compute-single-vm.md)
#### [Ejecución de varias máquinas virtuales en Azure por motivos de escalabilidad y disponibilidad](guidance-compute-multi-vm.md)
#### [Ejecución de máquinas virtuales Linux para una arquitectura de n niveles en Azure](guidance-compute-n-tier-vm-linux.md)
#### [Ejecución de máquinas virtuales Windows para una arquitectura de n niveles en Azure](guidance-compute-n-tier-vm.md)
#### [Ejecución de máquinas virtuales Linux en varias regiones para lograr alta disponibilidad](guidance-compute-multiple-datacenters-linux.md)
#### [Ejecución de máquinas virtuales Windows en varias regiones para lograr alta disponibilidad](guidance-compute-multiple-datacenters.md)

### [Conexión de la red local a Azure](guidance-connecting-your-on-premises-network-to-azure.md)
#### [Implementación de una arquitectura de red híbrida con Azure ExpressRoute](guidance-hybrid-network-expressroute.md)
#### [Implementación de una arquitectura de red híbrida con Azure y una VPN local](guidance-hybrid-network-vpn.md)
#### [Implementación de una arquitectura de red híbrida de alta disponibilidad](guidance-hybrid-network-expressroute-vpn-failover.md)

### Protección de los límites de la nube de Azure
#### [Implementación de una arquitectura de red híbrida segura en Azure](guidance-iaas-ra-secure-vnet-hybrid.md)
#### [Implementación de una red perimetral entre Internet y Azure](guidance-iaas-ra-secure-vnet-dmz.md)

### [Administración de identidades de Azure](guidance-ra-identity.md)
#### [Implementación de Azure Active Directory](guidance-identity-aad.md)
#### [Extensión de los servicios de directorio de Active Directory (ADDS) a Azure](guidance-identity-adds-extend-domain.md)
#### [Creación de un bosque de recursos de servicios de directorio de Active Directory (ADDS) en Azure](guidance-identity-adds-resource-forest.md)
#### [Implementación de los servicios de federación de Active Directory (ADFS) en Azure](guidance-identity-adfs.md)

### Arquitectura de referencia de aplicación web de PaaS
#### [Arquitectura de referencia de Azure: aplicación web básica](guidance-web-apps-basic.md)
#### [Arquitectura de referencia de Azure: aplicación web con alta disponibilidad](guidance-web-apps-multi-region.md)
#### [Mejora de la escalabilidad en una aplicación web](guidance-web-apps-scalability.md)


## Patrones de diseño en la nube

## Procedimientos recomendados para aplicaciones en la nube

### [Guía de diseño de una API](..\best-practices-api-design.md)
### [Guía de implementación de API](..\best-practices-api-implementation.md)
### [Guía de escalado automático](..\best-practices-auto-scaling.md)
### [Lista de comprobación de disponibilidad](..\best-practices-availability-checklist.md)
### [Guía sobre los trabajos de segundo plano](..\best-practices-background-jobs.md)
### [Continuidad empresarial y recuperación ante desastres (BCDR): regiones emparejadas de Azure](..\best-practices-availability-paired-regions.md)
### [Guía sobre el almacenamiento en caché](..\best-practices-caching.md)
### [Guía de Content Delivery Network (CDN)](..\best-practices-cdn.md)
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
#### [Ejecución de ElasticSearch en Azure](guidance-elasticsearch-running-on-azure.md)
#### [Ajuste del rendimiento de ingesta de datos para Elasticsearch en Azure](guidance-elasticsearch-tuning-data-ingestion-performance.md)
#### [Ajuste de la agregación de datos y el rendimiento de consultas con Elasticsearch en Azure](guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md)
#### [Configuración de resistencia y recuperación en Elasticsearch en Azure](guidance-elasticsearch-configuring-resilience-and-recovery.md)
#### [Creación de un entorno de pruebas de rendimiento para Elasticsearch en Azure](guidance-elasticsearch-creating-performance-testing-environment.md)
#### [Implementación de un plan de pruebas con JMeter para Elasticsearch](guidance-elasticsearch-implementing-jmeter-test-plan.md)
#### [Implementación de una muestra de JUnit en JMeter para probar el rendimiento de Elasticsearch](guidance-elasticsearch-deploying-jmeter-junit-sampler.md)
#### [Ejecución de pruebas automatizadas de resistencia de Elasticsearch](guidance-elasticsearch-running-automated-resilience-tests.md)
#### [Ejecución de pruebas automatizadas de rendimiento de Elasticsearch](guidance-elasticsearch-running-automated-performance-tests.md)

### [Administración de identidades para aplicaciones multiinquilino en Microsoft Azure](guidance-multitenant-identity.md)
#### [Introducción a la administración de identidades para aplicaciones multiinquilino en Microsoft Azure](guidance-multitenant-identity-intro.md)
#### [Acerca de la aplicación Tailspin Surveys](guidance-multitenant-identity-tailspin.md)
#### [Autenticación en aplicaciones multiinquilino con Azure AD y OpenID Connect](guidance-multitenant-identity-authenticate.md)
#### [Trabajo con identidades basadas en notificaciones en aplicaciones multiinquilino](guidance-multitenant-identity-claims.md)
#### [Registro e incorporación de inquilinos en una aplicación multiinquilino](guidance-multitenant-identity-signup.md)
#### [Roles de aplicación en aplicaciones multiinquilino](guidance-multitenant-identity-app-roles.md)
#### [Autorización basada en roles y en recursos en aplicaciones multiinquilino](guidance-multitenant-identity-authorize.md)
#### [Protección de una API web de back-end en una aplicación multiinquilino](guidance-multitenant-identity-web-api.md)
#### [Almacenamiento en caché de tokens de acceso en una aplicación multiinquilino](guidance-multitenant-identity-token-cache.md)
#### [Federación con un AD FS de cliente para aplicaciones multiinquilino en Azure](guidance-multitenant-identity-adfs.md)
#### [Uso de aserción de cliente para obtener tokens de acceso de Azure AD](guidance-multitenant-identity-client-assertion.md)
#### [Uso de Azure Key Vault para proteger los secretos de la aplicación](guidance-multitenant-identity-keyvault.md)

#### [Implementación de dispositivos virtuales de alta disponibilidad](guidance-nva-ha.md)


<!--HONumber=Nov16_HO2-->



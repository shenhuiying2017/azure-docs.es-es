# Información general
## [¿Qué es Log Analytics?](log-analytics-overview.md)
## [Seguridad de los datos](log-analytics-security.md)

# Introducción
## [Suscripción a Log Analytics](log-analytics-get-started.md)
## [Administración del acceso](log-analytics-manage-access.md)
## [Datos de uso](log-analytics-usage.md)
## [Preguntas frecuentes sobre Log Analytics](log-analytics-faq.md)
## [Proveedores de servicios](log-analytics-service-providers.md)

# Procedimientos
## Recopilación de datos
### Orígenes conectados
#### [Agentes de Windows](log-analytics-windows-agents.md)
#### [Agentes de Linux](log-analytics-agent-linux.md)
#### [Máquinas virtuales de Azure](log-analytics-azure-vm-extension.md)
#### [Recursos de Azure](log-analytics-azure-storage.md)
#### [Operations Manager](log-analytics-om-agents.md)
#### [Administrador de configuración](log-analytics-sccm.md)
#### [Puerta de enlace de OMS](log-analytics-oms-gateway.md)
### Orígenes de datos
#### [Introducción a orígenes de datos](log-analytics-data-sources.md)
#### [Eventos de Windows](log-analytics-data-sources-windows-events.md)
#### [Personalización de datos JSON](log-analytics-data-sources-json.md)
#### [Recopilación de datos de rendimiento](log-analytics-data-sources-collectd.md)
#### [Alertas de Nagios y Zabbix](log-analytics-data-sources-alerts-nagios-zabbix.md)
#### [Syslog](log-analytics-data-sources-syslog.md)
#### [Contadores de rendimiento](log-analytics-data-sources-performance-counters.md)
#### [Rendimiento de las aplicaciones de Linux](log-analytics-data-sources-linux-applications.md)
#### [Registros de IIS](log-analytics-data-sources-iis-logs.md)
#### [Registros personalizados](log-analytics-data-sources-custom-logs.md)
#### [Campos personalizados](log-analytics-custom-fields.md)

## Datos de consulta
### [Introducción a búsquedas de registros](log-analytics-log-searches.md)
### [Referencia de Search](log-analytics-search-reference.md)
#### [Expresiones regulares](log-analytics-log-searches-regex.md)
### [Actuación a partir de resultados de búsqueda](log-analytics-log-search-takeaction.md)
### [Grupos de equipos](log-analytics-computer-groups.md)

## Nueva búsqueda de registros
### [Actualización del área de trabajo](log-analytics-log-search-upgrade.md)
### [Preguntas más frecuentes](log-analytics-log-search-faq.md)
### [Introducción a búsquedas de registros](log-analytics-log-search-new.md)
### [Portales de búsqueda de registros](log-analytics-log-search-portals.md)
#### [Uso del portal de búsqueda de registros](log-analytics-log-search-log-search-portal.md)
### [Transición desde el lenguaje heredado](log-analytics-log-search-transition.md)
### [Conector de Flow](log-analytics-flow-tutorial.md)

## Análisis de datos
### [Paneles](log-analytics-dashboards.md)
### [Visualización del diseñador](log-analytics-view-designer.md)
### [Power BI](log-analytics-powerbi.md)
## Creación de alertas
### [Descripción de las alertas](log-analytics-alerts.md)
### [Acciones de alerta](log-analytics-alerts-actions.md)
### Creación de reglas de alerta
#### [Portal de OMS](log-analytics-alerts-creating.md)
#### [API de REST](log-analytics-api-alerts.md)
#### [Plantilla de Resource Manager](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md)
### [Ejemplo de acción de webhook](log-analytics-alerts-webhooks.md)
### [Solución de administración de alertas](log-analytics-solution-alert-management.md)
## Uso de soluciones
### [Introducción a soluciones](log-analytics-add-solutions.md)
### [Soluciones de destino](../operations-management-suite/operations-management-suite-solution-targeting.md?toc=%2fazure%2flog-analytics%2ftoc.json)
### [Análisis de registros de actividad](log-analytics-activity.md)
### [Evaluación de AD](log-analytics-ad-assessment.md)
### [Estado de replicación de AD](log-analytics-ad-replication-status.md)
### [Agent Health](../operations-management-suite/oms-solution-agenthealth.md?toc=%2fazure%2flog-analytics%2ftoc.json)
### [Administración de alertas](log-analytics-solution-alert-management.md)
### [Application Insights Connector](log-analytics-app-insights-connector.md)
### [Azure SQL Analytics](log-analytics-azure-sql.md)
### [Azure Web Apps Analytics](log-analytics-azure-web-apps-analytics.md)
### [Capacidad y rendimiento](log-analytics-capacity.md)
### [Seguimiento de cambios](log-analytics-change-tracking.md)
### [Contenedores](log-analytics-containers.md)
### [DNS Analytics](log-analytics-dns.md)
### [IT Service Management Connector](log-analytics-itsmc-overview.md)
#### [Conexiones de IT Service Management](log-analytics-itsmc-connections.md)
### [Key Vault](log-analytics-azure-key-vault.md)
### Mensajes de Logic Apps B2B
#### [Solución de mensajes de Logic Apps B2B](../logic-apps/logic-apps-track-b2b-messages-omsportal.md?toc=%2fazure%2flog-analytics%2ftoc.json)
#### [Esquema de seguimiento personalizado de Logic Apps B2B](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md?toc=%2fazure%2flog-analytics%2ftoc.json)
### [Evaluación de malware](log-analytics-malware.md)
### [Análisis de redes](log-analytics-azure-networking-analytics.md)
### [Monitor de rendimiento de red](log-analytics-network-performance-monitor.md)
### [Office 365](../operations-management-suite/oms-solution-office-365.md?toc=%2fazure%2flog-analytics%2ftoc.json)
### [Evaluación de SCOM](log-analytics-scom-assessment.md)
### [Auditoría de seguridad](../operations-management-suite/oms-security-getting-started.md?toc=%2fazure%2flog-analytics%2ftoc.json)
### [Service Fabric con PowerShell](log-analytics-service-fabric.md)
#### [Service Fabric con Azure Resource Manager](log-analytics-service-fabric-azure-resource-manager.md)
### [Mapa de servicio](../operations-management-suite/operations-management-suite-service-map.md?toc=%2fazure%2flog-analytics%2ftoc.json)
### [Evaluación de SQL](log-analytics-sql-assessment.md)
### [Surface Hub](log-analytics-surface-hubs.md)
### [Administración de actualizaciones](../operations-management-suite/oms-solution-update-management.md)
### [VMware](log-analytics-vmware.md)
### Análisis de Windows
#### [Cumplimiento de actualizaciones](https://technet.microsoft.com/itpro/windows/manage/update-compliance-get-started)
#### [Preparación para la actualización](https://technet.microsoft.com/itpro/windows/deploy/upgrade-readiness-get-started)
### [Datos de conexión](log-analytics-wire-data.md)
## Desarrollo
### [API de recopilador de datos](log-analytics-data-collector-api.md)
### [Cmdlets de PowerShell](log-analytics-powershell-workspace-configuration.md)
### [Plantillas de Resource Manager](log-analytics-template-workspace-configuration.md)
### [Log Search API](log-analytics-log-search-api.md)
#### [Python](log-analytics-log-search-api-python.md)
### [API de alerta](log-analytics-api-alerts.md)

# Referencia
## [PowerShell](/powershell/module/azurerm.operationalinsights)
## [REST](/rest/api/loganalytics)

# Recursos
## [Azure Roadmap](https://azure.microsoft.com/roadmap/)
## [Precios](https://azure.microsoft.com/pricing/details/log-analytics/)
## [Calculadora de precios](https://azure.microsoft.com/pricing/calculator/)
## [Actualizaciones del servicio](https://azure.microsoft.com/updates/?product=log-analytics)
## [Análisis de Windows](https://www.microsoft.com/en-us/WindowsForBusiness/windows-analytics)

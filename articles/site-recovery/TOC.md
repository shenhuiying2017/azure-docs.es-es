# Información general
## [¿Qué es Site Recovery?](site-recovery-overview.md)
## [¿Cómo funciona Site Recovery?](site-recovery-components.md)
## [Funcionamiento de la replicación de Hyper-V en Azure](site-recovery-hyper-v-azure-architecture.md)
## [¿Qué cargas de trabajo se pueden proteger?](site-recovery-workload.md)
## [Matriz de compatibilidad de Site Recovery](site-recovery-support-matrix-to-azure.md)
## [Preguntas más frecuentes](site-recovery-faq.md)
## [Vídeo introductorio](https://azure.microsoft.com/resources/videos/index/?services=site-recovery)

# Introducción
## [Replicación de máquinas virtuales VMware en Azure](site-recovery-vmware-to-azure.md)
## [Replicación de servidores físicos a Azure](site-recovery-physical-servers-to-azure.md)
## [Replicación de máquinas virtuales de Hyper-V en Azure (con VMM)](site-recovery-vmm-to-azure.md)
## [Replicación de máquinas virtuales de Hyper-V en Azure](site-recovery-hyper-v-site-to-azure.md)
## [Replicación de máquinas virtuales de Hyper-V en un sitio secundario (con VMM)](site-recovery-vmm-to-vmm.md)
## [Replicación de máquinas virtuales de VMware y servidores físicos en un sitio secundario](site-recovery-vmware-to-vmware.md)
## [Replicación de máquinas virtuales de VMware en Azure en un entorno multiinquilino (CSP)](site-recovery-multi-tenant-support-vmware-using-csp.md)

# Procedimientos
## Plan
### [Requisitos previos de la replicación de Azure](site-recovery-prereq.md)
### [Planeamiento de la infraestructura de red](site-recovery-network-design.md)
### [Planeamiento de la asignación de red](site-recovery-network-mapping.md)
### [Planeamiento de la capacidad y escalado de la replicación de VMware a Azure](site-recovery-plan-capacity-vmware.md)
### [Deployment Planner para la replicación de VMware en Azure](site-recovery-deployment-planner.md)
### [Capacity Planner para la replicación de Hyper-V](site-recovery-capacity-planner.md)
### [Control de la replicación de máquinas virtuales con acceso basado en roles](site-recovery-role-based-linked-access-control.md)

## Configuración
### [Configuración del entorno de origen](site-recovery-set-up-vmware-to-azure.md)
### [Configuración del entorno de destino](site-recovery-prepare-target-vmware-to-azure.md)
### [Configuración de opciones de replicación](site-recovery-setup-replication-settings-vmware.md)
### [Implementación de Mobility Service para la replicación de VMware](site-recovery-vmware-to-azure-install-mob-svc.md)
#### [Implementación de Mobility Service con System Center Configuration Manager](site-recovery-install-mobility-service-using-sccm.md)
#### [Implementación de Mobility Service con DSC de Automatización de Azure](site-recovery-automate-mobility-service-install.md)
### [Habilitar replicación](site-recovery-replicate-vmware-to-azure.md)
## Conmutación por error y conmutación por recuperación
### [Conmutación por error de máquinas protegidas](site-recovery-failover.md)
### [Configuración de planes de recuperación](site-recovery-create-recovery-plans.md)
#### [Adicción de runbooks de Azure a los planes de recuperación](site-recovery-runbook-automation.md)
### [Ejecución de una conmutación por error de prueba](site-recovery-test-failover-to-azure.md)
### [Reprotección de los equipos después de la conmutación por error](site-recovery-how-to-reprotect.md)
### [Conmutación por recuperación desde Azure](site-recovery-failback-azure-to-vmware.md)

## Migrar
### [Migración a Azure](site-recovery-migrate-to-azure.md)
### [Migración entre regiones de Azure](site-recovery-migrate-azure-to-azure.md)
### [Migrar instancias de AWS Windows a Azure](site-recovery-migrate-aws-to-azure.md)
## Cargas de trabajo
### [Active Directory y DNS](site-recovery-active-directory.md)
### [SQL Server](site-recovery-sql.md)
### [SharePoint](site-recovery-sharepoint.md)
### [Dynamics AX](site-recovery-dynamicsax.md)
### [RDS](site-recovery-workload.md#protect-rds)
### [Exchange](site-recovery-workload.md#protect-exchange)
### [SAP](site-recovery-workload.md#protect-sap)
### [Aplicaciones web basadas en IIS](site-recovery-iis.md)
### [Citrix XenApp y XenDesktop](site-recovery-citrix-xenapp-and-xendesktop.md)
### [Otras cargas de trabajo](site-recovery-workload.md#workload-summary)
## Automatización de la replicación
### [Automatización de la replicación de Hyper-V en Azure (sin VMM)](site-recovery-deploy-with-powershell-resource-manager.md)
### [Automatización de la replicación de Hyper-V en Azure (con VMM)](site-recovery-vmm-to-azure-powershell-resource-manager.md)
### [Automatización de la replicación de Hyper-V en un sitio secundario (con VMM)](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
## Administrar
### [Edición de la configuración de replicación](site-recovery-setup-replication-settings-vmware.md#edit-replication-policy.md)
### [Administración de servidores de proceso de Azure](site-recovery-vmware-setup-azure-ps-resource-manager.md)
### [Administración del servidor de configuración](site-recovery-vmware-to-azure-manage-configuration-server.md)
### [Administración de servidores de procesos de escalado horizontal](site-recovery-vmware-to-azure-manage-scaleout-process-server.md)
### [Administración de servidores vCenter](site-recovery-vmware-to-azure-manage-vCenter.md)
### [Quitar servidores y deshabilitar la protección](site-recovery-manage-registration-and-protection.md)
## [Supervisión y solución de problemas](site-recovery-monitoring-and-troubleshooting.md)

# Referencia
## [PowerShell](/powershell/module/azurerm.siterecovery)
## [PowerShell clásico](/powershell/module/azure/?view=azuresmps-3.7.0)
## [REST](https://msdn.microsoft.com/en-us/library/mt750497)

# Temas relacionados
## [Azure Automation](/azure/automation/)

# Recursos
## [Ruta de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/site-recovery/)
## [Foro](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hypervrecovmgr)
## [Blog](http://azure.microsoft.com/blog/tag/azure-site-recovery/)
## [Precios](https://azure.microsoft.com/pricing/details/site-recovery/)
## [Actualizaciones del servicio](https://azure.microsoft.com/updates/?product=site-recovery)

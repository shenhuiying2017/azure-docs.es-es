
# Información general
## [¿Qué es Azure Backup?](backup-introduction-to-azure-backup.md)

# Introducción
## [Copia de seguridad de máquinas virtuales de Azure](backup-azure-vms-first-look-arm.md)
## [Copias de seguridad de equipos con Windows Server o Windows](backup-try-azure-backup-in-10-mins.md)
## [Copias de seguridad de servidores VMware](backup-azure-backup-server-vmware.md)

# Cómo

## Azure Backup Server
### [Matriz de protección de Azure Backup Server](backup-mabs-protection-matrix.md)
### Instalación o actualización
#### [Preparación de cargas de trabajo del Azure Backup Server en Azure Portal](backup-azure-microsoft-azure-backup.md)
#### [Preparación de cargas de trabajo del Azure Backup Server en el portal clásico](backup-azure-microsoft-azure-backup-classic.md)
#### [Adición de almacenamiento a Azure Backup Server](backup-mabs-add-storage.md)
#### [Actualización de Azure Backup Server a v.2](backup-mabs-upgrade-to-v2.md)
#### [Instalación desatendida de Azure Backup Server](backup-mabs-unattended-install.md)
### Protección de cargas de trabajo
#### [Uso de Azure Backup Server para hacer copia de seguridad de un servidor de VMware](backup-azure-backup-server-vmware.md)
#### [Uso del Azure Backup Server para hacer copia de seguridad de Exchange](backup-azure-exchange-mabs.md)
#### [Uso del Azure Backup Server para hacer copia de seguridad de una granja de SharePoint](backup-azure-backup-sharepoint-mabs.md)
#### [Uso del Azure Backup Server para hacer copia de seguridad de SQL](backup-azure-sql-mabs.md)
#### [Protección del estado del sistema y reconstrucción completa](backup-mabs-system-state-and-bmr.md)
### [Recuperación de datos de Azure Backup Server](backup-azure-alternate-dpm-server.md)

## Máquinas virtuales de Azure
### Preparación de la VM
#### [Preparación de máquinas virtuales implementadas según el modelo de Resource Manager](backup-azure-arm-vms-prepare.md)
#### [Copias de seguridad coherentes con la aplicación de las máquinas virtuales Linux](backup-azure-linux-app-consistent.md)
#### [Preparación de máquinas virtuales de Azure](backup-azure-vms-prepare.md)
### Planeamiento del entorno
#### [Planeación de la infraestructura de copia de seguridad de máquinas virtuales](backup-azure-vms-introduction.md)
### Copia de seguridad de máquinas virtuales
#### [Copia de seguridad de máquinas virtuales de Azure en un almacén de Recovery Services](backup-azure-arm-vms.md)
#### [Copia de seguridad de máquinas virtuales cifradas](backup-azure-vms-encryption.md)
#### [Copia de seguridad de máquinas virtuales de Azure](backup-azure-vms.md)
### Administración y supervisión de máquinas virtuales
#### [Administración de copias de seguridad de VM de Azure en Azure Portal](backup-azure-manage-vms.md)
#### [Supervisión de alertas para copias de seguridad de VM de Azure en Azure Portal](backup-azure-monitor-vms.md)
#### [Administración y supervisión de copias de seguridad de VM de Azure en el portal clásico](backup-azure-manage-vms-classic.md)
### Restauración de datos desde máquinas virtuales
#### [Recuperación de archivos de las copias de seguridad de máquinas virtuales de Azure](backup-azure-restore-files-from-vm.md)
#### [Restauración de máquinas virtuales implementadas según el modelo de Resource Manager en Azure Portal](backup-azure-arm-restore-vms.md)
#### [Restauración de máquinas virtuales cifradas](backup-azure-vms-encryption.md)
#### [Restauración de máquinas virtuales en Azure](backup-azure-restore-vms.md)
#### [Restauración de la clave y el secreto de Key Vault para máquinas virtuales cifradas](backup-azure-restore-key-secret.md)

## Configuración de informes de Azure Backup
### [Configuración de informes de Azure Backup](backup-azure-configure-reports.md)
### [Modelo de datos para informes de Azure Backup](backup-azure-reports-data-model.md)
### [Modelo de datos de Log Analytics para Azure Backup](backup-azure-log-analytics-data-model.md)

## Data Protection Manager
### [Preparación de cargas de trabajo DPM en Azure Portal](backup-azure-dpm-introduction.md)
### [Preparación de cargas de trabajo DPM en el portal clásico](backup-azure-dpm-introduction-classic.md)
### [Uso de System Center DPM para hacer copias de seguridad de un servidor Exchange](backup-azure-backup-exchange-server.md)
### [Recuperación de datos en un servidor DPM alternativo](backup-azure-alternate-dpm-server.md)
### [Uso de DPM para hacer copias de seguridad de cargas de trabajo de SQL Server](backup-azure-backup-sql.md)
### [Uso de DPM para hacer copias de seguridad de una granja de SharePoint](backup-azure-backup-sharepoint.md)

## Uso de PowerShell
### [Máquinas virtuales de Azure en Azure Portal](backup-azure-vms-automation.md)
### [Máquinas virtuales de Azure en el portal clásico](backup-azure-vms-classic-automation.md)
### [DPM en Azure Portal](backup-dpm-automation.md)
### [DPM en el portal clásico](backup-dpm-automation-classic.md)
### [Windows Server en Azure Portal](backup-client-automation.md)
### [Windows Server en el portal clásico](backup-client-automation-classic.md)

## Azure SQL Database
### [Configuración de la retención de copia de seguridad a largo plazo](../sql-database/sql-database-configure-long-term-retention.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Visualización de copias de seguridad en un almacén de Recovery Services](../sql-database/sql-database-view-backups-in-vault.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Restauración de la retención de copia de seguridad a largo plazo](../sql-database/sql-database-restore-from-long-term-retention.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Eliminación de copias de seguridad a largo plazo de Azure SQL](../sql-database/sql-database-long-term-retention-delete.md?toc=%2fazure%2fbackup%2ftoc.json)

## Windows Server
### [Copia de seguridad de carpetas y archivos de Windows Server](backup-configure-vault.md)
### [Copia de seguridad del estado del sistema de Windows Server](backup-azure-system-state.md)
### [Recuperación de archivos de Azure en Windows Server](backup-azure-restore-windows-server.md)
### [Restauración del estado del sistema de Windows Server](backup-azure-restore-system-state.md)
### [Supervisión y administración de almacenes de Recovery Services](backup-azure-manage-windows-server.md)
### Copia de seguridad y restauración mediante el portal clásico
#### [Uso del modelo de implementación clásica por parte de Windows Server](backup-configure-vault-classic.md)
#### [Administración de almacenes de copia de seguridad mediante el modelo de implementación clásica](backup-azure-manage-windows-server-classic.md)
#### [Recuperación de archivos en Windows Server mediante el modelo de implementación clásica](backup-azure-restore-windows-server-classic.md)

## Almacén de Recovery Services
### [Introducción a los almacenes de Recovery Services](backup-azure-recovery-services-vault-overview.md)
### [Actualización de un almacén de Backup a un almacén de Recovery Services](backup-azure-upgrade-backup-to-recovery-services.md)
### [Eliminación de un almacén de Recovery Services](backup-azure-delete-vault.md)

## Solución de problemas
### [Problemas de copia de seguridad de máquinas virtuales de Azure en Azure Portal](backup-azure-vms-troubleshoot.md)
### [Problemas de copia de seguridad de máquinas virtuales de Azure en el portal clásico](backup-azure-vms-troubleshoot-classic.md)
### [Se ha producido un error en la copia de seguridad de VM de Azure: no se pudo comunicar con el agente de VM para el estado de la instantánea: la subtarea de la VM de la instantánea agotó el tiempo de espera](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)
### [Lentitud en la copia de seguridad de archivos y carpetas en Azure Backup](backup-azure-troubleshoot-slow-backup-performance-issue.md)
### [Solución de problemas de Azure Backup Server](backup-azure-mabs-troubleshoot.md)

# Conceptos

## P+F
### [Preguntas más frecuentes sobre el almacén de Recovery Services](backup-azure-backup-faq.md)
### [Preguntas más frecuentes sobre la copia de seguridad de máquina virtual de Azure](backup-azure-vm-backup-faq.md)
### [Preguntas más frecuentes sobre la copia de seguridad de archivos y carpetas con Azure Backup Agent](backup-azure-file-folder-backup-faq.md)

## [Control de acceso basado en roles](backup-rbac-rs-vault.md)
## [Seguridad para copias de seguridad híbridas](backup-azure-security-feature.md)
## [Configuración de copias de seguridad sin conexión](backup-azure-backup-import-export.md)
## [Reemplazo de la biblioteca de cintas](backup-azure-backup-cloud-as-tape.md)


# Referencia
## [PowerShell](/powershell/module/azurerm.recoveryservices.backup)
## [.NET](/dotnet/api/microsoft.azure.management.recoveryservices.backup)

# Recursos
## [Azure Roadmap](https://azure.microsoft.com/roadmap/)
## [Foro de MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=windowsazureonlinebackup)
## [Precios](https://azure.microsoft.com/pricing/details/backup/)
## [Calculadora de precios](https://azure.microsoft.com/pricing/calculator/)
## [Actualizaciones del servicio](https://azure.microsoft.com/updates/?product=backup)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=backup)

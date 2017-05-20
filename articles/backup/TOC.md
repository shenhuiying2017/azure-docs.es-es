
# Información general
## [¿Qué es Azure Backup?](backup-introduction-to-azure-backup.md)

# Introducción
## [Realización de copias de seguridad de archivos y carpetas](backup-try-azure-backup-in-10-mins.md)
## [Copia de seguridad de máquinas virtuales de Azure](backup-azure-vms-first-look.md)
## [Protección de máquinas virtuales de Azure](backup-azure-vms-first-look-arm.md)

# Procedimientos
## Uso de PowerShell
### [Máquinas virtuales de Azure en Azure Portal](backup-azure-vms-automation.md)
### [Máquinas virtuales de Azure en el portal clásico](backup-azure-vms-classic-automation.md)
### [DPM en Azure Portal](backup-dpm-automation.md)
### [DPM en el portal clásico](backup-dpm-automation-classic.md)
### [Windows Server en Azure Portal](backup-client-automation.md)
### [Windows Server en el portal clásico](backup-client-automation-classic.md)

## Servidor de Copia de seguridad de Azure
### [Preparación de cargas de trabajo del servidor de copia de seguridad de Azure en Azure Portal](backup-azure-microsoft-azure-backup.md)
### [Preparación de cargas de trabajo del servidor de copia de seguridad de Azure en el portal clásico](backup-azure-microsoft-azure-backup-classic.md)
### [Uso de Azure Backup Server para hacer copia de seguridad de un servidor de VMware](backup-azure-backup-server-vmware.md)
### [Uso del servidor de copia de seguridad de Azure para hacer copia de seguridad de Exchange](backup-azure-exchange-mabs.md)
### [Uso del servidor de copia de seguridad de Azure para hacer copia de seguridad de una granja de SharePoint](backup-azure-backup-sharepoint-mabs.md)
### [Uso del servidor de copia de seguridad de Azure para hacer copia de seguridad de SQL](backup-azure-sql-mabs.md)

## Data Protection Manager
### [Preparación de cargas de trabajo DPM en Azure Portal](backup-azure-dpm-introduction.md)
### [Preparación de cargas de trabajo DPM en el portal clásico](backup-azure-dpm-introduction-classic.md)
### [Uso de System Center DPM para hacer copias de seguridad de un servidor Exchange](backup-azure-backup-exchange-server.md)
### [Recuperación de datos en el almacén de copia de seguridad en un servidor DPM alternativo](backup-azure-alternate-dpm-server.md)
### [Uso de DPM para hacer copias de seguridad de cargas de trabajo de SQL Server](backup-azure-backup-sql.md)
### [Uso de DPM para hacer copias de seguridad de una granja de SharePoint](backup-azure-backup-sharepoint.md)

## Máquinas virtuales de Azure
### [Preparación de máquinas virtuales de Azure](backup-azure-vms-prepare.md)
### [Preparación de máquinas virtuales implementadas según el modelo de Resource Manager](backup-azure-arm-vms-prepare.md)
### [Planeación de la infraestructura de copia de seguridad de máquinas virtuales](backup-azure-vms-introduction.md)
### [Copia de seguridad de máquinas virtuales de Azure en el almacén de copias de seguridad](backup-azure-vms.md)
### [Copia de seguridad de máquinas virtuales de Azure en un almacén de Recovery Services](backup-azure-arm-vms.md)
### [Copia de seguridad y restauración de máquinas virtuales cifradas](backup-azure-vms-encryption.md)
### [Administración y supervisión de copias de seguridad de VM de Azure en el portal clásico](backup-azure-manage-vms-classic.md)
### [Administración de copias de seguridad de VM de Azure en Azure Portal](backup-azure-manage-vms.md)
### [Supervisión de alertas para copias de seguridad de VM de Azure en Azure Portal](backup-azure-monitor-vms.md)
### [Recuperación de archivos de las copias de seguridad de máquinas virtuales de Azure](backup-azure-restore-files-from-vm.md)
### [Restauración de máquinas virtuales en Azure](backup-azure-restore-vms.md)
### [Restauración de máquinas virtuales implementadas según el modelo de Resource Manager en Azure Portal](backup-azure-arm-restore-vms.md)
### [Restauración de la clave y el secreto de Key Vault para máquinas virtuales cifradas mediante Azure Backup](backup-azure-restore-key-secret.md)

## Base de datos SQL de Azure
### [Configuración de la retención de copia de seguridad a largo plazo](../sql-database/sql-database-configure-long-term-retention.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Visualización de copias de seguridad en un almacén de Recovery Services](../sql-database/sql-database-view-backups-in-vault.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Restauración de la retención de copia de seguridad a largo plazo](../sql-database/sql-database-restore-from-long-term-retention.md?toc=%2fazure%2fbackup%2ftoc.json)
### [Eliminación de copias de seguridad a largo plazo de Azure SQL](../sql-database/sql-database-long-term-retention-delete.md?toc=%2fazure%2fbackup%2ftoc.json)

## Archivos y carpetas de Windows
### [Uso del modelo de implementación clásica por parte de Windows Server](backup-configure-vault-classic.md)
### [Uso del modelo de implementación de Resource Manager por parte de Windows Server](backup-configure-vault.md)
### [Administración de almacenes de copia de seguridad mediante el modelo de implementación clásica](backup-azure-manage-windows-server-classic.md)
### [Supervisión y administración de almacenes de Recovery Services](backup-azure-manage-windows-server.md)
### [Recuperación de archivos en Windows Server mediante el modelo de implementación de Resource Manager](backup-azure-restore-windows-server.md)
### [Recuperación de archivos en Windows Server mediante el modelo de implementación clásica](backup-azure-restore-windows-server-classic.md)

## [Preguntas más frecuentes](backup-azure-backup-faq.md)

## Solución de problemas
### [Problemas de copia de seguridad de máquinas virtuales de Azure en Azure Portal](backup-azure-vms-troubleshoot.md)
### [Problemas de copia de seguridad de máquinas virtuales de Azure en el portal clásico](backup-azure-vms-troubleshoot-classic.md)
### [Servidor de Copia de seguridad de Azure](backup-azure-mabs-troubleshoot.md)
### [Se ha producido un error en la copia de seguridad de VM de Azure: no se pudo comunicar con el agente de VM para el estado de la instantánea: la subtarea de la VM de la instantánea agotó el tiempo de espera](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)
### [Lentitud en la copia de seguridad de archivos y carpetas en Azure Backup](backup-azure-troubleshoot-slow-backup-performance-issue.md)

# Conceptos
## [Introducción a los almacenes de Recovery Services](backup-azure-recovery-services-vault-overview.md)
## [Actualización de un almacén de Backup a un almacén de Recovery Services](backup-azure-upgrade-backup-to-recovery-services.md)
## [Eliminación de un almacén de Azure Backup](backup-azure-delete-vault.md)
## [Control de acceso basado en roles](backup-rbac-rs-vault.md)
## [Seguridad para copias de seguridad híbridas](backup-azure-security-feature.md)
## [Configuración de copias de seguridad sin conexión](backup-azure-backup-import-export.md)
## [Reemplazo de la biblioteca de cintas](backup-azure-backup-cloud-as-tape.md)
## [Copias de seguridad coherentes con la aplicación de las máquinas virtuales Linux](backup-azure-linux-app-consistent.md)

# Referencia
## [PowerShell](/powershell/module/azurerm.recoveryservices.backup)
## [.NET](/dotnet/api/microsoft.azure.management.recoveryservices.backup)

# Recursos
## [Precios](https://azure.microsoft.com/pricing/details/backup/)
## [Foro de MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=windowsazureonlinebackup)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=backup)
## [Actualizaciones del servicio](https://azure.microsoft.com/updates/?product=backup)

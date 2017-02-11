# Información general

## [Introducción](storage-introduction.md)

# Introducción

## [Guía de inicio rápido](storage-getting-started-guide.md)
## [Crear una cuenta de almacenamiento](storage-create-storage-account.md)

## Blob Storage
### [.NET](storage-dotnet-how-to-use-blobs.md)
### [Java](storage-java-how-to-use-blob-storage.md)
### [Node.js](storage-nodejs-how-to-use-blob-storage.md)
### [C++](storage-c-plus-plus-how-to-use-blobs.md)
### [Python](storage-python-how-to-use-blob-storage.md)
### [PHP](storage-php-how-to-use-blobs.md)
### [Ruby](storage-ruby-how-to-use-blob-storage.md)
### [iOS](storage-ios-how-to-use-blob-storage.md)
### [Xamarin](storage-xamarin-blob-storage.md)

## Queue Storage
### [.NET](storage-dotnet-how-to-use-queues.md)
### [Java](storage-java-how-to-use-queue-storage.md)
### [Node.js](storage-nodejs-how-to-use-queues.md)
### [C++](storage-c-plus-plus-how-to-use-queues.md)
### [Python](storage-python-how-to-use-queue-storage.md)
### [PHP](storage-php-how-to-use-queues.md)
### [Ruby](storage-ruby-how-to-use-queue-storage.md)

## Table Storage
### [.NET](storage-dotnet-how-to-use-tables.md)
### [Java](storage-java-how-to-use-table-storage.md)
### [Node.js](storage-nodejs-how-to-use-table-storage.md)
### [C++](storage-c-plus-plus-how-to-use-tables.md)
### [Python](storage-python-how-to-use-table-storage.md)
### [PHP](storage-php-how-to-use-table-storage.md)
### [Ruby](storage-ruby-how-to-use-table-storage.md)

## File Storage
### [Windows, .NET y PowerShell](storage-dotnet-how-to-use-files.md)
### [Linux](storage-how-to-use-files-linux.md)
### [Java](storage-java-how-to-use-file-storage.md)
### [C++](storage-c-plus-plus-how-to-use-files.md)
### [Python](storage-python-how-to-use-file-storage.md)

# Procedimientos
## [crear una cuenta de almacenamiento](storage-create-storage-account.md)
## Uso de blobs
### [Introducción al servicio](https://msdn.microsoft.com/library/dd179376.aspx)
### [Capas de acceso frecuente y esporádico](storage-blob-storage-tiers.md)
### [Dominios personalizados](storage-custom-domain-name.md)
### [Acceso anónimo a blobs](storage-manage-access-to-resources.md)
### [Ejemplos](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob)
## Uso de colas
### [Conceptos](https://msdn.microsoft.com/library/dd179353.aspx)
### [Ejemplos](https://azure.microsoft.com/documentation/samples/?service=storage&term=queue)
## Uso de tablas
### [Información general](https://msdn.microsoft.com/library/dd179463.aspx)
### [Guía de diseño de tablas](storage-table-design-guide.md)
### [Ejemplos](https://azure.microsoft.com/documentation/samples/?service=storage&term=table)
## Uso de archivos
### [Información general](/rest/api/storageservices/fileservices/File-Service-Concepts)
### [Solucionar problemas de Azure Files](storage-troubleshoot-file-connection-problems.md)
### [Ejemplos](https://azure.microsoft.com/documentation/samples/?service=storage&term=file)
## Uso de discos de máquinas virtuales
### Premium Storage
#### [Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual](storage-premium-storage.md)
#### [Migración a Premium Storage](storage-migration-to-premium-storage.md)
#### [Diseño para alto rendimiento](storage-premium-storage-performance.md)
### Standard Storage
#### [Copias de seguridad de discos de máquinas virtuales con instantáneas incrementales](storage-incremental-snapshots.md)
## Planeamiento y diseño
### [Replicación](storage-redundancy.md)
### [Objetivos de escalabilidad y rendimiento](storage-scalability-targets.md)
### [Lista de comprobación de rendimiento y escalabilidad](storage-performance-checklist.md)
### [Simultaneidad](storage-concurrency.md)
## Desarrollo
### Muestras
#### [.NET](storage-samples-dotnet.md)
#### [Java](storage-samples-java.md)
### [Diseño de aplicaciones de alta disponibilidad con RA-GRS](storage-designing-ha-apps-with-ragrs.md)
### [Configuración de cadenas de conexión](storage-configure-connection-string.md)
### [Uso del emulador de Storage](storage-use-emulator.md)
### [Establecimiento y recuperación de propiedades y metadatos](storage-properties-metadata.md)
## Administrar
### [PowerShell](storage-powershell-guide-full.md)
### [Versión preliminar de la CLI de Azure 2.0](storage-azure-cli.md)
### [CLI de Azure 1.0](storage-azure-cli-nodejs.md)
### [Azure Automation](automation-manage-storage.md)
## Protección
### [Guía de seguridad](storage-security-guide.md)
### [Cifrado de datos en reposo](storage-service-encryption.md)
### [Autenticación de clave compartida](https://msdn.microsoft.com/library/dd179428.aspx)
### [Firmas de acceso compartido (SAS)](storage-dotnet-shared-access-signature-part-1.md)
### [Tutorial: Cifrado y descifrado de blobs mediante Azure Key Vault](storage-encrypt-decrypt-blobs-key-vault.md)
### Cifrado de cliente
#### [.NET](storage-client-side-encryption.md)
#### [Java](storage-client-side-encryption-java.md)
#### [Python](storage-client-side-encryption-python.md)
## Supervisión y solución de problemas
### Métricas y registro
#### [Análisis de almacenamiento](storage-analytics.md)
#### [Habilitación y visualización de métrica](storage-enable-and-view-metrics.md)
#### [Supervisión, diagnóstico y solución de problemas](storage-monitoring-diagnosing-troubleshooting.md)
#### [Tutorial de solución de problemas](storage-e2e-troubleshooting.md)
### Solución de errores de eliminación de disco
#### [En una implementación de Resource Manager](storage-resource-manager-cannot-delete-storage-account-container-vhd.md)
#### [En una implementación clásica](storage-cannot-delete-storage-account-container-vhd.md)
### [Solución de problemas de File Storage](storage-troubleshoot-file-connection-problems.md)
### [Guía de recuperación ante desastres](storage-disaster-recovery-guidance.md)
## Transferencia de datos
### [Movimiento de datos hacia Storage y desde este](storage-moving-data.md)
### [Utilidad de línea de comandos AzCopy](storage-use-azcopy.md)
### [Uso del servicio Import-Export](storage-import-export-service.md)
### [Uso de las herramientas del servicio Import-Export](storage-import-export-tool-how-to.md)
#### [Configuración de las herramientas del servicio Import-Export](storage-import-export-tool-setup.md)
#### [Preparación de unidades de disco duro para un trabajo de importación](storage-import-export-tool-preparing-hard-drives-import.md)
##### [Establecimiento de las propiedades y metadatos durante el proceso de importación](storage-import-export-tool-setting-properties-metadata-import.md)
##### [Flujo de trabajo de ejemplo para preparar las unidades de disco duro para un trabajo de importación](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
##### [Referencia rápida de comandos usados con frecuencia para trabajos de importación](storage-import-export-tool-quick-reference.md)
#### [Vista previa de uso de disco para un trabajo de exportación](storage-import-export-tool-previewing-drive-usage-export-v1.md)
#### [Revisión del estado del trabajo con archivos de registro de copia](storage-import-export-tool-reviewing-job-status-v1.md)
#### [Reparación de un trabajo de importación](storage-import-export-tool-repairing-an-import-job-v1.md)
#### [Reparación de un trabajo de exportación](storage-import-export-tool-repairing-an-export-job-v1.md)
#### [Solución de problemas de la herramienta Import/Export](storage-import-export-tool-troubleshooting-v1.md)
#### [Formato del archivo de manifiesto de servicio Import-Export](storage-import-export-file-format-manifest.md)
#### [Formato de archivo de propiedades y metadatos del servicio Import-Export](storage-import-export-file-format-metadata-and-properties.md)
#### [Formato del archivo de registro del servicio Import-Export](storage-import-export-file-format-log.md)
### [Uso de las herramientas del servicio Import-Export (v1)](storage-import-export-tool-how-to-v1.md)
#### [Configuración de las herramientas del servicio Import-Export](storage-import-export-tool-setup-v1.md)
#### [Preparación de unidades de disco duro para un trabajo de importación](storage-import-export-tool-preparing-hard-drives-import-v1.md)
##### [Establecimiento de las propiedades y metadatos durante el proceso de importación](storage-import-export-tool-setting-properties-metadata-import-v1.md)
##### [Flujo de trabajo de ejemplo para preparar las unidades de disco duro para un trabajo de importación](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
##### [Referencia rápida de comandos usados con frecuencia para trabajos de importación](storage-import-export-tool-quick-reference-v1.md)
#### [Vista previa de uso de disco para un trabajo de exportación](storage-import-export-tool-previewing-drive-usage-export-v1.md)
#### [Revisión del estado del trabajo con archivos de registro de copia](storage-import-export-tool-reviewing-job-status-v1.md)
#### [Reparación de un trabajo de importación](storage-import-export-tool-repairing-an-import-job-v1.md)
#### [Reparación de un trabajo de exportación](storage-import-export-tool-repairing-an-export-job-v1.md)
#### [Solución de problemas de la herramienta Import/Export](storage-import-export-tool-troubleshooting-v1.md)
#### [Formato del archivo de manifiesto de servicio Import-Export](storage-import-export-file-format-manifest.md)
#### [Formato de archivo de propiedades y metadatos del servicio Import-Export](storage-import-export-file-format-metadata-and-properties.md)
#### [Formato del archivo de registro del servicio Import-Export](storage-import-export-file-format-log.md)
### [Uso de la API de REST del servicio Azure Import-Export](storage-import-export-using-the-rest-api.md)
#### [Creación de un trabajo de importación](storage-import-export-creating-an-import-job.md)
#### [Creación de un trabajo de exportación](storage-import-export-creating-an-export-job.md)
#### [Recuperación de la información de estado de un trabajo](storage-import-export-retrieving-state-info-for-a-job.md)
#### [Enumeración de trabajos](storage-import-export-enumerating-jobs.md)
#### [Cancelación y eliminación de trabajos](storage-import-export-cancelling-and-deleting-jobs.md)
#### [Realización de una copia de seguridad de los manifiestos de la unidad](storage-import-export-backing-up-drive-manifests.md)
#### [Diagnóstico y recuperación de errores para los trabajos de Import-Export](storage-import-export-diagnostics-and-error-recovery.md)
# Referencia
## [PowerShell](/powershell/storage)
## [CLI de Azure](/cli/azure/storage)
## .NET
### [Resource Manager](/dotnet/api/microsoft.azure.management.storage)
### [Movimiento de datos](https://msdn.microsoft.com/library/azure/mt684990.aspx)
### [Blobs, colas, tablas y archivos](https://msdn.microsoft.com/library/azure/mt347887.aspx)
## [Java](/java/api/)
## [Node.js](http://azure.github.io/azure-storage-node)
## [Ruby](http://azure.github.io/azure-storage-ruby)
## [Python](https://azure-storage.readthedocs.io/en/latest/index.html)
## [C++](http://azure.github.io/azure-storage-cpp)
## [iOS](https://github.com/Azure/azure-storage-ios)
## [Android](http://azure.github.io/azure-storage-android)
## REST
### [Blobs, colas, tablas y archivos](/rest/api/storageservices/fileservices/azure-storage-services-rest-api-reference)
### [Proveedor de recursos](/rest/api/storagerp)
### [Import/Export](/rest/api/storageimportexport)

# Temas relacionados
## Portal clásico
### [Creación de cuenta de almacenamiento](storage-create-storage-account-classic-portal.md)
### [Habilitación y visualización de métrica](storage-enable-and-view-metrics-classic-portal.md)
### [Supervisión, diagnóstico y solución de problemas](storage-monitoring-diagnosing-troubleshooting-classic-portal.md)
### [Tutorial de solución de problemas](storage-e2e-troubleshooting-classic-portal.md)

# Recursos
## [Precios](https://azure.microsoft.com/pricing/details/storage/blobs/)
## [Herramientas de cliente de Azure Storage](storage-explorers.md)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/windows-azure-storage)
## [Foro](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
## [Actualizaciones del servicio](https://azure.microsoft.com/updates/?product=storage)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=storage)

## Explorador de Azure Storage
### [Explorador de Storage (versión preliminar)](../vs-azure-tools-storage-manage-with-storage-explorer.md)
### [Administración de blobs con el Explorador de Storage (versión preliminar)](../vs-azure-tools-storage-explorer-blobs.md)

## Paquetes NuGet
### [Biblioteca de cliente de Azure Storage para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
### [Biblioteca de movimiento de datos de Azure Storage](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)
### [Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)

## Código fuente
### .NET
#### [Blob, cola, tabla y archivo](https://github.com/Azure/azure-storage-net)
#### [Movimiento de datos](https://github.com/Azure/azure-storage-net-data-movement)
#### [Proveedor de recursos](https://github.com/Azure/azure-sdk-for-net)
### [Node.js](http://azure.github.io/azure-storage-node/)
### [Java](https://github.com/Azure/azure-storage-java)
### [C++](https://github.com/Azure/azure-storage-cpp)
### [PHP](https://github.com/Azure/azure-storage-php)
### [Ruby](https://github.com/Azure/azure-storage-ruby)
### [Python](https://github.com/Azure/azure-storage-python)
### [iOS](https://github.com/Azure/azure-storage-ios)


<!--HONumber=Jan17_HO3-->



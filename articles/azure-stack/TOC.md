# Información general
## [¿Qué es Azure Stack?](azure-stack-poc.md)
## [Novedades](azure-stack-whats-new.md)
## [Características y conceptos principales](azure-stack-key-features.md)
## [Arquitectura POC](azure-stack-architecture.md)

# Evaluación e implementación
## Primeros pasos
### [Requisitos previos de implementación](azure-stack-deploy.md)
### [Implementación](azure-stack-run-powershell-script.md)
### [Activación de portales](azure-stack-run-powershell-script.md#activate-the-administrator-and-tenant-portals)
### [Registro](azure-stack-register.md)
## Procedimientos
### [Conexión a Azure Stack POC](azure-stack-connect-azure-stack.md)
### [Adición de imagen predeterminada](azure-stack-add-default-image.md)
### [Aprovisionamiento de una máquina virtual](azure-stack-provision-vm.md)
### [Cree una cuenta de almacenamiento](azure-stack-provision-storage-account.md)
### [Add a new Azure Stack tenant account in Azure Active Directory](azure-stack-add-new-user-aad.md)
### [Nueva implementación de la POC de Azure Stack](azure-stack-redeploy.md)

# Administrar
## Información general
### [Administración de regiones](azure-stack-region-management.md)
### [Uso de los portales](azure-stack-manage-portals.md)
## Primeros pasos
### [Conexión a Azure Stack](azure-stack-connect-azure-stack.md)
### Configuración del entorno de administración
#### [Instalación de PowerShell](azure-stack-powershell-install.md)
#### [Descarga de herramientas](azure-stack-powershell-download.md)
#### [Configuración de PowerShell](azure-stack-powershell-configure.md)
## Procedimientos
### [Administración de actualizaciones](azure-stack-updates.md)
### [Supervisión de estado y alertas](azure-stack-monitor-health.md)
### [Administración de recursos de red](azure-stack-viewing-public-ip-address-consumption-in-tp2.md)
### [Administración de recursos de almacenamiento](azure-stack-manage-storage-accounts.md)
### [Administración de máquinas virtuales de Windows Azure Pack](azure-stack-manage-windows-azure-pack.md)

# Seguridad y cumplimiento
## Procedimientos
### [Administración de RBAC](azure-stack-manage-permissions.md)
### [Adición de usuarios a AD FS](azure-stack-add-users-adfs.md)
### [Creación de entidades de servicio](Azure-stack-create-service-principals.md)

# Oferta de servicios
## Primeros pasos
### Creación de planes, ofertas y cuotas
#### [Establecimiento de cuotas](azure-stack-setting-quotas.md)
#### [Creación de un plan](azure-stack-create-plan.md)
#### [Creación de una oferta](azure-stack-create-offer.md)
#### [Suscripción a una oferta](azure-stack-subscribe-plan-provision-vm.md)
#### [Delegación de ofertas en Azure Stack](azure-stack-delegated-provider.md)
## Procedimientos
### Oferta de SQL o MySQL como PaaS
#### [Implementación de un proveedor de recursos de MySQL](azure-stack-mysql-resource-provider-deploy.md)
#### [Implementación de un proveedor de recursos de SQL](azure-stack-sql-resource-provider-deploy.md)
### Oferta de App Service como PaaS
#### [Introducción a App Service on Azure Stack](azure-stack-app-service-overview.md)
#### [Antes de comenzar](azure-stack-app-service-before-you-get-started.md)
#### [Implementación del proveedor de recursos de App Service](azure-stack-app-service-deploy.md)
#### [Implementación de App Service sin conexión](azure-stack-app-service-deploy-offline.md)
#### [Incorporación de más roles de trabajo web](azure-stack-app-service-add-worker-roles.md)
#### [Configuración de orígenes de implementación](azure-stack-app-service-configure-deployment-sources.md)
#### [Habilitación del FTP en App Service en Azure Stack](azure-stack-app-service-enable-ftp.md)
### Rellenado de Marketplace
#### [Introducción a Marketplace](azure-stack-marketplace.md)
#### [Descarga de elementos de Marketplace](azure-stack-download-azure-marketplace-item.md)
#### [Creación y publicación de un producto en Marketplace](azure-stack-create-and-publish-marketplace-item.md)
#### [Adición de una imagen de máquina virtual personalizada](azure-stack-add-vm-image.md)
#### [Implementación de máquinas virtuales Linux](azure-stack-linux.md)
### Uso y facturación
#### [Información general](azure-stack-billing-and-chargeback.md)
#### [Creación de informes de datos de uso](azure-stack-usage-reporting.md)
#### [API de uso de proveedor](azure-stack-provider-resource-api.md)
#### [API de uso de inquilino](azure-stack-tenant-resource-usage-api.md)
#### [Preguntas más frecuentes sobre uso](azure-stack-usage-related-faq.md)

# Uso de servicios
## Proceso
### [Adición de imagen de máquina virtual](azure-stack-add-vm-image.md)
### [Uso de invitados de Linux](azure-stack-linux.md)
## Almacenamiento
### [Información general](azure-stack-storage-overview.md)
### [Diferencias y consideraciones](azure-stack-acs-differences-tp2.md)
## Red
### [iDNS para Azure Stack](azure-stack-understanding-dns.md)
### [DNS en Azure Stack](azure-stack-dns.md)
### [Descripción de las conexiones VPN de sitio a sitio](azure-stack-create-vpn-connection-one-node-tp2.md)
## Key Vault
### [Información general](azure-stack-kv-intro.md)
### Introducción
#### [Administración con el portal](azure-stack-kv-manage-portal.md)
#### [Administración con PowerShell](azure-stack-kv-manage-powershell.md)
### Procedimientos
#### [Creación de una máquina virtual con un certificado](azure-stack-kv-push-secret-into-vm.md)
#### [Aplicación de ejemplo de Key Vault](azure-stack-kv-sample-app.md)

# Compilación de aplicaciones
## Información general
### [Desarrollo para Azure Stack](azure-stack-developer.md)
## Primeros pasos
### [Conexión a Azure Stack](azure-stack-connect-azure-stack.md)
### Configuración del entorno de desarrollo
#### [Instalación de PowerShell](azure-stack-powershell-install.md)
#### [Descarga de herramientas](azure-stack-powershell-download.md)
#### [Configuración de PowerShell](azure-stack-powershell-configure.md)
#### [Instalación de Visual Studio](azure-stack-install-visual-studio.md)
## Procedimientos
### [Uso del módulo de directivas](azure-stack-policy-module.md)
### Uso de plantillas
#### [Introducción a las plantillas](azure-stack-arm-templates.md)
#### [Desarrollo de plantillas](azure-stack-develop-templates.md)
#### [Comprobación de plantillas](azure-stack-validate-templates.md)
#### [Implementación con el portal](azure-stack-deploy-template-portal.md)
#### [Implementación con PowerShell](azure-stack-deploy-template-powershell.md)
#### [Implementación con Visual Studio](azure-stack-deploy-template-visual-studio.md)

# Solución de problemas
## [Problemas conocidos](azure-stack-troubleshooting.md)
## [Diagnósticos de Azure Stack](azure-stack-diagnostics.md)

# Referencia
## [Administración de perfiles de la versión de API](azure-stack-version-profiles.md)

# Recursos
## [Foro de MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStack)  
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-stack)


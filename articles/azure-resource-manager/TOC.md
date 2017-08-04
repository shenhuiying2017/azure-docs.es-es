# Información general
## [¿Qué es Resource Manager?](resource-group-overview.md)
## [Tipos y proveedores de recursos](resource-manager-supported-services.md)
## [Implementación de Resource Manager y clásica](resource-manager-deployment-model.md)
## [Gobierno de suscripción](resource-manager-subscription-governance.md)
## [Aplicaciones administradas](managed-application-overview.md)

# Introducción
## [Exportación de la plantilla](resource-manager-export-template.md)
## [Creación e implementación de una plantilla](resource-manager-create-first-template.md)
## [Visual Studio con Resource Manager](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

# Muestras
## [Ejemplos de código](https://azure.microsoft.com/en-us/resources/samples/?service=azure-resource-manager)
## PowerShell
### [Implementación de la plantilla](resource-manager-samples-powershell-deploy.md)

## CLI de Azure
### [Implementación de la plantilla](resource-manager-samples-cli-deploy.md)

# Procedimientos
## Crear plantillas
### [Procedimientos recomendados para las plantillas](resource-manager-template-best-practices.md)
### [Secciones de plantilla](resource-group-authoring-templates.md)
### [Vínculo a otras plantillas](resource-group-linked-templates.md)
### [Definición de las dependencias entre recursos](resource-group-define-dependencies.md)
### [Creación de varias instancias](resource-group-create-multiple.md)
### [Establecimiento de la ubicación](resource-manager-template-location.md)
### [Asignación de etiquetas](resource-manager-template-tags.md)
### [Establecimiento del nombre y tipo del recurso secundario](resource-manager-template-child-resource.md)
### [Actualización de recursos](resource-manager-update.md)
### [Uso de objetos para parámetros](resource-manager-objects-as-parameters.md)
### [Compartición del estado entre plantillas vinculadas](best-practices-resource-manager-state.md)
### [Patrones para diseñar plantillas](best-practices-resource-manager-design-templates.md)

## Implementación
### PowerShell
#### [Implementación de la plantilla](resource-group-template-deploy.md)
#### [Implementación de una plantilla privada con el token de SAS](resource-manager-powershell-sas-token.md)
#### [Exportación de la plantilla y reimplementación](resource-manager-export-template-powershell.md)
### CLI de Azure
#### [Implementación de la plantilla](resource-group-template-deploy-cli.md)
#### [Implementación de una plantilla privada con el token de SAS](resource-manager-cli-sas-token.md)
#### [Exportación de la plantilla y reimplementación](resource-manager-export-template-cli.md)
### [Portal](resource-group-template-deploy-portal.md)
### [API de REST](resource-group-template-deploy-rest.md)
### [Implementación en varios grupos de recursos](resource-manager-cross-resource-group-deployment.md)
### [Integración continua con Visual Studio Team Services](../vs-azure-tools-resource-groups-ci-in-vsts.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [Paso de valores seguros durante la implementación](resource-manager-keyvault-parameter.md)

## Administrar
### [PowerShell](powershell-azure-resource-manager.md)
### [CLI de Azure](xplat-cli-azure-resource-manager.md)
### [Portal](resource-group-portal.md)
### [API de REST](resource-manager-rest-api.md)
### [Uso de etiquetas para organizar los recursos](resource-group-using-tags.md)
### [Traslado de recursos a una suscripción o grupo nuevo](resource-group-move-resources.md)
### [Ejemplos de gobierno](resource-manager-subscription-examples.md)

## Control de acceso
### Creación de una entidad de servicio
#### [PowerShell](resource-group-authenticate-service-principal.md)
#### [CLI de Azure 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
#### [CLI de Azure 1.0](resource-group-authenticate-service-principal-cli.md)
#### [Portal](resource-group-create-service-principal-portal.md)
### [API de autenticación para acceder a las suscripciones](resource-manager-api-authentication.md)
### [Bloqueo de recursos](resource-group-lock-resources.md)

## Establecimiento de directivas de recursos
### [¿Qué son las directivas de recursos?](resource-manager-policy.md)
### [Uso del portal para asignar directivas](resource-manager-policy-portal.md)
### [Uso de scripts para asignar directivas](resource-manager-policy-create-assign.md)
### Ejemplos
#### [Etiquetas](resource-manager-policy-tags.md)
#### [Convenciones de nomenclatura](resource-manager-policy-naming-convention.md)
#### [Red](resource-manager-policy-network.md)
#### [Storage](resource-manager-policy-storage.md)
#### [Máquina virtual Linux](../virtual-machines/linux/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
#### [Máquina virtual Windows](../virtual-machines/windows/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)

## Uso de aplicaciones administradas
### [Publicación de la aplicación de catálogo de servicios](managed-application-publishing.md)
### [Consumo de la aplicación de catálogo de servicios](managed-application-consumption.md)
### [Publicación de la aplicación Marketplace](managed-application-author-marketplace.md)
### [Consumo de la aplicación Marketplace](managed-application-consume-marketplace.md)
### [Creación de definiciones de interfaz de usuario](managed-application-createuidefinition-overview.md)

## Auditoría
### [Visualización de registros de actividad](resource-group-audit.md)
### [Ver operaciones de implementación](resource-manager-deployment-operations.md)

## Solución de problemas
### [Errores de implementación frecuentes](resource-manager-common-deployment-errors.md)
### [Descripción de los errores de implementación](resource-manager-troubleshoot-tips.md)
### [Error RequestDisallowedByPolicy](resource-manager-policy-requestdisallowedbypolicy-error.md)
### Errores de implementación de máquinas virtuales
#### [Linux](../virtual-machines/linux/troubleshoot-deploy-vm.md)
#### [Windows](../virtual-machines/windows/troubleshoot-deploy-vm.md)

# Referencia
## [Formato de plantilla](/azure/templates/)
## [Funciones de plantillas](resource-group-template-functions.md)
### [Funciones de matriz y objeto](resource-group-template-functions-array.md)
### [Funciones de comparación](resource-group-template-functions-comparison.md)
### [Funciones de implementación](resource-group-template-functions-deployment.md)
### [Funciones lógicas](resource-group-template-functions-logical.md)
### [Funciones numéricas](resource-group-template-functions-numeric.md)
### [Funciones de recursos](resource-group-template-functions-resource.md)
### [Funciones de cadena](resource-group-template-functions-string.md)
## [Funciones de definición de interfaz de usuario](managed-application-createuidefinition-functions.md)
## [Elementos de definición de interfaz de usuario](managed-application-createuidefinition-elements.md)
### [Microsoft.Common.DropDown](managed-application-microsoft-common-dropdown.md)
### [Microsoft.Common.FileUpload](managed-application-microsoft-common-fileupload.md)
### [Microsoft.Common.OptionsGroup](managed-application-microsoft-common-optionsgroup.md)
### [Microsoft.Common.PasswordBox](managed-application-microsoft-common-passwordbox.md)
### [Microsoft.Common.Section](managed-application-microsoft-common-section.md)
### [Microsoft.Common.TextBox](managed-application-microsoft-common-textbox.md)
### [Microsoft.Compute.CredentialsCombo](managed-application-microsoft-compute-credentialscombo.md)
### [Microsoft.Compute.SizeSelector](managed-application-microsoft-compute-sizeselector.md)
### [Microsoft.Compute.UserNameTextBox](managed-application-microsoft-compute-usernametextbox.md)
### [Microsoft.Network.PublicIpAddressCombo](managed-application-microsoft-network-publicipaddresscombo.md)
### [Microsoft.Network.VirtualNetworkCombo](managed-application-microsoft-network-virtualnetworkcombo.md)
### [Microsoft.Storage.MultiStorageAccountCombo](managed-application-microsoft-storage-multistorageaccountcombo.md)
### [Microsoft.Storage.StorageAccountSelector](managed-application-microsoft-storage-storageaccountselector.md)
## [PowerShell](/powershell/module/azurerm.resources)
## [CLI de Azure](/cli/azure/resource)
## [.NET](/dotnet/api/microsoft.azure.management.resourcemanager)
## [Java](/java/api/com.microsoft.azure.management.resources)
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagement.html)
## [REST](/rest/api/resources/)

# Recursos
## [Azure Roadmap](https://azure.microsoft.com/roadmap/?category=monitoring-management)
## [Calculadora de precios](https://azure.microsoft.com/pricing/calculator/)
## [Actualizaciones del servicio](https://azure.microsoft.com/updates/?product=azure-resource-manager)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-resource-manager)
## [Solicitudes de limitación](resource-manager-request-limits.md)
## [Seguimiento de operaciones asincrónicas](resource-manager-async-operations.md)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=azure-resource-manager)

# Información general
## [Acerca de las máquinas virtuales](../../virtual-machines-windows-about.md)
## [Discos y VHD](../about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
## [Redes virtuales](../../../virtual-network/virtual-networks-overview.md)
## [Preguntas más frecuentes](faq.md)
## [Comparación entre máquinas virtuales, sitios web y servicios en la nube](../../../app-service-web/choose-web-site-cloud-service-vm.md)
## [Contenedores](../../virtual-machines-windows-containers.md)

# Primeros pasos
## [Creación de una VM desde el portal](tutorial.md)
## [Inicio de sesión en una VM](connect-logon.md)
## [Azure PowerShell](/powershell/azure/overview)
## [Instalación de la CLI de Azure](../../../cli-install-nodejs.md)

# Procedimientos

## Uso de Storage
### [Acoplamiento de un disco de datos](attach-disk.md)
### [Desacoplamiento de un disco de datos](detach-disk.md)
### [Uso de D: como disco de datos](../../virtual-machines-windows-change-drive-letter.md)

## Red
### [Cómo configurar puntos de conexión](setup-endpoints.md)
### [Conexión de VM con una red virtual o un servicio en la nube](connect-vms.md)
### [Conexión de redes virtuales clásicas a redes virtuales de Resource Manager](../../../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
### [Crear un equilibrador de carga](../../../load-balancer/load-balancer-get-started-internet-classic-portal.md)
### [Administración de grupos de seguridad de red con Azure Powershell](../../../virtual-network/virtual-networks-create-nsg-classic-ps.md)

## Implementación
### [Creación de una VM personalizada](createportal.md)
### [Creación y configuración de una VM mediante Azure Powershell](create-powershell.md)
### [Captura de una VM con Windows](capture-image.md)
### [Creación y carga de un disco duro virtual con PowerShell](createupload-vhd.md)
### [Automatización de la implementación de máquina virtual de Azure con Chef](../../virtual-machines-windows-chef-automation.md)
### [Creación y administración de máquinas virtuales en Visual Studio](manage-visual-studio.md)
### [Creación de una máquina virtual para una aplicación web con Visual Studio](web-app-visual-studio.md)
### [Ejecución de una tarea de proceso intensivo en Java](java-run-compute-intensive-task.md)
### [Aplicación web Hello World en Django](python-django-web-app.md)

## Configuración
### [Restablecimiento de una contraseña o del servicio de Escritorio remoto](../../virtual-machines-windows-reset-rdp.md)
### [Instalación y configuración de Symantec Endpoint Protection](install-symantec.md)
### [Instalación y configuración de Trend Micro Deep Security como servicio](install-trend.md)
### [Configuración de un conjunto de disponibilidad](configure-availability.md)
### [Cambio de tamaño de una VM con Windows creada en el modelo de implementación clásica](resize-vm.md)
### [Mantenimiento](planned-maintenance-schedule.md)

## administración
### [Migración de implementaciones clásicas a Resource Manager](../../virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
### [Administración de las VM con Azure PowerShell](manage-psh.md)
### [Acerca del agente de VM y extensiones](agents-and-extensions.md)
### [Administración de extensiones de VM](manage-extensions.md)
### [Extensión Custom Script para VM](extensions-customscript.md)
### [Inyección de datos personalizados en una VM de Azure](inject-custom-data.md)

## Plan
### [Acerca de las imágenes](about-images.md)
### [Tamaños de VM](../../virtual-machines-windows-sizes.md)
### [Planeamiento del mantenimiento de VM de Azure](../../virtual-machines-windows-planned-maintenance.md)
### [Instrucciones de implementación de los servicios de infraestructura de Azure](../../virtual-machines-windows-infrastructure-subscription-accounts-guidelines.md)

## Administración de cargas de trabajo
### [Informática de alto rendimiento (HPC)](../../virtual-machines-windows-hpcpack-cluster-options.md)
#### [Escalado automático de recursos](hpcpack-cluster-node-autogrowshrink.md)
#### [Administración de nodos de proceso](hpcpack-cluster-node-manage.md)
#### [Creación de un clúster](hpcpack-cluster-powershell-script.md)
#### [Configuración de un clúster para ejecutar aplicaciones MPI](hpcpack-rdma-cluster.md)
#### [Ejecución de cargas de trabajo de Excel y SOA](../../virtual-machines-windows-excel-cluster-hpcpack.md)
#### [Creación del nodo principal con una imagen de Marketplace](../../virtual-machines-windows-hpcpack-cluster-headnode.md)
#### [Envío de trabajos desde una instancia local a Azure](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md)
### [MongoDB](install-mongodb.md)
### [MySQL](mysql-2008r2.md)
### [Oracle](../../workloads/oracle/oracle-considerations.md)
### [SAP](sap-get-started.md)
### [SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md)
### [Tomcat](java-run-tomcat-app-server.md)

## Solución de problemas
### [Conexiones del Escritorio remoto](../../virtual-machines-windows-troubleshoot-rdp-connection.md)
####[Pasos detallados para solucionar problemas de conexión de Escritorio remoto](../../virtual-machines-windows-detailed-troubleshoot-rdp.md)
### [Acceso a una aplicación](../../virtual-machines-windows-troubleshoot-app-connection.md)
### [Problemas de la implementación clásica con la creación de una VM](troubleshoot-deployment-new-vm.md)
### [Problemas de la implementación clásica con el reinicio o el cambio de tamaño de una VM existente](virtual-machines-windows-classic-restart-resize-error-troubleshooting.md)
### [Restablecimiento de la contraseña de RDP](reset-rdp.md)
### [Conexión de un disco duro virtual para solucionar problemas de máquina virtual](troubleshoot-recovery-disks-portal.md)

# Referencia
## [PowerShell](/powershell/azure/overview)
## [CLI de Azure](/cli/azure/vm)
## [Java](/java/api)
## [.NET](/dotnet/api/microsoft.azure.management.compute)
## [Creación de plantillas de Resource Manager](../../../resource-group-authoring-templates.md)
## [Plantillas de la comunidad](https://azure.microsoft.com/documentation/templates)
## [REST de Compute](/rest/api/compute)
## [REST de Network](/rest/api)
## [REST de Storage](/rest/api/storageservices)

# Recursos
## [Azure Roadmap](https://azure.microsoft.com/roadmap/?category=compute)
## [Precios](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)
## [Calculadora de precios](https://azure.microsoft.com/pricing/calculator/)
## [Disponibilidad regional](https://azure.microsoft.com/regions/services/)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-machine)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=virtual-machines)

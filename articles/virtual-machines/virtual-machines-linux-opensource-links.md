<properties
	pageTitle="Linux y computación de código abierto en Azure | Microsoft Azure"
	description="Incluye artículos de Linux y computación de código abierto en Azure, como el uso básico de Linux, algunos conceptos fundamentales sobre cómo ejecutar o cargar imágenes de Linux en Azure y otros contenidos sobre tecnologías concretas y optimizaciones."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="06/27/2016"
	ms.author="rasquill"/>



# Informática de código abierto y Linux en Azure

Encuentre toda la documentación que necesita para crear y administrar máquinas virtuales de Linux en el modelo de implementación clásica.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## Introducción
- [Introducción a Linux en Azure](virtual-machines-linux-intro-on-azure.md)
- [Preguntas más frecuentes sobre Máquinas virtuales de Azure con el modelo de implementación clásica](virtual-machines-linux-classic-faq.md)
- [Acerca de las imágenes para las máquinas virtuales](virtual-machines-linux-classic-about-images.md)
- [Creación y carga de un disco duro virtual que contiene el sistema operativo Linux](virtual-machines-linux-classic-create-upload-vhd.md) (y también instrucciones para usar una [distribución aprobada por Azure](virtual-machines-linux-endorsed-distros.md))
- [Inicio de sesión en una máquina virtual de Linux con el Portal de Azure clásico](virtual-machines-linux-mac-create-ssh-keys.md)

## Instalación

- [Instalación de la CLI de Azure](../xplat-cli-install.md)


## Tutoriales

- [Instalación de la pila LAMP en una máquina virtual con Linux en Azure](virtual-machines-linux-create-lamp-stack.md)
- [Aplicación web de Ruby on Rails en una máquina virtual de Azure](virtual-machines-linux-classic-ruby-rails-web-app.md)
- [Instalación de Apache Qpid Proton-C para AMQP y Bus de servicio](../service-bus/service-bus-amqp-apache.md)

### Bases de datos
- [Optimización del rendimiento de MySQL en Azure](virtual-machines-linux-classic-optimize-mysql.md)
- [Clústeres de MySQL](virtual-machines-linux-classic-mysql-cluster.md)
- [Ejecución de Cassandra con Linux en Azure y acceso desde Node.js](virtual-machines-linux-classic-cassandra-nodejs.md)
- [creación de un clúster con varios maestros de MariaDbs](virtual-machines-linux-classic-mariadb-mysql-cluster.md)

### HPC
- [Introducción a los nodos de proceso de Linux en un clúster de HPC Pack en Azure](virtual-machines-linux-classic-hpcpack-cluster.md)
- [Ejecución de NAMD con Microsoft HPC Pack en nodos de proceso de Linux en Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
- [Configuración de un clúster de Linux RDMA para ejecutar aplicaciones MPI](virtual-machines-linux-classic-rdma-cluster.md)

### Docker
- [Uso de la extensión de la máquina virtual de Docker desde la interfaz de la línea de comandos de Azure (CLI de Azure)](virtual-machines-linux-classic-cli-use-docker.md)
- [Uso de la extensión de la máquina virtual de Docker con el Portal de Azure](virtual-machines-linux-classic-portal-use-docker.md)
- [Cómo usar una máquina Docker en Azure](virtual-machines-linux-docker-machine.md)

### Ubuntu
- [Clústeres de MySQL](virtual-machines-linux-classic-mysql-cluster.md)
- [Node.js and Cassandra](virtual-machines-linux-classic-cassandra-nodejs.md)

### OpenSUSE
- [Instalación y ejecución de MySQL](virtual-machines-linux-classic-mysql-on-opensuse.md)

### CoreOS
- [Uso de CoreOS en Azure](https://coreos.com/os/docs/latest/booting-on-azure.html)


## Planificación
- [Instrucciones de implementación de los servicios de infraestructura de Azure](virtual-machines-linux-infrastructure-subscription-accounts-guidelines.md)
- [selección de los nombres de usuario de Linux](virtual-machines-linux-usernames.md)
- [Configuración de un conjunto de disponibilidad para máquinas virtuales en el modelo de implementación clásica](virtual-machines-linux-classic-configure-availability.md)
- [Programación del mantenimiento planeado en VM de Azure](virtual-machines-linux-planned-maintenance-schedule.md)
- [Administración de la disponibilidad de las máquinas virtuales](virtual-machines-linux-manage-availability.md)
- [Mantenimiento planeado de máquinas virtuales Linux en Azure](virtual-machines-linux-planned-maintenance.md)


## Implementación
- [Creación de una máquina virtual personalizada con Linux](virtual-machines-linux-classic-createportal.md)
- [Conceptos básicos: Captura de una máquina virtual de Linux para usar como plantilla](virtual-machines-linux-classic-capture-image.md)
- [Información para las distribuciones no aprobadas](virtual-machines-linux-create-upload-generic.md)


## Administración

- [SSH](virtual-machines-linux-mac-create-ssh-keys.md)
- [cómo restablecer una contraseña o las propiedades SSH de Linux](virtual-machines-linux-classic-reset-access.md)
- [uso de la raíz](virtual-machines-linux-use-root-privileges.md)


## Recursos de Azure.

- [el Agente de Linux de Azure](virtual-machines-linux-agent-user-guide.md)
- [Características y extensiones de máquina virtual de Azure](virtual-machines-windows-extensions-features.md)
- [inserción de datos personalizados en una máquina virtual para su uso con Cloud-init](virtual-machines-windows-classic-inject-custom-data.md)


## Almacenamiento

- [asociación de un disco de datos a una máquina virtual Linux](virtual-machines-linux-classic-attach-disk.md)
- [separación de un disco de datos de una máquina virtual Linux](virtual-machines-linux-classic-detach-disk.md)
- [RAID](virtual-machines-linux-configure-raid.md)


## Redes
- [Cómo configurar puntos de conexión de máquinas virtuales Linux clásicas en Azure](virtual-machines-linux-classic-setup-endpoints.md)


## Solución de problemas
- [Solución de problemas de conexiones de Secure Shell (SSH) en una máquina virtual de Azure basada en Linux](virtual-machines-linux-troubleshoot-ssh-connection.md)
- [Solución de problemas de la implementación clásica con la creación de una máquina virtual de Linux en Azure](virtual-machines-linux-classic-troubleshoot-deployment-new-vm.md)
- [Solución de problemas de la implementación clásica con el reinicio o el cambio de tamaño de una máquina virtual con Linux existente en Azure](virtual-machines-linux-classic-restart-resize-error-troubleshooting.md)


## Referencia

- [Comandos CLI de Azure en modo de Administración de servicios de Azure (asm)](../virtual-machines-command-line-tools.md)
- [API de REST de administración de servicios de Azure](https://msdn.microsoft.com/library/azure/ee460799.aspx)




## Vínculos generales
Los vínculos siguientes son de blogs de Microsoft, páginas de Technet y sitios externos en lugar formar parte de la documentación de Azure.com anterior. Dado que Azure y el mundo de la informática de código abierto cambian con frecuencia, los siguientes vínculos seguramente no estarán actualizados, *a pesar de* que hacemos todo lo posible para seguir agregando nuevos temas y eliminando los obsoletos. Si hemos omitido alguno, comuníquenoslo en los comentarios o envíe una solicitud de extracción a nuestro [Repositorio de Github](https://github.com/Azure/azure-content/).

- [ejecución de ASP.NET 5 en Linux mediante contenedores Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)
- [Cómo implementar una imagen de máquina virtual CentOS desde OpenLogic](https://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
- [SUSE Update Infrastructure (Infraestructura de actualización de SUSE)](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
- [SUSE Linux Enterprise Server para la biblioteca de aplicaciones de nube de SAP](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)
- [creación de Linux de alta disponibilidad en Azure en 12 pasos](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
- [Automate Provisioning Linux on Azure with Azure CLI, node.js, jhawk (Automatización del aprovisionamiento de Linux en Azure con la CLI de Azure, node.js y jhawk)](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
- [GlusterFS en Azure](http://dastouri.azurewebsites.net/gluster-on-azure-part-1/)

### FreeBSD
- [Ejecución de FreeBSD en Azure](https://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
- [Implementación fácil de FreeBSD](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
- [Implementación de una imagen personalizada de FreeBSD](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
- [Kaspersky antivirus para el servidor de archivos de Linux](https://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

### NoSQL

- [8 bases de datos NoSql de código abierto para Azure](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
- [Slideshare (MSOpenTech): Experiencias con CouchDb en Azure](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
- [ejecución de CouchDB como servicio con node.js, CORS y Grunt](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)

- [Redis en Windows en el servicio de caché de Redis de Azure](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
- [anuncio del proveedor de estado de sesión de ASP.NET para la versión de vista previa de Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)

- [Blog: RavenHQ ya está disponible en Azure Marketplace](https://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

### Datos de gran tamaño
- [instalación de Hadoop en máquinas virtuales Linux de Azure](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
- [HDInsight de Azure](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/)

### Base de datos relacional
- [Arquitectura de MySQL de alta disponibilidad en Microsoft Azure](http://download.microsoft.com/download/6/1/C/61C0E37C-F252-4B33-9557-42B90BA3E472/MySQL_HADR_solution_in_Azure.pdf)
- [Instalación de Postgres con corosync, pg\_bouncer con ILB](https://github.com/chgeuer/postgres-azure)

### Informática de alto rendimiento de Linux (HPC)

- [Plantilla de inicio rápido: establecimiento de un clúster SLURM](https://github.com/Azure/azure-quickstart-templates/tree/master/slurm) (y [una entrada de blog](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx))
- [Plantilla de inicio rápido: Crear un clúster de HPC con nodos de proceso de Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)

### DevOps, administración y optimización

Dado que el mundo de DevOps, administración y optimización es bastante extenso y cambia muy rápidamente, debería considerar la siguiente lista como un punto de partida.

- [Vídeo: Azure Virtual Machines: Using Chef, Puppet and Docker for managing Linux VMs (Máquinas virtuales de Azure: Uso de Chef, Puppet y Docker para administrar máquinas virtuales de Linux)](https://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/)

- [Guía completa para la implementación automatizada del clúster de Kubernetes con CoreOS y Weave](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
- [Visualizador de Kubernetes](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)

- [complemento esclavo de Jenkins para Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
- [Repositorio de GitHub: Complemento de almacenamiento de Jenkins para Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)

- [Terceros: Complemento esclavo de Hudson para Azure](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
- [Terceros: Complemento de almacenamiento de Hudson para Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)

- [Vídeo: ¿Qué es Chef y cómo funciona?](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)

- [Vídeo: Cómo utilizar la automatización de Azure con máquinas virtuales Linux](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)

- [Blog: Cómo hacer DSC de Powershell para Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
- [GitHub: DSC de cliente Docker](https://github.com/anweiss/DockerClientDSC)

- [Complemento de compresor para Azure](https://github.com/msopentech/packer-azure)

<!---HONumber=AcomDC_0831_2016-->
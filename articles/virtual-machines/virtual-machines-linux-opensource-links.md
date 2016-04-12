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
	ms.date="03/26/2016"
	ms.author="rasquill"/>



# Informática de código abierto y Linux en Azure

Este documento tiene como finalidad reunir en un solo lugar todos los temas sobre el modelo de implementación clásica que Microsoft y sus socios han escrito en relación con la ejecución de máquinas virtuales basadas en Linux, así como otros entornos informáticos y aplicaciones de código abierto en Microsoft Azure.

Dado que Azure y el mundo de la informática de código abierto son destinos muy cambiantes, es prácticamente seguro que este documento no esté actualizado, *a pesar de* que hacemos todo lo posible para seguir agregando nuevos temas y eliminar los desactualizados. Si hemos omitido alguno, comuníquenoslo en los comentarios o envíe una solicitud de extracción a nuestro [Repositorio de Github](https://github.com/Azure/azure-content/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## Notas generales
Las secciones se desglosan a la derecha de esta página. (Los vínculos pueden aparecer en más de una sección, dado que los temas pueden tratar sobre más de un concepto, distribución o tecnología). Además, hay varios temas que describen diversas opciones de Linux, repositorios de imágenes, casos prácticos y temas de procedimientos para cargar sus propias imágenes personalizadas:

- [Azure Marketplace](https://azure.microsoft.com/marketplace/virtual-machines/)
- [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index)
- [Eventos y demostraciones: Microsoft Openness CEE](http://www.opennessatcee.com/)
- [Carga de su propia imagen de distro](virtual-machines-linux-classic-create-upload-vhd.md) (y también instrucciones de uso de una [Distribución respaldada por Azure](virtual-machines-linux-endorsed-distros.md))
- [Notas: Requisitos generales de Linux para ejecutarse en Azure](virtual-machines-linux-create-upload-generic.md)
- [Notas: Introducción general a Linux en Azure](virtual-machines-linux-intro-on-azure.md)

## Distros

Hay miles de distribuciones de Linux, normalmente desglosadas por los sistemas de administración de paquetes: Algunas están basadas en dpkg, como Debian y Ubuntu, y otras en rpm, como CentOS, SUSE y RedHat. Algunas compañías ofrecen imágenes de distros como partners formales de Microsoft y están respaldadas. Otros las proporciona la comunidad. Las distros de esta sección tienen artículos formales sobre ellas, aunque solo se hayan usado en los ejemplos de otras tecnologías.

### [Ubuntu](https://azure.microsoft.com/marketplace/partners/Canonical/)

Ubuntu es una distribución de Linux ampliamente conocida que está respaldada en Azure y que se basa en dpkg y en la administración de paquetes apt-get.

3. [Clústeres de MySQL](virtual-machines-linux-classic-mysql-cluster.md)
4. [Node.js and Cassandra](virtual-machines-linux-classic-cassandra-nodejs.md)
6. [Geeking out: Ejecución de ASP.NET 5 en Linux mediante contenedores Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)

### CentOS

La distribución de Linux CentOS es una plataforma estable, predecible, administrable y reproducible derivada de los orígenes de Red Hat Enterprise Linux (RHEL).

4. [Blog: Implementación de una imagen de máquina virtual CentOS desde OpenLogic](https://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
6. [Instalación de Apache Qpid Proton-C para AMQP y Bus de servicio](../service-bus/service-bus-amqp-apache.md/)

### SUSE Linux Enterprise Server y openSUSE

11. [Instalación y ejecución de MySQL](virtual-machines-linux-classic-mysql-on-opensuse.md)
13. [[SUSE forum] How to: Move to a New Patch Server (Foro SUSE Cómo: Moverse a un nuevo servidor de revisión)](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
14. [Imágenes: SUSE Linux Enterprise Server para la biblioteca de aplicaciones de nube de SAP](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)

### CoreOS

CoreOS es una distro pequeña y optimizada para el escalado de cálculo puro con un alto grado de control para la personalización.

10. [Galería de imágenes](https://azure.microsoft.com/marketplace/partners/coreos/)  
11. [Uso de CoreOS en Azure](virtual-machines-linux-classic-coreos-howto.md)
12. [Introducción a Fleet y Docker en CoreOS en Azure](virtual-machines-linux-classic-coreos-fleet-get-started.md)


### FreeBSD

12. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Date&search=FreeBSD)
13. [Blog: Ejecución de FreeBSD en Azure](https://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
14. [Blog: Implementación fácil de FreeBSD](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
15. [Blog: Implementación de una imagen personalizada de FreeBSD](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
18. [Marketplace: Kaspersky antivirus para el servidor de archivos de Linux](https://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

## Conceptos básicos

1. [Conceptos básicos: Interfaz de la línea de comandos de Azure (CLI de Azure)](../xplat-cli-install.md)
5. [Conceptos básicos: Selección de nombres de usuario de Linux](virtual-machines-linux-usernames.md)
6. [Conceptos básicos: Inicio de sesión en una máquina virtual de Linux con el Portal de Azure clásico](virtual-machines-linux-classic-log-on.md)
7. [Conceptos básicos: SSH](virtual-machines-linux-ssh-from-linux.md)
8. [Conceptos básicos: Restablecimiento de una contraseña o de las propiedades SSH de Linux](virtual-machines-linux-classic-reset-access.md)
9. [Conceptos básicos: Uso de la raíz](virtual-machines-linux-use-root-privileges.md)
10. [Conceptos básicos: Asociación de un disco de datos a una máquina virtual Linux](virtual-machines-linux-classic-attach-disk.md)
11. [Conceptos básicos: Desasociación de un disco de datos de una máquina virtual Linux](virtual-machines-linux-classic-detach-disk.md)
12. [Blogs con los conceptos básicos: Optimización del almacenamiento, los discos y el rendimiento con Linux y Azure](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
13. [Conceptos básicos: RAID](virtual-machines-linux-configure-raid.md)
14. [Conceptos básicos: Captura de una máquina virtual de Linux para usar como plantilla](virtual-machines-linux-classic-capture-image.md)
15. [Conceptos básicos: Instalación del agente de Linux de Azure](virtual-machines-linux-agent-user-guide.md)
16. [Conceptos básicos: Características y extensiones de máquina virtual de Azure](virtual-machines-windows-extensions-features.md)
17. [Conceptos básicos: Inserción de datos personalizados en una máquina virtual para su uso con Cloud-init](virtual-machines-windows-classic-inject-custom-data.md)
18. [Blogs con los conceptos básicos: Creación de Linux de alta disponibilidad en Azure en 12 pasos](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
19. [Blogs con los conceptos básicos: Automatización del aprovisionamiento de Linux en Azure con CLI de Azure, node.js, jhawk](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
23. [Referencia de la API de REST de administración de servicios de Azure](https://msdn.microsoft.com/library/azure/ee460799.aspx)
24. [GlusterFS en Azure](http://dastouri.azurewebsites.net/gluster-on-azure-part-1/)

## Repositorios e imágenes de la comunidad
4. [GitHub](https://github.com/Azure/) &mdash; para la CLI de Azure y muchos otros proyectos y herramientas.
5. [Registro del concentrador de Docker](https://registry.hub.docker.com/) &mdash; el registro para las imágenes del contenedor de Docker.

## Datos

En esta sección se incluye información acerca de distintos enfoques y tecnologías de almacenamiento, entre las que se incluyen NoSQL, datos relacionales y Big Data.

### NoSQL

1. [Blog: 8 bases de datos NoSql de código abierto para Azure](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
2. Couchdb
    - [Slideshare (MSOpenTech): Experiencias con CouchDb en Azure](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
    - [Blog: Ejecución de CouchDB como servicio con node.js, CORS y Grunt](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)
4. Cassandra
    - [Ejecución de Cassandra con Linux en Azure y acceso desde Node.js](virtual-machines-linux-classic-cassandra-nodejs.md)
5. Redis
    - [Blog: Redis en Windows en el servicio de caché de Redis de Azure](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
    - [Blog: Anuncio del proveedor de estado de sesión de ASP.NET para la versión de vista previa de Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)
6. RavenHQ
    - [Blog: RavenHQ ya está disponible en Azure Marketplace](https://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

### Datos de gran tamaño
2. Hadoop/Cloudera  
	- [Blog: Instalación de Hadoop en máquinas virtuales Linux de Azure](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
3. [HDInsight de Azure](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/): Un servicio Hadoop totalmente administrado en Azure.

### Base de datos relacional
2. MySQL
    - [Instalación y ejecución de MySQL](virtual-machines-linux-classic-mysql-on-opensuse.md)
    - [Optimización del rendimiento de MySQL en Azure](virtual-machines-linux-classic-optimize-mysql.md)
    - [Clústeres de MySQL](virtual-machines-linux-classic-mysql-cluster.md)
    - [Arquitectura de MySQL de alta disponibilidad en Microsoft Azure](http://download.microsoft.com/download/6/1/C/61C0E37C-F252-4B33-9557-42B90BA3E472/MySQL_HADR_solution_in_Azure.pdf)
7. MariaDB
    - [Creación de un clúster con varios maestros de MariaDbs](virtual-machines-linux-classic-mariadb-mysql-cluster.md)
8. [Instalación de Postgres con corosync, pg\_bouncer con ILB](https://github.com/chgeuer/postgres-azure)


## Autenticación y cifrado

La autenticación y el cifrado son aspectos cruciales del desarrollo de software, y hay una gran cantidad de temas en la web que describen cómo aprender y usar técnicas de seguridad adecuadas para ambos. Se describen aspectos del uso básico para estar en disposición de trabajar rápidamente con cargas de trabajo de código abierto y Linux, y también se indican las herramientas que se pueden utilizar para restablecer o quitar características de seguridad remota en Azure. Se trata de procedimientos básicos y pronto se agregarán escenarios más complejos.

4. [Conceptos básicos: Uso y administración de certificados](http://msdn.microsoft.com/library/azure/gg981929.aspx)
7. [Conceptos básicos: SSH](virtual-machines-linux-ssh-from-linux.md)
8. [Conceptos básicos: Restablecimiento de una contraseña o de las propiedades SSH de Linux](virtual-machines-linux-classic-reset-access.md)
9. [Conceptos básicos: Uso de la raíz](virtual-machines-linux-use-root-privileges.md)

## Informática de alto rendimiento de Linux (HPC)

Ejecute cargas de trabajo de HPC en clústeres de VM de Linux creados con herramientas de código abierto o con Microsoft HPC Pack.

1.	[Plantilla de inicio rápido: establecimiento de un clúster SLURM](https://azure.microsoft.com/documentation/templates/slurm/) (y [una entrada de blog](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx))
3.	[Plantilla de inicio rápido: Crear un clúster de HPC con nodos de proceso de Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)
4.	[Tutorial: Introducción a los nodos de proceso de Linux en un clúster de HPC Pack en Azure](virtual-machines-linux-classic-hpcpack-cluster.md)
5.	[Tutorial: Ejecute NAMD con Microsoft HPC Pack en los nodos de cálculo de Linux en Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
6.	[Tutorial: Configuración de un clúster de Linux RDMA para ejecutar aplicaciones MPI](virtual-machines-linux-classic-rdma-cluster.md)


## DevOps, administración y optimización

Esta sección comienza con una entrada de blog que contiene una serie de vídeos sobre [Vídeo: Máquinas virtuales de Azure: Uso de Chef, Puppet y Docker para administrar máquinas virtuales Linux](https://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/). Sin embargo, el mundo de devops, administración y optimización es bastante extenso y cambia muy rápidamente, por lo que debe tener en cuenta la lista situada más adelante, como punto de partida.

1. Docker
	- [Uso de la extensión de la máquina virtual de Docker desde la interfaz de la línea de comandos de Azure (CLI de Azure)](virtual-machines-linux-classic-cli-use-docker.md)
	- [Uso de la extensión de la máquina virtual de Docker con el Portal de Azure](virtual-machines-linux-classic-portal-use-docker.md)
    - [Introducción rápida a Docker en Azure Marketplace](virtual-machines-linux-classic-docker-quickstart.md)
	- [Cómo usar una máquina Docker en Azure](virtual-machines-linux-classic-docker-machine.md)

2. [Fleet con CoreOS](virtual-machines-linux-classic-coreos-howto.md)
4. Kubernetes
	- [Guía completa para la implementación automatizada del clúster de Kubernetes con CoreOS y Weave](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
	- [Visualizador de Kubernetes](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)
5. Jenkins y Hudson
	- [Blog: Complemento esclavo de Jenkins para Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
	- [Repositorio de GitHub: Complemento de almacenamiento de Jenkins para Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)
	- [Terceros: Complemento esclavo de Hudson para Azure](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
	- [Terceros: Complemento de almacenamiento de Hudson para Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
10. Chef
	- [Vídeo: ¿Qué es Chef y cómo funciona?](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)

12. Automatización de Azure
	- [Vídeo: Cómo utilizar la automatización de Azure con máquinas virtuales Linux](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
13. PowerShell DSC para Linux
    - [Blog: Cómo hacer DSC de Powershell para Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
    - [GitHub: DSC de cliente Docker](https://github.com/anweiss/DockerClientDSC)
13. [Ubuntu Juju](https://juju.ubuntu.com/docs/config-azure.html)
14. [Complemento de compresor para Azure](https://github.com/msopentech/packer-azure)

## Soporte técnico, solución de problemas y "Sencillamente no funciona"

1. Documentación de soporte técnico de Microsoft
	- [Compatibilidad: Compatibilidad con las imágenes de Linux en Microsoft Azure](http://support2.microsoft.com/kb/2941892)

<!---HONumber=AcomDC_0330_2016-->
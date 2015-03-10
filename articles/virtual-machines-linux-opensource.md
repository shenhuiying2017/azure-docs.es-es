<properties
  pageTitle="Informática de código abierto y Linux en Azure"
  description="En este tema se incluye de informática de código abierto y Linux en Azure, como el uso básico de Linux, algunos conceptos fundamentales acerca de cómo ejecutar o cargar imágenes de Linux en Azure y otros contenidos sobre tecnologías concretas y optimizaciones."
  documentationCenter=""
  authors="squillace"
  manager="timlt"
  editor="tysonn"/>

<tags
  ms.service="virtual-machines"
  ms.devlang="NA"
  ms.topic="article"
  ms.tgt_pltfrm="vm-linux"
  ms.workload="infrastructure-services"
  ms.date="02/17/2015"
  ms.author="rasquill"/>


<!--The next line, with one pound sign at the beginning, is the page title-->
# Informática de código abierto y Linux en Azure

El objeto de este documento es reunir en un solo lugar todos los temas que Microsoft y sus partners han escrito sobre la ejecución de máquinas virtuales basadas en Linux, así como otros entornos y aplicaciones informáticas de código abierto en Microsoft Azure. Como tanto Azure como el mundo de la informática de código abierto rápido son destinos muy cambiantes, con casi total seguridad este documento está desfasado, *despite* a pesar de que hacemos todo lo posible para seguir agregando nuevos temas y eliminar los desactualizados. Si hemos omitido alguno, comuníquenoslo en los comentarios o envíe una solicitud de extracción a nuestro [repositorio de Github](https://github.com/Azure/azure-content/).

Las secciones se desglosan como sigue. (Los vínculos pueden aparecer en más de una sección, dado que los temas pueden tratar sobre más de un concepto, distribución o tecnología).

- [Distros](#distros) &mdash; Temas que tratan sobre una distro concreta.
- [Conceptos básicos](#basics) &mdash; Muchas de los aspectos básicos que conocer o debe conocer.
- [Repositorios e imágenes de la comunidad](#images) &mdash; Otros lugares con información, repositorios y archivos binarios de gran utilidad.
- [Lenguajes y plataformas](#langsandplats)
- [Ejemplos y scripts](#samples)
- [Autenticación y cifrado](#security) &mdash; Temas importantes relacionados con la seguridad, no necesariamente específicos de Azure.
- [Devops, administración y optimización](#devops) &mdash; Una gran categoría, que cambia con rapidez.
- [Soporte técnico, solución de problemas y "Sencillamente no funciona"](#supportdebug) &mdash; De verdad.

Además, hay varios temas que describen diversas opciones de Linux, repositorios de imágenes, casos prácticos y temas de procedimientos para cargar sus propias imágenes personalizadas: 

- [Azure Marketplace](http://azure.microsoft.com/marketplace/virtual-machines/)
- [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index)
- [Eventos y demostraciones: Microsoft Openness CEE](http://www.opennessatcee.com/)
- [Codificación Carga de su propia imagen de distro](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd/) (y también instrucciones de uso de una [distribución respaldada por Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-endorsed-distributions/))
- [Notas: Requisitos generales de Linux para ejecutarse en Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd-generic/)
- [Notas: Introducción general a Linux en Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-introduction/)


### <a id='distros'>Distros</a>

Hay miles de distribuciones de Linux, normalmente desglosadas por los sistemas de administración de paquetes: Algunas están basadas en dpkg, como Debian y Ubuntu, y otras en rpm, como CentOS, SUSE y RedHat. Algunas compañías ofrecen imágenes de distros como partners formales de Microsoft y están respaldadas. Otros las proporciona la comunidad. Las distros de esta sección tienen artículos formales sobre ellas, aunque solo se hayan usado en los ejemplos de otras tecnologías.


#### [Ubuntu](http://azure.microsoft.com/marketplace/partners/Canonical/)

Ubuntu es una distribución de Linux ampliamente conocida que está respaldada en Azure y que se basa en dpkg y en la administración de paquetes apt-get.

1. [Codificación Carga de su propia imagen de Ubuntu](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd-ubuntu/)
2. [Codificación Pila LAMP de Ubuntu](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-install-lamp-stack/)
2. [Imágenes: Pila LAPP](http://azure.microsoft.com/marketplace/partners/bitnami/lappstack54310ubuntu1404/)
3. [Codificación Clústeres de MySQL](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-mysql-cluster/)
4. [Codificación Node.js y Cassandra](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-nodejs-running-cassandra/)
5. [Codificación Bloc de notas de IPython](http://azure.microsoft.com/documentation/articles/virtual-machines-python-ipython-notebook/)
6. [Geeking out: ejecución de ASP.NET 5 en Linux mediante contenedores Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)
7. [Imágenes: Servidor de Redis](http://azure.microsoft.com/marketplace/partners/cognosys/redisserver269ubuntu1204lts/)
8. [Imágenes: Servidor de Minecraft](http://azure.microsoft.com/marketplace/partners/bitnami/craftbukkitminecraft179r030ubuntu1210/)
9. [Imágenes: Moodle](http://azure.microsoft.com/marketplace/partners/bitnami/moodle270ubuntu1404/)
11. [Imágenes: Mono como un servicio](http://azure.microsoft.com/marketplace/partners/aegis/monoasaserviceubuntu1204/)

#### [Debian](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=Debian)

Debian es una distribución importante para el mundo de Linux y del código abierto basado en la administración de paquetes dpkg y apt-get. MSOpenTech VM Depot tiene varias imágenes que se pueden usar.

#### CentOS

La distribución de Linux CentOS es una plataforma estable, predecible, administrable y reproducible derivada de los orígenes de Red Hat Enterprise Linux (RHEL).

1. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=centos)
2. [Galería de imágenes](http://azure.microsoft.com/en-in/marketplace/partners/OpenLogic/)
3. [Codificación Preparación de una máquina virtual personalizada basada en CentOS para Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd-centos/)
4. [Blog: Cómo implementar una imagen de máquina virtual CentOS desde OpenLogic](http://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
6. [Codificación Instalación de Apache Qpid Proton-C para AMQP y Bus de servicio](http://msdn.microsoft.com/library/azure/dn235560.aspx)
7. [Imágenes: Apache 2.2.15 en OpenLogic CentOS 6.3](http://azure.microsoft.com/marketplace/partners/cognosys/apache2215onopenlogiccentos63/)
8. [Imágenes: Drupal 7.2, servidor LAMP en OpenLogic CentOS 6.3](http://azure.microsoft.com/marketplace/partners/cognosys/drupal720lampserveronopenlogiccentos63/)

#### SUSE Enterprise Linux y OpenSUSE

9. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=OpenSUSE)
11. [Codificación Instalación y ejecución de MySQL](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-mysql-use-opensuse/)
12. [Configuración Preparación de un SLES personalizado o una máquina virtual openSUSE](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd-suse/)  
13. [[Foro SUSE]Migración a un nuevo servidor de revisión](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
14. [Imágenes: SUSE Linux Enterprise Server para la biblioteca de aplicaciones de nube de SAP](http://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)

#### CoreOS

CoreOS es una distro pequeña y optimizada para el escalado de cálculo puro con un alto grado de control para la personalización.

10. [Galería de imágenes](http://azure.microsoft.com/en-in/marketplace/partners/coreos/)  
11. [Codificación Uso de CoreOS en Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-coreos-how-to/)
12. [Blog: TechEd Europe, contenedores de Linux y de cliente Docker de Windows](http://azure.microsoft.com/blog/2014/10/28/new-docker-coreos-topics-linux-on-azure/)
13. [Blog: Azure es cada vez más grande, más rápido y más abierto](http://azure.microsoft.com/blog/2014/10/20/azures-getting-bigger-faster-and-more-open/)
14. [GitHub: Inicio rápido para la implementación de CoreOS en Azure](https://github.com/timfpark/coreos-azure)
15. [GitHub: Implementación de aplicaciones de Java con Spring Boot, MongoDB y CoreOS](https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init)

#### [Oracle Linux](http://azure.microsoft.com/marketplace/?term=Oracle+Linux)
  2. [Preparación de una máquina virtual Oracle Linux para Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-vhd-oracle/)

#### FreeBSD

12. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Date&search=FreeBSD)
13. [Blog: Ejecución de FreeBSD en Azure](http://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
14. [Blog: Implementación fácil de FreeBSD](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
15. [Blog: Implementación de una imagen personalizada de FreeBSD](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
17. [Codificación Instalación del agente de Linux de Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-agent-user-guide/)
18. [Marketplace: Kaspersky antivirus para el servidor de archivos de Linux](http://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

### <a id='basics'>Conceptos básicos</a>

1. [Conceptos básicos: interfaz de línea de comandos (cli) de Azure](http://azure.microsoft.com/documentation/articles/xplat-cli/)
4. [Conceptos básicos: uso y administración de certificados](http://msdn.microsoft.com/library/azure/gg981929.aspx)
5. [Conceptos básicos: selección de los nombres de usuario de Linux](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-usernames/)
6. [Conceptos básicos: inicio de sesión en una máquina virtual de Linux con el Portal de Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-how-to-log-on/)
7. [Conceptos básicos: SSH](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/)
8. [Conceptos básicos: cómo restablecer una contraseña o las propiedades SSH de Linux](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-vmaccess-reset-password-or-ssh/)
9. [Conceptos básicos: uso de la raíz](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-root-privileges/)
10. [Conceptos básicos: asociación de un disco de datos a una máquina virtual Linux](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-how-to-attach-disk/)
11. [Conceptos básicos: separación de un disco de datos de una máquina virtual Linux](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-how-to-detach-disk/)
12. [Blogs con los conceptos básicos: optimización del almacenamiento, los discos y el rendimiento con Linux y Azure](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
13. [Conceptos básicos: RAID](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-configure-raid/)
14. [Conceptos básicos: captura de una máquina virtual de Linux para crear una plantilla](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/)
15. [Conceptos básicos: el Agente de Linux de Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-agent-user-guide/)
16. [Conceptos básicos: características y extensiones de máquina virtual de Azure](http://msdn.microsoft.com/library/azure/dn606311.aspx)
17. [Conceptos básicos: inserción de datos personalizados en una máquina virtual para su uso con Cloud-init](http://azure.microsoft.com/documentation/articles/virtual-machines-how-to-inject-custom-data/)
18. [Blogs con los conceptos básicos: creación de Linux de alta disponibilidad en Azure en 12 pasos](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
19. [Blogs con los conceptos básicos: automatización del aprovisionamiento de Linux en Azure con xplat, node.js, jhawk](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
20. [Conceptos básicos: extensión de la máquina virtual de Azure Docker](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-vm-extension/)
23. [API de REST de administración de servicios Azure](https://msdn.microsoft.com/es-es/library/azure/ee460799.aspx) (referencia)

### <a id='images'>Repositorios e imágenes de la comunidad</a>
3. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index) &mdash; para las imágenes de máquina virtual que proporciona la comunidad.
4. [Github](https://github.com/Azure/) &mdash; para xplat-cli y muchos otros proyectos y herramientas.
5. [Registro del concentrador de Docker](https://registry.hub.docker.com/)&mdash; el registro para las imágenes del contenedor de Docker.


### <a id='langsandplats'>Lenguajes y plataformas</a>
#### [Centro de desarrollo de Java de Azure](http://azure.microsoft.com/develop/java/)

1. [Imágenes](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=java)
2. [Codificación uso del Bus de servicio desde Java con AMQP 1.0](http://msdn.microsoft.com/library/azure/jj841073.aspx)
3. [Codificación Configurar Tomcat7 en Linux con el Portal de Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-setup-tomcat7-linux/)
4. [Vídeo: SDK de Java en Azure para la administración de servicios](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-157-The-Java-SDK-for-Azure-Management-with-Brady-Gaster)
5. [Blog: Introducción a las bibliotecas de administración de Azure para Java](http://azure.microsoft.com/blog/2014/09/15/getting-started-with-the-azure-java-management-libraries/)
5. [Repositorio de GitHub: Kit de herramientas de Azure para Eclipse con Java](https://github.com/MSOpenTech/WindowsAzureToolkitForEclipseWithJava)
6. [Referencia: kit de herramientas de Azure para Eclipse con Java](http://msdn.microsoft.com/library/azure/hh694271.aspx)
7. [Repositorio de GitHub: Complemento de herramientas técnicas abiertas de MS para IntelliJ IDEA y Android Studio](https://github.com/MSOpenTech/msopentech-tools-for-intellij)
7. [Blog: MSOpenTech contribuye con OpenJDK](http://msopentech.com/blog/2014/10/21/ms-open-techs-first-contribution-openjdk/)
8. [Imágenes: WebSphere](http://azure.microsoft.com/marketplace/partners/msopentech/was-8-5-was-8-5-5-3/)
9. [Imágenes: WebLogic](http://azure.microsoft.com/marketplace/?term=weblogic)
10. [Imágenes: JDK6 en Windows](http://azure.microsoft.com/marketplace/partners/msopentech/jdk6onwindowsserver2012/)
11. [Imágenes: JDK7 en Windows](http://azure.microsoft.com/marketplace/partners/msopentech/jdk7onwindowsserver2012/)
12. [Imágenes: JDK8 en Windows](http://azure.microsoft.com/marketplace/partners/msopentech/jdk8onwindowsserver2012r2/)

#### Lenguajes JVM

1. [Scala: ejecución de aplicaciones del marco Play en Servicios en la nube de Azure](http://msopentech.com/blog/2014/09/25/tutorial-running-play-framework-applications-microsoft-azure-cloud-services-2/)

#### Actualizaciones, instalaciones, tipos de SDK
4. [SDK de administración de servicios de Azure: Java](http://dl.windowsazure.com/javadoc/)
5. [SDK de administración de servicios de Azure: Go](https://github.com/MSOpenTech/azure-sdk-for-go)
5. [SDK de administración de servicios de Azure: Ruby](https://github.com/MSOpenTech/azure-sdk-for-ruby)
- [Codificación Instalación de Ruby on Rails](http://azure.microsoft.com/documentation/articles/virtual-machines-ruby-rails-web-app-linux/)
- [Codificación Instalación de Ruby on Rails con Capistrano, Nginx, Unicorn y PostgreSQL](http://azure.microsoft.com/documentation/articles/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/)
6. [SDK de administración de servicios de Azure: Python](https://github.com/Azure/azure-sdk-for-python)
- [Codificación Aplicación web Hello World en Django (Mac-Linux)](http://azure.microsoft.com/documentation/articles/virtual-machines-python-django-web-app-linux/)
7. [SDK de administración de servicios de Azure: Node.js](https://github.com/MSOpenTech/azure-sdk-for-node)
8. [SDK de administración de servicios de Azure: PHP](https://github.com/MSOpenTech/azure-sdk-for-php)
- [Codificación Instalación de la pila LAMP en una máquina virtual de Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-install-lamp-stack/)
- [Vídeo: Instalación de una pila LAMP en una máquina virtual de Azure](http://channel9.msdn.com/Shows/Azure-Friday/LAMP-stack-on-Azure-VMs-with-Guy-Bowerman)
9. [SDK de administración de servicios de Azure: .NET](https://github.com/Azure/azure-sdk-for-net)
10. [Blog: Mono, ASP.NET 5, Linux y Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)

### <a id='samples'>Ejemplos y scripts</a>

1. [Repositorio de Github de Linux y Azure de Patrick Chanezon](https://github.com/chanezon/azure-linux)
3. [Vídeo: cómo mover datos de USB locales en Linux a Azure mediante **usbip**](http://channel9.msdn.com/Blogs/Open/On-premises-USB-devices-on-Linux-on-Azure-via-usbip)
4. [Vídeo: acceso a la interfaz gráfica de usuario basada en Linux en Azure en el explorador con fernapp](http://channel9.msdn.com/Blogs/Open/Accessing-Linux-based-GUI-on-Azure-over-browser-with-fernapp)
5. [Vídeo: almacenamiento compartido en Linux mediante la vista previa de archivos de Azure, parte 1](http://channel9.msdn.com/Blogs/Open/Shared-storage-on-Linux-via-Azure-Files-Preview-Part-1)
6. [Vídeo: adopción de dispositivos de Linux en Azure con el Bus de servicio y los sitios web](http://channel9.msdn.com/Blogs/Open/Embracing-Linux-devices-on-Azure-via-Service-Bus-and-Web-Sites)
7. [Vídeo: conexión de una aplicación nativa memcached basada en Linux con Windows Azure](http://channel9.msdn.com/Blogs/Open/Connecting-a-Linux-based-native-memcache-application-to-Windows-Azure)
8. [Vídeo: equilibrio de cargas de servicios de alta disponibilidad de Linux en Azure (OpenLDAP y MySQL)](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL)


### <a id='data'>Datos</a>

En esta sección se incluye información acerca de distintos enfoques y tecnologías de almacenamiento, entre las que se incluyen NoSQL, datos relacionales y Big Data.

#### Nosql

1. [Blog: 8 bases de datos NoSql de código abierto para Azure](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
2. Couchdb
- [Slideshare (MSOpenTech): experiencias con CouchDb en Azure](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
- [Blog: ejecución de CouchDB como servicio con node.js, CORS y Grunt](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)
3. MongoDB
- [Codificación Creación de una aplicación Node.js en Azure con MongoDB y el complemento de MongoLab](http://azure.microsoft.com/documentation/articles/store-mongolab-web-sites-nodejs-store-data-mongodb/)
4. Cassandra
- [Codificación Ejecución de Cassandra con Linux en Azure y acceso desde Node.js](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-nodejs-running-cassandra/)
5. Redis
- [Blog: Redis en Windows en el servicio de caché de Redis de Azure](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
- [Blog: anuncio del proveedor de estado de sesión de ASP.NET para la versión de vista previa de Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)
6. RavenHQ
- [Blog: RavenHQ ya está disponible en Azure Marketplace](http://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

#### Datos de gran tamaño
2. Hadoop/Cloudera  
	- [Blog: instalación de Hadoop en máquinas virtuales Linux de Azure](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
	- [Codificación Empezar a trabajar con Hadoop y Hive con HDInsight](http://azure.microsoft.com/documentation/articles/hdinsight-get-started/)  
3. [HDInsight de Azure](http://azure.microsoft.com/services/hdinsight/): un servicio Hadoop totalmente administrado en Azure.

#### Datos relacionales
2. MySQL
- [Codificación Instalación y ejecución de MySQL](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-mysql-use-opensuse/)
- [Codificación Optimización del rendimiento de MySQL en Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-optimize-mysql-perf/)
- [Codificación Clústeres de MySQL](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-mysql-cluster/)
- [Codificación creación de una base de datos de MySQL con Marketplace](http://azure.microsoft.com/documentation/articles/store-php-create-mysql-database/)
- [Codificación Django y MySQL en Sitios web Azure con Python y Visual Studio](http://azure.microsoft.com/documentation/articles/web-sites-python-ptvs-django-mysql/)
- [Codificación PHP y MySQL en Sitios web Azure con WebMatrix](http://azure.microsoft.com/documentation/articles/web-sites-php-mysql-use-webmatrix/)
7. MariaDB
- [Codificación creación de un clúster con varios maestros de MariaDbs](http://azure.microsoft.com/documentation/articles/virtual-machines-mariadb-cluster/)
7. PostgreSQL
- [Codificación Instalación de Ruby on Rails con Capistrano, Nginx, Unicorn y PostgreSQL](http://azure.microsoft.com/documentation/articles/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/)


### <a id='security'>Autenticación y cifrado</a>

La autenticación y el cifrado son aspectos cruciales del desarrollo de software, y hay una gran cantidad de temas en la web que describen cómo aprender y usar técnicas de seguridad adecuadas para ambos. Se describen aspectos del uso básico para estar en disposición de trabajar rápidamente con cargas de trabajo de código abierto y Linux, y también se indican las herramientas que se pueden utilizar para restablecer o quitar características de seguridad remota en Azure.

4. [Conceptos básicos: uso y administración de certificados](http://msdn.microsoft.com/library/azure/gg981929.aspx)
7. [Conceptos básicos: SSH](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/)
8. [Conceptos básicos: cómo restablecer una contraseña o las propiedades SSH de Linux](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-vmaccess-reset-password-or-ssh/)
9. [Conceptos básicos: uso de la raíz](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-root-privileges/)

### <a id='devops'>Devops, administración y optimización</a>

Esta sección comienza con una entrada de blog que contiene una serie de vídeos sobre [Vídeo: máquinas virtuales de Azure, uso de Chef, Puppet y Docker para administrar máquinas virtuales Linux](http://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/). Sin embargo, el mundo de devops, administración y optimización es bastante extenso y cambia muy rápidamente, por lo que debe tener en cuenta la lista situada más adelante, como punto de partida.

1. Docker
	- [Extensión de la máquina virtual de Docker para Linux en Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-vm-extension/)
	- [Uso de la extensión de la máquina virtual de Docker desde la interfaz de línea de comandos multiplataforma (xplat-cli) de Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/)
	- [Uso de la extensión de la máquina virtual de Docker desde el Portal de vista previa de Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/)
	- [Introducción rápida a Docker en Azure Marketplace](http://azure.microsoft.com/documentation/articles/virtual-machines-docker-ubuntu-quickstart/)
2. [Fleet con CoreOS](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-coreos-how-to/)
3. Deis
	- [Repositorio de GitHub:
Instalación de Deis en un clúster de CoreOS en Azure](https://github.com/chanezon/azure-linux/tree/master/coreos/deis)
4. Kubernetes
	- [Visualizador de Kubernetes](http://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure)
5. Jenkins y Hudson
	- [Blog: complemento esclavo de Jenkins para Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
	- [Repositorio de GitHub: complemento de almacenamiento de Jenkins para Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)
	- [Otros fabricantes: complemento esclavo de Hudson para Azure](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
	- [Otros fabricantes: complemento de almacenamiento de Hudson para Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
10. Chef
	- [Chef y máquinas virtuales](http://azure.microsoft.com/documentation/articles/virtual-machines-windows-install-chef-client/)
	- [Vídeo: ¿Qué es Chef y cómo funciona?](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)

12. Automatización de Azure
	- [Vídeo: Cómo utilizar la automatización de Azure con máquinas virtuales de Linux](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
13. PowerShell DSC para Linux
    - [Blog: cómo hacer DSC de Powershell para Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
    - [GitHub: DSC de cliente docker](https://github.com/anweiss/DockerClientDSC)
13. [Ubuntu Juju](https://juju.ubuntu.com/docs/config-azure.html)

### <a id='supportdebug'>Soporte técnico, solución de problemas y "Sencillamente no funciona"</a>

1. Documentación de soporte técnico de Microsoft
	- [Soporte técnico Compatibilidad con las imágenes de Linux en Microsoft Azure](http://support2.microsoft.com/kb/2941892)


<!--Anchors-->
[Distros]: #distros
[Conceptos básicos]: #basics
[Repositorios e imágenes de la comunidad]: #images
[Lenguajes y plataformas]: #langsandplats
[Ejemplos y scripts]: #samples
[Autenticación y cifrado]: #security
[Devops, administración y optimización]: #devops
[Soporte técnico, solución de problemas y "Sencillamente no funciona"]: #supportdebug

<!--HONumber=45--> 

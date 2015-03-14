<properties 
	pageTitle="Extensión de máquina virtual Docker para Linux en Azure" 
	description="Describe Docker y contenedores, las extensiones de máquinas virtuales de Azure y apunta a más recursos para crear contenedores de Docker tanto desde xplat-cli como desde el portal." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.tgt_pltfrm="vm-linux" 
	ms.workload="infrastructure-services" 
	ms.date="10/21/2014" 
	ms.author="rasquill"/>

# Extensión de máquina virtual Docker para Linux en Azure
[Docker](https://www.docker.com/) es uno de los enfoques de virtualización más populares que utilizan los [contenedores de Linux](http://en.wikipedia.org/wiki/LXC) en lugar de máquinas virtuales como una forma de aislar datos y calcular recursos compartidos. Puede usar la extensión de VM Docker para que el [agente Linux de Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-agent-user-guide/) cree una VM Docker que hospede cualquier número de contenedores para sus aplicaciones de Azure.

En este tema se describe:

+ [Contenedores Docker y Linux]
+ [Uso de la extensión de VM Docker con Azure]
+ [Extensiones de máquina virtual para Linux y Windows] 

Para crear máquinas virtuales con funcionalidad Docker ahora, consulte:

+ [Uso de la extensión de VM Docker desde la interfaz entre plataformas de Azure (xplat-cli)]
+ [Uso de la extensión de VM Docker con el portal de Azure]
+ [Cómo empezar a trabajar rápidamente con Docker en Azure Marketplace]

## <a id='Docker and Linux Containers'>Contenedores Docker y Linux</a>
[Docker](https://www.docker.com/) es uno de los enfoques de virtualización más populares que utilizan los [contenedores de Linux](http://en.wikipedia.org/wiki/LXC) en lugar de máquinas virtuales como una forma de aislar datos y calcular recursos compartidos y proporciona otros servicios que le permiten generar o ensamblar aplicaciones rápidamente y distribuirlas entre otros contenedores Docker.

Los contenedores Docker y Linux no son [Hipervisores](http://en.wikipedia.org/wiki/Hypervisor) como Windows Hyper-V y [KVM](http://www.linux-kvm.org/page/Main_Page) en Linux (existen muchos otros ejemplos). Los hipervisores virtualizan el sistema operativo subyacente para permitir a los sistemas operativos completos ejecutarse dentro del hipervisor como si fuesen una aplicación. 

Los enfoques de Docker y otros  *container* han reducido radicalmente tanto el tiempo y el procesamiento de inicio consumido como la sobrecarga necesaria al utilizar características de aislamiento de procesos y archivos del kernel de Linux para exponer únicamente características de kernel en un contenedor aislado de otro modo.

En la siguiente tabla se describe a muy alto nivel el tipo de diferencias de características que existen entre hipervisores y contenedores de Linux. Tenga en cuenta que algunas características pueden ser más o menos deseables en función de sus propias necesidades de aplicación.

|   Característica      | Hipervisores | Contenedores  |
| :------------- |-------------| ----------- |
| Aislamiento de procesos | Más o menos completa | Si se obtiene la raíz, podría verse comprometido el host contenedor |
| Memoria en disco necesaria | Completa las aplicaciones y sistemas operativos | Requisitos de aplicación solo |
| Tiempo necesario para iniciar | Mucho más tiempo: Arranque de SO más carga de la aplicación | Mucho menos tiempo: Solamente las aplicaciones necesitan iniciarse porque el kernel ya está ejecutándose |
| Automatización de contenedor | Varía mucho según el sistema operativo y las aplicaciones | [Galería de imágenes de Docker](https://registry.hub.docker.com/); otros 

Para ver un análisis de alto nivel de contenedores y sus ventajas, consulte [Documento de alto nivel de Docker](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).

Para obtener más información sobre qué es Docker y cómo funciona en realidad, consulte [¿Qué es Docker?](https://www.docker.com/whatisdocker/).

#### Práctica recomendadas de seguridad para contenedores de Docker y Linux

Debido a que los contenedores comparten acceso al kernel del equipo host, si hay código malintencionado capaz de obtener la raíz es posible que también pueda obtener acceso no solo al equipo host si no también a los otros contenedores. Para proteger su sistema de contenedores con más garantías de las que ofrece la configuración predeterminada, [Docker recomienda](https://docs.docker.com/articles/security/) el uso de directivas de grupo o de [seguridad basada en roles](http://en.wikipedia.org/wiki/Role-based_access_control) adicionales, como [SELinux](http://selinuxproject.org/page/Main_Page) o [AppArmor](http://wiki.apparmor.net/index.php/Main_Page), por ejemplo, además de reducir todo lo posible las capacidades de kernel que se otorgan al contenedor. Además, hay muchos otros documentos en Internet que describen enfoques de seguridad mediante el uso de contenedores como Docker.

## <a id='How to use the Docker VM Extension with Azure'>Uso de la extensión de VM Docker con Azure</a>

La extensión de VM Docker es un componente que se instala en la instancia de máquina virtual creada y que instala automáticamente el motor de Docker y administra la comunicación remota con la máquina virtual. Hay dos formas de instalar la extensión de VM: Puede crear su máquina virtual usando el portal de administración o crear la imagen desde la interfaz de línea de comandos entre plataformas de Azure (xplat-cli). 

Puede usar el portal para agregar la extensión de VM Docker a cualquier máquina virtual de Linux compatible (actualmente, la única imagen que lo admite es la imagen de Ubuntu 14.04 LTS posterior a julio). No obstante, mediante la línea de comandos xplat-cli, puede instalar la extensión de VM Docker y crear y cargar los certificados de comunicación de Docker simultáneamente cuando cree la instancia de máquina virtual.

Para crear máquinas virtuales con funcionalidad Docker ahora, consulte:

+ [Uso de la extensión de VM Docker desde la interfaz entre plataformas de Azure (xplat-cli)]
+ [Uso de la extensión de VM Docker con el portal de Azure]

## <a id='Virtual Machine Extensions for Linux and Windows'>Extensiones de máquina virtual para Linux y Windows</a>
La extensión de VM Docker para Azure es solo una de las varias extensiones de VM que ofrecen un comportamiento especial, hay aún más en desarrollo. Por ejemplo, varias de las características de la [extensión del agente VM de Linux](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-agent-user-guide/) le permiten modificar y gestionar la imagen, incluyendo características de seguridad, características de kernel y red, etc. La extensión VMAccess para las imágenes de Windows le permite restablecer o modificar la configuración de Acceso a Escritorio remoto y restablecer la contraseña del administrador. 

Para obtener una lista completa, consulte [Extensiones de VM de Azure](http://msdn.microsoft.com/library/azure/dn606311.aspx).

<!--Anchors-->
[Uso de la extensión de VM Docker desde la interfaz entre plataformas de Azure (xplat-cli)]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Uso de la extensión de VM Docker con el portal de Azure]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/
[Cómo empezar a trabajar rápidamente con Docker en Azure Marketplace]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-ubuntu-quickstart/
[Contenedores Docker y Linux]: #Docker-and-Linux-Containers
[Uso de la extensión de VM Docker con Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
[Extensiones de máquina virtual para Linux y Windows]: #Virtual-Machine-Extensions-For-Linux-and-Windows




<!--HONumber=42-->

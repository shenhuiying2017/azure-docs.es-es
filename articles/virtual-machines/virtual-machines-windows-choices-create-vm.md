<properties
	pageTitle="Diferentes formas de crear una máquina virtual de Windows"
	description="Enumera las distintas formas de crear una máquina virtual de Windows y proporciona vínculos a instrucciones."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="index-page"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="09/15/2015"
	ms.author="cynthn"/>

# Diferentes formas de crear una máquina virtual de Windows

Azure ofrece varias formas de crear una máquina virtual porque las máquinas virtuales son adecuadas para distintos usuarios y objetivos. Esto significa que tendrá tomar algunas decisiones sobre la máquina virtual y cómo crearla. Este artículo ofrece un resumen de estas opciones y vínculos a instrucciones.

Recientemente se han incluido plantillas del Administrador de recursos de Azure como una manera de crear y administrar una máquina virtual y sus distintos recursos como una sola unidad de implementación lógica. Las instrucciones de este enfoque se incluyen a continuación, cuando sea posible. Para obtener más información acerca del Administrador de recursos de Azure y cómo administrar los recursos como una sola unidad, vea la [Información general][].

## Opciones de herramienta

### Interfaz gráfica de usuario: Portal de Azure o Portal de vista previa de Azure

La interfaz gráfica de usuario del Portal de Azure es una manera fácil de probar una máquina virtual, especialmente si no tiene experiencia con Azure. Use el Portal de Azure o el Portal de vista previa de Azure para crear la máquina virtual:

[Creación de una máquina virtual que ejecuta Windows][]

### Shell de comandos: CLI de Azure o Azure PowerShell

Si prefiere trabajar en un shell de comandos, elija entre la interfaz de la línea de comandos (CLI) de Azure para usuarios de Mac y Linux o Azure PowerShell, que tiene los cmdlets de Windows PowerShell para Azure y una consola personalizada.

Para la CLI de Azure, vea [Comandos equivalentes del Administrador de recursos y de Administración de servicios para las operaciones de máquina virtual con la CLI de Azure para Mac, Linux y Windows][]. Para usar una plantilla, vea [Implementación y administración de máquinas virtuales con plantillas del Administrador de recursos de Azure y la CLI de Azure][].

Para Azure PowerShell, vea [Uso de PowerShell para crear y preconfigurar máquinas virtuales basadas en Windows][]. Para usar una plantilla, vea [Implementación y administración de máquinas virtuales con plantillas del Administrador de recursos de Azure y PowerShell][]. Para crear máquinas virtuales en la pila de Administración de servicios, vea [Uso de Azure PowerShell para crear y preconfigurar máquinas virtuales basadas en Windows][].

### Entorno de desarrollo: Visual Studio

[Creación de una máquina virtual para un sitio web con Visual Studio][]

[Implementación de recursos de Azure mediante bibliotecas .NET de proceso, red y almacenamiento][]

## Sistema operativo y opciones de imagen

Elija una imagen basada en el sistema operativo que desea ejecutar. Azure y sus socios ofrecen muchas imágenes, algunas de los cuales incluyen aplicaciones y herramientas. O bien, use una de sus propias imágenes.

### Imágenes de Azure

Estas instrucciones muestran cómo utilizar una imagen de Azure para crear una máquina virtual que se ha personalizado con las opciones de red, equilibrio de carga, etc. Vea [Creación de una máquina virtual personalizada que ejecute Windows][]

### Uso de su propia imagen

Para usar una imagen basada en una máquina virtual de Azure existente *capture* esa máquina virtual o cargue una imagen suya, almacenada en un disco duro virtual (VHD):

- [Cómo capturar una máquina virtual Windows para usarla como imagen][]
- [Crear y cargar un VHD de Windows Server a Azure][]

## Pasos siguientes

[Inicio de sesión en la máquina virtual][]

[Acoplamiento de un disco de datos][]

## Recursos adicionales
[Entorno de prueba de la configuración base][]

[Entornos de prueba de nube híbrida de Azure][]

<!-- LINKS -->
[Información general]: ../resource-group-overview.md

[Creación de una máquina virtual que ejecuta Windows]: virtual-machines-windows-tutorial.md

[Comandos equivalentes del Administrador de recursos y de Administración de servicios para las operaciones de máquina virtual con la CLI de Azure para Mac, Linux y Windows]: xplat-cli-azure-manage-vm-asm-arm.md
[Implementación y administración de máquinas virtuales con plantillas del Administrador de recursos de Azure y la CLI de Azure]: virtual-machines-deploy-rmtemplates-azure-cli.md
[Create and preconfigure a Windows virtual machine with Resource Manager and Azure PowerShell]: virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md
[Implementación y administración de máquinas virtuales con plantillas del Administrador de recursos de Azure y PowerShell]: virtual-machines-deploy-rmtemplates-powershell.md
[Uso de Azure PowerShell para crear y preconfigurar máquinas virtuales basadas en Windows]: virtual-machines-ps-create-preconfigure-windows-vms.md
[Uso de PowerShell para crear y preconfigurar máquinas virtuales basadas en Windows]: virtual-machines-ps-create-preconfigure-windows-vms.md
[Creación de una máquina virtual personalizada que ejecute Windows]: virtual-machines-windows-create-custom.md

[Cómo capturar una máquina virtual Windows para usarla como imagen]: virtual-machines-capture-image-windows-server.md

[Crear y cargar un VHD de Windows Server a Azure]: virtual-machines-create-upload-vhd-windows-server.md


[Creación de una máquina virtual para un sitio web con Visual Studio]: virtual-machines-dotnet-create-visual-studio-powershell.md
[Implementación de recursos de Azure mediante bibliotecas .NET de proceso, red y almacenamiento]: virtual-machines-arm-deployment.md

[Inicio de sesión en la máquina virtual]: virtual-machines-log-on-windows-server.md

[Acoplamiento de un disco de datos]: storage-windows-attach-disk.md

[Entorno de prueba de la configuración base]: virtual-machines-base-configuration-test-environment.md

[Entornos de prueba de nube híbrida de Azure]: virtual-machines-hybrid-cloud-test-environments.md

<!---HONumber=Oct15_HO3-->
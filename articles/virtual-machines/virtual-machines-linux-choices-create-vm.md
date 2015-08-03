<properties
	pageTitle="Diferentes formas de crear una máquina virtual Linux"
	description="Enumera las distintas formas de crear una máquina virtual Linux y proporciona vínculos a instrucciones."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""/>

<tags
ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="07/20/2015"
	ms.author="dkshir"/>

# Diferentes formas de crear una máquina virtual Linux

Azure ofrece varias formas de crear una máquina virtual porque las máquinas virtuales son adecuadas para distintos usuarios y objetivos. Esto significa que deberá tomar algunas decisiones acerca de la máquina virtual y cómo se creará. Este artículo ofrece un resumen de estas opciones y vínculos a instrucciones.

Recientemente se han incluido plantillas del Administrador de recursos de Azure como una manera de crear y administrar una máquina virtual y sus distintos recursos como una sola unidad de implementación lógica. Las instrucciones de este enfoque se incluyen a continuación, cuando sea posible. Para obtener más información acerca del Administrador de recursos de Azure y cómo administrar los recursos como una sola unidad, consulte la [Información general][].

## Opciones de herramienta

### Interfaz gráfica de usuario: El Portal de Azure o el Portal de vista previa

La interfaz gráfica de usuario del Portal de Azure es una manera fácil de probar una máquina virtual, especialmente si no tiene experiencia con Azure. Utilice el Portal de Azure o el Portal de vista previa de Azure para crear la máquina virtual. Para obtener instrucciones generales, consulte [Crear una máquina virtual personalizada][] y seleccione cualquier imagen de Linux desde la **Galería**.

### Shell de comandos: CLI de Azure o Azure PowerShell

Si prefiere trabajar en un shell de comandos, elija entre la interfaz de la línea de comandos (CLI) de Azure para usuarios de Mac y Linux o Azure PowerShell, que tiene los cmdlets de Windows PowerShell para Azure y una consola personalizada.

Para la CLI de Azure, consulte [Creación de una máquina virtual que ejecuta Linux][] Para utilizar una plantilla, consulte [Implementación y administración de máquinas virtuales con plantillas del Administrador de recursos de Azure y la CLI de Azure][].

Para Azure PowerShell, consulte [Uso de Azure PowerShell para crear y preconfigurar máquinas virtuales basadas en Linux][]. Para utilizar una plantilla, consulte [Implementación y administración de máquinas virtuales con plantillas del Administrador de recursos de Azure y PowerShell][].

### Entorno de desarrollo: Visual Studio

[Creación de una máquina virtual para un sitio web con Visual Studio][]

[Implementación de recursos de Azure mediante bibliotecas .NET de proceso, red y almacenamiento][]

## Sistema operativo y opciones de imagen

Elija una imagen basada en el sistema operativo que desea ejecutar. Azure y sus socios ofrecen muchas imágenes, algunas de los cuales incluyen aplicaciones y herramientas. O bien, use una de sus propias imágenes.

### Imágenes de Azure

Estas instrucciones muestran cómo utilizar una imagen de Azure para crear una máquina virtual que se ha personalizado con las opciones de red, equilibrio de carga, etc. Consulte [Creación de una máquina virtual personalizada que ejecuta Linux en Azure][]

### Uso de su propia imagen

Para usar una imagen basada en una máquina virtual de Azure existente *capture* esa máquina virtual o cargue una imagen suya, almacenada en un disco duro virtual (VHD):

- [Captura de una máquina virtual Linux para usar como plantilla con la CLI][]
- [Creación y carga de un disco duro virtual que contiene el sistema operativo Linux][]

## Pasos siguientes

[Inicio de sesión en la máquina virtual][]

[Acoplamiento de un disco de datos][]

## Recursos adicionales
[Acerca de los ajustes de configuración de máquinas virtuales en Azure][]

[Entorno de prueba de la configuración base][]

[Entornos de prueba de nube híbrida de Azure][]

[Comandos equivalentes del Administrador de recursos y de Administración de servicios para las operaciones de máquina virtual con la CLI de Azure para Mac, Linux y Windows][]

<!-- LINKS -->
[Información general]: ../resource-group-overview.md

[Create a Virtual Machine Running Windows]: virtual-machines-windows-tutorial.md
[Create a Virtual Machine Running Linux]: virtual-machines-linux-tutorial.md

[Comandos equivalentes del Administrador de recursos y de Administración de servicios para las operaciones de máquina virtual con la CLI de Azure para Mac, Linux y Windows]: xplat-cli-azure-manage-vm-asm-arm.md
[Implementación y administración de máquinas virtuales con plantillas del Administrador de recursos de Azure y la CLI de Azure]: virtual-machines-deploy-rmtemplates-azure-cli.md
[Implementación y administración de máquinas virtuales con plantillas del Administrador de recursos de Azure y PowerShell]: virtual-machines-deploy-rmtemplates-powershell.md
[Uso de Azure PowerShell para crear y preconfigurar máquinas virtuales basadas en Linux]: virtual-machines-ps-create-preconfigure-linux-vms.md

[Creación de una máquina virtual personalizada que ejecuta Linux en Azure]: virtual-machines-linux-create-custom.md
[Captura de una máquina virtual Linux para usar como plantilla con la CLI]: virtual-machines-linux-capture-image.md

[Creación y carga de un disco duro virtual que contiene el sistema operativo Linux]: virtual-machines-linux-create-upload-vhd.md

[Creación de una máquina virtual para un sitio web con Visual Studio]: virtual-machines-dotnet-create-visual-studio-powershell.md
[Implementación de recursos de Azure mediante bibliotecas .NET de proceso, red y almacenamiento]: virtual-machines-arm-deployment.md

[Inicio de sesión en la máquina virtual]: virtual-machines-linux-how-to-log-on.md

[Acoplamiento de un disco de datos]: virtual-machines-linux-how-to-attach-disk.md

[Acerca de los ajustes de configuración de máquinas virtuales en Azure]: http://msdn.microsoft.com/library/azure/dn763935.aspx
[Entorno de prueba de la configuración base]: virtual-machines-base-configuration-test-environment.md
[Entornos de prueba de nube híbrida de Azure]: virtual-machines-hybrid-cloud-test-environments.md

[Creación de una máquina virtual que ejecuta Linux]: virtual-machines-linux-tutorial.md
[Crear una máquina virtual personalizada]: virtual-machines-create-custom.md

<!---HONumber=July15_HO4-->
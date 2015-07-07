<properties
	pageTitle="Comandos equivalentes del Administrador de recursos y de Administración de servicios para las operaciones de máquina virtual con la CLI de Azure para Mac, Linux y Windows"
	description="Muestra los comandos equivalente de la CLI de Azure para crear y administrar máquinas virtuales de Azure en los modos Administrador de recursos y Administración de servicios"
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="command-line-interface"
	ms.workload="infrastructure-services"
	ms.date="04/28/2015"
	ms.author="danlep"/>


# Comandos equivalentes del Administrador de recursos y de Administración de servicios para las operaciones de máquina virtual con la CLI de Azure para Mac, Linux y Windows
Este artículo muestra los comandos equivalentes de la interfaz de línea de comandos de Azure (CLI de Azure) para crear y administrar máquinas virtuales de Azure en los modos Administración de servicios (asm) y Administrador de recursos (arm). Utilícelo como una guía para migrar los scripts de un modo de comando a otro.

* Si aún no ha instalado la CLI de Azure y se ha conectado a su suscripción, consulte [Instalación de la CLI de Azure](../xplat-cli-install.md) y [Conexión a una suscripción de Azure desde la CLI de Azure](../xplat-cli-connect.md). Cuando use los comandos del modo arm asegúrese de conectarse con el método de inicio de sesión.

* Para comenzar con el modo arm en los modos de comando de CLI de Azure y de modificador, consulte [Uso de la interfaz de la línea de comandos de Azure con el Administrador de recursos](xplat-cli-azure-resource-manager.md).

* Para obtener ayuda en pantalla sobre los conozco y conocer las distintas opciones, escriba `azure <command> <subcommand> --help` o `azure help <command> <subcommand>`.

## Escenarios
A continuación encontrará varias operaciones habituales de las máquinas virtuales que se pueden realiza con la CLI de Azure en los modos asm y arm. En el caso de muchos comandos del modo arm, es preciso agregar el nombre de un grupo de recursos existente.

> [AZURE.NOTE]Estos ejemplos no incluyen las operaciones basadas en plantilla del modo arm. Para obtener información, consulte [Uso de la interfaz de la línea de comandos de Azure con el Administrador de recursos](xplat-cli-azure-resource-manager.md).

Escenario | modo asm | modo arm
-------------- | ----------- | -------------------------
Creación de la máquina virtual más básica | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`
Creación de una máquina virtual Linux | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Linux"`
Creación de una máquina virtual Windows | `azure vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] <resource-group> <name> <location> -y "Windows"`
Enumeración de máquinas virtuales | `azure  vm list [options]` | `azure  vm list [options] <resource_group>`
Obtención información acerca de una máquina virtual | `azure  vm show [options] <vm_name>` | `azure  vm show [options] <resource_group> <name>`
Inicio de una máquina virtual | `azure vm start [options] <name>` | `azure vm start [options] <resource_group> <name>`
Detención de una máquina virtual | `azure vm shutdown [options] <name>` | `azure vm stop [options] <resource_group> <name>`
Reinicio de una máquina virtual | `azure vm restart [options] <vname>` | `azure vm restart [options] <resource_group> <name>`
Eliminación de una máquina virtual | `azure vm delete [options] <name>` | `azure vm delete [options] <resource_group> <name>`
Captura de una máquina virtual | `azure vm capture [options] <name>` | `azure vm capture [options] <resource_group> <name>`
Creación de una máquina virtual a partir de una imagen del usuario | `azure  vm create [options] <dns-name> <image> [userName] [password]` | `azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>`
Creación de una máquina virtual a partir de un disco especializado | `azure  vm create [options]-d <custom-data-file> <dns-name> [userName] [password]` | `azue  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>`
Incorporación de un disco de datos a una máquina virtual | `azure  vm disk attach [options] <vm-name> <disk-image-name>`, O BIEN <br/> `vm disk attach-new [options] <vm-name> <size-in-gb> [blob-url]` | `azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]`
Eliminación de un disco de datos de una máquina virtual | `azure  vm disk detach [options] <vm-name> <lun>` | `azure  vm disk detach [options] <resource-group> <vm-name> <lun>`
Incorporación de una extensión genérica a una máquina virtual | `azure  vm extension set [options] <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>`
Incorporación de la extensión de acceso a máquinas virtuales a una máquina virtual | no disponible | `azure vm reset-access [options] <resource-group> <name>`
Incorporación de la extensión de Docker a una máquina virtual | `azure  vm docker create [options] <dns-name> <image> <user-name> [password]` | `azure  vm docker create [options] <resource-group> <name> <location> <os-type>`
Incorporación de la extensión de Chef a una máquina virtual | `azure  vm extension get-chef [options] <vm-name>` | no disponible
Deshabilitación de una extensión de máquina virtual | `azure  vm extension set [options] –b <vm-name> <extension-name> <publisher-name> <version>` | no disponible
Eliminación de una extensión de máquina virtual | `azure  vm extension set [options] –u <vm-name> <extension-name> <publisher-name> <version>` | `azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>`
Lista de extensiones de máquina virtual | `azure vm extension list [options]` | `azure  vm extension get [options] <resource-group> <vm-name>`
Enumeración de imágenes de máquina virtual | `azure vm image list [options]` | `azure vm image list [options] <location> <publisher> [offer] [sku]`, O BIEN <br/> `azure vm image list-publishers [options] <location>`, O BIEN <br/> `azure vm image list-offers [options] <location>`, O BIEN <br/> `azure vm image list-skus [options] <location>`
Visualización de una imagen de máquina virtual | `azure vm image show [options]` | no disponible


## Pasos siguientes

* Para obtener más información acerca del uso de la CLI de Azure para trabajar con recursos en modo arm, consulte [Uso de la interfaz de la línea de comandos de Azure con el Administrador de recursos](xplat-cli-azure-resource-manager.md) y [Administración del control de acceso basado en rol con la interfaz de línea de comandos de Azure](../role-based-access-control-xplat-cli.md).
* Para obtener ejemplos adicionales de los comandos CLI, consulte [Uso de la interfaz de la línea de comandos de Azure](../virtual-machines-command-line-tools.md) y [Uso de la CLI de Azure con el Administrador de recursos de Azure](azure-cli-arm-commands.md).
 

<!---HONumber=62-->
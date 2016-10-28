Para poder usar la CLI de Azure con los comandos y plantillas de Resource Manager para implementar los recursos de Azure y las cargas de trabajo mediante grupos de recursos, necesitará una cuenta de Azure. Si no tiene una cuenta, puede obtener [aquí una evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

Si aún no ha instalado la CLI de Azure y se ha conectado a su suscripción, consulte [Instalación de la CLI de Azure](../articles/xplat-cli-install.md), establezca el modo en `arm` con `azure config mode arm` y conéctese a Azure con el comando `azure login`.

## Comandos básicos de Azure Resource Manager en la CLI de Azure

Este artículo trata los comandos básicos que querrá usar con la CLI de Azure para administrar e interactuar con los recursos de ARM (principalmente VM) en la suscripción de Azure. Para obtener más ayuda con las opciones y modificadores de línea de comandos específicos, puede utilizar las opciones y la ayuda en línea de comandos escribiendo `azure <command> <subcommand> --help` o `azure help <command> <subcommand>`.

> [AZURE.NOTE] En estos ejemplos no se incluyen las operaciones basadas en plantillas que se recomiendan generalmente para implementaciones de máquina virtual en el Administrador de recursos. Para información, vea [Usar la CLI de Azure con el Administrador de recursos de Azure](../articles/xplat-cli-azure-resource-manager.md) e [Implementación y administración de máquinas virtuales con plantillas del Administrador de recursos de Azure y CLI de Azure](../articles/virtual-machines/virtual-machines-linux-cli-deploy-templates.md).

Tarea | Resource Manager
-------------- | ----------- | -------------------------
Creación de la máquina virtual más básica | `azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`<br/><br/>(Obtenga `image-urn` desde el comando `azure vm image list`. Vea [este artículo](../articles/virtual-machines/virtual-machines-linux-cli-ps-findimage.md) para ejemplos.)
Creación de una máquina virtual Linux | `azure  vm create [options] <resource-group> <name> <location> -y "Linux"`
Creación de una máquina virtual Windows | `azure  vm create [options] <resource-group> <name> <location> -y "Windows"`
Enumeración de máquinas virtuales | `azure  vm list [options]`
Obtención información acerca de una máquina virtual | `azure  vm show [options] <resource_group> <name>`
Inicio de una máquina virtual | `azure vm start [options] <resource_group> <name>`
Detención de una máquina virtual | `azure vm stop [options] <resource_group> <name>`
Cancelación de la asignación de una máquina virtual | `azure vm deallocate [options] <resource-group> <name>`
Reinicio de una máquina virtual | `azure vm restart [options] <resource_group> <name>`
Eliminación de una máquina virtual | `azure vm delete [options] <resource_group> <name>`
Captura de una máquina virtual | `azure vm capture [options] <resource_group> <name>`
Creación de una máquina virtual a partir de una imagen del usuario | `azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>`
Creación de una máquina virtual a partir de un disco especializado | `azue  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>`
Incorporación de un disco de datos a una máquina virtual | `azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]`
Eliminación de un disco de datos de una máquina virtual | `azure  vm disk detach [options] <resource-group> <vm-name> <lun>`
Incorporación de una extensión genérica a una máquina virtual |`azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>`
Incorporación de la extensión de acceso a máquinas virtuales a una máquina virtual | `azure vm reset-access [options] <resource-group> <name>`
Incorporación de la extensión de Docker a una máquina virtual | `azure  vm docker create [options] <resource-group> <name> <location> <os-type>`
Eliminación de una extensión de máquina virtual | `azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>`
Obtención del uso de los recursos de una máquina virtual | `azure vm list-usage [options] <location>`
Obtención de todos los tamaños disponibles de la máquina virtual | `azure vm sizes [options]`


## Pasos siguientes

* Para obtener ejemplos adicionales de los comandos de la CLI que superan la administración de la VM básica, consulte [Uso de la CLI de Azure con Azure Resource Manager](../articles/virtual-machines/azure-cli-arm-commands.md).

<!---HONumber=AcomDC_0713_2016-->
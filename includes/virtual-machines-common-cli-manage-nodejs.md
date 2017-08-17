Para poder usar la CLI de Azure con los comandos y plantillas de Resource Manager para implementar los recursos de Azure y las cargas de trabajo mediante grupos de recursos, necesitará una cuenta de Azure. Si no tiene una cuenta, puede obtener [aquí una evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

Si no ha instalado la CLI de Azure y se ha conectado a su suscripción, consulte [Instalación de la CLI de Azure](../articles/cli-install-nodejs.md), establezca el modo en `arm` con `azure config mode arm`, y conéctese a Azure con el comando `azure login`.

## <a name="cli-versions-to-complete-the-task"></a>Versiones de la CLI para completar la tarea
Puede completar la tarea mediante una de las siguientes versiones de la CLI:

- CLI de Azure 10: la CLI para los modelos de implementación clásico y de administración de recursos (este artículo)
- [CLI de Azure 2.0](../articles/virtual-machines/linux/cli-manage.md): la CLI de última generación para el modelo de implementación de administración de recursos

## <a name="basic-azure-resource-manager-commands-in-azure-cli"></a>Comandos básicos de Azure Resource Manager en la CLI de Azure
Este artículo trata los comandos básicos que querrá usar con la CLI de Azure para administrar e interactuar con los recursos (principalmente máquinas virtuales) en la suscripción de Azure.  Para obtener información más detallada acerca de las opciones y los modificadores de línea de comandos específicos, puede utilizar las opciones y la ayuda en línea de comandos, para lo que debe escribir `azure <command> <subcommand> --help` o `azure help <command> <subcommand>`.

> [!NOTE]
> En estos ejemplos no se incluyen las operaciones basadas en plantillas que se recomiendan generalmente para implementaciones de máquina virtual en el Administrador de recursos. Para obtener información, consulte [Use the Azure CLI to manage Azure resources and resource groups](../articles/xplat-cli-azure-resource-manager.md) (Uso de la CLI de Azure para administrar recursos y grupos de recursos de Azure) e [Implementación y administración de máquinas virtuales con plantillas de Azure Resource Manager y la CLI de Azure](../articles/virtual-machines/linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

| Task | Resource Manager |
| --- | --- | --- |
| Creación de la máquina virtual más básica |`azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`<br/><br/>(Obtenga `image-urn` del comando `azure vm image list`. En [este artículo](../articles/virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) encontrará algunos ejemplos.) |
| Creación de una máquina virtual Linux |`azure  vm create [options] <resource-group> <name> <location> -y "Linux"` |
| Creación de una máquina virtual Windows |`azure  vm create [options] <resource-group> <name> <location> -y "Windows"` |
| Enumeración de máquinas virtuales |`azure  vm list [options]` |
| Obtención información acerca de una máquina virtual |`azure  vm show [options] <resource_group> <name>` |
| Inicio de una máquina virtual |`azure vm start [options] <resource_group> <name>` |
| Detención de una máquina virtual |`azure vm stop [options] <resource_group> <name>` |
| Cancelación de la asignación de una máquina virtual |`azure vm deallocate [options] <resource-group> <name>` |
| Reinicio de una máquina virtual |`azure vm restart [options] <resource_group> <name>` |
| Eliminación de una máquina virtual |`azure vm delete [options] <resource_group> <name>` |
| Captura de una máquina virtual |`azure vm capture [options] <resource_group> <name>` |
| Creación de una máquina virtual a partir de una imagen del usuario |`azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>` |
| Creación de una máquina virtual a partir de un disco especializado |`azue  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>` |
| Incorporación de un disco de datos a una máquina virtual |`azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]` |
| Eliminación de un disco de datos de una máquina virtual |`azure  vm disk detach [options] <resource-group> <vm-name> <lun>` |
| Incorporación de una extensión genérica a una máquina virtual |`azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>` |
| Incorporación de la extensión de acceso a máquinas virtuales a una máquina virtual |`azure vm reset-access [options] <resource-group> <name>` |
| Incorporación de la extensión de Docker a una máquina virtual |`azure  vm docker create [options] <resource-group> <name> <location> <os-type>` |
| Eliminación de una extensión de máquina virtual |`azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>` |
| Obtención del uso de los recursos de una máquina virtual |`azure vm list-usage [options] <location>` |
| Obtención de todos los tamaños disponibles de la máquina virtual |`azure vm sizes [options]` |

## <a name="next-steps"></a>Pasos siguientes
* Para obtener ejemplos adicionales de los comandos de la CLI que superan la administración de la VM básica, consulte [Uso de la CLI de Azure con Azure Resource Manager](../articles/virtual-machines/azure-cli-arm-commands.md).

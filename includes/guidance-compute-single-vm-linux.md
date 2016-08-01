En este artículo se describe un conjunto de procedimientos probados para ejecutar una máquina virtual (VM) Linux en Azure, teniendo en cuenta la escalabilidad, la disponibilidad, la manejabilidad y la seguridad. Azure admite la ejecución de un número de distribuciones Linux conocidas, como CentOS, Debian, Red Hat Enterprise, Ubuntu y FreeBSD. Para más información, consulte [Azure y Linux][azure-linux].

> [AZURE.NOTE] Azure cuenta con dos modelos de implementación diferentes: [Resource Manager][resource-manager-overview] y clásico. En este artículo se utiliza el Administrador de recursos, que Microsoft recomienda para las implementaciones nuevas.

Se recomienda utilizar una sola máquina virtual para cargas de trabajo de producción, porque no hay ningún SLA de tiempo de actividad para máquinas virtuales individuales en Azure. Para obtener el SLA, debe implementar varias máquinas virtuales en un conjunto de disponibilidad. Para más información, consulte [Running multiple VMs on Azure for scalability and availability][multi-vm] (Ejecución de varias máquinas virtuales en Azure de cara a una mayor escalabilidad y disponibilidad).

## Diagrama de la arquitectura

El aprovisionamiento de una máquina virtual en Azure implica más piezas en movimiento que la propia máquina virtual. Existen elementos de proceso, red y almacenamiento.

![[0]][0]

- **Grupo de recursos.** Un [grupo de recursos][resource-manager-overview] es un contenedor que incluye recursos relacionados. Cree un grupo de recursos para contener los recursos de esta máquina virtual.

- **Máquina virtual**. Puede aprovisionar una VM desde una lista de imágenes publicadas o desde un archivo de disco duro virtual cargado en Almacenamiento de blobs de Azure.

- **Disco del sistema operativo.** El disco del sistema operativo es un disco duro virtual almacenado en [Almacenamiento de Azure][azure-storage]. Esto significa que persiste incluso si el equipo host deja de funcionar. El disco del sistema operativo es `/dev/sda1`.

- **Disco temporal.** La máquina virtual se crea con un disco temporal. Este disco se almacena en una unidad física del equipo host. _No_se guarda en Almacenamiento de Azure y podría desaparecer durante los reinicios y otros eventos del ciclo de vida de la máquina virtual. Use este disco solo para datos temporales, como archivos de paginación o de intercambio. El disco temporal es `/dev/sdb1` y se monta en `/mnt/resource` o `/mnt`.

- **Discos de datos.** Un [disco de datos][data-disk] es un disco duro virtual persistente para los datos de la aplicación. Los discos de datos se almacenan en Almacenamiento de Azure, como el disco del sistema operativo.

- **Red virtual y subred.** Cada VM en Azure se implementa en una red virtual, que se divide en subredes.

- **Dirección IP pública.** Se necesita una dirección IP pública para comunicarse con la máquina virtual&mdash; por ejemplo, a través de ssh.

- **La interfaz de red (NIC)**. La NIC permite que la VM se comunique con la red virtual.

- **Grupo de seguridad de red (NSG)**. El [NSG][nsg] se utiliza para permitir o denegar el tráfico de red a la subred. Puede asociar un NSG a una NIC individual o a una subred. Si se asocia con una subred, las reglas NSG se aplican a todas las máquinas virtuales de esa subred.
 
- **Diagnóstico.** El registro de diagnóstico es fundamental para administrar y solucionar problemas de la VM.

## Recomendaciones

### Recomendaciones de VM

- Se recomienda la serie GS, a menos que tenga una carga de trabajo especializada, como puede ser el caso de la informática de alto rendimiento. Para más información, consulte los [tamaños de máquina virtual][virtual-machine-sizes]. Al mover una carga de trabajo existente a Azure, comience con el tamaño de máquina virtual que más se acerque a los servidores locales. Luego, mida el rendimiento de la carga de trabajo real con respecto a la CPU, la memoria y la IOPS de disco, y ajuste el tamaño, si es necesario. Además, si tiene varias tarjetas NIC, tenga en cuenta el límite de NIC para cada tamaño.

- Cuando aprovisiona la VM y otros recursos, debe especificar una ubicación. Por lo general, se recomienda elegir una ubicación más cercana a los usuarios internos o clientes. Sin embargo, no todos los tamaños de máquina virtual están disponibles en todas las ubicaciones. Para más información, consulte [Servicios por región][services-by-region]. Para enumerar los tamaños de máquina virtual disponibles en una ubicación dada, ejecute el siguiente comando de la CLI de Azure:

    ```
    azure vm sizes --location <location>
    ```

- Para más información sobre cómo elegir una imagen de máquina virtual publicada, consulte [Navegación y selección de las imágenes de máquina virtual Windows en Azure con Powershell o CLI][select-vm-image].

### Recomendaciones de discos y almacenamiento

- Para un mejor rendimiento de E/S de disco, se recomienda [almacenamiento Premium][premium-storage], que almacena los datos en unidades de estado sólido (SSD). El costo se basa en el tamaño del disco aprovisionado. Las E/S por segundo y el rendimiento (es decir, la velocidad de transferencia de datos) también dependen del tamaño del disco, por lo que al aprovisionar un disco, debería tener en cuenta los tres factores (capacidad, E/S por segundo y rendimiento).

- Agregue uno o más discos de datos. Cuando se crea un nuevo disco duro virtual, no tiene formato. Inicie sesión en la VM para dar formato al disco. Los discos de datos se mostrarán como `/dev/sdc`, `/dev/sdd` y así sucesivamente. Puede ejecutar `lsblk` para mostrar los dispositivos de bloques, lo que incluye los discos. Para utilizar un disco de datos, cree una partición y un sistema de archivos nuevos y monte el disco. Por ejemplo:

    ```bat
    # Create a partition.
    sudo fdisk /dev/sdc     # Enter 'n' to partition, 'w' to write the change.     
    
    # Create a file system.
    sudo mkfs -t ext3 /dev/sdc1
    
    # Mount the drive.
    sudo mkdir /data1
    sudo mount /dev/sdc1 /data1
    ```

- Si tiene un gran número de discos de datos, tenga en cuenta los límites de E/S totales de la cuenta de almacenamiento. Para más información, consulte [Límites de discos de máquinas virtuales][vm-disk-limits].

- Cuando agrega un disco de datos, se asigna un identificador de número de unidad lógica (LUN) al disco. Opcionalmente, puede especificar el id. de LUN &mdash; por ejemplo, si va a reemplazar un disco y desea conservar el mismo id. de LUN o si tiene una aplicación que busca un id. de LUN específico. Sin embargo, recuerde que los id. de LUN debe ser únicos para cada disco.

- Puede cambiar el programador de E/S para optimizar el rendimiento de las SSD (usadas por el almacenamiento Premium). Una recomendación habitual es utilizar el programador NOOP para las SSD, pero para ello debe usar una herramienta como [iostat] para supervisar el rendimiento de E/S de disco para su carga de trabajo en particular.

- Para obtener el mejor rendimiento, cree una cuenta de almacenamiento independiente para contener los registros de diagnóstico. Una cuenta de almacenamiento con redundancia local (LRS) estándar es suficiente para este tipo de registros.

### Recomendaciones de red

- La dirección IP pública puede ser dinámica o estática. El valor predeterminado es dinámica.

    - Reserve una [dirección IP estática][static-ip] si necesita una dirección IP fija que no cambie, por ejemplo, si tiene que crear un registro D en DNS o necesita que se permita la dirección IP.

    - También puede crear un nombre de dominio completo (FQDN) para la dirección IP. Luego, puede registrar un [registro CNAME][cname-record] en DNS que apunte al FQDN. Para más información, consulte [Crear un nombre de dominio completo en el Portal de Azure][fqdn].

- Todos los NSG contienen un conjunto de [reglas predeterminadas][nsg-default-rules], incluida una que bloquea todo el tráfico entrante de Internet. No se puede eliminar las reglas predeterminadas, pero otras reglas pueden reemplazarlas. Para permitir el tráfico de Internet, cree reglas que permitan el tráfico entrante a puertos específicos &mdash; por ejemplo, el puerto 80 para HTTP.

- Para habilitar ssh, agregue una regla al NSG que permita el tráfico entrante al puerto TCP 22.

## Consideraciones sobre escalabilidad

- Puede escalar y reducir verticalmente una máquina virtual [cambiando su tamaño][vm-resize].

- Para escalar horizontalmente, coloque dos o más máquinas virtuales en un conjunto de disponibilidad detrás de un equilibrador de carga. Para más información, consulte [Running multiple VMs on Azure for scalability and availability][multi-vm] (Ejecución de varias máquinas virtuales en Azure de cara a una mayor escalabilidad y disponibilidad).

## Consideraciones sobre disponibilidad

- Como se mencionó anteriormente, no hay ningún SLA para una sola máquina virtual. Para obtener el SLA, debe implementar varias máquinas virtuales en un conjunto de disponibilidad.

- La máquina virtual puede verse afectada por un [mantenimiento planeado][planned-maintenance] o un [mantenimiento no planeado][manage-vm-availability]. Puede usar [registros de reinicio de máquina virtual][reboot-logs] para determinar si se produjo un reinicio de la máquina virtual por mantenimiento planeado.

- Los discos duros virtuales están respaldados por [Almacenamiento de Azure][azure-storage], que se replica para su disponibilidad y durabilidad.

- Para protegerse de la pérdida accidental de datos durante las operaciones normales (por ejemplo, debido a errores de usuario), debe implementar igualmente copias de seguridad a un momento dado mediante [instantáneas de blobs][blob-snapshot] u otra herramienta.

## Consideraciones sobre la manejabilidad

- **Grupos de recursos.** Coloque los recursos estrechamente acoplados que comparten el mismo ciclo de vida en un mismo [grupo de recursos][resource-manager-overview]. Los grupos de recursos le permiten implementar y supervisar los recursos como un grupo, y acumular los costos de facturación por grupo de recursos. También se pueden eliminar recursos en conjunto, lo que resulta muy útil para implementaciones de prueba. Asigne a los recursos nombres descriptivos. De esta forma será más fácil encontrarlos y comprender su función. Consulte [Recommended naming conventions for Azure resources][naming conventions] (Convenciones de nomenclatura recomendadas para los recursos de Azure).

- **ssh**. Antes de crear una máquina virtual Linux, genere un par de clave pública y privada RSA de 2048 bits. Utilice el archivo de clave pública al crear la máquina virtual. Para más información, consulte [Uso de SSH con Linux y Mac en Azure][ssh-linux].

- **Diagnósticos de máquina virtual.** Habilite la supervisión y el diagnóstico, como las métricas básicas de estado, los registros de infraestructura de diagnóstico y los [diagnósticos de arranque][boot-diagnostics]. Los diagnósticos de arranque pueden ayudarle a diagnosticar errores de arranque si la máquina virtual entra en un estado de imposibilidad de arranque. Para más información, consulte [Habilitación de supervisión y diagnóstico][enable-monitoring].

    El siguiente comando CLI habilita los diagnósticos:

    ```text
    azure vm enable-diag <resource-group> <vm-name>
    ```

- **Detención de una máquina virtual.** Azure hace una distinción entre estados "Detenidos" y "Asignación anulada". Se le cobrará cuando el estado de la VM sea "Detenido". No se le cobrará cuando la asignación de la VM esté anulada.

    Utilice el siguiente comando de la CLI para anular la asignación de una VM:

    ```text
    azure vm deallocate <resource-group> <vm-name>
    ```

    El botón **Detener** en el Portal de Azure también desasigna la máquina virtual. Sin embargo, si apaga desde dentro del sistema operativo mientras tiene la sesión iniciada, la máquina virtual se detiene pero _no_ se anula su asignación, por lo que se le seguirá cobrando.

- **Eliminación de una máquina virtual.** Si elimina una VM, no se eliminarán los discos duros virtuales. Esto significa que puede eliminar de forma segura la VM sin perder datos. Sin embargo, se le seguirá cobrando por el almacenamiento. Para eliminar el disco duro virtual, elimine el archivo desde [Almacenamiento de blobs][blob-storage].

  Para evitar eliminaciones por error, use un [bloqueo de recurso][resource-lock] para bloquear el grupo de recursos completo o recursos individuales, como la máquina virtual.

## Consideraciones sobre la seguridad

- Automatice las actualizaciones del sistema operativo mediante la extensión de máquina virtual [OSPatching]. Instale esta extensión cuando aprovisione la máquina virtual. Puede especificar la frecuencia de instalación de revisiones y si después de la aplicación de revisiones será necesario reiniciar el sistema.

- Utilice el [control de acceso basado en rol][rbac] (RBAC) para controlar el acceso a los recursos de Azure que implementa. RBAC le permite asignar roles de autorización a los miembros de su equipo de DevOps. Por ejemplo, el rol de lector puede ver recursos de Azure pero no crearlos, administrarlos o eliminarlos. Algunos roles son específicos de un tipo de recurso de Azure determinado. Por ejemplo, el rol Colaborador de máquina virtual puede reiniciar o desasignar una máquina virtual, restablecer la contraseña de administrador, crear una nueva máquina virtual, etc. Otros [roles de RBAC integrados][rbac-roles] que pueden resultar útiles para esta arquitectura de referencia incluyen [Usuario del laboratorio de desarrollo y pruebas][rbac-devtest] y [Colaborador de la red][rbac-network]. Un usuario puede asignarse a varios roles, y es posible crear roles personalizados para una especificación aún más detallada de los permisos.

    > [AZURE.NOTE] RBAC no limita las acciones que puede realizar un usuario que ha iniciado sesión en una máquina virtual. Esos permisos están determinados por el tipo de cuenta en el sistema operativo invitado.

- Utilice los [registros de auditoría][audit-logs] para ver las acciones de aprovisionamiento y otros eventos de máquina virtual.

- Considere la posibilidad de usar el [cifrado de discos de Azure][disk-encryption] si necesita cifrar los discos de datos y del sistema operativo.

## Componentes de soluciones

<!-- TO BE UPDATED WHEN THE NEW TEMPLATES ARE AVAILABLE -->

El siguiente script por lotes ejecuta los comandos de la [CLI de Azure][azure-cli] para implementar una única instancia de máquina virtual, así como los recursos de red y almacenamiento relacionados, tal como se muestra en el diagrama anterior.

El script usa las convenciones de nomenclatura que se describen en [Recommended naming conventions for Azure resources][naming conventions] (Convenciones de nomenclatura recomendadas para recursos de Azure).

Para ejecutar el script:

1. Genere una clave de autenticación de RSA de 2048 bits.

        ssh-keygen -t rsa -b 2048

2. Pase el id. de suscripción de Azure y el nombre del archivo de clave pública como parámetros al script.

        ./azurecli-single-vm-sample.sh <subscription ID> ~/.ssh/id_rsa.pub

3. Cuando finalice el script, inicie sesión en la máquina virtual con ssh. Use la clave privada para la autenticación.

        ssh testuser@<app>-vm1.<location>.cloudapp.azure.com -i ~/.ssh/id_rsa

    donde `<app>` es el valor de la variable de script `APP_NAME` y `<location>` es el valor de la variable `LOCATION`.

```bat
#!/bin/bash

############################################################################
#script for generating infrastructure for single VM running linux          #
# of user choice. It creates azure resource group, storage account for VM  #
# vnet, subnets for VM, and NSG rule                                       #
# tags for main variables used                                             #
# ScriptCommandParameters                                                  #
# ScriptVars                                                               #
############################################################################

############################################################################
# User defined functions for single VM script                              #
# errhandle : handles errors via trap if any exception happens             #
# in the cli execution or if the user interrupts with CTRL+C               #
# allowing for fast interruption                                           #
############################################################################

# error handling or interruption via ctrl-c.
# line number and error code of executed command is passed to errhandle function

trap 'errhandle $LINENO $?' SIGINT ERR

errhandle()
{
  echo "Error or Interruption at line ${1} exit code ${2} "
  exit ${2}
}

###############################################################################
############################## End of user defined functions ##################
###############################################################################

# 2 paramaters are expected
# public key file needs to be generated using ssh-keygen

if [ $# -ne 2  ]
then
	echo  "Usage:  ${0}  subscription-id public-ssh-key-file"
	exit
fi

if [ ! -f $2  ]
then
	echo "Public Key file ${2} does not exist. please generate it"
	echo "ssh-keygen -t rsa -b 2048"
	exit
fi

# Explicitly set the subscription to avoid confusion as to which subscription
# is active/default
# ScriptCommandParameters
SUBSCRIPTION=$1
PUBLICKEYFILE=$2

# ScriptVars
LOCATION=eastus2
APP_NAME=app1
ENVIRONMENT=dev
USERNAME=testuser
VM_NAME="${APP_NAME}-vm1"
RESOURCE_GROUP="${APP_NAME}-${ENVIRONMENT}-rg"
IP_NAME="${APP_NAME}-pip"
NIC_NAME="${VM_NAME}-1nic"
NSG_NAME="${APP_NAME}-nsg"
SUBNET_NAME="${APP_NAME}-subnet"
VNET_NAME="${APP_NAME}-vnet"
VHD_STORAGE="${VM_NAME//-}st1"
DIAGNOSTICS_STORAGE="${VM_NAME//-}diag"

# Use the following command to get the list of URNs for RHEL, UBUNTU, and OPENSUSE:
# RHEL
# azure vm image list $LOCATION  redhat RHEL 7.2
# UBUNTU
# azure vm image list $LOCATION canonical ubuntuserver 14.04.3-LTS
# SUSE
# azure vm image $LOCATION  suse opensuse 13.2

LINUX_BASE_IMAGE=redhat:rhel:7.2:7.2.20160302

# For a list of VM sizes see: 
#   https://azure.microsoft.com/documentation/articles/virtual-machines-size-specs/
# To see the VM sizes available in a region:
# 	azure vm sizes --location <location>
VM_SIZE=Standard_DS1

# Set up the postfix variables attached to most CLI commands

POSTFIX="--resource-group ${RESOURCE_GROUP} --subscription ${SUBSCRIPTION}"

azure config mode arm

#::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
#Create resources

#Create the enclosing resource group
azure group create --name $RESOURCE_GROUP --location $LOCATION --subscription $SUBSCRIPTION

# Create the VNet
azure network vnet create --address-prefixes 172.17.0.0/16 --name $VNET_NAME \
--location $LOCATION $POSTFIX

#Create the network security group
azure network nsg create --name $NSG_NAME --location $LOCATION $POSTFIX

#Create the subnet
azure network vnet subnet create --vnet-name $VNET_NAME --address-prefix  "172.17.0.0/24" \
--name $SUBNET_NAME --network-security-group-name $NSG_NAME $POSTFIX

#Create the public IP address (dynamic)
azure network public-ip create --name $IP_NAME --domain-name-label $VM_NAME \
--location $LOCATION $POSTFIX

#Create the NIC
azure network nic create --public-ip-name $IP_NAME --subnet-name $SUBNET_NAME \
--subnet-vnet-name $VNET_NAME --name $NIC_NAME --location $LOCATION $POSTFIX

#Create the storage account for the OS VHD
azure storage account create --type PLRS --location $LOCATION $POSTFIX $VHD_STORAGE

#Create the storage account for diagnostics logs
azure storage account create --type LRS --location $LOCATION $POSTFIX $DIAGNOSTICS_STORAGE

#Create the VM
azure vm create --name $VM_NAME --os-type Linux --image-urn  $LINUX_BASE_IMAGE \
--vm-size $VM_SIZE --vnet-subnet-name $SUBNET_NAME --vnet-name $VNET_NAME \
--nic-name $NIC_NAME --storage-account-name $VHD_STORAGE \
--os-disk-vhd "${VM_NAME}-osdisk.vhd" --admin-username $USERNAME \
--ssh-publickey-file $PUBLICKEYFILE --boot-diagnostics-storage-uri \
"https://${DIAGNOSTICS_STORAGE}.blob.core.windows.net/" --location $LOCATION $POSTFIX

#Attach a data disk
azure vm disk attach-new --vm-name $VM_NAME --size-in-gb 128 --vhd-name data1.vhd \
--storage-account-name $VHD_STORAGE $POSTFIX

#Allow SSH
azure network nsg rule create --nsg-name $NSG_NAME --direction Inbound --protocol Tcp \
--destination-port-range 22  --source-port-range "*"  --priority 100 --access Allow \
SSHAllow $POSTFIX

#Install patching extension
PATCH_CONFIG='{"rebootAfterPatch":"RebootIfNeed","startTime":"3:00","dayOfWeek":"Sunday","category":"ImportantAndRecommended"}'
azure vm extension set --name OSPatchingForLinux --publisher-name Microsoft.OSTCExtensions \
--public-config $PATCH_CONFIG --vm-name $VM_NAME --version 2.0 $POSTFIX
```

## Pasos siguientes

Para que se aplique el [SLA para máquinas virtuales][vm-sla], debe implementar dos o más instancias en un conjunto de disponibilidad. Para más información, consulte [Running multiple VMs on Azure for scalability and availability][multi-vm] (Ejecución de varias máquinas virtuales en Azure de cara a una mayor escalabilidad y disponibilidad).

<!-- links -->

[arm-templates]: ../articles/virtual-machines/virtual-machines-linux-cli-deploy-templates.md
[audit-logs]: https://azure.microsoft.com/es-ES/blog/analyze-azure-audit-logs-in-powerbi-more/
[azure-cli]: ../articles/virtual-machines-command-line-tools.md
[azure-linux]: ../articles/virtual-machines/virtual-machines-linux-azure-overview.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/es-ES/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md
[disk-encryption]: ../articles/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/azure-portal/insights-how-to-use-diagnostics.md
[fqdn]: ../articles/virtual-machines/virtual-machines-linux-portal-create-fqdn.md
[iostat]: https://en.wikipedia.org/wiki/Iostat
[manage-vm-availability]: ../articles/virtual-machines/virtual-machines-linux-manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[OSPatching]: https://github.com/Azure/azure-linux-extensions/tree/master/OSPatching
[planned-maintenance]: ../articles/virtual-machines/virtual-machines-linux-planned-maintenance.md
[premium-storage]: ../articles/storage/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-lab-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/es-ES/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/es-ES/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/es-ES/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/resource-group-overview.md
[select-vm-image]: ../articles/virtual-machines/virtual-machines-linux-cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/es-ES/regions/#services
[ssh-linux]: ../articles/virtual-machines/virtual-machines-linux-ssh-from-linux.md
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]: ../articles/virtual-machines/virtual-machines-linux-sizes.md
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]: ../articles/virtual-machines/virtual-machines-linux-change-vm-size.md
[vm-sla]: https://azure.microsoft.com/es-ES/support/legal/sla/virtual-machines/v1_0/
[0]: ./media/guidance-blueprints/compute-single-vm.png "Arquitectura general de una VM de Azure"

<!---HONumber=AcomDC_0720_2016-->
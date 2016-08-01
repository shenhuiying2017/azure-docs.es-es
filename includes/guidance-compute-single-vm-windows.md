En este artículo se describe un conjunto de procedimientos probados para ejecutar una máquina virtual (VM) Windows en Azure, teniendo en cuenta la escalabilidad, disponibilidad, manejabilidad y seguridad.

> [AZURE.NOTE] Azure cuenta con dos modelos de implementación diferentes: [Resource Manager][resource-manager-overview] y clásico. En este artículo se utiliza el Administrador de recursos, que Microsoft recomienda para las implementaciones nuevas.

Se recomienda utilizar una sola máquina virtual para cargas de trabajo de producción, porque no hay ningún SLA de tiempo de actividad para máquinas virtuales individuales en Azure. Para obtener el SLA, debe implementar varias máquinas virtuales en un conjunto de disponibilidad. Para más información, consulte [Running multiple VMs on Azure for scalability and availability][multi-vm] (Ejecución de varias máquinas virtuales Windows en Azure de cara a una mayor escalabilidad y disponibilidad).

## Diagrama de la arquitectura

El aprovisionamiento de una máquina virtual en Azure implica más piezas en movimiento que la propia máquina virtual. Existen elementos de proceso, red y almacenamiento.

![[0]][0]

- **Grupo de recursos.** Un [grupo de recursos][resource-manager-overview] es un contenedor que incluye recursos relacionados. Cree un grupo de recursos para contener los recursos de esta máquina virtual.

- **Máquina virtual**. Puede aprovisionar una VM desde una lista de imágenes publicadas o desde un archivo de disco duro virtual cargado en Almacenamiento de blobs de Azure.

- **Disco del sistema operativo.** El disco del sistema operativo es un disco duro virtual almacenado en [Almacenamiento de Azure][azure-storage]. Esto significa que persiste incluso si el equipo host deja de funcionar.

- **Disco temporal.** La máquina virtual se crea con un disco temporal (la unidad `D:` de Windows). Este disco se almacena en una unidad física del equipo host. _No_ se guarda en Almacenamiento de Azure y podría desaparecer durante los reinicios y otros eventos del ciclo de vida de la máquina virtual. Use este disco solo para datos temporales, como archivos de paginación o de intercambio.

- **Discos de datos.** Un [disco de datos][data-disk] es un disco duro virtual persistente para los datos de la aplicación. Los discos de datos se almacenan en Almacenamiento de Azure, como el disco del sistema operativo.

- **Red virtual y subred.** Cada VM en Azure se implementa en una red virtual, que se divide en subredes.

- **Dirección IP pública.** Es necesaria una dirección IP pública para comunicarse con la VM, por ejemplo, a través de Escritorio remoto (RDP).

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

- Agregue uno o más discos de datos. Cuando se crea un nuevo disco duro virtual, no tiene formato. Inicie sesión en la VM para dar formato al disco.

- Si tiene un gran número de discos de datos, tenga en cuenta los límites de E/S totales de la cuenta de almacenamiento. Para más información, consulte [Límites de discos de máquinas virtuales][vm-disk-limits].

- Para obtener el mejor rendimiento, cree una cuenta de almacenamiento independiente para contener los registros de diagnóstico. Una cuenta de almacenamiento con redundancia local (LRS) estándar es suficiente para este tipo de registros.

- Cuando sea posible, instale las aplicaciones en un disco de datos, no en el disco del sistema operativo. Sin embargo, en el caso de algunas aplicaciones heredadas, podría ser necesario instalar componentes en la unidad C:. En ese caso, puede [cambiar el tamaño del disco del sistema operativo][resize-os-disk] con PowerShell.

### Recomendaciones de red

- La dirección IP pública puede ser dinámica o estática. El valor predeterminado es dinámica.

    - Reserve una [dirección IP estática][static-ip] si necesita una dirección IP fija que no cambie, por ejemplo, si tiene que crear un registro D en DNS o necesita que se permita la dirección IP.

    - También puede crear un nombre de dominio completo (FQDN) para la dirección IP. Luego, puede registrar un [registro CNAME][cname-record] en DNS que apunte al FQDN. Para más información, consulte [Crear un nombre de dominio completo en el Portal de Azure][fqdn].

- Todos los NSG contienen un conjunto de [reglas predeterminadas][nsg-default-rules], incluida una que bloquea todo el tráfico entrante de Internet. No se puede eliminar las reglas predeterminadas, pero otras reglas pueden reemplazarlas. Para permitir el tráfico de Internet, cree reglas que permitan el tráfico entrante a puertos específicos &mdash; por ejemplo, el puerto 80 para HTTP.

- Para habilitar RDP, agregue una regla de NSG que permita el tráfico entrante al puerto TCP 3389.

## Consideraciones sobre escalabilidad

- Puede escalar y reducir verticalmente una máquina virtual [cambiando su tamaño][vm-resize].

- Para escalar horizontalmente, coloque dos o más máquinas virtuales en un conjunto de disponibilidad detrás de un equilibrador de carga. Para más información, consulte [Running multiple VMs on Azure for scalability and availability][multi-vm] (Ejecución de varias máquinas virtuales Windows en Azure de cara a una mayor escalabilidad y disponibilidad).

## Consideraciones sobre disponibilidad

- Como se mencionó anteriormente, no hay ningún SLA para una sola máquina virtual. Para obtener el SLA, debe implementar varias máquinas virtuales en un conjunto de disponibilidad.

- La máquina virtual puede verse afectada por un [mantenimiento planeado][planned-maintenance] o un [mantenimiento no planeado][manage-vm-availability]. Puede usar [registros de reinicio de máquina virtual][reboot-logs] para determinar si se produjo un reinicio de la máquina virtual por mantenimiento planeado.

- Los discos duros virtuales están respaldados por [Almacenamiento de Azure][azure-storage], que se replica para su disponibilidad y durabilidad.

- Para protegerse de la pérdida accidental de datos durante las operaciones normales (por ejemplo, debido a errores de usuario), debe implementar igualmente copias de seguridad a un momento dado mediante [instantáneas de blobs][blob-snapshot] u otra herramienta.

## Consideraciones sobre la manejabilidad

- **Grupos de recursos.** Coloque los recursos estrechamente acoplados que comparten el mismo ciclo de vida en un mismo [grupo de recursos][resource-manager-overview]. Los grupos de recursos le permiten implementar y supervisar los recursos como un grupo, y acumular los costos de facturación por grupo de recursos. También se pueden eliminar recursos en conjunto, lo que resulta muy útil para implementaciones de prueba. Asigne a los recursos nombres descriptivos. De esta forma será más fácil encontrarlos y comprender su función. Consulte [Recommended naming conventions for Azure resources][naming conventions] (Convenciones de nomenclatura recomendadas para los recursos de Azure).

- **Diagnósticos de máquina virtual.** Habilite la supervisión y el diagnóstico, como las métricas básicas de estado, los registros de infraestructura de diagnóstico y los [diagnósticos de arranque][boot-diagnostics]. Los diagnósticos de arranque pueden ayudarle a diagnosticar errores de arranque si la máquina virtual entra en un estado de imposibilidad de arranque. Para más información, consulte [Habilitación de supervisión y diagnóstico][enable-monitoring]. Utilice la extensión [Colección de registros de Azure][log-collector] para recopilar registros de la plataforma Azure y cargarlos en Almacenamiento de Azure.

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

- Use el [Azure Security Center][security-center] para obtener una visión central del estado de la seguridad de sus recursos en Azure. El Centro de seguridad supervisa los posibles problemas de seguridad, como actualizaciones del sistema o antimalware, y proporciona una imagen completa del estado de seguridad de su implementación.

    - El Centro de seguridad se configura por cada suscripción de Azure. Habilite la recopilación de datos de seguridad tal como se describe en [Uso del Centro de seguridad].
    - Después de habilitar la colección, el Centro de seguridad busca automáticamente las máquinas virtuales creadas en esa suscripción.

- **Administración de revisiones.** Si está habilitada esta opción, el Centro de seguridad comprueba si faltan actualizaciones críticas y de seguridad. Utilice la [configuración de directiva de grupo][group-policy] en la máquina virtual para habilitar las actualizaciones automáticas del sistema.

- **Antimalware.** Si está habilitada esta opción, el Centro de seguridad comprueba si está instalado software antimalware. También puede utilizar el Centro de seguridad para instalar software antimalware desde el Portal de Azure.

- Utilice el [control de acceso basado en rol][rbac] (RBAC) para controlar el acceso a los recursos de Azure que implementa. RBAC le permite asignar roles de autorización a los miembros de su equipo de DevOps. Por ejemplo, el rol de lector puede ver recursos de Azure pero no crearlos, administrarlos o eliminarlos. Algunos roles son específicos de un tipo de recurso de Azure determinado. Por ejemplo, el rol Colaborador de máquina virtual puede reiniciar o desasignar una máquina virtual, restablecer la contraseña de administrador, crear una nueva máquina virtual, etc. Otros [roles de RBAC integrados][rbac-roles] que pueden resultar útiles para esta arquitectura de referencia son, por ejemplo, el de [usuario del laboratorio de desarrollo y pruebas][rbac-devtest] y el de [colaborador de la red][rbac-network]. Un usuario puede asignarse a varios roles, y es posible crear roles personalizados para una especificación aún más detallada de los permisos.

    > [AZURE.NOTE] RBAC no limita las acciones que puede realizar un usuario que ha iniciado sesión en una máquina virtual. Esos permisos están determinados por el tipo de cuenta en el sistema operativo invitado.

- Para restablecer la contraseña de administrador local, ejecute el comando `vm reset-access` de la CLI de Azure.

    ```text
    azure vm reset-access -u <user> -p <new-password> <resource-group> <vm-name>
    ```

- Utilice los [registros de auditoría][audit-logs] para ver las acciones de aprovisionamiento y otros eventos de máquina virtual.

- Considere la posibilidad de usar el [cifrado de discos de Azure][disk-encryption] si necesita cifrar los discos de datos y del sistema operativo.

## Componentes de soluciones

El siguiente script por lotes de Windows ejecuta los comandos de la [CLI de Azure][azure-cli] para implementar una única instancia de máquina virtual, así como los recursos de red y almacenamiento relacionados, tal como se muestra en el diagrama anterior.

El script usa las convenciones de nomenclatura que se describen en [Recommended naming conventions for Azure resources][naming conventions] (Convenciones de nomenclatura recomendadas para recursos de Azure).

```bat
ECHO OFF
SETLOCAL

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Set up variables for deploying resources to Azure.
:: Change these variables for your own deployment.

:: The APP_NAME variable must not exceed 4 characters in size.
:: If it does the 15 character size limitation of the VM name may be exceeded.

SET APP_NAME=app1
SET LOCATION=eastus2
SET ENVIRONMENT=dev
SET USERNAME=testuser


:: For Windows, use the following command to get the list of URNs:
:: azure vm image list %LOCATION% MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
SET WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

:: For a list of VM sizes see:
::   https://azure.microsoft.com/documentation/articles/virtual-machines-size-specs/
:: To see the VM sizes available in a region:
:: 	azure vm sizes --location <location>
SET VM_SIZE=Standard_DS1

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

IF "%2"=="" (
    ECHO Usage: %0 subscription-id admin-password
    EXIT /B
    )

:: Explicitly set the subscription to avoid confusion as to which subscription
:: is active/default
SET SUBSCRIPTION=%1
SET PASSWORD=%2

:: Set up the names of things using recommended conventions
SET RESOURCE_GROUP=%APP_NAME%-%ENVIRONMENT%-rg
SET VM_NAME=%APP_NAME%-vm0

SET IP_NAME=%APP_NAME%-pip
SET NIC_NAME=%VM_NAME%-0nic
SET NSG_NAME=%APP_NAME%-nsg
SET SUBNET_NAME=%APP_NAME%-subnet
SET VNET_NAME=%APP_NAME%-vnet
SET VHD_STORAGE=%VM_NAME:-=%st0
SET DIAGNOSTICS_STORAGE=%VM_NAME:-=%diag

:: Set up the postfix variables attached to most CLI commands
SET POSTFIX=--resource-group %RESOURCE_GROUP% --subscription %SUBSCRIPTION%

CALL azure config mode arm

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Create resources

:: Create the enclosing resource group
CALL azure group create --name %RESOURCE_GROUP% --location %LOCATION% ^
  --subscription %SUBSCRIPTION%

:: Create the VNet
CALL azure network vnet create --address-prefixes 172.17.0.0/16 ^
  --name %VNET_NAME% --location %LOCATION% %POSTFIX%

:: Create the network security group
CALL azure network nsg create --name %NSG_NAME% --location %LOCATION% %POSTFIX%

:: Create the subnet
CALL azure network vnet subnet create --vnet-name %VNET_NAME% --address-prefix ^
  172.17.0.0/24 --name %SUBNET_NAME% --network-security-group-name %NSG_NAME% ^
  %POSTFIX%

:: Create the public IP address (dynamic)
CALL azure network public-ip create --name %IP_NAME% --location %LOCATION% %POSTFIX%

:: Create the NIC
CALL azure network nic create --public-ip-name %IP_NAME% --subnet-name ^
  %SUBNET_NAME% --subnet-vnet-name %VNET_NAME%  --name %NIC_NAME% --location ^
  %LOCATION% %POSTFIX%

:: Create the storage account for the OS VHD
CALL azure storage account create --type PLRS --location %LOCATION% %POSTFIX% ^
  %VHD_STORAGE%

:: Create the storage account for diagnostics logs
CALL azure storage account create --type LRS --location %LOCATION% %POSTFIX% ^
  %DIAGNOSTICS_STORAGE%

:: Create the VM
CALL azure vm create --name %VM_NAME% --os-type Windows --image-urn ^
  %WINDOWS_BASE_IMAGE% --vm-size %VM_SIZE%   --vnet-subnet-name %SUBNET_NAME% ^
  --vnet-name %VNET_NAME% --nic-name %NIC_NAME% --storage-account-name ^
  %VHD_STORAGE% --os-disk-vhd "%VM_NAME%-osdisk.vhd" --admin-username ^
  "%USERNAME%" --admin-password "%PASSWORD%" --boot-diagnostics-storage-uri ^
  "https://%DIAGNOSTICS_STORAGE%.blob.core.windows.net/" --location %LOCATION% ^
  %POSTFIX%

:: Attach a data disk
CALL azure vm disk attach-new --vm-name %VM_NAME% --size-in-gb 128 --vhd-name ^
  data1.vhd --storage-account-name %VHD_STORAGE% %POSTFIX%

:: Allow RDP
CALL azure network nsg rule create --nsg-name %NSG_NAME% --direction Inbound ^
  --protocol Tcp --destination-port-range 3389 --source-port-range * ^
  --priority 100 --access Allow RDPAllow %POSTFIX%
```


## Pasos siguientes

Para que se aplique el [SLA para máquinas virtuales][vm-sla] debe implementar dos o más instancias en un conjunto de disponibilidad. Para más información, consulte [Running multiple VMs on Azure for scalability and availability][multi-vm] (Ejecución de varias máquinas virtuales Windows en Azure de cara a una mayor escalabilidad y disponibilidad).

<!-- links -->

[arm-templates]: ../articles/virtual-machines/virtual-machines-windows-cli-deploy-templates.md
[audit-logs]: https://azure.microsoft.com/es-ES/blog/analyze-azure-audit-logs-in-powerbi-more/
[azure-cli]: ../articles/virtual-machines-command-line-tools.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/es-ES/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/virtual-machines/virtual-machines-windows-about-disks-vhds.md
[disk-encryption]: ../articles/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/azure-portal/insights-how-to-use-diagnostics.md
[fqdn]: ../articles/virtual-machines/virtual-machines-windows-portal-create-fqdn.md
[group-policy]: https://technet.microsoft.com/es-ES/library/dn595129.aspx
[log-collector]: https://azure.microsoft.com/es-ES/blog/simplifying-virtual-machine-troubleshooting-using-azure-log-collector/
[manage-vm-availability]: ../articles/virtual-machines/virtual-machines-windows-manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[planned-maintenance]: ../articles/virtual-machines/virtual-machines-windows-planned-maintenance.md
[premium-storage]: ../articles/storage/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-lab-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/es-ES/blog/viewing-vm-reboot-logs/
[resize-os-disk]: ../articles/virtual-machines/virtual-machines-windows-expand-os-disk.md
[Resize-VHD]: https://technet.microsoft.com/es-ES/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/es-ES/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/resource-group-overview.md
[security-center]: https://azure.microsoft.com/es-ES/services/security-center/
[select-vm-image]: ../articles/virtual-machines/virtual-machines-windows-cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/es-ES/regions/#services
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[Uso del Centro de seguridad]: ../articles/security-center/security-center-get-started.md#use-security-center
[virtual-machine-sizes]: ../articles/virtual-machines/virtual-machines-windows-sizes.md
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]: ../articles/virtual-machines/virtual-machines-linux-change-vm-size.md
[vm-sla]: https://azure.microsoft.com/es-ES/support/legal/sla/virtual-machines/v1_0/
[0]: ./media/guidance-blueprints/compute-single-vm.png "Arquitectura general de una VM de Azure"

<!---HONumber=AcomDC_0720_2016-->
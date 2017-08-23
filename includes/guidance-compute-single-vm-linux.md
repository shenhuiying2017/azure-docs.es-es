En este artículo se describe un conjunto de procedimientos probados para ejecutar una máquina virtual (VM) Linux en Azure, teniendo en cuenta la escalabilidad, la disponibilidad, la manejabilidad y la seguridad. Azure admite la ejecución de varias distribuciones Linux conocidas, como CentOS, Debian, Red Hat Enterprise, Ubuntu y FreeBSD. Para más información, consulte [Azure y Linux][azure-linux].

> [!NOTE]
> Azure cuenta con dos modelos de implementación diferentes: [Resource Manager][resource-manager-overview] y clásico. En este artículo se utiliza el Administrador de recursos, que Microsoft recomienda para las implementaciones nuevas.
> 
> 

No se recomienda usar una sola máquina virtual para cargas de trabajo críticas, ya que se crea un único punto de error. Para una mayor disponibilidad, implemente varias máquinas virtuales en un [conjunto de disponibilidad][availability-set]. Para más información, consulte el artículo sobre la [ejecución de varias máquinas virtuales en Azure][multi-vm]. 

## <a name="architecture-diagram"></a>Diagrama de la arquitectura
El aprovisionamiento de una máquina virtual en Azure implica más piezas en movimiento que la propia máquina virtual. Existen elementos de proceso, red y almacenamiento que debe tener en cuenta.

> Se puede descargar un documento de Visio que incluye este diagrama de arquitectura, y que está disponible en el [Centro de descarga de Microsoft][visio-download]. Este diagrama está en la página "Compute - single VM".
> 
> 

![[0]][0]

* **Grupo de recursos.** Un [*grupo de recursos*][resource-manager-overview] es un contenedor que incluye recursos relacionados. Cree un grupo de recursos para contener los recursos de esta máquina virtual.
* **Máquina virtual**. Puede aprovisionar una máquina virtual desde una lista de imágenes publicadas o desde un archivo de disco duro virtual (VHD) cargado en Azure Blob Storage.
* **Disco del sistema operativo.** El disco del sistema operativo es un VHD almacenado en [Azure Storage][azure-storage]. Esto significa que persiste incluso si el equipo host deja de funcionar. El disco del sistema operativo es `/dev/sda1`.
* **Disco temporal.** La máquina virtual se crea con un disco temporal. Este disco se almacena en una unidad física del equipo host. *No* se guarda en Azure Storage y podría desaparecer durante los reinicios y otros eventos del ciclo de vida de la máquina virtual. Use este disco solo para datos temporales, como archivos de paginación o de intercambio. El disco temporal es `/dev/sdb1` y se monta en `/mnt/resource` o `/mnt`.
* **Discos de datos.** Un [disco de datos][data-disk] es un VHD persistente usado para los datos de la aplicación. Los discos de datos se almacenan en Azure Storage, como el disco del sistema operativo.
* **Red virtual y subred.** Cada máquina virtual de Azure se implementa en una red virtual, que se divide a su vez en subredes.
* **Dirección IP pública.** Se necesita una dirección IP pública para comunicarse con la máquina virtual&mdash;; por ejemplo, a través de SSH.
* **Interfaz de red (NIC)**. La NIC permite que la VM se comunique con la red virtual.
* **Grupo de seguridad de red (NSG)**. El [NSG][nsg] se usa para permitir o denegar el tráfico de red a la subred. Puede asociar un NSG a una NIC individual o a una subred. Si se asocia con una subred, las reglas NSG se aplican a todas las máquinas virtuales de esa subred.
* **Diagnóstico.** El registro de diagnóstico es fundamental para administrar y solucionar problemas de la VM.

## <a name="recommendations"></a>Recomendaciones

Las siguientes recomendaciones sirven para la mayoría de los escenarios. Sígalas a menos que tenga un requisito concreto que las invalide. 

### <a name="vm-recommendations"></a>Recomendaciones de VM

Azure ofrece muchos tamaños de máquinas virtuales, pero se recomiendan las series DS y GS, ya que estos tamaños admiten [Premium Storage][premium-storage]. Seleccione uno de estos tamaños de máquina a menos que tenga una carga de trabajo especializada, como puede ser el caso de la informática de alto rendimiento. Para más información, consulte el artículo sobre [tamaños de máquinas virtuales][virtual-machine-sizes].

Si desplaza una carga de trabajo existente a Azure, comience con el tamaño de máquina virtual que más se parezca a los servidores locales. Luego, mida el rendimiento de la carga de trabajo real con respecto a la CPU, la memoria y las operaciones de entrada/salida por segundo (IOPS) de disco, y ajuste el tamaño, si es necesario. Si necesita varias NIC para la máquina virtual, tenga en cuenta que su número máximo es una función del [tamaño de máquinas virtuales][vm-size-tables].

Cuando aprovisiona la máquina virtual y otros recursos, debe especificar una región. Por lo general, se recomienda elegir una región más cercana a los usuarios internos o clientes. Sin embargo, no todos los tamaños de máquina virtual están disponibles en toda la región. Para más información, consulte los [servicios por región][services-by-region]. Para enumerar los tamaños de VM disponibles en una región dada, ejecute el siguiente comando de la interfaz de la línea de comandos (CLI) de Azure:

```
azure vm sizes --location <location>
```

Para más información sobre cómo elegir una imagen de VM publicada, consulte [Selección de imágenes de máquinas virtuales con la CLI de Azure][select-vm-image].

### <a name="disk-and-storage-recommendations"></a>Recomendaciones de discos y almacenamiento

Para un mejor rendimiento de la E/S de disco, se recomienda [Premium Storage][premium-storage], que almacena los datos en unidades de estado sólido (SSD). El costo se basa en el tamaño del disco aprovisionado. Las E/S por segundo y el rendimiento (es decir, la velocidad de transferencia de datos), también dependen del tamaño del disco, por lo que al aprovisionar un disco, debería tener en cuenta los tres factores (capacidad, E/S por segundo y rendimiento). 

Cree cuentas de almacenamiento de Azure distintas para cada máquina virtual para almacenar los discos duros virtuales (VHD) con el fin de evitar alcanzar los límites de E/S por segundo para cuentas de almacenamiento. 

Agregue uno o más discos de datos. Cuando se crea un disco duro virtual, no tiene formato. Inicie sesión en la máquina virtual para dar formato al disco. En el shell de Linux, se muestran los discos de datos como `/dev/sdc`, `/dev/sdd`, y así sucesivamente. Puede ejecutar `lsblk` para mostrar los dispositivos de bloques, lo que incluye los discos. Para utilizar un disco de datos, cree una partición y un sistema de archivos y monte el disco. Por ejemplo:

```bat
# Create a partition.
sudo fdisk /dev/sdc     # Enter 'n' to partition, 'w' to write the change.     

# Create a file system.
sudo mkfs -t ext3 /dev/sdc1

# Mount the drive.
sudo mkdir /data1
sudo mount /dev/sdc1 /data1
```

Si tiene un gran número de discos de datos, tenga en cuenta los límites de E/S totales de la cuenta de almacenamiento. Para más información, consulte [Límites de discos de máquinas virtuales][vm-disk-limits].

Cuando agrega un disco de datos, se asigna un identificador de número de unidad lógica (LUN) al disco. Opcionalmente, puede especificar el id. de LUN&mdash;; por ejemplo, si va a reemplazar un disco y desea conservar el mismo id. de LUN o si tiene una aplicación que busca un id. de LUN específico. Sin embargo, recuerde que los id. de LUN debe ser únicos para cada disco.

Puede cambiar el programador de E/S para optimizar el rendimiento de las SSD, ya que los discos de las máquinas virtuales con cuentas de almacenamiento premium son SSD. Una recomendación habitual es utilizar el programador NOOP para las SSD, pero para ello debe usar una herramienta como [iostat] para supervisar el rendimiento de E/S de disco para su carga de trabajo en particular.

Para obtener el mejor rendimiento, cree una cuenta de almacenamiento independiente para contener los registros de diagnóstico. Una cuenta de almacenamiento con redundancia local (LRS) estándar es suficiente para este tipo de registros.

### <a name="network-recommendations"></a>Recomendaciones de red

La dirección IP pública puede ser dinámica o estática. El valor predeterminado es dinámica.

* Reserve una [dirección IP estática][static-ip] si necesita una dirección IP fija que no cambie; por ejemplo, si tiene que crear un registro D en DNS o necesita que la dirección IP se agregue a una lista segura.
* También puede crear un nombre de dominio completo (FQDN) para la dirección IP. Después, puede registrar un [registro CNAME][cname-record] en DNS que apunte al nombre de dominio completo. Para más información, consulte [Crear un nombre de dominio completo en Azure Portal][fqdn].

Todos los NSG contienen un conjunto de [reglas predeterminadas][nsg-default-rules], incluida una que bloquea todo el tráfico de entrada de Internet. No se puede eliminar las reglas predeterminadas, pero otras reglas pueden reemplazarlas. Para permitir el tráfico de Internet, cree reglas que permitan el tráfico entrante a puertos específicos; por ejemplo, el puerto 80 para HTTP.  

Para habilitar SSH, agregue una regla al NSG que permita el tráfico entrante al puerto TCP 22.

## <a name="scalability-considerations"></a>Consideraciones sobre escalabilidad

Para escalar o reducir verticalmente, [cambie el tamaño de la máquina virtual][vm-resize]. 

Para escalar horizontalmente, coloque dos o más máquinas virtuales en un conjunto de disponibilidad detrás de un equilibrador de carga. Para más información, consulte [Running multiple VMs on Azure for scalability and availability][multi-vm] (Ejecución de varias máquinas virtuales en Azure de cara a una mayor escalabilidad y disponibilidad).

## <a name="availability-considerations"></a>Consideraciones sobre disponibilidad

Para una mayor disponibilidad, implemente varias máquinas virtuales en un conjunto de disponibilidad. Este procedimiento también ofrece un [Acuerdo de Nivel de Servicio][vm-sla] (SLA) superior. 

La máquina virtual puede verse afectada por un [mantenimiento planeado][planned-maintenance] o un [mantenimiento no planeado][manage-vm-availability]. Puede usar [registros de reinicio de máquina virtual][reboot-logs] para determinar si se produjo un reinicio de la máquina virtual por un mantenimiento planeado.

Los VHD se almacenan en [Azure Storage][azure-storage], que se replica para su disponibilidad y durabilidad.

Para protegerse de la pérdida accidental de datos durante las operaciones normales (por ejemplo, debido a errores de usuario), debe implementar también copias de seguridad de un momento dado mediante [instantáneas de blobs][blob-snapshot] u otra herramienta.

## <a name="manageability-considerations"></a>Consideraciones sobre la manejabilidad

**Grupos de recursos.** Coloque los recursos estrechamente acoplados que comparten el mismo ciclo de vida en un mismo [grupo de recursos][resource-manager-overview]. Los grupos de recursos le permiten implementar y supervisar los recursos como un grupo, y acumular los costos de facturación por grupo de recursos. También se pueden eliminar recursos en conjunto, lo que resulta muy útil para implementaciones de prueba. Asigne a los recursos nombres descriptivos. De esta forma será más fácil encontrarlos y comprender su función. Consulte [Recommended Naming Conventions for Azure Resources][naming conventions] (Convenciones de nomenclatura recomendadas para los recursos de Azure).

**SSH**. Antes de crear una máquina virtual Linux, genere un par de clave pública y privada RSA de 2048 bits. Utilice el archivo de clave pública al crear la máquina virtual. Para más información, consulte [Uso de SSH con Linux y Mac en Azure][ssh-linux].

**Diagnósticos de máquina virtual.** Habilite la supervisión y el diagnóstico, como las métricas básicas de estado, los registros de infraestructura de diagnóstico y los [diagnósticos de arranque][boot-diagnostics]. Los diagnósticos de arranque pueden ayudarle a diagnosticar un error de arranque si la máquina virtual entra en un estado de imposibilidad de arranque. Para más información, consulte [Habilitación de supervisión y diagnóstico][enable-monitoring].  

El siguiente comando CLI habilita los diagnósticos:

```
azure vm enable-diag <resource-group> <vm-name>
```

**Detención de una máquina virtual.** Azure hace una distinción entre los estados "Detenido" y "Desasignado". Se le cobra cuando el estado de la máquina virtual se detiene, pero no cuando se desasigna la máquina virtual.

Utilice el siguiente comando de la CLI para desasignar una máquina virtual:

```
azure vm deallocate <resource-group> <vm-name>
```

En Azure Portal, con el botón **Detener**, se desasigna la máquina virtual. Sin embargo, si apaga desde dentro del sistema operativo mientras tiene la sesión iniciada, la VM se detiene pero *no* se desasigna, por lo que se le seguirá cobrando.

**Eliminación de una máquina virtual.** Si elimina una VM, no se eliminarán los discos duros virtuales. Esto significa que puede eliminar de forma segura la VM sin perder datos. Sin embargo, se le seguirá cobrando por el almacenamiento. Para eliminar el VHD, elimine el archivo de [Blob Storage][blob-storage].

Para evitar eliminaciones por error, use un [bloqueo de recurso][resource-lock] para bloquear el grupo de recursos completo o recursos individuales, como la máquina virtual. 

## <a name="security-considerations"></a>Consideraciones sobre la seguridad

Automatice las actualizaciones del sistema operativo mediante la extensión de máquina virtual [OSPatching] . Instale esta extensión cuando aprovisione la máquina virtual. Puede especificar la frecuencia de instalación de revisiones y si después de la aplicación de revisiones será necesario reiniciar el sistema.

Use el [control de acceso basado en rol][rbac] (RBAC) para controlar el acceso a los recursos de Azure que implementa. RBAC le permite asignar roles de autorización a los miembros de su equipo de DevOps. Por ejemplo, el rol de lector puede ver recursos de Azure pero no crearlos, administrarlos o eliminarlos. Algunos roles son específicos de un tipo de recurso de Azure determinado. Por ejemplo, el rol Colaborador de máquina virtual puede reiniciar o desasignar una máquina virtual, restablecer la contraseña de administrador, crear una máquina virtual, etc. Otros [roles de RBAC integrados][rbac-roles] que pueden resultar útiles para esta arquitectura de referencia son, por ejemplo, el de [Usuario de DevTest Lab][rbac-devtest] y el de [Colaborador de la red][rbac-network]. 

Un usuario puede asignarse a varios roles, y es posible crear roles personalizados para una especificación aún más detallada de los permisos.

d> [!NOTE]
> RBAC no limita las acciones que puede realizar un usuario que ha iniciado sesión en una máquina virtual. Esos permisos están determinados por el tipo de cuenta en el sistema operativo invitado.   
> 
> 

Use los [registros de auditoría][audit-logs] para ver las acciones de aprovisionamiento y otros eventos de la máquina virtual.

Considere la posibilidad de usar [Azure Disk Encryption][disk-encryption] si necesita cifrar los discos de datos y del sistema operativo. 

## <a name="solution-deployment"></a>Implementación de la solución
Hay disponible una implementación de esta arquitectura de referencia en [GitHub][github-folder]. Incluye una red virtual, un grupo de seguridad de red y una única máquina virtual. Para implementar la arquitectura, siga estos pasos: 

1. Haga clic con el botón derecho en el botón siguiente y seleccione "Abrir vínculo en una nueva pestaña" o "Abrir vínculo en una nueva ventana".
   [![Implementación en Azure](../articles/guidance/media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-single-vm%2Fazuredeploy.json)
2. Una vez abierto el vínculo en Azure Portal, debe especificar los valores de algunas de las opciones: 
   
   * El nombre del **Grupo de recursos** ya está definido en el archivo de parámetros, así que seleccione **Crear nuevo** y escriba `ra-single-vm-rg` en el cuadro de texto.
   * Seleccione la región en el cuadro de lista desplegable **Ubicación**.
   * No modifique los cuadros de texto **URI raíz de plantilla** o **URI raíz de parámetro**.
.   * Seleccione **Linux** en el cuadro de lista desplegable **Tipo de OS**.
   * Revise los términos y condiciones, y haga clic en la casilla **Acepto los términos y condiciones indicados anteriormente**.
   * Haga clic en el botón **Comprar**.
3. Espere a que la implementación se complete.
4. Los archivos de parámetros incluyen un nombre de usuario y una contraseña de administrador codificados de forma rígida, y es muy recomendable cambiar ambos inmediatamente. Haga clic en la máquina virtual llamada `ra-single-vm0 ` en Azure Portal. A continuación, haga clic en **Restablecer contraseña** en la sección **Soporte técnico y solución de problemas**. Seleccione **Restablecer contraseña** en el cuadro de lista desplegable **Modo**, seleccione un **Nombre de usuario** y una **Contraseña**. Haga clic en el botón **Actualizar** para conservar el nuevo nombre de usuario y contraseña.

## <a name="next-steps"></a>Pasos siguientes
Para una mayor disponibilidad, implemente dos o más máquinas virtuales detrás de un equilibrador de carga. Para más información, consulte el artículo sobre la [ejecución de varias máquinas virtuales en Azure][multi-vm].

<!-- links -->

[audit-logs]: https://azure.microsoft.com/en-us/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]:../articles/virtual-machines/windows/create-availability-set.md
[azure-cli]: /cli/azure/get-started-with-az-cli2
[azure-linux]:../articles/virtual-machines/linux/overview.md
[azure-storage]:../articles/storage/common/storage-introduction.md
[blob-snapshot]:../articles/storage/blobs/storage-blob-snapshots.md
[blob-storage]:../articles/storage/common/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]:../articles/virtual-machines/linux/about-disks-and-vhds.md
[disk-encryption]: ../articles/security/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/monitoring-and-diagnostics/insights-how-to-use-diagnostics.md
[fqdn]:../articles/virtual-machines/linux/portal-create-fqdn.md
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/
[iostat]: https://en.wikipedia.org/wiki/Iostat
[manage-vm-availability]:../articles/virtual-machines/linux/manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[OSPatching]: https://github.com/Azure/azure-linux-extensions/tree/master/OSPatching
[planned-maintenance]:../articles/virtual-machines/linux/planned-maintenance.md
[premium-storage]:../articles/storage/common/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-labs-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/en-us/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/en-us/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/en-us/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/azure-resource-manager/resource-group-overview.md
[select-vm-image]:../articles/virtual-machines/linux/cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[ssh-linux]:../articles/virtual-machines/linux/mac-create-ssh-keys.md
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-account-limits]: ../articles/azure-subscription-service-limits.md#storage-limits
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]:../articles/virtual-machines/linux/sizes.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]:../articles/virtual-machines/linux/change-vm-size.md
[vm-size-tables]:../articles/virtual-machines/windows/sizes.md#size-tables
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[readme]: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm
[components]: #Solution-components
[blocks]: https://github.com/mspnp/template-building-blocks
[0]: ./media/guidance-blueprints/compute-single-vm.png "Arquitectura de una única máquina virtual Linux en Azure"


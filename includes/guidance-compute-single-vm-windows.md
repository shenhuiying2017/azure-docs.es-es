En este artículo se describe un conjunto de procedimientos probados para ejecutar una máquina virtual (VM) Windows en Azure, teniendo en cuenta la escalabilidad, disponibilidad, manejabilidad y seguridad.

> [AZURE.NOTE] Azure cuenta con dos modelos de implementación diferentes: [Azure Resource Manager][resource-manager-overview] y el clásico. En este artículo se utiliza el Administrador de recursos, que Microsoft recomienda para las implementaciones nuevas.

No se recomienda usar una sola VM para cargas de trabajo de producción, porque no hay ningún Acuerdo de Nivel de Servicio (SLA) de tiempo de actividad para VM individuales en Azure. Para obtener el Acuerdo de Nivel de Servicio, debe implementar varias VM en un [conjunto de disponibilidad][availability-set]. Para más información, consulte [Ejecución de varias máquinas virtuales Windows en Azure][multi-vm].

## Diagrama de la arquitectura

El aprovisionamiento de una máquina virtual en Azure implica más piezas en movimiento que la propia máquina virtual. Existen elementos de proceso, red y almacenamiento.

![[0]][0]

- **Grupo de recursos.** Un [_grupo de recursos_][resource-manager-overview] es un contenedor que incluye recursos relacionados. Cree un grupo de recursos para contener los recursos de esta máquina virtual.

- **Máquina virtual**. Puede aprovisionar una VM desde una lista de imágenes publicadas o desde un archivo de disco duro virtual (VHD) cargado en Almacenamiento de blobs de Azure.

- **Disco del sistema operativo.** El disco del sistema operativo es un disco duro virtual almacenado en [Almacenamiento de Azure][azure-storage]. Esto significa que persiste incluso si el equipo host deja de funcionar.

- **Disco temporal.** La VM se crea con un disco temporal (la unidad `D:` de Windows). Este disco se almacena en una unidad física del equipo host. _No_ se guarda en Almacenamiento de Azure y podría desaparecer durante los reinicios y otros eventos del ciclo de vida de la máquina virtual. Use este disco solo para datos temporales, como archivos de paginación o de intercambio.

- **Discos de datos.** Un [disco de datos][data-disk] es un disco duro virtual persistente para los datos de la aplicación. Los discos de datos se almacenan en Almacenamiento de Azure, como el disco del sistema operativo.

- **Red virtual y subred.** Cada VM en Azure se implementa en una red virtual, que se divide a su vez en subredes.

- **Dirección IP pública.** Es necesaria una dirección IP pública para comunicarse con la VM, por ejemplo, a través de Escritorio remoto (RDP).

- **Interfaz de red (NIC)**. La NIC permite que la VM se comunique con la red virtual.

- **Grupo de seguridad de red (NSG)**. El [NSG][nsg] se usa para permitir o denegar el tráfico de red a la subred. Puede asociar un NSG a una NIC individual o a una subred. Si se asocia con una subred, las reglas NSG se aplican a todas las máquinas virtuales de esa subred.
 
- **Diagnóstico.** El registro de diagnóstico es fundamental para administrar y solucionar problemas de la VM.

## Recomendaciones

### Recomendaciones de VM

- Se recomienda la serie GS, a menos que tenga una carga de trabajo especializada, como puede ser el caso de la informática de alto rendimiento. Para más información, consulte [Tamaños de máquinas virtuales][virtual-machine-sizes]. Al mover una carga de trabajo existente a Azure, comience con el tamaño de máquina virtual que más se acerque a los servidores locales. Luego, mida el rendimiento de la carga de trabajo real con respecto a la CPU, la memoria y las operaciones de entrada/salida por segundo (IOPS) de disco, y ajuste el tamaño, si es necesario. Además, si tiene varias tarjetas NIC, tenga en cuenta el límite de NIC para cada tamaño.

- Cuando aprovisiona la VM y otros recursos, debe especificar una ubicación. Por lo general, se recomienda elegir una ubicación más cercana a los usuarios internos o clientes. Sin embargo, no todos los tamaños de máquina virtual están disponibles en todas las ubicaciones. Para más información, consulte [Servicios por región][services-by-region]. Para enumerar los tamaños de VM disponibles en una ubicación dada, ejecute el siguiente comando de la interfaz de la línea de comandos (CLI) de Azure:

    ```
    azure vm sizes --location <location>
    ```

- Para más información sobre cómo elegir una imagen de VM publicada, consulte [Navegación y selección de las imágenes de máquina virtual de Azure][select-vm-image].

### Recomendaciones de discos y almacenamiento

- Para un mejor rendimiento de E/S de disco, se recomienda el [almacenamiento premium][premium-storage], que almacena los datos en unidades de estado sólido (SSD). El costo se basa en el tamaño del disco aprovisionado. Las E/S por segundo y el rendimiento también dependen del tamaño del disco, por lo que al aprovisionar un disco, debería tener en cuenta los tres factores (capacidad, E/S por segundo y rendimiento).

- Una cuenta de almacenamiento puede admitir de 1 a 20 máquinas virtuales.

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

- Puede escalar y reducir verticalmente una VM [cambiando su tamaño][vm-resize].

- Para escalar horizontalmente, coloque dos o más máquinas virtuales en un conjunto de disponibilidad detrás de un equilibrador de carga. Para más información, consulte [Ejecución de varias máquinas virtuales Windows en Azure][multi-vm].

## Consideraciones sobre disponibilidad

- Como se mencionó anteriormente, no hay ningún SLA para una sola máquina virtual. Para obtener el SLA, debe implementar varias máquinas virtuales en un conjunto de disponibilidad.

- La VM puede verse afectada por un [mantenimiento planeado][planned-maintenance] o un [mantenimiento no planeado][manage-vm-availability]. Puede usar [registros de reinicio de VM][reboot-logs] para determinar si se produjo un reinicio de la máquina virtual por mantenimiento planeado.

- Los discos duros virtuales están respaldados por [Almacenamiento de Azure][azure-storage], que se replica para su disponibilidad y durabilidad.

- Para protegerse de la pérdida accidental de datos durante las operaciones normales (por ejemplo, debido a errores de usuario), debe implementar igualmente copias de seguridad a partir de un momento específico mediante [instantáneas de blobs][blob-snapshot] u otra herramienta.

## Consideraciones sobre la manejabilidad

- **Grupos de recursos.** Coloque los recursos estrechamente acoplados que comparten el mismo ciclo de vida en un mismo [grupo de recursos][resource-manager-overview]. Los grupos de recursos le permiten implementar y supervisar los recursos como un grupo, y acumular los costos de facturación por grupo de recursos. También se pueden eliminar recursos en conjunto, lo que resulta muy útil para implementaciones de prueba. Asigne a los recursos nombres descriptivos. De esta forma será más fácil encontrarlos y comprender su función. Consulte [Recommended naming conventions for Azure resources][naming conventions] (Convenciones de nomenclatura recomendadas para los recursos de Azure).

- **Diagnósticos de máquina virtual.** Habilite la supervisión y el diagnóstico, como las métricas básicas de estado, los registros de infraestructura de diagnóstico y los [diagnósticos de arranque][boot-diagnostics]. Los diagnósticos de arranque pueden ayudarle a diagnosticar un error de arranque si la VM entra en un estado de imposibilidad de arranque. Para más información, consulte [Habilitación de supervisión y diagnóstico][enable-monitoring]. Use la extensión [Colección de registros de Azure][log-collector] para recopilar registros de la plataforma Azure y cargarlos en Almacenamiento de Azure.

    El siguiente comando CLI habilita los diagnósticos:

    ```text
    azure vm enable-diag <resource-group> <vm-name>
     ```

- **Detención de una máquina virtual.** Azure hace una distinción entre los estados "Detenido" y "Desasignado". Se le cobrará cuando el estado de la VM sea "Detenido". No se le cobrará cuando la VM esté desasignada.

    Utilice el siguiente comando de la CLI para desasignar una máquina virtual:

    ```text
    azure vm deallocate <resource-group> <vm-name>
    ```

    El botón **Detener** en el Portal de Azure también desasigna la VM. Sin embargo, si apaga desde dentro del sistema operativo mientras tiene la sesión iniciada, la VM se detiene pero _no_ se desasigna, por lo que se le seguirá cobrando.

- **Eliminación de una máquina virtual.** Si elimina una VM, no se eliminarán los discos duros virtuales. Esto significa que puede eliminar de forma segura la VM sin perder datos. Sin embargo, se le seguirá cobrando por el almacenamiento. Para eliminar el disco duro virtual, elimine el archivo de [Almacenamiento de blobs][blob-storage].

  Para evitar eliminaciones por error, use un [bloqueo de recurso][resource-lock] para bloquear el grupo de recursos completo o recursos individuales, como la VM.

## Consideraciones sobre la seguridad

- Use [Azure Security Center][security-center] para obtener una visión central del estado de la seguridad de sus recursos en Azure. El Centro de seguridad supervisa los posibles problemas de seguridad, como actualizaciones del sistema o antimalware, y proporciona una imagen completa del estado de seguridad de su implementación.

    - El Centro de seguridad se configura por cada suscripción de Azure. Habilite la recolección de datos de seguridad tal como se describe en [Uso del Centro de seguridad].

    - Una vez que habilite la recolección, el Centro de seguridad busca automáticamente las VM creadas en esa suscripción.

- **Administración de revisiones.** Si está habilitada esta opción, el Centro de seguridad comprueba si faltan actualizaciones críticas y de seguridad. Use la [configuración de directiva de grupo][group-policy] de la VM para habilitar las actualizaciones automáticas del sistema.

- **Antimalware.** Si está habilitada esta opción, el Centro de seguridad comprueba si está instalado software antimalware. También puede Security Center para instalar software antimalware desde el Portal de Azure.

- Use el [control de acceso basado en rol][rbac] (RBAC) para controlar el acceso a los recursos de Azure que implementa. RBAC le permite asignar roles de autorización a los miembros de su equipo de DevOps. Por ejemplo, el rol de lector puede ver recursos de Azure pero no crearlos, administrarlos o eliminarlos. Algunos roles son específicos de un tipo de recurso de Azure determinado. Por ejemplo, el rol Colaborador de máquina virtual puede reiniciar o desasignar una máquina virtual, restablecer la contraseña de administrador, crear una nueva máquina virtual, etc. Otros [roles de RBAC integrados][rbac-roles] que pueden resultar útiles para esta arquitectura de referencia son, por ejemplo, el de [Usuario de DevTest Lab][rbac-devtest] y el de [Colaborador de la red][rbac-network]. Un usuario puede asignarse a varios roles, y es posible crear roles personalizados para una especificación aún más detallada de los permisos.

    > [AZURE.NOTE] RBAC no limita las acciones que puede realizar un usuario que ha iniciado sesión en una máquina virtual. Esos permisos están determinados por el tipo de cuenta en el sistema operativo invitado.

- Para restablecer la contraseña de administrador local, ejecute el comando `vm reset-access` de la CLI de Azure.

    ```text
    azure vm reset-access -u <user> -p <new-password> <resource-group> <vm-name>
    ```

- Use los [registros de auditoría][audit-logs] para ver las acciones de aprovisionamiento y otros eventos de VM.

- Considere la posibilidad de usar el [cifrado de discos de Azure][disk-encryption] si necesita cifrar los discos de datos y del sistema operativo.

## Componentes de soluciones

Dispone de un script de solución sencillo, [Deploy-ReferenceArchitecture.ps1][solution-script], que puede usar para implementar la arquitectura que sigue las recomendaciones que se describen en este artículo. Este script usa plantillas de [Resource Manager][ARM-Templates]. Las plantillas están disponibles como un conjunto de bloques de creación fundamentales, cada uno de los cuales realiza una acción concreta, como crear una red virtual o configurar un NSG. La finalidad del script es organizar la implementación de la plantilla.

Las plantillas incluyen parámetros, que están contenidos en archivos JSON independientes. Puede modificar los parámetros de estos archivos para configurar la implementación de forma que satisfaga sus propios requisitos. No es necesario modificar las plantillas propiamente dichas. Tenga en cuenta que no debe cambiar los esquemas de los objetos en los archivos de parámetros.

Al editar las plantillas, cree objetos que sigan las convenciones de nomenclatura descritas en [Recommended naming conventions for Azure resources][naming conventions] (Convenciones de nomenclatura recomendadas para los recursos de Azure).

El script hace referencia a los siguientes archivos de parámetros para compilar la máquina virtual y la infraestructura adyacente:

- **[virtualNetwork.parameters.json][vnet-parameters]**. Este archivo define la configuración de red virtual, como nombre, espacio de direcciones, subredes y las direcciones de los servidores DNS necesarios. Tenga en cuenta que las direcciones de subred deben incluir el espacio de direcciones de la red virtual.

	<!-- source: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm/parameters/windows/virtualNetwork.parameters.json#L4-L21 -->
	```json
  "parameters": {
    "virtualNetworkSettings": {
      "value": {
        "name": "ra-single-vm-vnet",
        "resourceGroup": "ra-single-vm-rg",
        "addressPrefixes": [
          "172.17.0.0/16"
        ],
        "subnets": [
          {
            "name": "ra-single-vm-sn",
            "addressPrefix": "172.17.0.0/24"
          }
        ],
        "dnsServers": [ ]
      }
    }
  }
	```

- **[networkSecurityGroups.parameters.json][nsg-parameters]**. Este archivo contiene las definiciones de NSG y reglas de NSG. El parámetro `name` del bloque `virtualNetworkSettings` especifica la red virtual a la que está asociada el NSG. El parámetro `subnets` del bloque `networkSecurityGroupSettings` identifica las subredes que aplican las reglas de NSG en la red virtual. Estos elementos se deben definir en el archivo **virtualNetwork.parameters.json**.

	Tenga en cuenta que la regla de seguridad predeterminada que se muestra en el ejemplo permite al usuario conectarse a la máquina virtual a través de una conexión a Escritorio remoto (RDP). Puede abrir puertos adicionales (o denegar el acceso a través de puertos específicos) si agrega más elementos a la matriz `securityRules`.

	<!-- source: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm/parameters/windows/networkSecurityGroups.parameters.json#L4-L36 -->
	```json
  "parameters": {
    "virtualNetworkSettings": {
      "value": {
        "name": "ra-single-vm-vnet",
        "resourceGroup": "ra-single-vm-rg"
      }
    },
    "networkSecurityGroupsSettings": {
      "value": [
        {
          "name": "ra-single-vm-nsg",
          "subnets": [
            "ra-single-vm-sn"
          ],
          "networkInterfaces": [
          ],
          "securityRules": [
            {
              "name": "RDPAllow",
              "direction": "Inbound",
              "priority": 100,
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "*",
              "sourcePortRange": "*",
              "destinationPortRange": "3389",
              "access": "Allow",
              "protocol": "Tcp"
            }
          ]
        }
      ]
    }
  }
	```

- **[virtualMachineParameters.json][vm-parameters]**. Este archivo define la configuración de la propia máquina virtual, como su nombre y tamaño, las credenciales de seguridad del usuario administrador, los discos que se van a crear y las cuentas de almacenamiento que contendrán estos discos.

	Debe especificar una imagen en la sección `imageReference`. Los valores que aparecen a continuación crean una máquina virtual con la última compilación de Windows Server 2012 R2 Datacenter. Puede utilizar el siguiente comando de CLI de Azure para obtener una lista de todas las imágenes de Windows disponibles en una región (el ejemplo utiliza la región westus):

	```text
	azure vm image list westus MicrosoftWindowsServer WindowsServer
	```

	El parámetro `subnetName` de la sección `nics` especifica la subred de la VM. De forma simular, el parámetro `name` en `virtualNetworkSettings` identifica la red virtual que se usará. Estos valores deben ser el nombre de una subred y la red virtual definida en el archivo **virtualNetwork.parameters.json**.

	Puede crear varias VM, bien mediante el uso compartido de una cuenta de almacenamiento o con sus propias cuentas de almacenamiento si modifica la configuración de la sección `buildingBlockSettings`. Si crea varias VM, también debe especificar el nombre de un conjunto de disponibilidad que se usará o creará en la sección `availabilitySet`.

	<!-- source: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm/parameters/windows/virtualMachine.parameters.json#L4-L64 -->
	```json
   "parameters": {
    "virtualMachinesSettings": {
      "value": {
        "namePrefix": "ra-single-vm",
        "computerNamePrefix": "cn",
        "size": "Standard_DS1_v2",
        "osType": "windows",
        "adminUsername": "testuser",
        "adminPassword": "AweS0me@PW",
        "sshPublicKey": "",
        "osAuthenticationType": "password",
        "nics": [
          {
            "isPublic": "true",
            "subnetName": "ra-single-vm-sn",
            "privateIPAllocationMethod": "dynamic",
            "publicIPAllocationMethod": "dynamic",
            "enableIPForwarding": false,
            "dnsServers": [
            ],
            "isPrimary": "true"
          }
        ],
        "imageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2012-R2-Datacenter",
          "version": "latest"
        },
        "dataDisks": {
          "count": 2,
          "properties": {
            "diskSizeGB": 128,
            "caching": "None",
            "createOption": "Empty"
          }
        },
        "osDisk": {
          "caching": "ReadWrite"
        },
        "extensions": [ ],
        "availabilitySet": {
          "useExistingAvailabilitySet": "No",
          "name": ""
        }
      }
    },
    "virtualNetworkSettings": {
      "value": {
        "name": "ra-single-vm-vnet",
        "resourceGroup": "ra-single-vm-rg"
      }
    },
    "buildingBlockSettings": {
      "value": {
        "storageAccountsCount": 1,
        "vmCount": 1,
        "vmStartIndex": 0
      }
    }
  }
	```

## Implementación de la solución

La solución da por supuesto los siguientes requisitos previos:

- Tiene una suscripción de Azure en la que puede crear grupos de recursos.

- Descargó e instaló la compilación más reciente de Azure PowerShell. Consulte [aquí][azure-powershell-download] para obtener instrucciones.

Para ejecutar el script que implementa la solución:

1. Cree una carpeta que contenga subcarpetas con los nombres `Scripts` y `Templates`.

2. En la carpeta Plantillas, cree otra subcarpeta denominada Windows.

3. Descargue el archivo [Deploy-ReferenceArchitecture.ps1][solution-script] en la carpeta Scripts.

4. Descargue los siguientes archivos en la carpeta Plantillas/Windows:

	- [virtualNetwork.parameters.json][vnet-parameters]

	- [networkSecurityGroups.parameters.json][nsg-parameters]

	- [virtualMachineParameters.json][vm-parameters]

5. Edite el archivo Deploy-ReferenceArchitecture.ps1 en la carpeta Scripts y cambie la línea siguiente para especificar el grupo de recursos que se debe crear o usar como contenedor de la máquina virtual y los recursos creados por el script:

	<!-- source: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm/Deploy-ReferenceArchitecture.ps1#L37 -->
	```powershell
	$resourceGroupName = "ra-single-vm-rg"
	```
6. Edite cada uno de los archivos JSON de la carpeta Plantillas/Windows para definir los parámetros de la red virtual, el NSG y la VM, como se describió anteriormente en la sección Componentes de soluciones.

	>[AZURE.NOTE] Asegúrese de establecer el parámetro `resourceGroup` de la sección `virtualNetworkSettings` del archivo virtualMachineParameters.json tal como se especificó en el archivo Deploy-ReferenceArchitecture.ps1.

7. Abra una ventana de PowerShell, vaya a la carpeta Scripts y ejecute el siguiente comando:

	```powershell
	.\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Windows
	```

	Reemplace `<subscription id>` por el id. de suscripción de Azure.

	En `<location>`, especifique una región de Azure, como `eastus` o `westus`.

8. Cuando haya finalizado el script, use el Portal de Azure para comprobar que la red, el NSG y la máquina virtual se han creado correctamente.

## Pasos siguientes

Para que se aplique el [Acuerdo de Nivel de Servicio para máquinas virtuales][vm-sla], debe implementar dos o más instancias en un conjunto de disponibilidad. Para más información, consulte [Ejecución de varias máquinas virtuales en Azure][multi-vm].

<!-- links -->

[audit-logs]: https://azure.microsoft.com/es-ES/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]: ../articles/virtual-machines/virtual-machines-windows-create-availability-set.md
[azure-cli]: ../articles/virtual-machines-command-line-tools.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/es-ES/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/virtual-machines/virtual-machines-windows-about-disks-vhds.md
[disk-encryption]: ../articles/security/azure-security-disk-encryption.md
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
[ARM-Templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/Deploy-ReferenceArchitecture.ps1
[vnet-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/parameters/windows/virtualNetwork.parameters.json
[nsg-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/parameters/windows/networkSecurityGroups.parameters.json
[vm-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/parameters/windows/virtualMachine.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[0]: ./media/guidance-blueprints/compute-single-vm.png "Arquitectura de una única máquina virtual Windows en Azure"

<!---HONumber=AcomDC_0831_2016-->
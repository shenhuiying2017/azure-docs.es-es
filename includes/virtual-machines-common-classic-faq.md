


En este artículo encontrará respuestas a algunas de las preguntas más comunes de los usuarios sobre las máquinas virtuales de Azure creadas con el modelo de implementación clásico.

## <a name="can-i-migrate-my-vm-created-in-the-classic-deployment-model-to-the-new-resource-manager-model"></a>¿Puedo migrar mi máquina virtual creada en el modelo de implementación clásico al nuevo modelo de Resource Manager?
Sí. Para obtener instrucciones sobre cómo realizar la migración, consulte:

* [Migración del modelo clásico a Azure Resource Manager mediante Azure PowerShell](../articles/virtual-machines/windows/migration-classic-resource-manager-ps.md).
* [Migración del modelo clásico a Azure Resource Manager mediante la CLI de Azure](../articles/virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md).

## <a name="what-can-i-run-on-an-azure-vm"></a>¿Qué puedo ejecutar en una máquina virtual de Azure?
Todos los suscriptores pueden ejecutar software de servidor en una máquina virtual de Azure. Puede ejecutar versiones recientes de Windows Server, así como varias distribuciones de Linux. Para obtener más información de soporte técnico, consulte:

• Para máquinas virtuales de Windows: [soporte de software del servidor de Microsoft para Azure Virtual Machines](http://go.microsoft.com/fwlink/p/?LinkId=393550)

• Para máquinas virtuales de Linux: [Linux en distribuciones aprobadas por Azure](http://go.microsoft.com/fwlink/p/?LinkId=393551)

Para imágenes de cliente de Windows, hay disponibles determinadas versiones de Windows 7 y Windows 8.1 para suscriptores de MSDN Azure y suscriptores de pago por uso de desarrollo y prueba de MSDN, para tareas de desarrollo y prueba. Para obtener más información, como instrucciones y limitaciones, consulte [Imágenes de cliente de Windows para los suscriptores de MSDN](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/).

## <a name="why-are-affinity-groups-being-deprecated"></a>¿Por qué se han dejado de usar los grupos de afinidad?
Los grupos de afinidad son un concepto heredado que hace referencia a la agrupación geográfica de las implementaciones de servicios en la nube y las cuentas de almacenamiento de un cliente en Azure. En los primeros diseños de red de Azure, su función original era la de mejorar el rendimiento de las redes VM a VM. También permitían el lanzamiento inicial de redes virtuales (VNet) que estaban limitadas a un pequeño conjunto de hardware en una región.

La red de Azure actual dentro de una región está diseñada de tal modo que hace innecesarios los grupos de afinidad. Las redes virtuales también se encuentran en el ámbito regional y, por tanto, ya no es necesario usar un grupo de afinidad. Debido a estas mejoras, ya no recomendamos el uso de grupos de afinidad, dado que pueden implicar ciertas limitaciones en algunos escenarios. El uso de grupos de afinidad asociará innecesariamente las máquinas virtuales a un hardware específico, lo cual limitará la elección de los tamaños de máquinas virtuales disponibles. También puede provocar errores relacionados con la capacidad al intentar agregar nuevas máquinas virtuales cuando el hardware específico asociado al grupo de afinidad está al límite de su capacidad.

Las características de los grupos de afinidad ya están en desuso en el modelo de implementación de Azure Resource Manager y en Azure Portal. Próximamente, el Portal de Azure clásico dejará de ser compatible con la creación de grupos de afinidad y la creación de recursos de almacenamiento que están anclados a un grupo de afinidad. Aunque no es necesario modificar los servicios en la nube existentes que usan un grupo de afinidad, los grupos de afinidad no deben usarse en los nuevos servicios en la nube, a menos que lo recomiende uno de los profesionales de soporte técnico de Azure.

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>¿Cuánto almacenamiento puedo usar con una máquina virtual?
Cada disco de datos puede ser de hasta 1 TB. El número de discos de datos que puede usar depende del tamaño de la máquina virtual. Para obtener más información, consulte [Tamaños de máquinas virtuales](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Una cuenta de almacenamiento de Azure proporciona almacenamiento para el disco del sistema operativo y los discos de datos. Cada disco es un archivo .vhd almacenado como un blob en páginas. Para obtener información detallada sobre los precios, consulte [Detalles de precios de almacenamiento](http://go.microsoft.com/fwlink/p/?LinkId=396819).

## <a name="which-virtual-hard-disk-types-can-i-use"></a>¿Qué tipos de disco duro virtual se puede usar?
Azure solo admite discos duros virtuales fijos con formato VHD. Si tiene un disco VHDX que quiere usar en Azure, debe convertirlo mediante el Administrador de Hyper-V o el cmdlet [convert-VHD](http://go.microsoft.com/fwlink/p/?LinkId=393656) . Una vez hecho esto, use el cmdlet [Add-AzureVHD](https://msdn.microsoft.com/library/azure/dn495173.aspx) (en modo de administración de servicios) para cargar el VHD en una cuenta de almacenamiento de Azure para que pueda usarlo con máquinas virtuales.

* Para obtener instrucciones para Linux, consulte [Creación y carga de un disco duro virtual que contiene el sistema operativo Linux](../articles/virtual-machines/linux/classic/create-upload-vhd-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="are-these-virtual-machines-the-same-as-hyper-v-virtual-machines"></a>¿Son estas máquinas virtuales las mismas que las máquinas virtuales de Hyper-V?
En muchos aspectos son similares a las máquinas virtuales Hyper-V de "generación 1", pero no son exactamente lo mismo. Ambos tipos proporcionan hardware virtualizado, y los discos duros virtuales de formato VHD son compatibles. Esto significa que puede moverlos entre Hyper-V y Azure. Tres diferencias claves que a veces sorprenden a los usuarios de Hyper-V son:

* Azure no proporciona acceso a consolas a una máquina virtual. No hay ninguna manera de obtener acceso a una máquina virtual hasta que se hace arranque.
* Las máquinas virtuales de Azure de la mayoría de [tamaños](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) solo tienen un adaptador de red virtual, lo que significa que solo pueden tener una dirección IP externa. (Los tamaños A8 y A9 emplean un segundo adaptador de red para la comunicación de aplicaciones entre las instancias en escenarios limitados).
* Las máquinas virtuales de Azure no admiten funciones de máquina virtual de Hyper-V de generación 2. Para más información sobre estas características, consulte [Especificaciones de máquina virtual para Hyper-V](http://technet.microsoft.com/library/dn592184.aspx) e [Introducción a las máquinas virtuales de generación 2](https://technet.microsoft.com/library/dn282285.aspx).

## <a name="can-these-virtual-machines-use-my-existing-on-premises-networking-infrastructure"></a>¿Pueden estas máquinas virtuales usar mi infraestructura de red local existente?
Para las máquinas virtuales creadas en el modelo de implementación clásico, puede usar la instancia de Azure Virtual Network para extender su infraestructura existente. El enfoque es como configurar una sucursal. Puede aprovisionar y administrar redes privadas virtuales (VPN) en Azure, así como conectarlas de forma segura con la infraestructura de TI local. Para obtener más información, consulte [Información general de la red virtual](../articles/virtual-network/virtual-networks-overview.md).

Necesitará especificar la red a la que desea que pertenezca la máquina virtual al crear la máquina virtual. No puede unir una máquina virtual existente a una red virtual. Sin embargo, puede solucionar este problema separando el disco duro virtual (VHD) de la máquina virtual existente y, a continuación, usarlo para crear una nueva máquina virtual con la configuración de red que desee.

## <a name="how-can-i-access--my-virtual-machine"></a>¿Cómo puedo acceder a mi máquina virtual?
Deberá establecer una conexión remota para iniciar sesión en la máquina virtual, usando Conexión a Escritorio remoto para una máquina virtual de Windows o un Shell seguro (SSH) para una máquina virtual de Linux. Para obtener instrucciones, consulte:

* [Inicio de sesión en una máquina virtual Windows mediante el Portal de Azure clásico](../articles/virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Se admite un máximo de 2 conexiones simultáneas, a menos que el servidor está configurado como un host de sesión de servicios de escritorio remoto.  
* [Creación de claves SSH en Linux y Mac para máquinas virtuales de Linux en Azure](../articles/virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). De forma predeterminada, SSH permite un máximo de 10 conexiones simultáneas. Puede aumentar este número editando el archivo de configuración.

Si tiene problemas con el Escritorio remoto o SSH, instale y use la extensión [VMAccess](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), ya que le ayudará a solucionar el problema.

En las máquinas virtuales de Windows, entre las opciones adicionales se incluyen:

* En Azure Portal, busque la máquina virtual y, a continuación, haga clic en **Restablecer acceso remoto** desde la barra de comandos.
* Revise [Solución de problemas de conexiones del Escritorio remoto a una máquina virtual de Azure con Windows](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Use Acceso remoto a Windows PowerShell para conectarse a la máquina virtual o crear extremos adicionales para que otros recursos se conecten a la máquina virtual. Para más información, consulte [Configuración de puntos de conexión en una máquina virtual](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Si está familiarizado con Hyper-V, puede que esté buscando una herramienta similar a VMConnect. Azure no ofrece una herramienta similar porque no admite el acceso de la consola a una máquina virtual.

## <a name="can-i-use-the-temporary-disk-the-d-drive-for-windows-or-devsdb1-for-linux-to-store-data"></a>¿Puedo usar el disco temporal (la unidad D: de Windows o/dev/sdb1 de Linux) para almacenar los datos?
No debería usar el disco temporal (la unidad D: por defecto de Windows o /dev/sdb1 de Linux) para almacenar los datos. Estos discos solamente proporcionan almacenamiento de forma temporal, por lo que se arriesgaría a perder datos que no se pueden recuperar. Esto puede ocurrir cuando se mueve la máquina virtual a un host diferente. Cambiar el tamaño de una máquina virtual, actualizar el host o un error de hardware en el host son algunas de las razones por las que se puede mover una máquina virtual.

## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>¿Cómo puedo cambiar la letra de la unidad del disco temporal?
En una máquina virtual de Windows, puede cambiar la letra de la unidad moviendo el archivo de la página y reasignando letras de unidad, pero deberá asegurarse de realizar los pasos en un orden específico. Para obtener instrucciones, consulte [Cambio de la letra de unidad del disco temporal de Windows](../articles/virtual-machines/windows/change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="how-can-i-upgrade-the-guest-operating-system"></a>¿Cómo puedo actualizar el sistema operativo invitado?
Generalmente, el término "Actualizar" significa cambiar a una versión más reciente del sistema operativo sin modificar el hardware. En las máquinas virtuales de Azure, el proceso para cambiar a una versión más reciente es diferente para Linux y Windows:

* Las máquinas virtuales de Linux usan las herramientas de administración de paquetes y procedimientos adecuados para la distribución.
* En una máquina virtual de Windows, debe migrar el servidor con algo parecido a las herramientas de migración de Windows Server. No intente actualizar el sistema operativo invitado cuando resida en Azure. No se admite debido al riesgo de perder el acceso a una máquina virtual. Si se producen problemas durante la actualización, podría perder la capacidad de iniciar una sesión de escritorio remoto y no sería capaz de solucionar los problemas.

Para obtener información general sobre las herramientas y procesos para migrar a Windows Server, consulte [Migración de roles y características a Windows Server](http://go.microsoft.com/fwlink/p/?LinkId=396940).

## <a name="whats-the-default-user-name-and-password-on-the-virtual-machine"></a>¿Cuál es el nombre de usuario predeterminado y la contraseña en la máquina virtual?
Las imágenes proporcionadas por Azure no tienen un nombre de usuario configurado previamente y una contraseña. Cuando cree la máquina virtual mediante una de esas imágenes, deberá proporcionar un nombre de usuario y una contraseña, que usará para iniciar sesión en la máquina virtual.

Si ha olvidado el nombre de usuario o contraseña y ha instalado el agente de máquina virtual, puede instalar y usar la extensión [VMAccess](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para corregir el problema.

Detalles adicionales:

* En cuanto a las imágenes de Linux, si usa Azure Portal, "azureuser" se proporciona como un nombre de usuario predeterminado, pero puede cambiarlo usando la opción "De la Galería" en lugar de "Creación rápida" como forma de crear la máquina virtual. Utilizar “De la Galería” también le permite decidir si desea usar una contraseña, una clave SSH o ambos para iniciar la sesión. La cuenta de usuario es un usuario sin privilegios que tiene acceso “sudo” para ejecutar comandos privilegiados. La cuenta “root” está deshabilitada.
* Para las imágenes de Windows, deberá proporcionar un nombre de usuario y una contraseña al crear la máquina virtual. La cuenta se agrega al grupo de administradores.

## <a name="can-azure-run-anti-virus-on-my-virtual-machines"></a>¿Puede Azure ejecutar antivirus en las máquinas virtuales?
Azure ofrece varias opciones para soluciones antivirus, pero la administración depende de usted. Por ejemplo, podría necesitar una suscripción independiente para el software antimalware y tendrá que decidir cuándo ejecutar análisis e instalar las actualizaciones. Puede agregar soporte antivirus con una extensión de máquina virtual de Microsoft Antimalware, Symantec Endpoint Protection o TrendMicro Deep Security Agent al crear una máquina virtual de Windows o en un momento posterior. Las extensiones de Symantec y TrendMicro le permiten usar una suscripción de prueba gratuita de tiempo limitado o una suscripción existente de empresa. Microsoft Antimalware es gratuito. Para obtener información, consulte:

* [Instalación y configuración de Endpoint Protection en una máquina virtual de Azure](http://go.microsoft.com/fwlink/p/?LinkId=404207)
* [Instalación y configuración de Trend Micro Deep Security como servicio en una máquina virtual de Azure](http://go.microsoft.com/fwlink/p/?LinkId=404206)
* [Implementación de soluciones antimalware en Azure Virtual Machines](https://azure.microsoft.com/blog/2014/05/13/deploying-antimalware-solutions-on-azure-virtual-machines/)

## <a name="what-are-my-options-for-backup-and-recovery"></a>¿Qué opciones tengo para la copia de seguridad y la recuperación?
Azure Backup está disponible como una vista previa en determinadas regiones. Para obtener más información, consulte [Copia de seguridad de máquinas virtuales de Azure](../articles/backup/backup-azure-arm-vms.md). Hay otras soluciones disponibles de socios certificados. Para averiguar lo que está actualmente disponible, busque Azure Marketplace.

Otra opción es usar las capacidades de instantánea del almacenamiento de blobs. Para ello, deberá apagar la máquina virtual antes de cualquier operación que se base en una instantánea de blob. Esto guarda las escrituras de datos pendientes y coloca el sistema de archivos en un estado coherente.

## <a name="how-does-azure-charge-for-my-vm"></a>¿Cómo cobra Azure mi máquina virtual?
Azure cobra un precio por hora basado en el tamaño y el sistema operativo de la máquina virtual. En las fracciones de hora, solo se cobra por los minutos de uso. Si crea la máquina virtual con una imagen de máquina virtual que contiene determinado software preinstalado, es posible que se apliquen cargos adicionales de software por hora. Azure cobra por separado el almacenamiento de los discos de datos y de sistema operativo de las máquinas virtuales. El almacenamiento en disco temporal es gratuito.

Se le cobrará cuando el estado de la máquina virtual esté en ejecución o detenido, pero no se le cobrará cuando el estado de la máquina virtual sea Detenido (sin asignar). Para poner una máquina virtual en estado Detenido (sin asignar), realice una de las acciones siguientes:

* Apague o elimine la máquina virtual desde Azure Portal.
* Use el cmdlet Stop-AzureVM, disponible en el módulo de Azure PowerShell.
* Use la operación de Cerrar rol en la API de REST de administración de servicios y especifique StoppedDeallocated para el elemento PostShutdownAction.

Para obtener más información, consulte [Precios de máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/).

## <a name="will-azure-reboot-my-vm-for-maintenance"></a>¿Reiniciará Azure mi máquina virtual para efectuar tareas de mantenimiento?
En ocasiones Azure reinicia la máquina virtual como parte de las actualizaciones de mantenimiento regular planeadas en los centros de datos de Azure.

Es posible que se produzcan eventos de mantenimiento no planeados cuando Azure detecte un problema grave de hardware que afecte a la máquina virtual. Para los eventos no planeados, Azure migra la máquina virtual a un host correcto automáticamente y reinicia la máquina virtual.

Para cualquier máquina virtual independiente (es decir, la máquina virtual no forma parte de un conjunto de disponibilidad), Azure envía una notificación al administrador de servicios de la suscripción por correo electrónico al menos una semana antes de efectuar las tareas de mantenimiento planeadas porque las máquinas virtuales pueden reiniciarse durante la actualización. Las aplicaciones que se ejecutan en las máquinas virtuales podrían experimentar tiempos de inactividad.

También puede usar Azure Portal o Azure PowerShell para ver los registros de reinicio si este se produjo debido a una tarea de mantenimiento planeado. Para obtener más información, consulte [Visualización de registros de reinicio de máquina virtual](https://azure.microsoft.com/blog/2015/04/01/viewing-vm-reboot-logs/).

Para proporcionar redundancia, coloque dos o más máquinas virtuales configuradas de manera similar en el mismo conjunto de disponibilidad. Esto ayuda a asegurarse de que haya al menos una máquina virtual esté disponible durante el mantenimiento, sea este planeado o no. Azure garantiza determinados niveles de disponibilidad de la máquina virtual para esta configuración. Para más información, consulte [Administración de la disponibilidad de las máquinas virtuales](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="additional-resources"></a>Recursos adicionales
[Acerca de Azure Virtual Machines](../articles/virtual-machines/virtual-machines-linux-about.md)

[Creación y administración de máquinas virtuales Linux con la CLI de Azure](../articles/virtual-machines/linux/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Creación y administración de máquinas virtuales Windows con el módulo de Azure PowerShell](../articles/virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


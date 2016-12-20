



Las extensiones de VM pueden ayudarle a:

* Modificar las características de seguridad e identidad, como el restablecimiento de valores de cuenta y el uso de antimalware
* Iniciar, detener o configurar la supervisión y el diagnóstico
* Restablecer o instalar características de conectividad, como RDP y SSH
* Diagnosticar, supervisar y administrar las máquinas virtuales

Además, existen muchas otras características; con frecuencia, se lanzan nuevas características de Extensión de VM. En este artículo, se describen los Agentes de VM de Azure para Windows y Linux y cómo admiten la funcionalidad de Extensión de VM. Si desea obtener un listado de las extensiones de máquina virtual por categoría de característica, consulte [Características y extensiones de VM de Azure](../articles/virtual-machines/virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="azure-vm-agents-for-windows-and-linux"></a>Agentes de VM de Azure para Windows y Linux
El Agente de máquinas virtuales de Azure (Agente de VM) es un proceso ligero y protegido que instala, configura y eliminar extensiones de VM en instancias de Máquinas virtuales de Azure desde la Galería de imágenes y en instancias de VM personalizadas, si tienen instalado el Agente de VM. El Agente de VM actúa como el servicio de control local seguro para la VM de Azure. Las extensiones que carga el agente proporcionan características específicas para aumentar la productividad mediante el uso de la instancia.

Existen dos Agentes de VM de Azure, uno para máquinas virtuales de Windows y uno para máquinas virtuales de Linux. De manera predeterminada, el agente de máquina virtual se instala automáticamente cuando crea una máquina virtual desde la Galería de imágenes, pero también puede instalar el agente de máquina virtual una vez creada la instancia o puede instalarlo en una imagen de VM personalizada que luego carga usted mismo.

> [!IMPORTANT]
> Estos agentes de máquina virtual son servicios muy ligeros que permiten la administración protegida de las instancias de máquina virtual. Es posible que existan casos en los que no desee tener el Agente de VM. Si es así, asegúrese de crear máquinas virtuales que no tengan instalado el Agente de VM. A pesar de que el Agente de VM se puede quitar físicamente, el comportamiento de cualquier extensión de VM en la instancia es indefinido. Como resultado, en este momento no es compatible la eliminación del agente de máquina virtual una vez instalado.
> 
> 

El Agente de VM se habilita en las siguientes situaciones:

* Cuando se crea una instancia de una máquina virtual mediante el método **Creación rápida** en el Portal de Azure clásico, o bien mediante el método **Creación personalizada** en dicho portal y asegurándose de que la casilla **Instalar el agente de máquina virtual** esté activada (como se muestra en la imagen siguiente). Para más información, consulte [Creación de una máquina virtual personalizada](../articles/virtual-machines/virtual-machines-windows-classic-createportal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
  
  ![Casilla del Agente de VM](./media/virtual-machines-common-classic-agents-and-extensions/IC719409.png)
* Cuando crea una instancia de una máquina virtual mediante los cmdlets [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx) o [New-AzureQuickVM](https://msdn.microsoft.com/library/azure/dn495183.aspx). Puede crear una máquina virtual sin tener instalado el Agente de VM; para ello, agregue el parámetro **–DisableGuestAgent** al cmdlet [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx) .
* Mediante la descarga e instalación manuales del agente de máquina virtual (en su versión de Windows o de Linux) en una instancia de máquina virtual existente y el posterior establecimiento del valor **ProvisionGuestAgent** en **true** con PowerShell o una llamada de REST. (Si no establece este valor después de instalar manualmente el Agente de VM, la incorporación del Agente de VM no se detectará de manera adecuada). El ejemplo de código siguiente muestra cómo hacerlo con PowerShell, donde los argumentos `$svc` y `$name` ya están determinados.
  
      $vm = Get-AzureVM –ServiceName $svc –Name $name
      $vm.VM.ProvisionGuestAgent = $TRUE
      Update-AzureVM –Name $name –VM $vm.VM –ServiceName $svc
* Mediante la creación de una imagen de máquina virtual que tiene instalado el agente de máquina virtual antes de cargarla a Azure. En el caso de una VM de Windows, descargue el [archivo .msi del agente de máquina virtual de Windows](http://go.microsoft.com/fwlink/?LinkID=394789) e instale dicho agente. En el caso de una máquina virtual Linux, lo instalará desde el repositorio de GitHub, que se encuentra en <https://github.com/Azure/WALinuxAgent>. Para más información sobre cómo instalar el agente de máquina virtual en Linux, consulte la [Guía de usuario de agente de máquina virtual Linux de Azure](../articles/virtual-machines/virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

> [!NOTE]
> En PaaS, el agente de máquina virtual se denomina **WindowsAzureGuestAgent** y siempre está disponible en las máquinas virtuales de rol web y rol de trabajo. [para más información, consulte [Windows Azure Role Architecture](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) (Arquitectura de roles de Azure)]. El agente de máquina virtual para máquinas virtuales de rol ahora puede agregar extensiones a las máquinas virtuales de servicio en la nube, de la misma forma en que lo hace para las máquinas virtuales persistentes. La diferencia principal entre Extensiones de VM en VM de rol y VM persistentes es que, en el caso de las VM de rol, las extensiones se agregan primero al servicio en la nube y luego a las implementaciones dentro de ese servicio en la nube.
> 
> Utilice el cmdlet [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) para mostrar todas las extensiones de VM de rol disponibles.
> 
> 

## <a name="find-add-update-and-remove-vm-extensions"></a>Buscar, agregar, actualizar y quitar extensiones de VM
Para obtener información sobre estas tareas, consulte [Adición, búsqueda, actualización y eliminación de extensiones de máquina virtual de Azure](../articles/virtual-machines/virtual-machines-windows-classic-manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).



<!--HONumber=Nov16_HO3-->



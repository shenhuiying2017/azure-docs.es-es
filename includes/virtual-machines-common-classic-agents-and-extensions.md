

Las extensiones de VM pueden ayudarle a:

* Modificar las características de seguridad e identidad, como el restablecimiento de valores de cuenta y el uso de antimalware
* Iniciar, detener o configurar la supervisión y el diagnóstico
* Restablecer o instalar características de conectividad, como RDP y SSH
* Diagnosticar, supervisar y administrar las máquinas virtuales

Hay muchas otras características. Periódicamente se lanzan nuevas características de extensión de máquina virtual. En este artículo, se describen los Agentes de VM de Azure para Windows y Linux y cómo admiten la funcionalidad de Extensión de VM. Si desea obtener un listado de las extensiones de máquina virtual por categoría de característica, consulte [Características y extensiones de VM de Azure](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="azure-vm-agents-for-windows-and-linux"></a>Agentes de VM de Azure para Windows y Linux
El agente de Azure Virtual Machines (agente de VM) es un proceso ligero y protegido que instala, configura y elimina extensiones de máquina virtual en instancias de Azure Virtual Machines. El Agente de VM actúa como el servicio de control local seguro para la VM de Azure. Las extensiones que carga el agente proporcionan características específicas para aumentar la productividad mediante el uso de la instancia.

Existen dos agentes de Azure Virtual Machines, uno para máquinas virtuales Windows y otro para máquinas virtuales Linux.

Si desea que una instancia de máquina virtual use una o varias extensiones de máquina virtual, es preciso que tenga un agente de máquina virtual instalado. La imagen de una máquina virtual que se crea mediante Azure Portal y una imagen de **Marketplace** instala automáticamente un agente de máquina virtual en el proceso de creación. Si una instancia de la máquina virtual no tiene un agente de máquina virtual, se puede instalar después de crear la máquina virtual. O bien, el agente puede instalar en una imagen de máquina virtual personalizada que posteriormente se carga.

> [!IMPORTANT]
> Estos agentes de máquina virtual son servicios muy ligeros que permiten la administración protegida de las instancias de máquina virtual. Es posible que existan casos en los que no desee tener el Agente de VM. Si es así, asegúrese de crear máquinas virtuales que no tengan instalado el agente de máquina virtual mediante la CLI de Azure o PowerShell. A pesar de que el Agente de VM se puede quitar físicamente, el comportamiento de cualquier extensión de VM en la instancia es indefinido. Como consecuencia, no se admite la eliminación de agentes de máquina virtual instalados.
>

El Agente de VM se habilita en las siguientes situaciones:

* Cuando se crea una instancia de una máquina virtual mediante Azure Portal y seleccionando una imagen de **Marketplace**,
* Cuando crea una instancia de una máquina virtual mediante los cmdlets [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx) o [New-AzureQuickVM](https://msdn.microsoft.com/library/azure/dn495183.aspx). Para crear una máquina virtual sin ningún agente de máquina virtual, agregue el parámetro **–DisableGuestAgent** al cmdlet [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx).

* Cuando el agente de máquina virtual se descarga e instala de forma manual en una instancia existente de la máquina virtual y se establece el valor **ProvisionGuestAgent** en **true**. Esta técnica se puede usar para agentes Windows y Linux, mediante el uso de un comando de PowerShell o una llamada de REST. (Si no establece el valor de **ProvisionGuestAgent** después de instalar manualmente el agente de máquina virtual, la incorporación de este no se detectará correctamente). El código de ejemplo siguiente muestra cómo hacerlo mediante PowerShell, donde los argumentos `$svc` y `$name` ya se han determinado:

      $vm = Get-AzureVM –ServiceName $svc –Name $name
      $vm.VM.ProvisionGuestAgent = $TRUE
      Update-AzureVM –Name $name –VM $vm.VM –ServiceName $svc

* Cuando se crea una imagen de máquina virtual que incluye a un agente de máquina virtual instalado. Una vez que la imagen con el agente de máquina virtual existe, dicha imagen se puede cargar en Azure. En el caso de una VM de Windows, descargue el [archivo .msi del agente de máquina virtual de Windows](http://go.microsoft.com/fwlink/?LinkID=394789) e instale dicho agente. En el caso de una máquina virtual Linux, instálelo desde el repositorio de GitHub, que se encuentra en <https://github.com/Azure/WALinuxAgent>. Para más información sobre cómo instalar el agente de máquina virtual en Linux, consulte la [Guía de usuario de agente de máquina virtual Linux de Azure](../articles/virtual-machines/linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

> [!NOTE]
> En PaaS, el agente de máquina virtual se denomina **WindowsAzureGuestAgent** y siempre está disponible en las máquinas virtuales de rol web y rol de trabajo. [para más información, consulte [Windows Azure Role Architecture](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) (Arquitectura de roles de Azure)]. El agente de máquina virtual para máquinas virtuales de rol ahora puede agregar extensiones a las máquinas virtuales de servicio en la nube, de la misma forma en que lo hace para las máquinas virtuales persistentes. La principal diferencia entre las extensiones de máquina virtual en las máquinas virtuales de rol y en las máquinas virtuales persistentes es cuando se agregan las extensiones de máquina virtual. Con las máquinas virtuales de rol, las extensiones se agregan primero al servicio en la nube y, después, a las implementaciones de dicho servicio en la nube.
>
> Utilice el cmdlet [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) para mostrar todas las extensiones de VM de rol disponibles.
>
>

## <a name="find-add-update-and-remove-vm-extensions"></a>Buscar, agregar, actualizar y quitar extensiones de VM
Para obtener información sobre estas tareas, consulte [Adición, búsqueda, actualización y eliminación de extensiones de máquina virtual de Azure](../articles/virtual-machines/windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

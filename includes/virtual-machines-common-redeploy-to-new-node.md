
Si ha estado teniendo dificultades para solucionar problemas con la conexión de Escritorio remoto (RDP) a una máquina virtual de Azure basada en Windows o con la conexión de SSH a una máquina virtual de Azure basada en Linux, este artículo le ayudará a resolverlos por sí mismo, sin necesidad de notificárselo al servicio de soporte o de cambiar el tamaño de la máquina virtual. Microsoft Azure volverá a implementar la máquina virtual cuando invoque esa operación a través de Azure PowerShell.

Tenga en cuenta que, cuando se complete esa operación, se perderán los datos de disco efímeros y se actualizarán las direcciones IP dinámicas asociadas con la máquina virtual.


## Uso de Azure PowerShell

Asegúrese de que tiene la versión más reciente de Azure PowerShell 1.x instalada en su equipo. Para obtener más información, lea [Cómo instalar y configurar Azure PowerShell](../articles/powershell-install-configure.md).

Use este comando de Azure PowerShell para volver a implementar la máquina virtual:

	Set-AzureRmVM -Redeploy -ResourceGroupName $rgname -Name $vmname 


Mientras se ejecuta el comando, compruebe la máquina virtual en el [Portal de Azure](https://portal.azure.com). Fíjese en que el valor de **Estado** de la máquina virtual cambia del siguiente modo:

1. El valor inicial de **Estado** es *En ejecución*.

	![Estado inicial de nueva implementación](./media/virtual-machines-common-redeploy-to-new-node/statusrunning1.png)

2. El valor de **Estado** cambia a *Actualizando*.

	![Estado Actualizando de nueva implementación](./media/virtual-machines-common-redeploy-to-new-node/statusupdating.png)

3. El valor de **Estado** cambia a *Iniciando*.

	![Estado Iniciando de nueva implementación](./media/virtual-machines-common-redeploy-to-new-node/statusstarting.png)

4. El valor de **Estado** vuelve a cambiar a *En ejecución*.

	![Estado final de nueva implementación](./media/virtual-machines-common-redeploy-to-new-node/statusrunning2.png)

Cuando el valor de **Estado** vuelva a ser *En ejecución*, la máquina virtual se habrá vuelto a implementar correctamente.

<!---HONumber=AcomDC_0309_2016-->
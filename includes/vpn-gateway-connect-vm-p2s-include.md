Puede conectarse a una máquina virtual que se ha implementado en la red virtual mediante la creación de una conexión a Escritorio remoto a la máquina virtual. La mejor manera de comprobar inicialmente que puede conectarse a la máquina virtual es hacerlo mediante su dirección IP privada, en lugar del nombre de equipo. Con este método prueba si puede conectarse, no si la resolución de nombres está configurada correctamente.

1. Busque la dirección IP privada. Para buscar la dirección IP privada de una máquina virtual, examine sus propiedades en Azure Portal o use PowerShell.

  - Azure Portal: busque la máquina virtual en Azure Portal. Vea las propiedades de la máquina virtual. Se enumera la dirección IP privada.

  - PowerShell: utilice el ejemplo para ver una lista de las máquinas virtuales y las direcciones IP privadas de los grupos de recursos. No es preciso modificar el ejemplo para usarlo.

    ```powershell
    $VMs = Get-AzureRmVM
    $Nics = Get-AzureRmNetworkInterface | Where VirtualMachine -ne $null

    foreach($Nic in $Nics)
    {
      $VM = $VMs | Where-Object -Property Id -eq $Nic.VirtualMachine.Id
      $Prv = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAddress
      $Alloc = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAllocationMethod
      Write-Output "$($VM.Name): $Prv,$Alloc"
    }
    ```

2. Compruebe que está conectado a su red virtual mediante la conexión VPN de punto a sitio.
3. Abra **Conexión a Escritorio remoto**, para lo que debe escribir "RDP" o "Conexión a Escritorio remoto" en el cuadro de búsqueda de la barra de tareas y, después, seleccione Conexión a Escritorio remoto. Conexión a Escritorio remoto también se puede abrir con el comando "mstsc" de PowerShell. 
4. En Conexión a Escritorio remoto, escriba la dirección IP privada de la máquina virtual. Puede hacer clic en "Mostrar opciones" para ajustar más parámetros adicionales y, después, conéctese.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Solución de problemas de una conexión de RDP a una máquina virtual

Si tiene problemas para conectarse a una máquina virtual a través de su conexión VPN, compruebe los siguientes factores:

- Compruebe que la conexión VPN se ha establecido correctamente.
- Compruebe que se conecta a la dirección IP privada de la máquina virtual.
- Use "ipconfig" para comprobar la dirección IPv4 asignada al adaptador de Ethernet en el equipo desde el que está intentando conectarse. Si la dirección IP está dentro del intervalo de direcciones de la red virtual a la que se va a conectar o dentro del intervalo de direcciones de su VPNClientAddressPool, esto se conoce como un espacio de direcciones superpuesto. Cuando el espacio de direcciones se superpone de esta manera, el tráfico de red no llega a Azure, sino que se mantiene en la red local.
- Si puede conectarse a la máquina virtual mediante la dirección IP privada, pero no el nombre del equipo, compruebe que ha configurado el DNS correctamente. Para más información acerca de cómo funciona la resolución de nombres para las máquinas virtuales, consulte [Resolución de nombres para las máquinas virtuales e instancias de rol](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
- Compruebe que el paquete de configuración de cliente de VPN se generó después de que se especificaran las direcciones IP del servidor DNS para la red virtual. Si actualizó las direcciones IP de servidor DNS, genere un nuevo paquete de configuración de cliente de VPN e instálelo.
- Para más información acerca de las conexiones RDP, consulte [Solución de problemas de conexiones del Escritorio remoto a una máquina virtual de Azure](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md).
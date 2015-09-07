## Cómo crear una red virtual clásica mediante la CLI de Azure

Puede utilizar la CLI de Azure para administrar los recursos de Azure desde el símbolo del sistema de cualquier equipo que ejecute Windows, Linux o bien OSX. Para crear una red virtual mediante la CLI de Azure, siga estos pasos.

1. Si nunca ha usado la CLI de Azure, consulte [Instalar y configurar la CLI de Azure](xplat-cli.md) y siga las instrucciones hasta el punto donde deba seleccionar su cuenta y suscripción de Azure.
2. Ejecute el comando **azure network vnet create** para crear una red virtual y una subred, como se muestra a continuación. Observe la salida del comando CLI. En la lista que se muestra en la salida se explican los parámetros utilizados.

			azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
	
			info:    Executing command network vnet create
			+ Looking up network configuration
			+ Looking up locations
			+ Setting network configuration
			info:    network vnet create command OK

	- **--vnet**. Nombre de la red virtual que se va a crear. En este escenario, *TestVNet*.
	- **-e (or --address-space)**. Espacio de direcciones de red virtual. En este escenario, *192.168.0.0*.
	- **-i (or -cidr)**. Máscara de red en formato CIDR. En este escenario, *16*.
	- **-n (or --subnet-name**). Nombre de la primera subred. En este escenario, *FrontEnd*.
	- **-p (or --subnet-start-ip)**. Dirección IP inicial de la subred o el espacio de direcciones de la subred. En este escenario, *192.168.1.0*.
	- **-r (or --subnet-cidr)**. Máscara de red en formato CIDR para subred. En este escenario, *24*.
	- **-l (or --location)**. Región de Azure donde se creará la red virtual. En este escenario, *Central US*.

3. Ejecute el comando **azure network vnet create** para crear una subred, como se muestra a continuación. Observe la salida del comando. En la lista que se muestra en la salida se explican los parámetros utilizados.

			azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
	
			info:    Executing command network vnet subnet create
			+ Looking up network configuration
			+ Creating subnet "BackEnd"
			+ Setting network configuration
			+ Looking up the subnet "BackEnd"
			+ Looking up network configuration
			data:    Name                            : BackEnd
			data:    Address prefix                  : 192.168.2.0/24
			info:    network vnet subnet create command OK

	- **-t (or --vnet-name**. Nombre de la red virtual donde se creará la subred. En este escenario, *TestVNet*.
	- **-n (or --name)**. Nombre de la nueva subred. En este escenario, *BackEnd*.
	- **-a (or --address-prefix)**. Bloque CIDR de subred. En este escenario, *192.168.2.0/24*.

4. Ejecute el comando **azure network vnet show** para ver las propiedades de la nueva red virtual, tal como se muestra a continuación.

			azure network vnet show

			info:    Executing command network vnet show
			Virtual network name: TestVNet
			+ Looking up the virtual network sites
			data:    Name                            : TestVNet
			data:    Location                        : Central US
			data:    State                           : Created
			data:    Address space                   : 192.168.0.0/16
			data:    Subnets:
			data:      Name                          : FrontEnd
			data:      Address prefix                : 192.168.1.0/24
			data:
			data:      Name                          : BackEnd
			data:      Address prefix                : 192.168.2.0/24
			data:
			info:    network vnet show command OK

<!---HONumber=August15_HO9-->
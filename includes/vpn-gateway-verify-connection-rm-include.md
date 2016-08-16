### Para comprobar la conexión mediante PowerShell

Puede comprobar que la conexión se realizó correctamente mediante el cmdlet `Get-AzureRmVirtualNetworkGatewayConnection`, con o sin `-Debug`.

1. Puede usar el siguiente ejemplo de cmdlet, configurando los valores para que coincidan con los tuyos. Cuando se le pida, seleccione "A" para ejecutar "todo". En el ejemplo, `-Name` hace referencia al nombre de la conexión que creó y quiere probar.

		Get-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName MyRG

2. Cuando el cmdlet haya finalizado, consulte los valores. En el ejemplo siguiente, el estado de conexión aparece como "Conectado" y pueden verse los bytes de entrada y salida.

		Body:
		{
		  "name": "MyGWConnection",
		  "id":
		"/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/connections/MyGWConnection",
		  "properties": {
		    "provisioningState": "Succeeded",
		    "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
		    "virtualNetworkGateway1": {
		      "id":
		"/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/virtualNetworkGa
		teways/vnetgw1"
		    },
		    "localNetworkGateway2": {
		      "id":
		"/subscriptions/086cfaa0-0d1d-4b1c-94544-f8e3da2a0c7789/resourceGroups/MyRG/providers/Microsoft.Network/localNetworkGate
		ways/LocalSite"
		    },
		    "connectionType": "IPsec",
		    "routingWeight": 10,
		    "sharedKey": "abc123",
		    "connectionStatus": "Connected",
		    "ingressBytesTransferred": 33509044,
		    "egressBytesTransferred": 4142431
		  }

### Para comprobar la conexión mediante el Portal de Azure

En el Portal de Azure, puede ver el estado de conexión navegando a la conexión. Hay varias formas de hacerlo. A continuación se muestra una manera de navegar a la conexión.

1. En el [Portal de Azure](http://portal.azure.com), vaya a **Puertas de enlace de red virtual**. Haga clic en el nombre de la puerta de enlace.
2. En el panel, bajo **Configuración**, haga clic en **Conexiones**. Puede ver el estado de cada conexión.
3. Al hacer clic en el nombre de la conexión, puede ver más información sobre ella. En la página Essentials de la conexión, preste atención al **estado de la conexión**. El estado será "Correcto" y "Conectado" cuando haya realizado una conexión correcta. Puede comprobar el flujo de datos observando **Datos de entrada** y **Datos de salida**.

	En el ejemplo siguiente, **Estado de la conexión** es 'No conectado'.

	![Comprobación de la conexión](./media/vpn-gateway-verify-connection-rm-include/connectionverify450.png)

<!---HONumber=AcomDC_0810_2016-->
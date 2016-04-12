### Para comprobar la conexión mediante el uso del Portal de Azure

Puede comprobar una conexión VPN en el Portal de Azure, para ello vaya a **Puertas de enlace de red virtual** **>** ***haga clic en el nombre de la puerta de enlace*** **>** **Configuración** **>** **Conexiones**. Al seleccionar el nombre de la conexión, puede ver más información sobre la conexión. En el ejemplo siguiente, la conexión no está conectada y no hay ningún dato que fluya a través de ella.


![Comprobación de la conexión](./media/vpn-gateway-verify-connection-rm-include/connectionverify450.png)


### Para comprobar la conexión mediante el uso de PowerShell

También se puede comprobar que la conexión se realizó correctamente mediante el uso de `Get-AzureRmVirtualNetworkGatewayConnection –Debug`. Puedes usar el siguiente ejemplo de cmdlet, configurando los valores para que coincidan con los tuyos. Cuando se le pida, seleccione *A* para poder ejecutar Todo.

	Get-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Debug

 Cuando el cmdlet haya finalizado, desplázate para ver los valores. En el ejemplo siguiente, el estado de conexión aparece como *Connected* y pueden verse los bytes de entrada y salida.

	Body:
	{
	  "name": "localtovon",
	  "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/connections/loca
	ltovon",
	  "properties": {
	    "provisioningState": "Succeeded",
	    "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
	    "virtualNetworkGateway1": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworkGa
	teways/vnetgw1"
	    },
	    "localNetworkGateway2": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/localNetworkGate
	ways/LocalSite"
	    },
	    "connectionType": "IPsec",
	    "routingWeight": 10,
	    "sharedKey": "abc123",
	    "connectionStatus": "Connected",
	    "ingressBytesTransferred": 33509044,
	    "egressBytesTransferred": 4142431
	  }

<!---HONumber=AcomDC_0406_2016-->
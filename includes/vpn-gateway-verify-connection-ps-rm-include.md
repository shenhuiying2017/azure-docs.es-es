Puede comprobar que la conexión se realizó correctamente mediante el uso del cmdlet "Get-AzureRmVirtualNetworkGatewayConnection", con o sin "-Debug". 

1. Puede usar el siguiente ejemplo de cmdlet, configurando los valores para que coincidan con los tuyos. Cuando se le pida, seleccione "A" para ejecutar "todo". En el ejemplo, " -Name" hace referencia al nombre de la conexión que desea probar.

  ```powershell
  Get-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName MyRG
  ```
2. Cuando el cmdlet haya finalizado, consulte los valores. En el ejemplo siguiente, el estado de conexión aparece como "Conectado" y pueden verse los bytes de entrada y salida.
   
  ```
  "connectionStatus": "Connected",
  "ingressBytesTransferred": 33509044,
  "egressBytesTransferred": 4142431
  ```
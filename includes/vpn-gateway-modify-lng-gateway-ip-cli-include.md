### <a name="to-modify-the-local-network-gateway-gatewayipaddress"></a>Para modificar la puerta de enlace de red local "gatewayIpAddress"

Si el dispositivo VPN al que desea conectarse ha cambiado su dirección IP pública, debe modificar la puerta de enlace de red local para reflejar ese cambio. La dirección IP de puerta de enlace se puede cambiar sin quitar una conexión de puerta de enlace VPN existente (si tiene una). Para modificar la dirección IP de puerta de enlace, reemplace los valores "Site2" y "TestRG1" por los suyos propios mediante el comando [az network local-gateway update](https://docs.microsoft.com/cli/azure/network/local-gateway#update).

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 --name Site2 --resource-group TestRG1
```

Compruebe que la dirección IP sea correcta en la salida:

```
"gatewayIpAddress": "23.99.222.170",
```
Puede comprobar que la conexión se realizó correctamente mediante el comando [az network vpn-connection show](/cli/azure/network/vpn-connection#show). Configure los valores para que coincidan con los suyos. En el ejemplo, " --name" hace referencia al nombre de la conexión que creó y quiere probar.

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```

Mientras la conexión termina de establecerse, su estado de conexión muestra el estado "Conectando". Una vez establecida, el estado cambia a "Conectado" y se pueden ver los bytes de entrada y salida.
Puede comprobar que la conexión se realizó correctamente mediante el comando [az network vpn-connection show](/cli/azure/network/vpn-connection#show). En el ejemplo, " --name" hace referencia al nombre de la conexión que desea probar. Mientras la conexión aún se está estableciendo, su estado de conexión muestra "Conectando". Una vez establecida la conexión, el estado cambia a "Conectado".

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```


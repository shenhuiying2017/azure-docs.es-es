Cree un grupo de recursos con el comando [az group create](/cli/azure/group#create).

[!INCLUDE [resource group intro text](resource-group.md)]

En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *westeurope*.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

Para ver las ubicaciones disponibles, ejecute el comando `az appservice list-locations`. Generalmente crea recursos en una región cercana.

En Cloud Shell, cree un grupo de recursos con el comando [az group create](/cli/azure/group?view=azure-cli-latest#az_group_create).

[!INCLUDE [resource group intro text](resource-group.md)]

En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *Europa Occidental*. Para ver todas las ubicaciones admitidas de App Service, ejecute el comando `az appservice list-locations`.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

Generalmente se crean el grupo de recursos y los recursos en una región cercana. 

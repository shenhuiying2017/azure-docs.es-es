## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos con el comando [az group create](/cli/azure/group#az_group_create). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran recursos de Azure como instancias de Function App, bases de datos y cuentas de almacenamiento.

En el ejemplo siguiente se crea un grupo de recursos denominado `myResourceGroup`.  
Si no usa Cloud Shell, inicie sesión primero con `az login`.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```
Generalmente se crean el grupo de recursos y los recursos en una región cercana. Para ver todas las ubicaciones compatibles con los planes de App Service, ejecute el comando [az appservice list-locations](/cli/azure/appservice#az_appservice_list_locations).
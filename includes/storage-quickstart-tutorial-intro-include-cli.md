## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos de Azure con el comando [az group create](/cli/azure/group#az_group_create). Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

Las cuentas de almacenamiento de uso general se crean con el comando [az storage account create](/cli/azure/storage/account#create). Las cuentas de almacenamiento de uso general se pueden para los cuatro servicios: blobs, archivos, tablas y colas. 

```azurecli-interactive
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --sku Standard_LRS \
    --encryption blob
```

## <a name="specify-storage-account-credentials"></a>Especificación de las credenciales de la cuenta de almacenamiento

La CLI de Azure necesita las credenciales de la cuenta de almacenamiento para la mayoría de los comandos en este tutorial. Aunque hay varias opciones para hacerlo, una de las formas más sencillas de especificarlas es establecer las variables de entorno `AZURE_STORAGE_ACCOUNT` y `AZURE_STORAGE_ACCESS_KEY`.

En primer lugar, muestre las claves de la cuenta de almacenamiento con el comando [az storage account keys list](/cli/azure/storage/account/keys#list):

```azurecli-interactive
az storage account keys list \
    --account-name mystorageaccount \
    --resource-group myResourceGroup \
    --output table
```

Ahora, establezca las variables de entorno `AZURE_STORAGE_ACCOUNT` y `AZURE_STORAGE_ACCESS_KEY`. Puede hacerlo en el shell de Bash utilizando el comando `export`:

```bash
export AZURE_STORAGE_ACCOUNT="mystorageaccountname"
export AZURE_STORAGE_ACCESS_KEY="myStorageAccountKey"
```
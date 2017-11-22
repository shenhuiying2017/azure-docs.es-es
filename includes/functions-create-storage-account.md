## <a name="create-an-azure-storage-account"></a>Creación de una cuenta de Azure Storage

Functions usa una cuenta de uso general en Azure Storage para mantener el estado y otra información acerca de sus funciones. Cree una cuenta de almacenamiento de uso general en el grupo de recursos que ha creado mediante el comando [az storage account create](/cli/azure/storage/account#create).

En el siguiente comando, sustituya un nombre de cuenta de almacenamiento único de manera global donde vea el marcador de posición `<storage_name>`. Los nombres de cuentas de almacenamiento deben tener entre 3 y 24 caracteres, y solo pueden contener números y letras minúsculas.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

Después de que se haya creado la cuenta de almacenamiento, la CLI de Azure muestra información similar al ejemplo siguiente:

```json
{
  "creationTime": "2017-04-15T17:14:39.320307+00:00",
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myresourcegroup/...",
  "kind": "Storage",
  "location": "westeurope",
  "name": "myfunctionappstorage",
  "primaryEndpoints": {
    "blob": "https://myfunctionappstorage.blob.core.windows.net/",
    "file": "https://myfunctionappstorage.file.core.windows.net/",
    "queue": "https://myfunctionappstorage.queue.core.windows.net/",
    "table": "https://myfunctionappstorage.table.core.windows.net/"
  },
     ....
    // Remaining output has been truncated for readability.
}
```
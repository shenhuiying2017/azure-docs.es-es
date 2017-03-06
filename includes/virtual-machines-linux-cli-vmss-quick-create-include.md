## <a name="prerequisites"></a>Requisitos previos

Si todavía no tiene, debe obtener una [evaluación gratuita de suscripción de Azure](https://azure.microsoft.com/pricing/free-trial/) e instalar la [CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="create-the-scale-set"></a>Creación del conjunto de escalado

En primer lugar, cree un grupo de recursos en el cual implementar el conjunto de escalado:

```azurecli
az group create --location westus --name myResourceGroup
```

Ahora cree un conjunto de escalado mediante el comando `az vmss create`. En el ejemplo siguiente se crea un conjunto de escalado de Linux denominado `myvmss` con&5; instancias de máquina virtual en el grupo de recursos denominado `myrg`:

```azurecli
az vmss create --resource-group myResourceGroup --name myVmss \
    --image UbuntuLTS --admin-username azureuser \
    --authentication-type password --admin-password P4$$w0rd
```

En el ejemplo siguiente se crea un conjunto de escalado de Windows con la misma configuración:

```azurecli
az vmss create --resource-group myResourceGroup --name myVmss \
    --image Win2016Datacenter --admin-username azureuser \
    --authentication-type password --admin-password P4$$w0rd
```

Si desea elegir una imagen de SO diferente, puede ver las imágenes disponibles con el comando `az vm image list` o `az vm image list --all`. Para ver la información de conexión de las máquinas virtuales en el conjunto de escalado, use el comando `az vmss list_instance_connection_info`:

```azurecli
az vmss list_instance_connection_info --resource-group myResourceGroup --name myVmss
```
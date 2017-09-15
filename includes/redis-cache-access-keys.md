Para establecer conexión con la instancia de Caché en Redis de Azure, los clientes de dicha caché necesitan el nombre de host, los puertos y las claves. Es posible que algunos clientes utilicen nombres ligeramente diferentes para estos elementos. Puede recuperar esta información en Azure Portal o mediante herramientas de línea de comandos como la CLI de Azure.

### <a name="retrieve-host-name-ports-and-access-keys-using-the-azure-portal"></a>Recuperación del nombre de host, los puertos y las claves de acceso mediante Azure Portal
Para recuperar el nombre de host, los puertos y las claves de acceso mediante Azure Portal, [acceda](../articles/redis-cache/cache-configure.md#configure-redis-cache-settings) a la memoria caché en [Azure Portal](https://portal.azure.com) y haga clic en **Claves de acceso** y **Propiedades** en el **menú de recursos**. 

![Configuración de caché en Redis](media/redis-cache-access-keys/redis-cache-hostname-ports-keys.png)

### <a name="retrieve-host-name-ports-and-access-keys-using-azure-cli"></a>Recuperación del nombre de host, puertos y claves de acceso mediante la CLI de Azure
Para recuperar el nombre de host y los puertos utilizando la CLI de Azure 2.0 puede llamar a [az redis show](https://docs.microsoft.com/cli/azure/redis#az_redis_show), y para recuperar las claves puede llamar a [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys). El script siguiente llama a estos dos comandos y transmite el nombre de host, puertos y claves en la consola.

```azurecli
#/bin/bash

# Retrieve the hostname, ports, and keys for contosoCache located in contosoGroup

# Retrieve the hostname and ports for an Azure Redis Cache instance
redis=($(az redis show --name contosoCache --resource-group contosoGroup --query [hostName,enableNonSslPort,port,sslPort] --output tsv))

# Retrieve the keys for an Azure Redis Cache instance
keys=($(az redis list-keys --name contosoCache --resource-group contosoGroup --query [primaryKey,secondaryKey] --output tsv))

# Display the retrieved hostname, keys, and ports
echo "Hostname:" ${redis[0]}
echo "Non SSL Port:" ${redis[2]}
echo "Non SSL Port Enabled:" ${redis[1]}
echo "SSL Port:" ${redis[3]}
echo "Primary Key:" ${keys[0]}
echo "Secondary Key:" ${keys[1]}
```

Para más información acerca de este script, consulte [Get the hostname, ports, and keys for Azure Redis Cache](../articles/redis-cache/scripts/cache-keys-ports.md) (Obtención del nombre de host, los puertos y las claves para Azure Redis Cache). Para más información sobre la CLI de Azure 2.0, consulte [Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (Instalación de la CLI de Azure 2.0) y [Get started with Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) (Introducción a la CLI de Azure 2.0).

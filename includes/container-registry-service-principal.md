## <a name="create-a-service-principal"></a>Creación de una entidad de servicio

Para crear a una entidad de servicio con acceso a su registro de contenedor, puede usar el siguiente script. Actualice la variable `ACR_NAME` con el nombre de su registro de contenedor y, de manera opcional, el valor `--role` del comando [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] para otorgar permisos diferentes. Debe tener la [CLI de Azure](/cli/azure/install-azure-cli) instalada para poder usar este script.

Tras ejecutar el script, tome nota del **identificador** y la **contraseña** de la entidad de servicio. Cuando disponga de las credenciales, podrá configurar las aplicaciones y los servicios para autenticarse en su registro de contenedor como la entidad de servicio.

```bash
#!/bin/bash

# Modify for your environment. The ACR_NAME is the name of your Azure Container
# Registry, and the SERVICE_PRINCIPAL_NAME can be any unique name within your
# subscription (you can use the default below).
ACR_NAME=myregistryname
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate some values required for subsequent command args
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create the service principal with rights scoped to the registry.
# Default permissions are for both docker push and pull access. Modify the
# '--role' argument value as desired:
# reader:      pull only
# contributor: push and pull
# owner:       push, pull, and assign roles
SP_PASSWD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME --scopes $ACR_REGISTRY_ID --role reader --query password --output tsv)
SP_APP_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output the service principal's credentials; use these in your services and
# applications to authenticate to the container registry.
echo "Service principal ID: $SP_APP_ID"
echo "Service principal password: $SP_PASSWD"
```

## <a name="use-an-existing-service-principal"></a>Uso de una entidad de servicio existente

Para conceder al registro acceso a una entidad de servicio existente, debe asignar un nuevo rol a la entidad de servicio. Al igual que con la creación de una nueva entidad de servicio, puede conceder acceso de extracción, inserción y extracción, así como de propietario.

El script siguiente usa el comando [az role assignment create][az-role-assignment-create] para conceder permisos de *extracción* a la entidad de servicio especificada en la variable `SERVICE_PRINCIPAL_ID`. Ajuste el valor `--role` si quiere conceder un nivel de acceso diferente.

```bash
#!/bin/bash

# Modify for your environment. The ACR_NAME is the name of your Azure Container
# Registry, and the SERVICE_PRINCIPAL_ID is the service principal's 'appId' or
# one of its 'servicePrincipalNames' values.
ACR_NAME=myregistryname
SERVICE_PRINCIPAL_ID=<service-principal-ID>

# Populate value required for subsequent command args
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Assign the desired role to the service principal. Modify the '--role' argument
# value as desired:
# reader:      pull only
# contributor: push and pull
# owner:       push, pull, and assign roles
az role assignment create --assignee $SERVICE_PRINCIPAL_ID --scope $ACR_REGISTRY_ID --role reader
```

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
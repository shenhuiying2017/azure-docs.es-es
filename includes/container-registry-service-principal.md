## <a name="create-a-service-principal"></a>Creación de una entidad de servicio

Para crear a una entidad de servicio con acceso a su registro de contenedor, puede usar el siguiente script. Actualice la variable `ACR_NAME` con el nombre de su registro de contenedor y, de manera opcional, el valor `--role` del comando [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] para otorgar permisos diferentes. Debe tener la [CLI de Azure](/cli/azure/install-azure-cli) instalada para poder usar este script.

Tras ejecutar el script, tome nota del **identificador** y la **contraseña** de la entidad de servicio. Cuando disponga de las credenciales, podrá configurar las aplicaciones y los servicios para autenticarse en su registro de contenedor como la entidad de servicio.

[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

## <a name="use-an-existing-service-principal"></a>Uso de una entidad de servicio existente

Para conceder al registro acceso a una entidad de servicio existente, debe asignar un nuevo rol a la entidad de servicio. Al igual que con la creación de una nueva entidad de servicio, puede conceder acceso de extracción, inserción y extracción, así como de propietario.

El script siguiente usa el comando [az role assignment create][az-role-assignment-create] para conceder permisos de *extracción* a la entidad de servicio especificada en la variable `SERVICE_PRINCIPAL_ID`. Ajuste el valor `--role` si quiere conceder un nivel de acceso diferente.

[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

Puede encontrar ambos scripts en GitHub, así como versiones de PowerShell:

* [CLI de Azure][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create

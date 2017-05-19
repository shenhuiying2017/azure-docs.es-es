Inicie sesión en la suscripción de Azure con el comando [az login](/cli/azure/#login) y siga las instrucciones de la pantalla. Para más información, consulte [Get started with Azure CLI 2.0](/cli/azure/get-started-with-azure-cli)(Introducción a la CLI de Azure 2.0).

```azurecli
az login
```

Si tiene más de una suscripción de Azure, enumere las suscripciones de la cuenta.

```azurecli
az account list --all
```

Especifique la suscripción que desea usar.

```azurecli
az account set --subscription <replace_with_your_subscription_id>
```
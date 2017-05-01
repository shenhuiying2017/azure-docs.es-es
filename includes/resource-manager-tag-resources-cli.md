Para agregar una etiqueta a un grupo de recursos, use el **conjunto de grupos de Azure**. Si el grupo de recursos no tiene etiquetas, pase la etiqueta.

```azurecli
azure group set -n tag-demo-group -t Dept=Finance
```

Las etiquetas se actualizan en conjunto. Si desea agregar una etiqueta a un grupo de recursos que ya tiene etiquetas, pase todas las etiquetas. 

```azurecli
azure group set -n tag-demo-group -t Dept=Finance;Environment=Production;Project=Upgrade
```

Los recursos de un grupo de recursos no heredan las etiquetas. Para agregar una etiqueta a un recurso, use el **conjunto de recursos de Azure**. Pase el número de versión de API para el tipo de recurso al que está agregando la etiqueta. Si necesita recuperar la versión de API, utilice el siguiente comando con el proveedor de recursos para el tipo que se va a establecer:

```azurecli
azure provider show -n Microsoft.Storage --json
```

En los resultados, busque el tipo de recurso que desee.

```azurecli
"resourceTypes": [
{
  "resourceType": "storageAccounts",
  ...
  "apiVersions": [
    "2016-01-01",
    "2015-06-15",
    "2015-05-01-preview"
  ]
}
...
```

Ahora, proporcione esa versión de API, el nombre del grupo de recursos, el nombre del recurso, el tipo de recurso y el valor de etiqueta como parámetros.

```azurecli
azure resource set -g tag-demo-group -n storagetagdemo -r Microsoft.Storage/storageAccounts -t Dept=Finance -o 2016-01-01
```

Las etiquetas existen directamente en los recursos y grupos de recursos. Para ver las etiquetas existentes, obtenga un grupo de recursos y sus recursos con **azure group show**.

```azurecli
azure group show -n tag-demo-group --json
```

Que devuelve metadatos sobre el grupo de recursos, incluidas las etiquetas aplicadas al mismo.

```azurecli
{
  "id": "/subscriptions/4705409c-9372-42f0-914c-64a504530837/resourceGroups/tag-demo-group",
  "name": "tag-demo-group",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "location": "southcentralus",
  "tags": {
    "Dept": "Finance",
    "Environment": "Production",
    "Project": "Upgrade"
  },
  ...
}
```

Para ver las etiquetas de un recurso específico, use **azure resource show**.

```azurecli
azure resource show -g tag-demo-group -n storagetagdemo -r Microsoft.Storage/storageAccounts -o 2016-01-01 --json
```

Para recuperar todos los recursos con un valor de etiqueta, utilice:

```azurecli
azure resource list -t Dept=Finance --json
```

Para recuperar todos los grupos de recursos con un valor de etiqueta, utilice:

```azurecli
azure group list -t Dept=Finance
```

Puede ver las etiquetas existentes en su suscripción con el siguiente comando:

```azurecli
azure tag list
```

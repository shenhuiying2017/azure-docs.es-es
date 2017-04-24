## <a name="clean-up-deployment"></a>Limpieza de la implementación 

Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar el grupo de recursos, las instancias de Azure Redis Cache y todos los recursos relacionados en el grupo de recursos.

```azurecli
az group delete --name contosoGroup
```
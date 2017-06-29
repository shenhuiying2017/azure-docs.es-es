En los pasos anteriores, creó recursos de Azure en un grupo de recursos. Si no cree que vaya a necesitar estos recursos en el futuro, puede eliminarlos mediante la eliminación del grupo de recursos.
 
1. Ejecute el comando siguiente para asegurarse de que el grupo de recursos no incluya recursos que desee guardar:

  ```azurecli
  az group show --name myResourceGroup
  ```

2. Si los recursos que se muestran son todos elementos que desea eliminar, ejecute el siguiente comando:
 
  ```azurecli
  az group delete --name myResourceGroup
  ```

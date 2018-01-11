En [Azure Portal](https://portal.azure.com), haga clic en **Grupos de recursos** > **cloud-shell-storage-\<su_región>** > **\<storage_account_name>**.

![Búsqueda de la cuenta de almacenamiento de Cloud Shell](../articles/app-service/media/app-service-deploy-zip/upload-choose-storage-account.png)

En la página **Información general** de la cuenta de almacenamiento, seleccione **Archivos**.

Seleccione el recurso compartido de archivos generado automáticamente y seleccione **Cargar**. Este recurso compartido de archivos está montado en Cloud Shell como `clouddrive`.

![Búsqueda del botón Cargar](../articles/app-service/media/app-service-deploy-zip/upload-select-button.png)

Haga clic en el selector de archivos, seleccione el archivo ZIP y haga clic en **Cargar**. 

En Cloud Shell, use `ls` para comprobar que puede ver el archivo ZIP cargado en el recurso compartido `clouddrive` predeterminado.

```azurecli-interactive
ls clouddrive
```

## <a name="deploy-template-from-cloud-shell"></a>Implementación de una plantilla desde Cloud Shell

Puede usar [Cloud Shell](../articles/cloud-shell/overview.md) para implementar la plantilla. Pero primero debe cargar la plantilla en el recurso compartido de archivos de Cloud Shell. Si no ha usado Cloud Shell, vea [Introducción a Azure Cloud Shell](../articles/cloud-shell/overview.md) para más información sobre su configuración.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

1. Seleccione el grupo de recursos de Cloud Shell. El patrón de nombre es `cloud-shell-storage-<region>`.

   ![Selección de un grupo de recursos](./media/resource-manager-cloud-shell-deploy/select-cs-resource-group.png)

1. Seleccione la cuenta de almacenamiento de Cloud Shell.

   ![Selección de la cuenta de almacenamiento](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Seleccione **Archivos**.

   ![Seleccionar archivos](./media/resource-manager-cloud-shell-deploy/select-files.png)

1. Seleccione el recurso compartido de archivos de Cloud Shell. El patrón de nombre es `cs-<user>-<domain>-com-<uniqueGuid>`.

   ![Selección de recurso compartido de archivos](./media/resource-manager-cloud-shell-deploy/select-file-share.png)

1. Seleccione **Agregar directorio**.

   ![Agregar directorio](./media/resource-manager-cloud-shell-deploy/select-add-directory.png)

1. Asígnele el nombre **plantillas** y seleccione **Correcto**.

   ![Nombre de directorio](./media/resource-manager-cloud-shell-deploy/name-templates.png)

1. Seleccione el nuevo directorio.

   ![Selección de directorio](./media/resource-manager-cloud-shell-deploy/select-templates.png)

1. Seleccione **Cargar**.

   ![Selección de carga](./media/resource-manager-cloud-shell-deploy/select-upload.png)

1. Busque y cargue la plantilla.

   ![Carga de archivo](./media/resource-manager-cloud-shell-deploy/upload-files.png)

1. Abra el símbolo del sistema.

   ![Apertura de Cloud Shell](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)

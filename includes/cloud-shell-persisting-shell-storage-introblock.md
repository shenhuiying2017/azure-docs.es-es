# <a name="persist-files-in-azure-cloud-shell"></a>Persistencia de archivos en Azure Cloud Shell
Cloud Shell utiliza Azure File Storage para conservar los archivos entre sesiones.

## <a name="set-up-a-clouddrive-file-share"></a>Configuración de un recurso compartido de archivos de clouddrive
En el primer inicio, Cloud Shell le pedirá que asocie un recurso compartido de archivos nuevo o existente para conservar los archivos entre sesiones.

> [!NOTE]
> Bash y PowerShell comparten el mismo recurso compartido de archivos. Solo un recurso compartido de archivos se puede asociar con el montaje automático en Cloud Shell.

### <a name="create-new-storage"></a>creación de nuevo almacenamiento

Al usar la configuración básica y seleccionar solo una suscripción, Cloud Shell crea tres recursos en su nombre en la región admitida que esté más próxima:
* Grupos de recursos: `cloud-shell-storage-<region>`
* Cuenta de almacenamiento: `cs<uniqueGuid>`
* Recurso compartido de archivos: `cs-<user>-<domain>-com-<uniqueGuid>`

![La configuración de la suscripción](../articles/cloud-shell/media/persisting-shell-storage/basic-storage.png)

El recurso compartido de archivos se monta como `clouddrive` en su directorio `$Home`. Se trata de una acción única y el recurso compartido de archivos se monta automáticamente en sesiones posteriores. 

En Bash, el recurso compartido de archivos también contiene una imagen de 5 GB que se crea para que el usuario conserve automáticamente los datos en su directorio `$Home`. 

### <a name="use-existing-resources"></a>Uso de recursos existentes

Mediante la opción avanzada puede asociar recursos existentes. Cuando aparezca el mensaje del programa de instalación, seleccione **Mostrar configuración avanzada** para ver otras opciones. Las listas desplegables se filtran para la región asignada de Cloud Shell y las cuentas de almacenamiento con redundancia local y de almacenamiento con redundancia geográfica.

En Bash, los recursos compartidos de archivos existentes reciben una imagen de 5 GB para que el usuario conserve el directorio `$Home`.

![La configuración de grupo de recursos](../articles/cloud-shell/media/persisting-shell-storage/advanced-storage.png)

### <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Restringir la creación de recursos con una directiva de recursos de Azure
Las cuentas de almacenamiento creadas en Cloud Shell se etiquetan con `ms-resource-usage:azure-cloud-shell`. Si quiere impedir que los usuarios creen cuentas de almacenamiento en Cloud Shell, cree una [directiva de recursos de Azure para etiquetas](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy-tags) que se desencadene mediante esta etiqueta específica.

## <a name="supported-storage-regions"></a>Regiones de almacenamiento admitidas
Las cuentas de almacenamiento de Azure asociadas tienen que residir en la misma región que la máquina de Cloud Shell en la que se montan.

Para buscar la región asignada debe:
* Leer la nota en el cuadro de diálogo "Configuración de almacenamiento avanzada"
* Consultar el nombre de la cuenta de almacenamiento creada por usted (p. ej: `cloud-shell-storage-westus`)
* Ejecutar `env` y localizar la variable `ACC_LOCATION`

La máquina de Cloud Shell existe en las regiones siguientes:
|Ámbito|Region|
|---|---|
|América|Este de EE. UU., centro-sur de EE. UU. y oeste de EE. UU.|
|Europa|Norte de Europa y Oeste de Europa|
|Asia Pacífico|India central, Sudeste Asiático|


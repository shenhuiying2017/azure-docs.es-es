## <a name="prerequisites"></a>Requisitos previos

### <a name="azure-subscription"></a>Suscripción de Azure
Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

### <a name="azure-roles"></a>Roles de Azure
Para crear instancias de Data Factory, la cuenta de usuario que use para iniciar sesión en Azure debe ser un miembro de los roles **colaborador** o **propietario**, o de **administrador** de la suscripción de Azure. En Azure Portal, haga clic en el **nombre de usuario**, en la esquina superior derecha, y seleccione **Permisos** para ver los permisos que tiene en la suscripción. Si tiene acceso a varias suscripciones, elija la correspondiente. Para obtener instrucciones de ejemplo sobre cómo agregar un usuario a un rol, consulte el artículo sobre la [adición de roles](../articles/billing/billing-add-change-azure-subscription-administrator.md).

### <a name="azure-storage-account"></a>Cuenta de Azure Storage
En esta guía de inicio rápido, use una cuenta de Azure Storage (en concreto Blob Storage) de uso general como almacén de datos de **origen** y **destino**. Si no dispone de una cuenta de Azure Storage de uso general, consulte el artículo [Creación de una cuenta de almacenamiento](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account), donde se indica cómo crearla. 

#### <a name="get-storage-account-name-and-account-key"></a>Obtener un nombre y una clave de cuenta de Storage
En esta guía de inicio rápido use el nombre y la clave de su cuenta de Azure Storage. El siguiente procedimiento especifica los pasos necesarios para obtener el nombre y la clave de una cuenta de Storage. 

1. Inicie un explorador web y navegue hasta [Azure Portal](https://portal.azure.com). Inicie sesión con su nombre de usuario y contraseña de Azure. 
2. Haga clic en **Más servicios>** en el menú de la izquierda, filtre por la palabra clave **Almacenamiento** y seleccione **Cuentas de almacenamiento**.

    ![Buscar cuenta de Storage](media/data-factory-quickstart-prerequisites/search-storage-account.png)
3. En la lista de cuentas de Storage, filtre por su cuenta de almacenamiento (si fuera necesario) y, después, seleccione **su cuenta de Storage**. 
4. En la página **Cuenta de almacenamiento**, seleccione **Claves de acceso** en el menú.

    ![Obtener nombre y clave de la cuenta de Storage](media/data-factory-quickstart-prerequisites/storage-account-name-key.png)
5. Copie los valores de los campos **Nombre de cuenta de almacenamiento** y **clave1** en el Portapapeles. Péguelos en el Bloc de notas o en cualquier otro editor y guárdelo. Los usará más adelante en esta guía.   

#### <a name="create-input-folder-and-files"></a>Creación de archivos y carpetas de entrada
En esta sección se crea un contenedor de blobs denominado **adftutorial** en la instancia de Azure Blob Storage. Luego, cree una carpeta denominada **entrada** en el contenedor y cargue un archivo de ejemplo en la carpeta de entrada. 

1. En la página **Cuenta de almacenamiento**, cambie a **Información general** y, a continuación, haga clic en **Blobs**. 

    ![Selección de la opción Blobs](media/data-factory-quickstart-prerequisites/select-blobs.png)
2. En la página **Blob service**, haga clic en **+ Contenedor** en la barra de herramientas. 

    ![Botón Agregar contenedor](media/data-factory-quickstart-prerequisites/add-container-button.png)    
3. En el cuadro de diálogo **Nuevo contenedor**, escriba **adftutorial** para el nombre y haga clic en **Aceptar**. 

    ![Especificación de un nombre de contenedor](media/data-factory-quickstart-prerequisites/new-container-dialog.png)
4. Haga clic en **adftutorial** en la lista de contenedores. 

    ![Selección del contenedor](media/data-factory-quickstart-prerequisites/seelct-adftutorial-container.png)
1. En la página **Contenedor**, haga clic en **Cargar** en la barra de herramientas.  

    ![Botón Cargar](media/data-factory-quickstart-prerequisites/upload-toolbar-button.png)
6. En la página **Cargar blob**, haga clic en **Avanzado**.

    ![Hacer clic en el vínculo Avanzado](media/data-factory-quickstart-prerequisites/upload-blob-advanced.png)
7. Inicie **Bloc de notas** y cree un archivo denominado **emp.txt** con el contenido siguiente. Luego guárdelo en la carpeta **c:\ADFv2QuickStartPSH**. Cree la carpeta **ADFv2QuickStartPSH** si aún no existe.
    
    ```
    John, Doe
    Jane, Doe
    ```    
8. En Azure Portal, en la página **Cargar blob**, busque y seleccione el archivo **emp.txt** para el campo **Archivos**. 
9. Escriba **entrada** como valor de **Cargar en carpeta**. 

    ![Carga de la configuración del blob](media/data-factory-quickstart-prerequisites/upload-blob-settings.png)    
10. Confirme que la carpeta es **entrada** y el archivo es **emp.txt**, y haga clic en **Cargar**.
11. Debería ver el archivo **emp.txt** y el estado de la carga en la lista. 
12. Cierre la página **Cargar blob** haciendo clic en **X**, en la esquina. 

    ![Cierre de la página de carga del blob](media/data-factory-quickstart-prerequisites/close-upload-blob.png)
1. Mantenga abierta la página **Contenedor**. Úsela para comprobar la salida al final de esta guía de inicio rápido.
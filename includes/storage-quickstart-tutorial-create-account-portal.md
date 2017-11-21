## <a name="create-a-storage-account-using-the-azure-portal"></a>Creación de una cuenta de almacenamiento mediante Azure Portal

En primer lugar, cree una nueva cuenta de almacenamiento de uso general que se utilizará para este tutorial de inicio rápido. 

1. Vaya a [Azure Portal](https://portal.azure.com) e inicie sesión con su cuenta de Azure. 
2. En el menú de concentrador, seleccione **Nuevo** > **Almacenamiento** > **Cuenta de almacenamiento: blob, archivo, tabla, cola**. 
3. Escriba un nombre único para la cuenta de almacenamiento. Tenga en cuenta estas reglas a la hora de poner nombre a la cuenta de almacenamiento:
    - El nombre debe tener entre 3 y 24 caracteres.
    - El nombre puede contener solo números y letras minúsculas.
4. Asegúrese de que se establecen los valores predeterminados siguientes: 
    - El **Modelo de implementación** es **Resource Manager**.
    - El **Tipo de cuenta** es **Uso general**.
    - El **Rendimiento** es **Estándar**.
    - La **Replicación** es **Almacenamiento con redundancia local (LRS)**.
5. Seleccione su suscripción. 
6. Para **Grupo de recursos**, cree uno nuevo y asígnele un nombre único. 
7. Seleccione la **Ubicación** que desee usar para la cuenta de almacenamiento.
8. Active **Anclar al panel** y haga clic en **Crear** para crear la cuenta de almacenamiento. 

Después de crear la cuenta de almacenamiento, se ancla al panel. Haga clic en ella para abrirla. En **Configuración**, haga clic en **Claves de acceso**. Seleccione la clave principal y copie la **Cadena de conexión** asociada en el Portapapeles y péguela en un editor de texto para utilizarla posteriormente.

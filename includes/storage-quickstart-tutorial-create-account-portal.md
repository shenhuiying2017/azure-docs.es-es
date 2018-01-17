## <a name="create-a-storage-account-by-using-the-azure-portal"></a>Creación de una cuenta de almacenamiento mediante Azure Portal

En primer lugar, cree una nueva cuenta de almacenamiento de uso general que se utilizará para este tutorial de inicio rápido. 

1. Vaya a [Azure Portal](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM) e inicie sesión con su cuenta de Azure. 
2. Escriba un nombre único para la cuenta de almacenamiento. Tenga en cuenta estas reglas a la hora de poner nombre a la cuenta de almacenamiento:
    - El nombre debe tener entre 3 y 24 caracteres.
    - El nombre solo puede contener números y letras minúsculas.
3. Asegúrese de que se establecen los valores predeterminados siguientes: 
    - El **Modelo de implementación** se establece en **Resource Manager**.
    - El **Tipo de cuenta** es **Uso general**.
    - El **Rendimiento** es **Estándar**.
    - La **Replicación** es **Almacenamiento con redundancia local (LRS)**.
4. Seleccione su suscripción. 
5. Para **Grupo de recursos**, cree uno nuevo y asígnele un nombre único. 
6. Seleccione la ubicación que desee usar para la cuenta de almacenamiento.
7. Seleccione **Anclar al panel** y **Crear** para crear la cuenta de almacenamiento. 

Después de crear la cuenta de almacenamiento, se ancla al panel. Selecciónela para abrirla. En **Configuración**, seleccione **Claves de acceso**. Seleccione la clave principal y copie la cadena de conexión asociada en el Portapapeles. A continuación, pegue la cadena en un editor de texto para usarla posteriormente.

### <a name="windows-powershell"></a>Windows PowerShell

#### <a name="install-powershell"></a>Instale PowerShell.
Instale la versión más reciente de PowerShell si no se encuentra en el equipo. 

1. En el explorador web, navegue hasta la página [SDK y descargas de Azure SDK](https://azure.microsoft.com/downloads/). 
2. Haga clic en **Instalación de Windows** en la sección **Herramientas de línea de comandos** -> **PowerShell**. 
3. Para instalar PowerShell, ejecute el archivo **MSI**. 

Para obtener instrucciones detalladas, consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-powershell"></a>Inicio de sesión en PowerShell

1. Inicie **PowerShell** en su equipo. Mantenga PowerShell abierto hasta el final de esta guía de inicio rápido. Si lo cierra y vuelve a abrirlo, deberá ejecutar los comandos de nuevo.

    ![Inicie PowerShell](media/data-factory-quickstart-prerequisites-2/search-powershell.png)
1. Ejecute el siguiente comando y escriba los mismos nombre de usuario y contraseña de Azure que utiliza para iniciar sesión en Azure Portal:
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. Si tiene varias suscripciones de Azure, ejecute el siguiente comando para ver todas las suscripciones de esta cuenta:

    ```powershell
    Get-AzureRmSubscription
    ```
3. Ejecute el comando siguiente para seleccionar la suscripción con la que desea trabajar. Reemplace **SubscriptionId** con el identificador de la suscripción de Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

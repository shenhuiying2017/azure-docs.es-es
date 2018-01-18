## <a name="set-up-the-development-environment"></a>Configuración del entorno de desarrollo

En esta sección se explica cómo configurar el entorno de desarrollo. Se incluyen la creación de una aplicación de ASP.NET MVC, la incorporación de una conexión de servicios conectados, la incorporación de un controlador y la especificación de las directivas de espacio de nombres necesarias.

### <a name="create-an-aspnet-mvc-app-project"></a>Creación de un proyecto ASP.NET MVC

1. Abra Visual Studio.

1. En el menú principal, seleccione **Archivo** > **Nuevo** > **Proyecto**.

1. En el cuadro de diálogo **Nuevo proyecto**, seleccione **Web** > **Aplicación web ASP.NET (.NET Framework)**. En el campo **Nombre**, especifique **StorageAspNet**. Seleccione **Aceptar**.

    ![Captura de pantalla del cuadro de diálogo Nuevo proyecto](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. En el cuadro de diálogo **Nueva aplicación web ASP.NET**, seleccione **MVC** y, a continuación, **Aceptar**.

    ![Captura de pantalla del cuadro de diálogo Nueva aplicación web ASP.NET](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Uso de servicios conectados para conectarse a una cuenta de Azure Storage

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto.

2. En el menú contextual, seleccione **Agregar** > **Servicio conectado**.

1. En el cuadro de diálogo **Servicios conectados**, seleccione **Almacenamiento en la nube con Azure Storage** y luego **Configurar**.

    ![Captura de pantalla del cuadro de diálogo Servicios conectados](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. En el cuadro de diálogo **Azure Storage**, seleccione la cuenta de Azure Storage que se usará en este tutorial. Para crear una nueva cuenta de Azure Storage, seleccione **Crear una nueva cuenta de almacenamiento** y rellene el formulario. Después de seleccionar una cuenta de almacenamiento existente o de crear una nueva, seleccione **Agregar**. Visual Studio instala el paquete NuGet de Azure Storage y una cadena de conexión de almacenamiento a **Web.config**.

> [!TIP]
> Para aprender a crear una cuenta de almacenamiento con [Azure Portal](https://portal.azure.com), consulte [Crear una cuenta de almacenamiento](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account).
>
> También se puede crear una cuenta de Azure Storage mediante [Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), la [CLI de Azure](../articles/storage/common/storage-azure-cli.md) o [Azure Cloud Shell](../articles/cloud-shell/overview.md).


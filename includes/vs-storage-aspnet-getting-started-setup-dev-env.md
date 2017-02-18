## <a name="set-up-the-development-environment"></a>Configuración del entorno de desarrollo

En esta sección se explica la configuración de su entorno de desarrollo, incluida la creación de una aplicación de ASP.NET MVC, la adición de una conexión de Servicios conectados, la adición de un controlador y la especificación de las directivas de espacio de nombres necesarios.

### <a name="create-an-aspnet-mvc-app-project"></a>Creación de un proyecto ASP.NET MVC

1. Abra Visual Studio.

1. Seleccione **Archivo-> Nuevo-> Proyecto** en el menú principal

1. En el cuadro de diálogo **Nuevo proyecto**, especifique las opciones tal y como se resalta en la ilustración siguiente:

    ![Creación de un proyecto ASP.NET](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. Seleccione **Aceptar**.

1. En el cuadro de diálogo **Nuevo proyecto ASP.NET**, especifique las opciones tal y como se resalta en la ilustración siguiente:

    ![Especificación de MVC](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

1. Seleccione **Aceptar**.

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Uso de Servicios conectados para conectarse a una cuenta de almacenamiento de Azure

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y, en el menú contextual, seleccione **Agregar > Servicio conectado**.

1. En el cuadro de diálogo **Agregar servicio conectado**, seleccione **Azure Storage** y seleccione **Configurar**.

    ![Cuadro de diálogo Servicio conectado](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. En el cuadro de diálogo **Azure Storage**, seleccione la cuenta de almacenamiento de Azure deseada con la que desea trabajar y seleccione **Agregar**.


<!--HONumber=Dec16_HO3-->



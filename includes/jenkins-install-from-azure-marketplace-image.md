1. En el explorador, abra la [imagen de Azure Marketplace para Jenkins](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview).

1. Seleccionar **Obtenerla ahora**.

    ![Seleccione Obtenerla ahora para iniciar el proceso de instalación de la imagen de Jenkins Marketplace.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-get-it-now.png)

1. Después de revisar la información acerca de los precios, términos y detalles, seleccione **Continuar**.

    ![Información acerca de los precios y términos de la imagen de Jenkins Marketplace.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-pricing-and-terms.png)

1. Seleccione **Crear** para configurar el servidor de Jenkins en Azure Portal. 

    ![Instale la imagen de Jenkins Marketplace.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-create.png)

1. En la pestaña **Aspectos básicos**, especifique los siguientes valores:

    - **Nombre**: escriba `Jenkins`.
    - **Usuario**: escriba el nombre de usuario que se utilizará al iniciar sesión en la máquina virtual en la que se ejecuta Jenkins.
    - **Tipo de autenticación**: seleccione **Contraseña**.
    - **Contraseña**: escriba la contraseña que se utilizará al iniciar sesión en la máquina virtual en la que se ejecuta Jenkins.
    - **Confirmar contraseña**: vuelva a escribir la contraseña que se utilizará al iniciar sesión en la máquina virtual en la que se ejecuta Jenkins.
    - **Jenkins release type** (Tipo de versión de Jenkins): seleccione **LTS**.
    - **Suscripción**: seleccione la suscripción de Azure en la que quiere instalar Jenkins.
    - **Grupo de recursos**: seleccione **Crear nuevo** y escriba un nombre para el grupo de recursos que actúa como contenedor lógico para la colección de recursos que componen la instalación Jenkins.
    - **Ubicación**: seleccione **Este de EE. UU.**.

    ![Escriba la información del grupo de recursos y la autenticación de Jenkins en la pestaña Aspectos básicos.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-basic.png)

1. Seleccione **Aceptar** para continuar a la pestaña **Configuración**. 

1. En la pestaña **Configuración**, especifique los siguientes valores:

    - **Tamaño**: seleccione la opción de tamaño correcta para la máquina virtual de Jenkins.
    - **Tipo de disco de la máquina virtual**: especifique cualquier HDD (unidad de disco duro) o SSD (unidad de estado sólido) para indicar qué tipo de disco de almacenamiento se permite para la máquina virtual de Jenkins.
    - **Dirección IP pública**: por defecto, el nombre de la dirección IP es el nombre de Jenkins que especificó en la página anterior con el sufijo -IP. Puede seleccionar la opción para cambiar este comportamiento predeterminado.
    - **Etiqueta de nombre de dominio**: especifique el valor para la dirección URL completa a la máquina virtual de Jenkins.

    ![Especifique la configuración de la máquina virtual para Jenkins en la pestaña Configuración.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-settings.png)

1. Seleccione **Aceptar** para continuar a la pestaña **Resumen**.

1. Cuando se muestra la pestaña **Resumen**, se valida la información especificada. Cuando reciba el mensaje **Validación superada**, haga clic en **Aceptar**. 

    ![La pestaña Resumen muestra y valida las opciones seleccionadas.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-summary.png)

1. Cuando se muestre la pestaña **Crear**, seleccione **Crear** para crear la máquina virtual de Jenkins. Cuando el servidor esté listo, se mostrará una notificación en Azure Portal.

    ![Notificación de que Jenkins está listo.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-notification.png)
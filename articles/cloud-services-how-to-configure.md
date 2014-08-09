<properties linkid="manage-services-how-to-configure-a-cloud-service" urlDisplayName="How to configure" pageTitle="How to configure a cloud service - Azure" metaKeywords="Configuring cloud services" description="Learn how to configure cloud services in Azure. Learn to update the cloud service configuration and configure remote access to role instances." metaCanonical="" services="cloud-services" documentationCenter="" title="How to Configure Cloud Services" authors="davidmu" solutions="" manager="" editor="" />

Configuración de servicios en la nube
=====================================

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

Puede configurar la mayoría de los ajustes más usados para un servicio en la nube en el Portal de administración de Azure. O bien, si desea actualizar los archivos de configuración directamente, descargue un archivo de configuración de servicio para actualizar y, a continuación, cargue el archivo actualizado y actualice el servicio en la nube con los cambios en la configuración. De cualquier manera, las actualizaciones de la configuración se realizan en todas las instancias de rol.

También puede habilitar una conexión de Escritorio remoto con uno o todos los roles que se ejecutan en su servicio en la nube. Escritorio remoto le permite tener acceso al escritorio de su aplicación mientras se ejecuta, además de solucionar y diagnosticar problemas. Puede habilitar una conexión de Escritorio remoto con su rol incluso si no configuró el archivo de definición de servicio (.csdef) para el Escritorio remoto durante el desarrollo de la aplicación. No es necesario volver a implementar su aplicación para habilitar una conexión de Escritorio remoto.

Azure solo puede asegurar un 99,95 % de disponibilidad del servicio durante las actualizaciones de la configuración si tiene al menos dos instancias de rol (máquinas virtuales) para cada rol. Esto permite que una máquina virtual procese las solicitudes del cliente mientras la otra se actualiza. Para obtener más información, consulte [Contratos de nivel de servicio](https://www.windowsazure.com/en-us/support/legal/sla/).

Tabla de contenido
------------------

-   [Actualización de la configuración del servicio en la nube](#update)
-   [Configuración del acceso remoto a las instancias de rol](#remoteaccess)

Actualización de la configuración del servicio en la nube
---------------------------------------------------------

1.  En el [Portal de administración de Azure](http://manage.windowsazure.com/), haga clic en **Servicios en la nube**. A continuación, haga clic en el nombre del servicio en la nube para abrir el panel.

2.  Haga clic en **Configure**.

    En la página **Configure**, puede configurar la supervisión, actualizar la configuración de roles y seleccionar el sistema operativo invitado y la familia para las instancias de rol (máquinas virtuales).

    ![Página de configuración](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage1.png)

3.  En la configuración de supervisión, establezca el nivel de supervisión en Verbose o Minimal, y configure las cadenas de conexión del diagnóstico que se requieren para la supervisión detallada. Para obtener instrucciones, consulte [Supervisión de servicios en la nube](../how-to-monitor-a-cloud-service/).

4.  Para los roles de servicio (agrupados por rol), puede actualizar la siguiente configuración:

    > -   **Configuración** Modifique los valores de los ajustes de la configuración diversa que se especifican en los elementos *ConfigurationSettings* del archivo de configuración del servicio (.cscfg).

    > -   **Certificados** Cambie la huella digital del certificado que se está usando en el cifrado SSL para un rol. Para cambiar un certificado, debe primero cargar el certificado nuevo (en la página **Certificados**). Posteriormente, actualice la huella digital en la cadena de certificado que aparece en la configuración de roles.

5.  En la **configuración del sistema operativo**, puede cambiar la familia o la versión del sistema operativo para las instancias de rol (máquinas virtuales) o seleccionar **Automatic** para reanudar las actualizaciones automáticas de la versión del sistema operativo actual. La configuración del sistema operativo se aplica a los roles web de los roles de trabajo, pero no afectan a los roles de las máquinas virtuales que se agregaron a los servicios hospedados en el Portal de administración de Azure anterior.

    Al implementar un servicio o en la nube nuevo, puede seleccionar el sistema operativo Windows Server 2008 R2, Windows Server 2008 con Service Pack 2 (SP2) o Windows Server 2012. Durante la implementación, se instala la versión más reciente del sistema operativo en todas las instancias de rol y los sistemas operativos se actualizan automáticamente de manera predeterminada.

    Si debido a los requisitos de compatibilidad en su código necesita una versión diferente del sistema operativo para que su servicio en la nube pueda ejecutarse, puede seleccionar una familia y versión del sistema operativo. Al elegir una versión específica del sistema operativo, se suspenden las actualizaciones automáticas del sistema operativo para el servicio en la nube. Deberá asegurarse de que los sistemas operativos reciban las actualizaciones.

    Si soluciona todos los problemas de compatibilidad que tienen sus aplicaciones con la versión más reciente del sistema operativo, puede reanudar las actualizaciones automáticas del sistema operativo al establecer la versión del sistema operativo en **Automatic**.

    ![Configuración del SO](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage_OSSettings.png)

6.  Para guardar los ajustes de configuración y transmitirlos a las instancias de rol, haga clic en **Save**. (Haga clic en **Discard** para cancelar los cambios). Se agregan **Save** y **Discard** a la barra de comandos después de cambiar un ajuste.

### Para actualizar un archivo de configuración del servicio en la nube manualmente

1.  Descargue un archivo de configuración del servicio en la nube (.cscfg) con la configuración actual. En la página **Configure** del servicio en la nube, haga clic en **Download**. A continuación, haga clic en **Save** o en **Save As** para guardar el archivo.

2.  Después de actualizar el archivo de configuración del servicio, cargue y aplique las actualizaciones de la configuración:

    a. En la página **Configure**, haga clic en **Upload**.

    Se abre **Cargar un nuevo archivo de configuración**.

    ![Cargar configuración](./media/cloud-services-how-to-configure/CloudServices_UploadConfigFile.png)

    b. En **Archivo de configuración**, utilice **Browse** para seleccionar el archivo .cscfg actualizado.

    c. Si su servicio en la nube contiene cualquier rol que tiene una sola instancia, active la casilla **Apply configuration even if one or more roles contain a single instance** para permitir que continúen las actualizaciones de configuración para los roles.

    A menos que defina como mínimo dos instancias de cada rol, Azure no puede garantizar una disponibilidad de su servicio en la nube de al menos un 99,95 % durante las actualizaciones de la configuración del servicio. Para obtener más información, consulte [Contratos de nivel de servicio](http://www.windowsazure.com/es-es/support/legal/sla/).

    d. Haga clic en OK (marca de verificación).

Configuración del acceso remoto a las instancias de rol
-------------------------------------------------------

Escritorio remoto le permite tener acceso al escritorio de un rol que se ejecuta en Azure. Puede usar la conexión de Escritorio remoto para solucionar y diagnosticar problemas con su aplicación mientras se ejecuta. Puede habilitar una conexión de Escritorio remoto en su rol durante el diseño de la aplicación o después de que haya implementado la aplicación en Azure (mientras el rol se está ejecutando). La habilitación de una conexión de Escritorio remoto en un rol en ejecución a través del Portal de administración no requiere que vuelva a implementar la aplicación. Para autenticar la conexión de Escritorio remoto puede usar un certificado que se haya cargado anteriormente o puede crear un certificado nuevo.

En la pagina **Configure** de su servicio en la nube, puede habilitar el Escritorio remoto o cambiar la cuenta o la contraseña de Administrador local que se usan para conectarse a las máquinas virtuales, el certificado que se usa en la autenticación o la fecha de caducidad.

**Nota:**

Si su servicio en la nube consta de dos o más máquinas virtuales conectadas basadas en Windows Server, no es necesario que configure el acceso remoto debido a que estas maquinas virtuales se configuran automáticamente para el Escritorio remoto.

### Para configurar el Acceso remoto en el archivo de definición del servicio

Agregue elementos de **Import** al archivo de definición de servicio (.csdef) para importar los módulos RemoteAccess y RemoteForwarder al modelo de servicio. Cuando esos módulos están presentes, Azure agrega los ajustes de configuración para Escritorio remoto en el archivo de configuración del servicio. Para completar la configuración de Escritorio remoto, necesitará importar un certificado a Azure y especificar dicho certificado en el archivo de configuración del servicio. Para obtener más información, consulte [Establecer una conexión a Escritorio remoto para un rol de Azure](http://msdn.microsoft.com/es-es/library/windowsazure/hh124107.aspx).

### Para habilitar o modificar el Acceso remoto para las instancias de rol en el Portal de administración

1.  Inicie sesión en el [Portal de administración](http://manage.windowsazure.com/) y haga clic en **Servicios en la nube**. A continuación, haga clic en el nombre del servicio en la nube para abrir el panel.

2.  Abra la página **Configure** para el servicio en la nube y haga clic en **Remote**.

    La sección **Configure Remote Desktop** muestra los ajustes (de haberlos) que se agregaron al archivo de configuración del servicio cuando se implementó el servicio en la nube, como se muestra a continuación.

    ![Servicios en la nube remotos](./media/cloud-services-how-to-configure/CloudServices_Remote.png)

> [WACOM.NOTE] **Advertencia:** se reiniciarán todas las instancias de rol la primera vez que habilite el Escritorio remoto y haga clic en Aceptar (marca de verificación). Para evitar un reinicio, el certificado que se usó para cifrar la contraseña debe instalarse en el rol. Si no se instala ningún certificado, verá la siguiente opción: ![CloudServices\_CreateNewCertDropDown](./media/cloud-services-how-to-configure/CloudServices_CreateNewCertDropDown.png)

    Para evitar un reinicio, instale un certificado y luego vuelva a este cuadro de diálogo (consulte [Uso de Escritorio remoto con los roles de Azure][] para obtener más información). Si selecciona un certificado existente, se enviará entonces una actualización de configuración a todas las instancias en el rol.

1.  En **Roles**, seleccione el rol de servicio que desea actualizar o seleccione **All** para todos los roles.

2.  Realice alguno de los siguientes cambios:

-   Para habilitar Escritorio remoto, seleccione la casilla **Enable Remote Desktop**. Para deshabilitar el Escritorio remoto, desmarque la casilla.

-   Cree una cuenta para usarla en las conexiones de Escritorio remoto con las instancias de rol.

-   Actualice la contraseña de la cuenta existente.

-   Seleccione un certificado cargado para usar en la autenticación (cargue el certificado usando **Upload** en la página **Certificates**) o cree un certificado nuevo.

-   Cambie la fecha de caducidad para la configuración de Escritorio remoto.

1.  Después terminar las actualizaciones de la configuración, haga clic en OK (marca de verificación).

2.  Para conectarse a una instancia de rol:

    a. Haga clic en **Instances** para abrir la página **Instances**.

    b. Seleccione una instancia de rol que tenga el Escritorio remoto configurado.

    c. Haga clic en **Connect** y siga las instrucciones para abrir el escritorio de la máquina virtual.

    d. Haga clic en **Open** y, a continuación, haga clic en **Connect** para iniciar la conexión del Escritorio remoto.

### Para deshabilitar el Acceso remoto para las instancias de rol en el Portal de administración

1.  Inicie sesión en el [Portal de administración](http://manage.windowsazure.com/) y haga clic en **Servicios en la nube**. A continuación, haga clic en el nombre del servicio en la nube para abrir el panel.

2.  Abra la página **Configure** para el servicio en la nube y haga clic en **Remote**.

3.  En **Roles**, seleccione el rol de servicio que desea actualizar o seleccione **All** para todos los roles.

4.  Desmarque o desactive la casilla **Enable Remote Desktop**.

5.  Haga clic en OK (marca de verificación).


[Set Up a Remote Desktop Connection for a Role in Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/hh124107.aspx

[Using Remote Desktop with Azure Roles]: http://msdn.microsoft.com/es-es/library/windowsazure/gg443832.aspx
			

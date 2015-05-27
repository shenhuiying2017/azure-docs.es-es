<properties 
	pageTitle="Configuración de un servicio en la nube - Azure" 
	description="Aprenda a configurar servicios en la nube en Azure. Aprenda a actualizar la configuración del servicio en la nube y configurar el acceso remoto en instancias de rol." 
	services="cloud-services" 
	documentationCenter="" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/02/2015" 
	ms.author="adegeo"/>




# Configuración de servicios en la nube

Puede configurar la mayoría de los ajustes más usados para un servicio en la nube en el Portal de administración de Azure. O bien, si desea actualizar los archivos de configuración directamente, descargue un archivo de configuración de servicio para actualizar y, a continuación, cargue el archivo actualizado y actualice el servicio en la nube con los cambios en la configuración. De cualquier manera, las actualizaciones de la configuración se realizan en todas las instancias de rol.

También puede habilitar una conexión de Escritorio remoto con uno o todos los roles que se ejecutan en su servicio en la nube. Escritorio remoto le permite tener acceso al escritorio de su aplicación mientras se ejecuta, además de solucionar y diagnosticar problemas. Puede habilitar una conexión de Escritorio remoto con su rol incluso si no configuró el archivo de definición de servicio (.csdef) para el Escritorio remoto durante el desarrollo de la aplicación. No es necesario volver a implementar su aplicación para habilitar una conexión de Escritorio remoto.

Azure solo puede asegurar un 99,95 % de disponibilidad del servicio durante las actualizaciones de la configuración si tiene al menos dos instancias de rol para cada rol. Esto permite que una máquina virtual procese las solicitudes del cliente mientras la otra se actualiza. Para obtener más información, consulte [Contratos de nivel de servicio](https://www.windowsazure.com/support/legal/sla/).

## configuración del servicio en la nube

1. En el [Portal de administración de Azure](http://manage.windowsazure.com/), haga clic en **Servicios en la nube**, en el nombre del servicio en la nube y, a continuación, en **Configurar**.

	![Página de configuración](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage1.png)
	
	En la página **Configurar**, puede configurar la supervisión, actualizar la configuración de roles y seleccionar el sistema operativo invitado y la familia para las instancias de rol.

2. En **supervisión**, establezca el nivel de supervisión en Detallado o Mínimo y configure las cadenas de conexión del diagnóstico que se requieren para la supervisión detallada. Para obtener instrucciones, vea [Supervisión de servicios en la nube](how-to-monitor-a-cloud-service.md).


3. Para los roles de servicio (agrupados por rol), puede actualizar la siguiente configuración:

  >**Configuració**<br/> Modifique los valores de los ajustes de la configuración diversa que se especifican en los elementos *ConfigurationSettings* del archivo de configuración del servicio (.cscfg).

  >**Certificados**<br/> Cambie la huella digital del certificado que se está usando en el cifrado SSL para un rol. Para cambiar un certificado, debe primero cargar el certificado nuevo (en la página **Certificados**). Posteriormente, actualice la huella digital en la cadena de certificado que aparece en la configuración de roles.

4. En **sistema operativo**, puede cambiar la familia o la versión del sistema operativo para las instancias de rol o seleccionar **Automático** para habilitar las actualizaciones automáticas de la versión del sistema operativo actual. Esta configuración del sistema operativo se aplica a roles web y roles de trabajo, pero no afecta a Máquinas virtuales.

  Durante la implementación, se instala la versión más reciente del sistema operativo en todas las instancias de rol y los sistemas operativos se actualizan automáticamente de manera predeterminada.

  Si debido a los requisitos de compatibilidad en su código necesita una versión diferente del sistema operativo para que su servicio en la nube pueda ejecutarse, puede seleccionar una familia y versión del sistema operativo. Al elegir una versión específica del sistema operativo, se suspenden las actualizaciones automáticas del sistema operativo para el servicio en la nube. Deberá asegurarse de que los sistemas operativos reciban las actualizaciones.

  Si soluciona todos los problemas de compatibilidad que tienen sus aplicaciones con la versión más reciente del sistema operativo, puede habilitar las actualizaciones automáticas del sistema operativo al establecer la versión del sistema operativo en **Automático**.

  ![Configuración del SO](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage_OSSettings.png)

5. Para guardar los ajustes de configuración y transmitirlos a las instancias de rol, haga clic en **Guardar**. (Haga clic en **Descartar** para cancelar los cambios). **Guardar** y **Descartar** se agregan a la barra de comandos después de cambiar un parámetro de configuración.

### Para actualizar un archivo de configuración del servicio en la nube manualmente

1. Descargue un archivo de configuración del servicio en la nube (.cscfg) con la configuración actual. En la página **Configurar** del servicio en la nube, haga clic en **Descargar**. A continuación, haga clic en **Guardar** o en **Guardar como** para guardar el archivo.

2. Después de actualizar el archivo de configuración del servicio, cargue y aplique las actualizaciones de la configuración:

	a. En la página **Configurar**, haga clic en **Cargar**.

	![Cargar configuración](./media/cloud-services-how-to-configure/CloudServices_UploadConfigFile.png)

	b. En **Archivo de configuración**, use **Examinar** para seleccionar el archivo .cscfg actualizado.

	c. Si su servicio en la nube contiene cualquier rol que tiene una sola instancia, active la casilla **Aplicar la configuración aunque una o más reglas contenga una instancia única** para permitir que continúen las actualizaciones de configuración para los roles.

	A menos que defina como mínimo dos instancias de cada rol, Azure no puede garantizar una disponibilidad de su servicio en la nube de al menos un 99,95 % durante las actualizaciones de la configuración del servicio. Para obtener más información, consulte [Contratos de nivel de servicio](http://www.windowsazure.com/support/legal/sla/).

	d. Haga clic en **Aceptar** (marca de verificación).


## acceso remoto a las instancias de rol

Escritorio remoto le permite tener acceso al escritorio de un rol que se ejecuta en Azure. Puede usar la conexión de Escritorio remoto para solucionar y diagnosticar problemas con su aplicación mientras se ejecuta. Puede habilitar una conexión de Escritorio remoto en su rol durante el diseño de la aplicación o después de que haya implementado la aplicación en Azure (mientras el rol se está ejecutando). La habilitación de una conexión de Escritorio remoto en un rol en ejecución a través del Portal de administración no requiere que vuelva a implementar la aplicación. Para autenticar la conexión de Escritorio remoto puede usar un certificado que se haya cargado anteriormente o puede crear un certificado nuevo.

En la pagina **Configurar** de su servicio en la nube, puede habilitar el Escritorio remoto o cambiar la cuenta o la contraseña de Administrador local que se usan para conectarse a las máquinas virtuales, el certificado que se usa en la autenticación o la fecha de caducidad.

### Para configurar el Acceso remoto en el archivo de definición del servicio

Agregue elementos para **Importar** al archivo de definición de servicio (.csdef) para importar los módulos RemoteAccess y RemoteForwarder al modelo de servicio. Cuando esos módulos están presentes, Azure agrega los ajustes de configuración para Escritorio remoto en el archivo de configuración del servicio. Para completar la configuración de Escritorio remoto, necesitará importar un certificado a Azure y especificar dicho certificado en el archivo de configuración del servicio. Para obtener más información, consulte [Establecer una conexión a Escritorio remoto para un rol de Azure][].

###Para habilitar o modificar el Acceso remoto para las instancias de rol en el Portal de administración###

1. Haga clic en **Servicios en la nube**, en el nombre del servicio en la nube y, a continuación, en **Configurar**.

2. Haga clic en **Remoto**.

  ![Servicios en la nube remotos](./media/cloud-services-how-to-configure/CloudServices_Remote.png)

  **Advertencia:** se reiniciarán todas las instancias de rol la primera vez que habilite el Escritorio remoto y haga clic en Aceptar (marca de verificación). Para evitar un reinicio, el certificado que se usó para cifrar la contraseña debe instalarse en el rol.

  Para evitar un reinicio, instale un certificado y luego vuelva a este cuadro de diálogo (consulte [Uso de Escritorio remoto con los roles de Azure][] para obtener más información). Si selecciona un certificado existente, se enviará entonces una actualización de configuración a todas las instancias en el rol.

3. En **Roles**, seleccione el rol de servicio que desea actualizar o seleccione **Todos** para todos los roles.

4. Realice alguno de los siguientes cambios:

  - Para habilitar Escritorio remoto, seleccione la casilla **Habilitar Escritorio remoto**. Para deshabilitar el Escritorio remoto, desmarque la casilla.

  - Cree una cuenta para usarla en las conexiones de Escritorio remoto con las instancias de rol.

  - Actualice la contraseña de la cuenta existente.

  - Seleccione un certificado cargado para usar en la autenticación (cargue el certificado usando **Cargar** en la página **Certificados**) o cree un certificado nuevo.

  - Cambie la fecha de caducidad para la configuración de Escritorio remoto.

5. Después terminar las actualizaciones de la configuración, haga clic en **Aceptar** (marca de verificación).

6. Para conectarse a una instancia de rol:

  a. Haga clic en **Instancias** para abrir la página **Instancias**.

  b. Seleccione una instancia de rol que tenga el Escritorio remoto configurado.

  c. Haga clic en **Conectar** y siga las instrucciones para abrir el escritorio.

  d. Haga clic en **Abrir** y, a continuación, en **Conectar** para iniciar la conexión del Escritorio remoto.

### Para deshabilitar el Acceso remoto para las instancias de rol en el Portal de administración

1. Haga clic en **Servicios en la nube**, en el nombre del servicio en la nube y, a continuación, en **Configurar**.

2. Haga clic en **Remoto**.

3. En **Roles**, seleccione el rol de servicio que desea actualizar o seleccione **Todos** para todos los roles.

4. Desmarque o desactive la casilla **Habilitar Escritorio remoto**.

5. Haga clic en **Aceptar** (marca de verificación).

[Establecer una conexión a Escritorio remoto para un rol de Azure]: http://msdn.microsoft.com/library/windowsazure/hh124107.aspx

[Uso de Escritorio remoto con los roles de Azure]: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx

<!--HONumber=54-->
<properties
	pageTitle="Versión preliminar de Servicios de dominio de Azure Active Directory: guía de administración | Microsoft Azure"
	description="Unión de una máquina virtual de Windows Server a Servicios de dominio de Azure AD"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/08/2016"
	ms.author="maheshu"/>

# Unión de una máquina virtual de Windows Server a un dominio administrado

> [AZURE.SELECTOR]
- [Portal de Azure clásico - Windows](active-directory-ds-admin-guide-join-windows-vm.md)
- [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)

<br>

Este artículo muestra cómo unir una máquina virtual en la que se ejecuta Windows Server 2012 R2 a un dominio administrado con Servicios de dominio de Azure AD, mediante el Portal de Azure clásico.


## Paso 1: Crear la máquina virtual de Windows
Siga las instrucciones proporcionadas en el tutorial [Creación de una máquina virtual que ejecuta Windows en el Portal de Azure clásico](../virtual-machines/virtual-machines-windows-classic-tutorial.md). Es muy importante asegurarse de que la máquina virtual recién creada está unida a la misma red virtual en la que ha habilitado Servicios de dominio de Azure AD. Tenga en cuenta que la opción "Creación rápida" no permite unir la máquina virtual a una red virtual. Por lo tanto, debe utilizar la opción "De la galería" para crear la máquina virtual.

Realice los siguientes pasos de configuración para crear una máquina virtual de Windows unida a la red virtual en la que ha habilitado Servicios de dominio de Azure AD.

1. En el Portal de Azure clásico, en la barra de comandos que se encuentra en la parte inferior de la ventana, haga clic en **Nuevo**.

2. En **Proceso**, haga clic en **Máquina virtual**, y, a continuación, en **De la galería**.

3. La primera pantalla la opción **Elija una imagen** permite elegir una imagen para su máquina virtual de la lista de imágenes disponibles. Elija la imagen adecuada.

    ![Seleccionar imagen](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)

4. La segunda pantalla permite seleccionar el nombre del equipo y el tamaño, así como el nombre de usuario y la contraseña del usuario administrativo. Use el nivel y el tamaño necesarios para ejecutar la aplicación o carga de trabajo. El nombre de usuario que se seleccionará aquí es un usuario de administrador local en la máquina. No escriba las credenciales de la cuenta de usuario del dominio aquí.

    ![Configurar máquina virtual](./media/active-directory-domain-services-admin-guide/create-windows-vm-config.png)

5. La tercera pantalla permite configurar los recursos para las redes, el almacenamiento y la disponibilidad. En la lista desplegable **Región/grupo de afinidad/red virtual**, asegúrese de seleccionar la red virtual en la que habilitó Servicios de dominio de Azure AD. Especifique un **Nombre DNS de servicio en la nube** según corresponda para la máquina virtual.

    ![Seleccionar una red virtual para una máquina virtual](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [AZURE.WARNING]
    Asegúrese de que unir la máquina virtual a la misma red virtual en la que habilitó Servicios de dominio de Azure AD. Esto garantizará que la máquina virtual pueda "ver" el dominio y realizar tareas como unirse al dominio. Si opta por crear la máquina virtual en otra red virtual, asegúrese de que la red virtual está conectada a la red virtual en la que habilitó Servicios de dominio de Azure AD.

6. La cuarta pantalla le permite instalar el agente de máquina virtual y configurar algunas de las extensiones disponibles.

    ![¡Listo!](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)

7. Una vez creada la máquina virtual, en el portal clásico se muestra la nueva máquina virtual en el nodo **Máquinas virtuales**. La máquina virtual y el servicio en la nube se inician automáticamente, y su estado aparece como **En ejecución**.

    ![La máquina virtual está activa y se está ejecutando](./media/active-directory-domain-services-admin-guide/create-windows-vm-running.png)


## Paso 2: Conéctese a la nueva máquina virtual de Windows Server con la cuenta de administrador local.
Ahora, se conectará a la máquina virtual de Windows Server recién creada para unirse al dominio. Para conectarse a la máquina virtual, use las credenciales de administrador local que especificó al crearla.

Realice los pasos siguientes para conectarse a la máquina virtual.

1. Vaya al nodo **Máquinas virtuales** en el portal clásico. Seleccione la máquina virtual que acaba de crear y haga clic en **Conectar** en la barra de comandos situada en la parte inferior de la ventana.

    ![Conexión a máquina virtual de Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. El portal clásico le solicitará que abra o guarde un archivo .rdp, que se utiliza para conectarse a la máquina virtual. Haga clic en el archivo .rdp cuando haya terminado de descargarse.

3. En el aviso de inicio de sesión, escriba sus **credenciales de administrador local**, especificadas al crear la máquina virtual. Por ejemplo, "localhost\\mahesh" en el ejemplo anterior.

En este punto, debe iniciar sesión en la máquina virtual de Windows recién creada con las credenciales de administrador local. El siguiente paso es unir la máquina virtual al dominio.


## Paso 3: Unión de una máquina virtual de Windows Server al dominio administrado con AAD-DS
Realice los pasos siguientes para unir la máquina virtual de Windows Server al dominio administrado con AAD-DS.

1. Conéctese a Windows Server como se muestra en el paso 2 anterior. En la pantalla Inicio, abra **Administrador del servidor**.

2. Haga clic en **Servidor local** en el panel izquierdo de la ventana Administrador del servidor.

    ![Inicio del Administrador del servidor en la máquina virtual](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)

3. Haga clic en **WORKGROUP** en la sección **PROPIEDADES**. Se abrirá la página de propiedades **Propiedades del sistema**. Para unirse al dominio, haga clic en **Cambiar**.

    ![Página Propiedades del sistema](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)

4. Especifique el nombre de dominio de su dominio administrado con Servicios de dominio de Azure AD en el cuadro de texto **Dominio** y haga clic en **Aceptar**.

    ![Especificar el dominio al que se unirá](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)

5. Se le solicitará que especifique sus credenciales para unirse al dominio. Asegúrese de **especificar las credenciales de un usuario que pertenezca al grupo Administradores del controlador de dominio de AAD**. Solo los miembros de este grupo tienen privilegios para unir máquinas al dominio administrado.

    ![Especificar las credenciales para unirse al dominio](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)

6. Puede especificar credenciales de cualquiera de las maneras siguientes:

    - Formato UPN: sufijo UPN de la cuenta de usuario como está configurado en Azure AD. En este ejemplo, el sufijo UPN del usuario "bob" es "bob@domainservicespreview.onmicrosoft.com".

    - Formato SAMAccountName: puede especificar el nombre de cuenta en formato SAMAccountName. En este ejemplo, el usuario "bob" tendría que especificar "CONTOSO100\\bob". Tenga en cuenta que si varios usuarios tienen el mismo prefijo UPN (p. ej. "bob") en su inquilino de Azure AD, tendrán problemas de inicio de sesión en el dominio con el formato SAMAccountName. En estos casos, se puede utilizar el formato UPN de forma confiable para iniciar sesión en el dominio.

7. Una vez que la unión al dominio es correcta, verá el siguiente mensaje que le da la bienvenida al dominio. Debe reiniciar la máquina virtual para completar la operación de unión al dominio.

    ![Bienvenido al dominio](./media/active-directory-domain-services-admin-guide/join-domain-done.png)


## Solución de problemas de unión al dominio
### Problemas de conectividad
Si la máquina virtual no puede encontrar el dominio, consulte los siguientes pasos de solución de problemas:

- Asegúrese de que la máquina virtual está conectada a la misma red virtual en la que habilitó Servicios de dominio. De lo contrario, la máquina virtual no podrá conectarse al dominio y, por tanto, no podrá unirse al dominio.

- Si la máquina virtual está conectada a otra red virtual, asegúrese de que esta red virtual está conectada a la red virtual en la que habilitó Servicios de dominio.

- Intente hacer ping al dominio con el nombre de dominio del dominio administrado (p. ej. "ping contoso100.com"). Si no puede hacerlo, intente hacer ping a las direcciones IP para el dominio que aparece en la página en la que habilitó Servicios de dominio de Azure AD (p. ej. "ping 10.0.0.4"). Si puede hacer ping a la dirección IP pero no al dominio, DNS puede estar configurado incorrectamente. Es posible que no haya configurado las direcciones IP del dominio como servidores DNS de la red virtual.

- Intente vaciar la memoria caché de resolución DNS en la máquina virtual ("ipconfig /flushdns").

Si aparece el cuadro de diálogo que solicita credenciales para unirse al dominio, no tiene problemas de conectividad.


### Problemas relacionados con la credenciales
Consulte los siguientes pasos si tiene problemas con las credenciales y no puede unirse al dominio.

- Asegúrese de que está utilizando las credenciales de una cuenta de usuario que pertenezca al grupo "Administradores de controlador de dominio de AAD". Los usuarios que no pertenecen a este grupo no pueden unir máquinas al dominio administrado.

- Asegúrese de que ha [habilitado la sincronización de contraseñas](active-directory-ds-getting-started-password-sync.md) según los pasos que se describen en la Guía de introducción.

- Asegúrese de que usa el UPN del usuario como está configurado en Azure AD (p. ej. "bob@domainservicespreview.onmicrosoft.com") para iniciar sesión.

- Asegúrese de haber esperado suficiente tiempo para que la sincronización de contraseñas se complete, como se especifica en la Guía de introducción.

<!---HONumber=AcomDC_0420_2016-->
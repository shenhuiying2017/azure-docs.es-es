<properties
	pageTitle="Versión preliminar de Servicios de dominio de Azure Active Directory: guía de administración | Microsoft Azure"
	description="Unión de una máquina virtual Red Hat Enterprise Linux a un dominio administrado de Azure AD"
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
	ms.date="07/06/2016"
	ms.author="maheshu"/>

# Unión de una máquina virtual de Red Hat Enterprise Linux 7 a un dominio administrado
Este artículo muestra cómo unir una máquina virtual de Red Hat Enterprise Linux (RHEL) 7 a un dominio administrado con Servicios de dominio de Azure AD.

## Aprovisionamiento de una máquina virtual de Red Hat Enterprise Linux
Realice los pasos siguientes para aprovisionar una máquina virtual de RHEL 7 mediante el portal de Azure.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

    ![Panel de Portal de Azure](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-dashboard.png)

2. Haga clic en **Nuevo** en el panel izquierdo y escriba **Red Hat** en la barra de búsqueda, como se muestra en la siguiente captura de pantalla. Debe ver las entradas para Red Hat Enterprise Linux en los resultados de búsqueda. Haga clic en **Red Hat Enterprise Linux 7.2**.

    ![Seleccionar RHEL en resultados](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-find-rhel-image.png)

3. En los resultados de búsqueda del panel **Todo** se debe mostrar la imagen de Red Hat Enterprise Linux 7.2. Haga clic en **Red Hat Enterprise Linux 7.2** para más información acerca de la imagen de la máquina virtual.

    ![Seleccionar RHEL en resultados](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-select-rhel-image.png)

4. En el panel **Red Hat Enterprise Linux 7.2** debería ver más información sobre la imagen de la máquina virtual. En el menú desplegable **Seleccionar un modelo de implementación**, seleccione **Clásico**. Después, haga clic en el botón **Crear**.

    ![Ver detalles de la imagen](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-clicked.png)

5. En el panel **Crear VM**, escriba el **Nombre de host** para la nueva máquina virtual. Especifique también un nombre de usuario de administrador local en el campo **Nombre de usuario** y una **Contraseña**. También puede decidir utilizar una clave SSH para autenticar al usuario de administrador local. Seleccione también un **Plan de tarifa** para la máquina virtual.

    ![Crear máquina virtual: detalles básicos](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-basic-details.png)

6. Haga clic en **Configuración opcional**. Esto debería abrir el panel **Configuración opcional**. En el panel **Configuración opcional**, haga clic en **Red**, como se muestra en la captura de pantalla siguiente.

    ![Crear máquina virtual: configurar la red virtual](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-configure-vnet.png)

7. Esto debería abrir el panel **Red**. En el panel **Red**, haga clic en **Red virtual** para seleccionar la red virtual en la que se debe implementar la máquina virtual de Linux. Se debe abrir el panel **Red virtual**. En el panel **Red virtual**, elija la opción **Usar una red virtual existente**. A continuación, seleccione la red virtual en la que Servicios de dominio de Azure AD estará disponible. En este ejemplo, se ha elegido la red virtual "MyPreviewVNet".

    ![Crear máquina virtual: seleccionar la red virtual](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-select-vnet.png)

8. En el panel **Configuración opcional**, haga clic en el botón **Aceptar**.

    ![Crear máquina virtual: red virtual seleccionada](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-vnet-selected.png)

9. Ahora está listo para crear la máquina virtual. En el panel **Crear VM**, haga clic en el botón **Crear**.

    ![Crear máquina virtual: listo](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm.png)

10. Debe comenzar la implementación de la nueva máquina virtual basada en la imagen de RHEL 7.2.

  ![Crear máquina virtual: implementación iniciada](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployment-started.png)

11. Después de unos minutos, la máquina virtual se debe implementar correctamente y estar lista para su uso.

  ![Crear máquina virtual: implementado](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployed.png)



## Conexión remota a la máquina virtual de Linux recién aprovisionada
Se ha aprovisionado la máquina virtual de RHEL 7.2 en Azure. La siguiente tarea es conectarse de forma remota a la máquina virtual.

**Conéctese a la máquina virtual de RHEL 7.2**: siga las instrucciones del artículo [Inicio de sesión en una máquina virtual con Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md).

En los demás pasos se considera que está utilizando PuTTY como el cliente SSH para conectarse a la máquina virtual de RHEL. Para más información, consulte la página [PuTTY Download Page](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) (Página de descarga de PuTTY).

1. Abra el programa PuTTY.

2. Escriba el **Nombre de host** para la máquina virtual de RHEL recién creada. En este ejemplo, la máquina virtual tiene el nombre de host "contoso rhel.cloudapp .net". Si no está seguro del nombre de host de su máquina virtual, consulte el panel de máquinas virtuales en el Portal de Azure.

    ![Conexión a PuTTY](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-connect.png)

3. Inicie sesión en la máquina virtual con las credenciales de administrador local que especificó al crear la máquina virtual. En este ejemplo, se usa la cuenta de administrador local "mahesh".

    ![Inicio de sesión de PuTTY](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-login.png)


## Instalación de los paquetes necesarios en la máquina virtual de Linux
Después de conectarse a la máquina virtual, la siguiente tarea es instalar los paquetes necesarios para unir un dominio a la máquina virtual. Lleve a cabo los siguiente pasos:

1. **Instale realmd**: el paquete realmd se utiliza para unirse a un dominio. En el terminal PuTTY, escriba el siguiente comando:

    sudo yum install realmd

    ![Instalar realmd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-realmd.png)

    Después de unos minutos, el paquete realmd debe estar instalado en la máquina virtual.

    ![realmd instalado](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-installed.png)

3. **Instale sssd**: el paquete realmd depende de sssd para realizar las operaciones de unión al dominio. En el terminal PuTTY, escriba el siguiente comando:

    sudo yum install sssd

	![Instalar sssd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-sssd.png)

    Después de unos minutos, el paquete sssd debe estar instalado en la máquina virtual.

    ![realmd instalado](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-sssd-installed.png)

4. **Instale kerberos**: en el terminal PuTTY, escriba el siguiente comando.

    sudo yum install krb5-workstation krb5-libs

	![Instalar kerberos](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-kerberos.png)

	Después de unos minutos, el paquete realmd debe estar instalado en la máquina virtual.

	![Kerberos instalado](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kerberos-installed.png)


## Unión de la máquina virtual de Linux al dominio administrado
Ahora que los paquetes necesarios están instalados en la máquina virtual de Linux, la siguiente tarea es unir la máquina virtual al dominio administrado.

1. Detecte el dominio administrado con Servicios de dominio de AAD. En el terminal PuTTY, escriba el siguiente comando:

    sudo realm discover CONTOSO100.COM

	![Detección de dominio](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-discover.png)

	Si **realm discover** no puede encontrar el dominio administrado, asegúrese de que se puede acceder al dominio desde la máquina virtual (pruebe con ping). Asegúrese de que la máquina virtual se ha implementado realmente en la misma red virtual en la que el dominio administrado está disponible.

2. Inicialice kerberos. En el terminal PuTTY, escriba el siguiente comando: Asegúrese de especificar un usuario que pertenezca al grupo "Administradores del controlador de dominio de AAD". Solo estos usuarios pueden unir equipos al dominio administrado.

    kinit bob@CONTOSO100.COM

    ![Kinit](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kinit.png)

    Asegúrese de especificar el nombre de dominio en mayúsculas o kinit generará un error.

3. Una la máquina al dominio. En el terminal PuTTY, escriba el siguiente comando: Asegúrese de especificar el mismo usuario que especificó en el paso anterior ("kinit").

    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM'

	![Unión a dominio kerberos](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-join.png)

Debe obtener un mensaje (Máquina inscrita correctamente en el dominio kerberos) cuando la máquina está unida correctamente al dominio administrado.


## Verificación de la unión a un dominio
Puede verificar rápidamente si el equipo se ha unido correctamente al dominio administrado. Esto puede hacerse mediante la conexión a la máquina virtual de RHEL recién unida al dominio con ssh y una cuenta de usuario del dominio y, a continuación, la comprobación de si la cuenta de usuario se ha resuelto correctamente.

1. En el terminal PuTTY, escriba el comando siguiente para conectarse a la nueva máquina virtual de RHEL recién unida al dominio con SSH. Use una cuenta de dominio que pertenezca a la cuenta administrada (p. ej. "bob@CONTOSO100.COM" en este caso).

    ssh -l bob@CONTOSO100.COM contoso-rhel.cloudapp.net

2. En el terminal PuTTY, escriba el comando siguiente para ver si el directorio principal del usuario se ha inicializado correctamente.

	pwd

3. En el terminal PuTTY, escriba el comando siguiente para ver si los miembros del grupo del usuario se está resolviendo correctamente.

    id

A continuación se muestra un resultado de ejemplo de estos comandos.

![Verificación de la unión a un dominio](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-verify-domain-join.png)


## Solución de problemas de unión al dominio
Consulte el artículo [Solución de problemas de unión al dominio](active-directory-ds-admin-guide-join-windows-vm.md#troubleshooting-domain-join).


## Más información
- [Inicio de sesión en una máquina virtual con Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md)
- [Installing Kerberos](https://access.redhat.com/documentation/es-ES/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html) (Instalación de Kerberos)
- [Red Hat Enterprise Linux 7 - Windows Integration Guide](https://access.redhat.com/documentation/es-ES/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html) (Red Hat Enterprise Linux 7: guía de integración de Windows)

<!---HONumber=AcomDC_0824_2016-->
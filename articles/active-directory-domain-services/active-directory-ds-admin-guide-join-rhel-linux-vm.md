---
title: "Azure Active Directory Domain Services: Unión de una máquina virtual de Red Hat Enterprise Linux a un dominio administrado | Microsoft Docs"
description: "Unión de una máquina virtual Red Hat Enterprise Linux a un dominio administrado de Azure AD"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 87291c47-1280-43f8-8fb2-da1bd61a4942
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: d36b4c1391dad88603ad823055e11c0a4a329c3c
ms.openlocfilehash: 89e97cb903e04efa77cfc6b02484d28a9ccfe6fb


---
# <a name="join-a-red-hat-enterprise-linux-7-virtual-machine-to-a-managed-domain"></a>Unión de una máquina virtual de Red Hat Enterprise Linux 7 a un dominio administrado
Este artículo muestra cómo unir una máquina virtual de Red Hat Enterprise Linux (RHEL) 7 a un dominio administrado con Servicios de dominio de Azure AD.

## <a name="provision-a-red-hat-enterprise-linux-virtual-machine"></a>Aprovisionamiento de una máquina virtual de Red Hat Enterprise Linux
Realice los pasos siguientes para aprovisionar una máquina virtual de RHEL 7 mediante el Portal de Azure.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

    ![Panel de Portal de Azure](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-dashboard.png)
2. Haga clic en **Nuevo** en el panel izquierdo y escriba **Red Hat** en la barra de búsqueda, como se muestra en la siguiente captura de pantalla. Las entradas para Red Hat Enterprise Linux en los resultados de búsqueda. Haga clic en **Red Hat Enterprise Linux 7.2**.

    ![Seleccionar RHEL en resultados](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-find-rhel-image.png)
3. En los resultados de búsqueda del panel **Todo** se debe mostrar la imagen de Red Hat Enterprise Linux 7.2. Haga clic en **Red Hat Enterprise Linux 7.2** para obtener más información sobre la imagen de la máquina virtual.

    ![Seleccionar RHEL en resultados](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-select-rhel-image.png)
4. En el panel **Red Hat Enterprise Linux 7.2** debería ver más información sobre la imagen de la máquina virtual. En el menú desplegable **Seleccionar un modelo de implementación**, seleccione **Clásico**. Después, haga clic en el botón **Crear** .

    ![Ver detalles de la imagen](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-clicked.png)
5. En la página **Aspectos básicos** del Asistente para **crear máquina virtual**, escriba el **nombre de host** para la nueva máquina virtual. Especifique también un nombre de usuario de administrador local en el campo **Nombre de usuario** y una **Contraseña**. También puede decidir utilizar una clave SSH para autenticar al usuario de administrador local. Seleccione también un **Plan de tarifa** para la máquina virtual.

    ![Crear máquina virtual: página de aspectos básicos](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-basic-details.png)
6. En la página **Tamaño** del Asistente para **crear máquina virtual**, seleccione el tamaño de la máquina virtual.

    ![Crear máquina virtual: seleccionar tamaño](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-select-vm-size.png)

7. En la página **Configuración** del Asistente para **crear máquina virtual**, seleccione la cuenta de almacenamiento para la máquina virtual. Haga clic en **Red virtual** para seleccionar la red virtual en la que se debe implementar la máquina virtual Linux. En la hoja **Red virtual**, seleccione la red virtual en la que Azure AD Domain Services estará disponible. En este ejemplo, se ha elegido la red virtual "MyPreviewVNet".

    ![Crear máquina virtual: seleccionar la red virtual](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-select-vnet.png)
8. En la página **Resumen** del Asistente para **crear máquina virtual**, revise y haga clic en el botón **Aceptar**.

    ![Crear máquina virtual: red virtual seleccionada](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-vnet-selected.png)
9. Debe comenzar la implementación de la nueva máquina virtual basada en la imagen de RHEL 7.2.

    ![Crear máquina virtual: implementación iniciada](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployment-started.png)
10. Después de unos minutos, la máquina virtual se debe implementar correctamente y estar lista para su uso.

    ![Crear máquina virtual: implementado](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployed.png)

## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Conexión remota a la máquina virtual de Linux recién aprovisionada
Se ha aprovisionado la máquina virtual de RHEL 7.2 en Azure. La siguiente tarea es conectarse de forma remota a la máquina virtual.

**Conectarse a la máquina virtual de RHEL 7.2** Siga las instrucciones del artículo [Inicio de sesión en una máquina virtual con Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

En el resto de los pasos se supone que usa el cliente de SSH PuTTY para conectarse a la máquina virtual de RHEL. Para más información, consulte la página [PuTTY Download Page](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)(Página de descarga de PuTTY).

1. Abra el programa PuTTY.
2. Escriba el **Nombre de host** para la máquina virtual de RHEL recién creada. En este ejemplo, la máquina virtual tiene el nombre de host "contoso rhel.cloudapp .net". Si no está seguro del nombre de host de su máquina virtual, consulte el panel de máquinas virtuales en el Portal de Azure.

    ![Conexión a PuTTY](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-connect.png)
3. Inicie sesión en la máquina virtual con las credenciales de administrador local que ha especificado al crear la máquina virtual. En este ejemplo, se usa la cuenta de administrador local "mahesh".

    ![Inicio de sesión de PuTTY](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-login.png)

## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Instalación de los paquetes necesarios en la máquina virtual de Linux
Después de conectarse a la máquina virtual, la siguiente tarea es instalar los paquetes necesarios para unir un dominio a la máquina virtual. Lleve a cabo los siguiente pasos:

1. **Instale realmd** : el paquete realmd se utiliza para unirse a un dominio. En el terminal PuTTY, escriba el comando siguiente:

    sudo yum install realmd

    ![Instalar realmd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-realmd.png)

    Después de unos minutos, el paquete realmd debe estar instalado en la máquina virtual.

    ![realmd instalado](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-installed.png)
2. **Instale sssd** : el paquete realmd depende de SSSD para realizar las operaciones de unión al dominio. En el terminal PuTTY, escriba el comando siguiente:

    sudo yum install sssd

    ![Instalar sssd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-sssd.png)

    Después de unos minutos, el paquete sssd debe estar instalado en la máquina virtual.

    ![realmd instalado](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-sssd-installed.png)
3. **Instale Kerberos**: en el terminal PuTTY, escriba el comando siguiente:

    sudo yum install krb5-workstation krb5-libs

    ![Instalar kerberos](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-kerberos.png)

    Después de unos minutos, el paquete realmd debe estar instalado en la máquina virtual.

    ![Kerberos instalado](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kerberos-installed.png)

## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Unión de la máquina virtual de Linux al dominio administrado
Ahora que los paquetes necesarios están instalados en la máquina virtual de Linux, la siguiente tarea es unir la máquina virtual al dominio administrado.

1. Detecte el dominio administrado con Servicios de dominio de AAD. En el terminal PuTTY, escriba el comando siguiente:

    sudo realm discover CONTOSO100.COM

    ![realm discover](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-discover.png)

    Si **realm discover** no puede encontrar el dominio administrado, asegúrese de que se puede acceder al dominio desde la máquina virtual (pruebe con ping). Asegúrese de que la máquina virtual se ha implementado realmente en la misma red virtual en la que el dominio administrado está disponible.
2. Inicialice kerberos. En el terminal PuTTY, escriba el siguiente comando: Asegúrese de especificar un usuario que pertenezca al grupo "Administradores del controlador de dominio de AAD". Solo estos usuarios pueden unir equipos al dominio administrado.

    kinit bob@CONTOSO100.COM

    ![kinit ](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kinit.png)

    Asegúrese de especificar el nombre de dominio en mayúsculas o kinit generará un error.
3. Una la máquina al dominio. En el terminal PuTTY, escriba el siguiente comando: Especifique el mismo usuario que ha especificado el paso anterior ("kinit").

    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM'

    ![Unión a dominio kerberos](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-join.png)

Debe obtener un mensaje (Máquina inscrita correctamente en el dominio kerberos) cuando la máquina está unida correctamente al dominio administrado.

## <a name="verify-domain-join"></a>Verificación de la unión a un dominio
Puede verificar rápidamente si el equipo se ha unido correctamente al dominio administrado. Conecte a la máquina virtual de RHEL recién unida al dominio con SSH y una cuenta de usuario del dominio y, después,compruebe si la cuenta de usuario se ha resuelto correctamente.

1. En el terminal PuTTY, escriba el comando siguiente para conectarse a la nueva máquina virtual de RHEL recién unida al dominio con SSH. Use una cuenta de dominio que pertenezca al dominio administrado (por ejemplo, 'bob@CONTOSO100.COM' en este caso).

    ssh -l bob@CONTOSO100.COM contoso-rhel.cloudapp.net
2. En el terminal PuTTY, escriba el comando siguiente para ver si el directorio principal se ha inicializado correctamente.

    pwd
3. En el terminal PuTTY, escriba el comando siguiente para ver si los miembros del grupo se está resolviendo correctamente.

    id

Este es un ejemplo de los resultados de estos comandos:

![Verificación de la unión a un dominio](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-verify-domain-join.png)

## <a name="troubleshooting-domain-join"></a>Solución de problemas de unión al dominio
Consulte el artículo [Solución de problemas de unión al dominio](active-directory-ds-admin-guide-join-windows-vm.md#troubleshooting-domain-join) .

## <a name="related-content"></a>Contenido relacionado
* [Introducción a Azure AD Domain Services](active-directory-ds-getting-started.md)
* [Unión de una máquina virtual de Windows Server a un dominio administrado](active-directory-ds-admin-guide-join-windows-vm.md)
* [Inicio de sesión en una máquina virtual con Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Installing Kerberos (Instalación de Kerberos)](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)
* [Red Hat Enterprise Linux 7 - Windows Integration Guide (Red Hat Enterprise Linux 7: guía de integración de Windows)](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)



<!--HONumber=Jan17_HO2-->



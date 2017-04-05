---
title: "Azure Active Directory Domain Services: conexión de una máquina virtual con Windows Server a un dominio administrado | Microsoft Docs"
description: "Unión de una máquina virtual de Windows Server a Servicios de dominio de Azure AD"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 74dbdb33-05db-4d47-badc-0d7bb6d0c8cb
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 9f8d21f6964d26a2e17e31d1f2947e7eb07c177d
ms.lasthandoff: 03/25/2017


---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Unión de una máquina virtual de Windows Server a un dominio administrado
> [!div class="op_single_selector"]
> * [Portal de Azure clásico - Windows](active-directory-ds-admin-guide-join-windows-vm.md)
> * [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)
>
>

<br>

Este artículo muestra cómo unir una máquina virtual en la que se ejecuta Windows Server 2012 R2 a un dominio administrado con Servicios de dominio de Azure AD, mediante el Portal de Azure clásico.

## <a name="step-1-create-the-windows-server-virtual-machine"></a>Paso 1: Crear la máquina virtual de Windows
Siga las instrucciones del tutorial [Creación de una máquina virtual que ejecuta Windows en el Portal de Azure clásico](../virtual-machines/windows/classic/tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) . Es importante asegurarse de que la máquina virtual recién creada está unida a la misma red virtual en la que ha habilitado Servicios de dominio de Azure AD. Tenga en cuenta que la opción Creación rápida no permite unir la máquina virtual a una red virtual. Por lo tanto, debe utilizar la opción De la galería para crear la máquina virtual.

Realice los siguientes pasos de configuración para crear una máquina virtual Windows unida a la red virtual en la que ha habilitado Servicios de dominio de Azure AD.

1. En el Portal de Azure clásico, en la barra de comandos que se encuentra en la parte inferior de la ventana, haga clic en **Nuevo**.
2. En **Compute**, haga clic en **Máquina virtual** y, luego, en **De la galería**.
3. La primera pantalla la opción **Elija una imagen** permite elegir una imagen para su máquina virtual de la lista de imágenes disponibles. Elija la imagen adecuada.

    ![Seleccionar imagen](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)
4. La segunda pantalla permite seleccionar el nombre del equipo y el tamaño, así como el nombre de usuario y la contraseña del usuario administrativo. Use el nivel y el tamaño necesarios para ejecutar la aplicación o carga de trabajo. El nombre de usuario que se seleccionará aquí es un usuario de administrador local de la máquina. No escriba las credenciales de la cuenta de usuario del dominio aquí.

    ![Configurar máquina virtual](./media/active-directory-domain-services-admin-guide/create-windows-vm-config.png)
5. La tercera pantalla permite configurar los recursos relacionados con las redes, el almacenamiento y la disponibilidad. En la lista desplegable **Región/grupo de afinidad/red virtual** , asegúrese de seleccionar la red virtual en la que habilitó Servicios de dominio de Azure AD. Especifique un **Nombre DNS de servicio en la nube** según corresponda para la máquina virtual.

    ![Seleccionar una red virtual para una máquina virtual](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

   > [!WARNING]
   > Asegúrese de que conecta la máquina virtual a la misma red virtual en la que ha habilitado Servicios de dominio de Azure AD. Esto garantizará que la máquina virtual pueda "ver" el dominio y realizar tareas como unirse al dominio. Si elige crear la máquina virtual en otra red virtual, conecte esta última a la red virtual en la que ha habilitado Servicios de dominio de Azure AD.
   >
   >
6. La cuarta pantalla le permite instalar el agente de máquina virtual y configurar algunas de las extensiones disponibles.

    ![¡Listo!](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)
7. Una vez creada la máquina virtual, en el portal clásico se muestra la nueva máquina virtual en el nodo **Máquinas virtuales** . La máquina virtual y el servicio en la nube se inician automáticamente, y su estado aparece como **En ejecución**.

    ![La máquina virtual está activa y se está ejecutando](./media/active-directory-domain-services-admin-guide/create-windows-vm-running.png)

## <a name="step-2-connect-to-the-windows-server-virtual-machine-using-the-local-administrator-account"></a>Paso 2: Conéctese a la nueva máquina virtual de Windows Server con la cuenta de administrador local.
Ahora, se conectará a la máquina virtual Windows Server recién creada para unirse al dominio. Para conectarse a la máquina virtual, use las credenciales de administrador local que especificó al crearla.

Realice los pasos siguientes para conectarse a la máquina virtual.

1. Vaya al nodo **Máquinas virtuales** en el portal clásico. Seleccione la máquina virtual que acaba de crear en el paso 1 y haga clic en la opción **Conectar** de la barra de comandos situada en la parte inferior de la ventana.

    ![Conexión a máquina virtual de Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
2. El portal clásico le solicitará que abra o guarde un archivo con extensión .rdp, que se utiliza para conectarse a la máquina virtual. Haga clic en el archivo para abrirlo cuando haya terminado de descargarse.
3. En el aviso de inicio de sesión, escriba sus **credenciales de administrador local**, especificadas al crear la máquina virtual. Por ejemplo, hemos utilizado localhost\mahesh en este ejemplo.

En este punto, debe iniciar sesión en la máquina virtual de Windows recién creada con las credenciales de administrador local. El siguiente paso es unir la máquina virtual al dominio.

## <a name="step-3-join-the-windows-server-virtual-machine-to-the-aad-ds-managed-domain"></a>Paso 3: Unión de una máquina virtual de Windows Server al dominio administrado con AAD-DS
Realice los pasos siguientes para unir la máquina virtual Windows Server al dominio administrado con AAD-DS.

1. Conéctese a Windows Server tal y como se muestra en el paso 2 anterior. En la pantalla Inicio, abra **Administrador del servidor**.
2. Haga clic en la opción **Servidor local** del panel izquierdo de la ventana Administrador del servidor.

    ![Inicio del Administrador del servidor en la máquina virtual](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)
3. Haga clic en **WORKGROUP** en la sección **PROPIEDADES**. En la página de propiedades **Propiedades del sistema**, haga clic en **Cambiar** para unirse al dominio.

    ![Página Propiedades del sistema](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)
4. Especifique el nombre de su dominio administrado de Servicios de dominio de Azure AD en el cuadro de texto **Dominio** y haga clic en **Aceptar**.

    ![Especificar el dominio al que se unirá](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)
5. Se le solicitará que especifique sus credenciales para unirse al dominio. Asegúrese de **especificar las credenciales de un usuario que pertenezca al grupo Administradores del controlador de dominio de AAD** . Solo los miembros de este grupo tienen privilegios para unir máquinas al dominio administrado.

    ![Especificar las credenciales para unirse al dominio](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)
6. Puede especificar credenciales de cualquiera de las maneras siguientes:

   * Formato UPN: especifique el sufijo UPN de la cuenta de usuario como está configurado en Azure AD. En este ejemplo, el sufijo UPN del usuario "bob" es "bob@domainservicespreview.onmicrosoft.com".
   * Formato SAMAccountName: puede especificar el nombre de cuenta en formato SAMAccountName. En este ejemplo, el usuario "bob" tendría que especificar "CONTOSO100\bob".

     > [!NOTE]
     > **Se recomienda usar el formato de UPN para especificar las credenciales.** SAMAccountName se puede generar automáticamente si el prefijo UPN de un usuario es demasiado largo (por ejemplo, 'joereallylongnameuser'). Si varios usuarios tienen el mismo prefijo UPN (por ejemplo, 'bob') en su inquilino de Azure AD, el servicio puede generar automáticamente su formato SAMAccountName. En estos casos, se puede utilizar el formato UPN de forma confiable para iniciar sesión en el dominio.
     >
     >
7. Una vez que la unión al dominio es correcta, verá el siguiente mensaje que le da la bienvenida al dominio. Reinicie la máquina virtual para completar la operación de unión al dominio.

    ![Bienvenido al dominio](./media/active-directory-domain-services-admin-guide/join-domain-done.png)

## <a name="troubleshooting-domain-join"></a>Solución de problemas de unión al dominio
### <a name="connectivity-issues"></a>Problemas de conectividad
Si la máquina virtual no puede encontrar el dominio, consulte los siguientes pasos de solución de problemas:

* Asegúrese de que la máquina virtual está conectada a la misma red virtual en la que habilitó Servicios de dominio. De lo contrario, la máquina virtual no podrá conectarse al dominio y, por tanto, no podrá unirse al dominio.
* Si la máquina virtual está conectada a otra red virtual, asegúrese de que esta red virtual está conectada a la red virtual en la que habilitó Servicios de dominio.
* Trate de hacer ping al dominio con el nombre del dominio administrado (por ejemplo, ping contoso100.com). Si no puede hacerlo, trate de hacer ping a las direcciones IP del dominio que aparece en la página en la que habilitó Servicios de dominio de Azure AD (por ejemplo, ping 10.0.0.4). Si puede hacer ping a la dirección IP pero no al dominio, DNS puede estar configurado incorrectamente. Es posible que no haya configurado las direcciones IP del dominio como servidores DNS de la red virtual.
* Intente vaciar la memoria caché de resolución DNS en la máquina virtual ("ipconfig /flushdns").

Si aparece el cuadro de diálogo que solicita credenciales para unirse al dominio, no tiene problemas de conectividad.

### <a name="credentials-related-issues"></a>Problemas relacionados con las credenciales
Consulte los siguientes pasos si tiene problemas con las credenciales y no puede unirse al dominio.

* Pruebe a usar el formato UPN para especificar las credenciales. El atributo SAMAccountName de su cuenta se puede generar automáticamente si hay varios usuarios con el mismo prefijo UPN en el inquilino o si el prefijo UPN es demasiado largo. Por lo tanto, el formato del atributo SAMAccountName de su cuenta puede que no sea el mismo que espera o que usa en su dominio local.
* Pruebe a utilizar las credenciales de una cuenta de usuario que pertenezca al grupo 'Administradores de controlador de dominio de AAD' para conectar equipos al dominio administrado.
* Asegúrese de que ha [habilitado la sincronización de contraseñas](active-directory-ds-getting-started-password-sync.md) según los pasos que se describen en la Guía de introducción.
* Asegúrese de que utiliza el UPN del usuario como está configurado en Azure AD (por ejemplo, "bob@domainservicespreview.onmicrosoft.com") para iniciar sesión.
* Asegúrese de haber esperado suficiente tiempo para que la sincronización de contraseñas se complete, como se especifica en la Guía de introducción.

## <a name="related-content"></a>Contenido relacionado
* [Introducción a Azure AD Domain Services](active-directory-ds-getting-started.md)
* [Administer an Azure AD Domain Services managed domain (Administración de un dominio administrado con Servicios de dominio de Azure AD)](active-directory-ds-admin-guide-administer-domain.md)


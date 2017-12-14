---
title: "Azure Active Directory Domain Services: conexión de una máquina virtual con Windows Server a un dominio administrado | Microsoft Docs"
description: "Unión de una máquina virtual de Windows Server a Servicios de dominio de Azure AD"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 29316313-c76c-4fb9-8954-5fa5ec82609e
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: maheshu
ms.openlocfilehash: 1ea3f7271bd165bf42d520e4a0267a80dcca58d5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Unión de una máquina virtual de Windows Server a un dominio administrado
Este artículo muestra cómo implementar una máquina virtual Windows Server mediante Azure Portal. A continuación, muestra cómo unir la máquina virtual a un dominio administrado de Azure AD Domain Services.

## <a name="step-1-create-the-windows-server-virtual-machine"></a>Paso 1: Crear la máquina virtual de Windows
Realice los siguientes pasos de configuración para crear una máquina virtual Windows unida a la red virtual en la que ha habilitado Servicios de dominio de Azure AD.

1. Inicie sesión en Azure Portal en [http://portal.azure.com](http://portal.azure.com).
2. Haga clic en el botón **Nuevo** de la esquina superior izquierda de Azure Portal.
3. Seleccione **Compute**y, después, seleccione **Windows Server 2016 Datacenter**.

    ![Seleccionar imagen](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)
4. Configure las opciones básicas para la máquina virtual en la página **Básico** del asistente.

    ![Configuración de las opciones básicas para la máquina virtual](./media/active-directory-domain-services-admin-guide/create-windows-vm-basics.png)

    > [!TIP]
    > El nombre de usuario y la contraseña que escriba aquí son para una cuenta de administrador local utilizada para iniciar sesión en la máquina virtual. Elija una contraseña segura para proteger la máquina virtual frente a ataques de fuerza bruta de contraseñas. No escriba las credenciales de la cuenta de usuario del dominio aquí.
    >

5. Seleccione un **Tamaño** para la máquina virtual. Para ver más tamaños, seleccione **Ver todo** o cambie el filtro **Supported disk type** (Tipo de disco admitido).

    ![Seleccionar el tamaño de la máquina virtual](./media/active-directory-domain-services-admin-guide/create-windows-vm-size.png)

6. En la página **Configuración** del asistente, seleccione la red virtual en la que está implementado el dominio administrado de Azure AD Domain Services. Elija una subred diferente que aquella en la que está implementado el dominio administrado. Para las demás opciones, conserve los valores predeterminados y haga clic en **Aceptar**.

    ![Seleccionar una red virtual para una máquina virtual](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [!TIP]
    > **Elija la red y subred virtual correctas.**
    > Seleccione la red virtual en la que está implementado el dominio administrado o una red virtual que esté conectada a ella mediante emparejamiento de redes virtuales. Si selecciona una red virtual no conectada, no se puede unir la máquina virtual al dominio administrado.
    > Le recomendamos que implemente el dominio administrado en una subred dedicada. Por lo tanto, no seleccione la subred en la que se ha habilitado el dominio administrado.

7. En la página **Compra**, revise la configuración y haga clic en **Aceptar** para implementar la máquina virtual.
8. La implementación de la máquina virtual se ancla al panel de Azure Portal.

    ![¡Listo!](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)
9. Una vez finalizada la implementación, puede ver información acerca de la máquina virtual en la página **Información general**.


## <a name="step-2-connect-to-the-windows-server-virtual-machine-using-the-local-administrator-account"></a>Paso 2: Conéctese a la nueva máquina virtual de Windows Server con la cuenta de administrador local.
Ahora, conéctese a la máquina virtual Windows Server recién creada para unirla al dominio. Use las credenciales de administrador local que especificó al crear la máquina virtual.

Realice los pasos siguientes para conectarse a la máquina virtual.

1. Haga clic en el botón **Conectar** en la página **Información general**. Se crea y se descarga un archivo de Protocolo de Escritorio remoto (.rdp).

    ![Conexión a máquina virtual de Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
2. Para conectarse a la máquina virtual, abra el archivo RDP descargado. Cuando se le solicite, haga clic en **Conectar**.
3. En el aviso de inicio de sesión, escriba sus **credenciales de administrador local**, especificadas al crear la máquina virtual. Por ejemplo, hemos utilizado localhost\mahesh en este ejemplo.
4. Puede recibir una advertencia de certificado durante el proceso de inicio de sesión. Haga clic en Sí o en Continuar para continuar con la conexión.

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

   * **Formato UPN: (recomendado)** Especifique el sufijo UPN de la cuenta de usuario como está configurado en Azure AD. En este ejemplo, el sufijo UPN del usuario "bob" es "bob@domainservicespreview.onmicrosoft.com".
   * **Formato SAMAccountName:** Puede especificar el nombre de cuenta en formato SAMAccountName. En este ejemplo, el usuario "bob" tendría que especificar "CONTOSO100\bob".

     > [!TIP]
     > **Se recomienda usar el formato de UPN para especificar las credenciales.**
     > SAMAccountName se puede generar automáticamente si el prefijo UPN de un usuario es demasiado largo (por ejemplo, "joehasareallylongname"). Si varios usuarios tienen el mismo prefijo UPN (por ejemplo, 'bob') en su inquilino de Azure AD, el servicio puede generar automáticamente su formato SAMAccountName. En estos casos, se puede utilizar el formato UPN de forma confiable para iniciar sesión en el dominio.
     >

7. Una vez que la unión al dominio es correcta, verá el siguiente mensaje que le da la bienvenida al dominio. Reinicie la máquina virtual para completar la operación de unión al dominio.

    ![Bienvenido al dominio](./media/active-directory-domain-services-admin-guide/join-domain-done.png)


## <a name="troubleshooting-domain-join"></a>Solución de problemas de unión al dominio
### <a name="connectivity-issues"></a>Problemas de conectividad
Si la máquina virtual no puede encontrar el dominio, consulte los siguientes pasos de solución de problemas:

* Asegúrese de que la máquina virtual está conectada a la misma red virtual en la que habilitó Servicios de dominio. De lo contrario, la máquina virtual no podrá conectarse al dominio y, por tanto, no podrá unirse al dominio.
* Asegúrese de que la máquina virtual está en una red virtual que a su vez está conectada a la red virtual en la que habilitó Domain Services.
* Trate de hacer ping al dominio con el nombre del dominio administrado (por ejemplo, ping contoso100.com). Si no puede hacerlo, trate de hacer ping a las direcciones IP del dominio que aparece en la página en la que habilitó Servicios de dominio de Azure AD (por ejemplo, ping 10.0.0.4). Si puede hacer ping a la dirección IP pero no al dominio, DNS puede estar configurado incorrectamente. Compruebe si las direcciones IP del dominio están configuradas como servidores DNS de la red virtual.
* Intente vaciar la memoria caché de resolución DNS en la máquina virtual ("ipconfig /flushdns").

Si aparece el cuadro de diálogo que solicita credenciales para unirse al dominio, no tiene problemas de conectividad.

### <a name="credentials-related-issues"></a>Problemas relacionados con las credenciales
Consulte los siguientes pasos si tiene problemas con las credenciales y no puede unirse al dominio.

* Pruebe a usar el formato UPN para especificar las credenciales. El atributo SAMAccountName de su cuenta se puede generar automáticamente si hay varios usuarios con el mismo prefijo UPN en el inquilino o si el prefijo UPN es demasiado largo. Por lo tanto, el formato del atributo SAMAccountName de su cuenta puede que no sea el mismo que espera o que usa en su dominio local.
* Intente usar las credenciales de una cuenta de usuario que pertenezca al grupo "Administradores de controlador de dominio de AAD".
* Asegúrese de que ha [habilitado la sincronización de contraseñas](active-directory-ds-getting-started-password-sync.md) según los pasos que se describen en la Guía de introducción.
* Asegúrese de que utiliza el UPN del usuario como está configurado en Azure AD (por ejemplo, "bob@domainservicespreview.onmicrosoft.com") para iniciar sesión.
* Asegúrese de haber esperado suficiente tiempo para que la sincronización de contraseñas se complete, como se especifica en la Guía de introducción.

## <a name="related-content"></a>Contenido relacionado
* [Introducción a Azure AD Domain Services](active-directory-ds-getting-started.md)
* [Administer an Azure AD Domain Services managed domain (Administración de un dominio administrado con Servicios de dominio de Azure AD)](active-directory-ds-admin-guide-administer-domain.md)

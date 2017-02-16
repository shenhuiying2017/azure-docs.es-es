---
title: "Azure AD Connect: Inicio de sesión único | Microsoft Docs"
description: "Este tema le proporcionará la información necesaria para realizar un inicio de sesión único desde una instancia de Active Directory (AD) local en otra de Azure Active Directory (Azure AD) basada en la nube y en los servicios conectados."
services: active-directory
keywords: "qué es Azure AD Connect, instalar Active Directory, componentes necesarios para Azure AD, SSO, inicio de sesión único"
documentationcenter: 
author: billmath
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 44c5726d0bf621e463aa2f3ab061ed12d48418b0
ms.openlocfilehash: b36d639b5d6b18de3a8233f801cdd0abf3f25825


---

# <a name="what-is-single-sign-on-sso-preview"></a>Qué es el inicio de sesión único (SSO) (versión preliminar)
Inicio de sesión único es una opción que se puede habilitar en Azure Active Directory Connect tanto con [sincronización de hash de contraseña](active-directory-aadconnectsync-implement-password-synchronization.md) como con [autenticación de paso a través](active-directory-aadconnect-pass-through-authentication.md).  Cuando se habilita, los usuarios solo tienen que escribir su nombre de usuario y no necesitan escribir su contraseña para iniciar sesión en Azure Active Directory (Azure AD) u otros servicios en la nube cuando estén en sus máquinas corporativas y se conecten a la red corporativa.

![Inicio de sesión único](./media/active-directory-aadconnect-sso/sso1.png)

Al proporcionar SSO a los usuarios finales, el acceso a los servicios basados en la nube es mucho más familiar y supone para la organización un proceso seguro y sencillo que no requiere ningún componente local adicional.

SSO es una característica que se habilita a través de AAD Connect y funciona con sincronización de hash de contraseña o autenticación de paso a través y la instancia de Active Directory local.  Para que los usuarios finales usen el inicio de sesión único en su entorno, debe asegurarse de que estos:


- Se encuentran en una máquina unida al dominio.
- Tienen conexión directa con un controlador de dominio, por ejemplo, en la red con cable o inalámbrica corporativa, o a través de una conexión de acceso remoto, como una conexión VPN.
- Definen los puntos de conexión de Kerberos en la nube como parte de la zona de Intranet de los exploradores.

Si falta cualquiera de los elementos anteriores, por ejemplo, que la máquina esté fuera de la red corporativa y Active Directory no esté disponible, simplemente se solicitará al usuario que escriba su contraseña tal y como lo haría sin el inicio de sesión único.

## <a name="supported-clients"></a>Clientes compatibles
Se admite el inicio de sesión único a través de clientes basados en el explorador web y los clientes de Office que admiten la [autenticación moderna](https://aka.ms/modernauthga) en máquinas con la capacidad de autenticación de Kerberos, como Windows.  La tabla siguiente proporciona detalles de los clientes basados en el explorador en distintos sistemas operativos.

| SO\Explorador |Internet Explorer|Chrome|Firefox|perimetral
| --- | --- |--- | --- |--- |
|Windows 10|Sí|Sí|Sí*|No
|Windows 8.1|Sí|Sí|Sí*|N/D
|Windows 8|Sí|Sí|Sí*|N/D
|Windows 7|Sí|Sí|Sí*|N/D
|Mac|N/D|N/D|N/D|N/D

* Requiere una configuración independiente.

>[!NOTE]
>Para los clientes basados en Windows 10, la recomendación es usar [Azure AD Join](../active-directory-azureadjoin-overview.md) para una mejor experiencia con Azure AD.

## <a name="how-single-sign-on-works"></a>Funcionamiento del inicio de sesión único

Cuando se habilita el inicio de sesión único en Azure AD Connect, se crea una cuenta de equipo denominada AZUREADSSOACCT en la instancia de Active Directory local y la clave de descifrado de Kerberos se comparte de forma segura con Azure AD.  Además, se crean dos nombres de entidad de servicio (SPN) de Kerberos que representan las direcciones URL en la nube que se usan durante la autenticación entre el cliente y Azure AD.

Una vez completada la configuración, el proceso de autenticación es el mismo que el de cualquier otra aplicación basada en Autenticación integrada de Windows (IWA).  Si está familiarizado con cómo funciona IWA, ya sabe cómo funciona el inicio de sesión único con Azure AD.  Si no lo conoce, el proceso de IWA es como sigue:

![Inicio de sesión único](./media/active-directory-aadconnect-sso/sso2.png)

En primer lugar el usuario intenta obtener acceso a un recurso que confía en los tokens emitidos por Azure AD, como SharePoint Online.  SharePoint Online entonces redirige al usuario para que se autentique con Azure AD. El usuario proporciona a continuación su nombre de usuario para que Azure AD pueda determinar si el inicio de sesión único está habilitado para su organización. Suponiendo que el inicio de sesión único esté habilitado para la organización, ocurriría lo siguiente.

1.  Azure AD solicita al cliente, con una respuesta 401 No autorizado, que proporcione un vale de Kerberos.
2.  El cliente solicita un vale desde Active Directory para Azure AD.
3.  Active Directory ubica la cuenta de la máquina, creada por Azure AD Connect, y devuelve un vale de Kerberos al cliente, cifrado con el secreto de la cuenta de la máquina. El vale incluye la identidad del usuario que inició sesión en la máquina.
4.  El cliente envía el vale de Kerberos que adquirió desde Active Directory a Azure AD.
5.  Azure AD descifra el vale de Kerberos mediante la clave previamente compartida y devuelve un token al usuario o le pide que proporcione pruebas adicionales, como la autenticación multifactor, según requiera el recurso.

El inicio de sesión único es una característica oportunista, lo que significa que, si se produce un error por algún motivo, el usuario solo tiene que escribir su contraseña en la página de inicio de sesión como de costumbre.

## <a name="enabling-sso-with-pass-through-authentication-or-password-hash-sync"></a>Habilitación de SSO con la autenticación de paso a través o la sincronización de hash de contraseña
Azure AD Connect proporciona un proceso sencillo para habilitar el inicio de sesión único con la autenticación de paso a través o la sincronización de hash de contraseña.  Debe asegurarse de que tiene derechos de administrador de dominio en uno de los dominios de cada bosque de la sincronización para permitir la configuración de los nombres de entidad de servicio (SPN) de Kerberos en la cuenta de la máquina.  El nombre de usuario y la contraseña no se almacenan en Azure AD Connect ni en Azure AD y solo se usan para esta operación.

Al instalar Azure AD Connect, seleccione la instalación personalizada para poder seleccionar la opción de inicio de sesión único en la página de inicio de sesión del usuario. Para más información, consulte [Instalación personalizada de Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

![Inicio de sesión único](./media/active-directory-aadconnect-sso/sso3.png)

Una vez habilitado el inicio de sesión único, puede continuar con el asistente hasta llegar a la página de inicio de sesión único.

![Inicio de sesión único](./media/active-directory-aadconnect-sso/sso4.png)

Proporcione los detalles de la cuenta correspondiente en cada uno de los bosques de la lista y el inicio de sesión único se habilitará para su directorio de Azure.

>[!NOTE]
>Azure AD Connect debe ser capaz de comunicarse con *.msappproxy.net en el puerto 9090 (TCP) para configurar SSO. Esto solo es necesario durante la configuración y no se utiliza durante las autenticaciones de los usuarios finales.

## <a name="ensuring-clients-sign-in-automatically"></a>Seguridad de que los clientes inician sesión automáticamente
De forma predeterminada, los exploradores no intentarán enviar credenciales a los servidores web a menos que la dirección URL se defina en la zona de Intranet.  Por lo general, el explorador examina la dirección URL para calcular la zona correspondiente.  Por ejemplo, si la dirección URL es http://intranet/, el explorador enviará automáticamente las credenciales, ya que la dirección URL se asignará a la zona de intranet.  Sin embargo, si la dirección URL contiene un punto, como http://intranet.contoso.com/, el servidor no enviará automáticamente las credenciales y tratará la dirección URL como lo haría con cualquier sitio de internet.

Como las direcciones URL que se usan para el inicio de sesión único en Azure AD contienen un punto (son nombres de host globalmente enrutables), deben agregarse explícitamente a la zona de intranet de la máquina para que el explorador envíe automáticamente las credenciales del usuario con la sesión iniciada en forma de vale de Kerberos para Azure AD.  La manera más fácil de agregar las direcciones URL necesarias para la zona de intranet es simplemente crear una directiva de grupo en Active Directory.

1.  Abra las herramientas de administración de directivas de grupo.
2.  Edite la directiva de grupo que se aplicará a todos los usuarios.  Por ejemplo, la directiva de dominio predeterminada.
3.  Vaya a **User Configuration\Administrative Templates\Windows Components\Internet Explorer\Internet Control Panel\Security Page** y seleccione **Site to Zone Assignment List** (Lista de asignación de sitio a zona).
![Inicio de sesión único](./media/active-directory-aadconnect-sso/sso6.png) </br>
4.  Habilite la directiva y escriba los valores/datos siguientes en el cuadro de diálogo.</br>

        Value: https://autologon.microsoftazuread-sso.com
        Data: 1
        Value: https://aadg.windows.net.nsatc.net
        Data: 1'
5.  Tendrá que tener un aspecto similar al siguiente: ![Inicio de sesión único](./media/active-directory-aadconnect-sso/sso7.png)

6.  Haga clic en Aceptar y en Aceptar nuevo.

Los usuarios ya están listos para el inicio de sesión único.

>[!NOTE]
>De forma predeterminada, Chrome usará el mismo conjunto de direcciones URL de sitios de confianza, como Internet Explorer.  Si tiene una configuración distinta para Chrome, debe actualizarla de manera independiente.

## <a name="troubleshooting-single-sign-on-issues"></a>Solución de problemas de inicio de sesión único
Para el inicio de sesión único, es importante comprobar que el cliente está configurado correctamente, por ejemplo:

1.  Que https://autologon.microsoftazuread-sso.com y https://aadg.windows.net.nsatc.net están definidos en la zona de intranet.
2.  Que la estación de trabajo está unida al dominio.
3.  Que el usuario ha iniciado sesión con una cuenta de dominio.
4.  Que la máquina está conectada a la red corporativa.
5.  Que la hora de la máquina está sincronizada con la instancia de Active Directory y que la hora de los controladores de dominio difiere como máximo en 5 minutos de la hora correcta.
6.  Purgue los vales de Kerberos existentes de los clientes; por ejemplo, ejecutando el comando "klist purge" desde un símbolo del sistema.

Si ha confirmado los requisitos anteriores, para más información, revise los registros de la consola del explorador.  Los registros de la consola se encuentran en Herramientas de desarrollo,  pueden ayudarle a determinar el problema.

## <a name="event-log-entries"></a>Entradas del registro
Cada vez que un usuario utiliza el inicio de sesión único, se agrega una entrada al registro de eventos del controlador de dominio (si está habilitada la auditoría de eventos correctos).  Revise los registros para buscar estos eventos en el evento de seguridad 4769 asociado a la cuenta de máquina AzureADSSOAcc$.  El siguiente filtro busca todos los eventos de seguridad asociados con la cuenta de máquina:

    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>



<!--HONumber=Jan17_HO1-->



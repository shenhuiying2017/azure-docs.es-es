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
ms.date: 02/08/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: a268907eea2862ae2d054f30accfd4d771a7d880
ms.openlocfilehash: ae97e66b8fb0992550c5a4f1f8418c5cb7e496b5

---

# <a name="what-is-single-sign-on-sso-preview"></a>Qué es el inicio de sesión único (SSO) (versión preliminar)
Inicio de sesión único es una opción que se puede habilitar en Azure Active Directory Connect bien con [sincronización de contraseña](active-directory-aadconnectsync-implement-password-synchronization.md) o con [autenticación de paso a través](active-directory-aadconnect-pass-through-authentication.md). Cuando está habilitada, los usuarios solo tienen que escribir su nombre de usuario y no necesitan escribir la contraseña para iniciar sesión en Azure Active Directory (Azure AD) ni otros servicios en la nube cuando estén conectados a la red corporativa desde sus máquinas corporativas.

![Inicio de sesión único](./media/active-directory-aadconnect-sso/sso1.png)

Al proporcionar SSO a los usuarios finales, el acceso a los servicios basados en la nube es mucho más familiar y supone para la organización un proceso seguro y sencillo que no requiere ningún componente local adicional.

SSO es una característica que se habilita a través de Azure AD Connect y funciona con sincronización de contraseña o autenticación de paso a través y la instancia de Active Directory local. Para que los usuarios finales usen el inicio de sesión único en su entorno, debe asegurarse de que estos:

- Se encuentran en una máquina unida al dominio.
- Tienen conexión directa con un controlador de dominio, por ejemplo, en la red con cable o inalámbrica corporativa, o a través de una conexión de acceso remoto, como una conexión VPN.
- Definen los puntos de conexión de Kerberos en la nube como parte de la zona de intranet del explorador.

Si falta cualquiera de estos requisitos, por ejemplo, si la máquina está fuera de la red corporativa, se solicita al usuario que escriba su contraseña tal y como lo haría sin el inicio de sesión único.

## <a name="supported-clients"></a>Clientes compatibles
Se admite el inicio de sesión único a través de clientes basados en el explorador web y clientes de Office que admiten la [autenticación moderna](https://aka.ms/modernauthga) en máquinas con la funcionalidad de autenticación de Kerberos, como Windows. En la tabla siguiente, se proporcionan detalles de los clientes basados en el explorador en distintos sistemas operativos.

| SO\Explorador |Internet Explorer|Chrome|Firefox|perimetral
| --- | --- |--- | --- | --- |
|Windows 10|Sí|Sí|Sí*|No
|Windows 8.1|Sí|Sí|Sí*|N/D
|Windows 8|Sí|Sí|Sí*|N/D
|Windows 7|Sí|Sí|Sí*|N/D
|Mac|N/D|N/D|N/D|N/D

\*Requiere una configuración independiente.

>[!NOTE]
>Para los clientes basados en Windows 10, la recomendación es usar [Azure AD Join](../active-directory-azureadjoin-overview.md) para una mejor experiencia con Azure AD.

## <a name="how-single-sign-on-works"></a>Funcionamiento del inicio de sesión único

Cuando se habilita el inicio de sesión único en Azure AD Connect, se crea una cuenta de equipo denominada AZUREADSSOACCT en la instancia de Active Directory local y la clave de descifrado de Kerberos se comparte de forma segura con Azure AD. Además, se crean dos nombres de entidad de servicio (SPN) de Kerberos que representan las direcciones URL en la nube que se usan durante la autenticación entre el cliente y Azure AD.

Una vez completada la configuración, el proceso de autenticación es el mismo que el de cualquier otra aplicación basada en Autenticación integrada de Windows (IWA). Si está familiarizado con el funcionamiento de IWA, ya sabe cómo funciona el inicio de sesión único con Azure AD. Si no lo conoce, el proceso de IWA es como sigue:

![Inicio de sesión único](./media/active-directory-aadconnect-sso/sso2.png)

En primer lugar, el usuario intenta acceder a un recurso que confía en tokens emitidos por Azure AD, como SharePoint Online. Entonces, SharePoint Online redirige al usuario para que se autentique con Azure AD. A continuación, el usuario proporciona su nombre de usuario para que Azure AD pueda determinar si el inicio de sesión único está habilitado para su organización. Suponiendo que sí lo esté, se produciría el siguiente tráfico.

1.    Azure AD solicita al cliente, con una respuesta 401 No autorizado, que proporcione un vale de Kerberos.
2.    El cliente solicita un vale de Active Directory para Azure AD.
3.    Active Directory ubica la cuenta de la máquina, creada por Azure AD Connect, y devuelve un vale Kerberos al cliente, cifrado con el secreto de la cuenta de la máquina. El vale incluye la identidad del usuario que inició sesión en la máquina.
4.    El cliente envía el vale de Kerberos que adquirió desde Active Directory a Azure AD.
5.    Azure AD descifra el vale Kerberos mediante la clave antes compartida. A continuación, devuelve un token al usuario o pide al usuario que proporcione prueba adicional, como la autenticación multifactor según sea necesario para el recurso.

El inicio de sesión único es una característica oportunista, lo que significa que, si se produce un error por algún motivo, basta con que el usuario escriba su contraseña en la página de inicio de sesión como de costumbre.

## <a name="enabling-sso-with-pass-through-authentication-or-password-sync"></a>Habilitación de SSO con la autenticación de paso a través o la sincronización de contraseña
Azure AD Connect proporciona un proceso sencillo para habilitar el inicio de sesión único con la autenticación de paso a través o la sincronización de contraseña. Asegúrese de que tiene derechos de administrador de dominio en uno de los dominios de cada bosque de la sincronización para permitir la configuración de los nombres de entidad de servicio (SPN) de Kerberos en la cuenta de la máquina. El nombre de usuario y la contraseña no se almacenan en Azure AD Connect ni en Azure AD y solo se usan para esta operación.

Al instalar Azure AD Connect, seleccione una instalación personalizada para poder seleccionar la opción de inicio de sesión único en la página de inicio de sesión del usuario. Para más información, consulte [Instalación personalizada de Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

![Inicio de sesión único](./media/active-directory-aadconnect-sso/sso3.png)

Una vez habilitado el inicio de sesión único, puede continuar con el asistente para instalación hasta llegar a la página de inicio de sesión único.

![Inicio de sesión único](./media/active-directory-aadconnect-sso/sso4.png)

Proporcione los detalles de la cuenta correspondiente para cada uno de los bosques de la lista y el inicio de sesión único quedará habilitado para su directorio de Azure.

>[!NOTE]
>Azure AD Connect debe ser capaz de comunicarse con \*.msappproxy.net en el puerto 9090 (TCP) para configurar SSO. Esta apertura del puerto solo es necesaria durante la configuración y no se utiliza durante las autenticaciones de los usuarios finales.

## <a name="ensuring-clients-sign-in-automatically"></a>Seguridad de que los clientes inicien sesión automáticamente
De forma predeterminada, los exploradores no intentan enviar credenciales a los servidores web a menos que la dirección URL se defina en la zona de intranet. Por lo general, el explorador examina la dirección URL para calcular la zona correspondiente. Por ejemplo, si la dirección URL es http://intranet/, el explorador envía automáticamente las credenciales, ya que la dirección URL se asigna a la zona de intranet. Sin embargo, si la dirección URL contiene un punto, como http://intranet.contoso.com/, el equipo no envía automáticamente las credenciales y la trata como cualquier sitio de Internet.

Dado que las direcciones URL usadas para el inicio de sesión único en Azure AD contienen un punto, deben agregarse explícitamente a la zona de intranet de la máquina. Esta configuración hace que el explorador envíe automáticamente a Azure AD las credenciales del usuario cuya sesión esté iniciada en ese momento en forma de vale Kerberos. La manera más fácil de agregar las direcciones URL necesarias para la zona de intranet es crear una directiva de grupo en Active Directory.

1.    Abra las herramientas de administración de directivas de grupo.
2.    Edite la directiva de grupo que se aplica a todos los usuarios, por ejemplo, la **directiva de dominio predeterminada**.
3.    Vaya a **User Configuration\Administrative Templates\Windows Components\Internet Explorer\Internet Control Panel\Security Page** y seleccione **Site to Zone Assignment List** (Lista de asignación de sitio a zona).
![Inicio de sesión único](./media/active-directory-aadconnect-sso/sso6.png)  
4.    Habilite la directiva y escriba los valores/datos siguientes en el cuadro de diálogo.  

        Value: https://autologon.microsoftazuread-sso.com  
        Data 1  
        Value: https://aadg.windows.net.nsatc.net  
        Data 1  
5.    El archivo debe tener un aspecto similar al siguiente:  
![Inicio de sesión único](./media/active-directory-aadconnect-sso/sso7.png)

6.    Haga clic en **Aceptar** y en **Aceptar** de nuevo.

Los usuarios ya están listos para el inicio de sesión único.

>[!NOTE]
>De forma predeterminada, Chrome usa el mismo conjunto de direcciones URL de sitios de confianza que Internet Explorer. Si tiene una configuración distinta para Chrome, debe actualizar estos sitios de manera independiente.

## <a name="troubleshooting-single-sign-on-issues"></a>Solución de problemas de inicio de sesión único
Para el inicio de sesión único, es importante comprobar que el cliente esté configurado correctamente, por ejemplo:

1.    Que https://autologon.microsoftazuread-sso.com y https://aadg.windows.net.nsatc.net están definidos en la zona de intranet.
2.    Que la estación de trabajo está unida al dominio.
3.    Que el usuario ha iniciado sesión con una cuenta de dominio.
4.    Que la máquina está conectada a la red corporativa.
5.    Que la hora de la máquina está sincronizada con la instancia de Active Directory y que la hora de los controladores de dominio difiere como máximo en 5 minutos de la hora correcta.
6.    Purgue los vales Kerberos existentes de los clientes; por ejemplo, ejecute el comando **klist purge** desde un símbolo del sistema.

Si ha confirmado los requisitos anteriores, para más información, revise los registros de la consola del explorador. Los registros de la consola se encuentran en Herramientas de desarrollo, y pueden ayudarle a determinar el problema.

## <a name="event-log-entries"></a>Entradas del registro
Si está habilitada la auditoría de aciertos, cada vez que un usuario utilice el inicio de sesión único, se agrega una entrada al registro de eventos del controlador de dominio. Revise los registros para buscar estos eventos en el evento de seguridad 4769 asociado a la cuenta de equipo **AzureADSSOAcc$**. El siguiente filtro busca todos los eventos de seguridad asociados con la cuenta de máquina:

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```



<!--HONumber=Feb17_HO2-->



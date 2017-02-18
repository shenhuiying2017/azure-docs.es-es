---
title: "Azure AD Connect: Autenticación de paso a través | Microsoft Docs"
description: "Este tema le proporcionará la información necesaria sobre el funcionamiento de las autenticaciones de paso a través de Azure AD en Active Directory (AD) local para proporcionar acceso a Azure Active Directory (Azure AD) y a los servicios conectados."
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
ms.sourcegitcommit: 67b832253619afe789a4dfdb95893e8c0ae62bee
ms.openlocfilehash: 17890fddf948ddc0e89a9107ac5fe65223cd05e1

---

# <a name="what-is-azure-ad-pass-through-authentication"></a>Qué es la autenticación de paso a través de Azure AD
Al utilizar las mismas credenciales (nombre de usuario y contraseña) para acceder a los recursos corporativos y a los servicios basados en la nube, los usuarios no tienen que recordar unas credenciales distintas. Esto reduce las posibilidades de que se olviden de cómo iniciar sesión y presenta la ventaja de reducir la necesidad de que el departamento de soporte técnico tenga que restablecer contraseñas.

Mientras que en muchas organizaciones se utiliza la [sincronización de contraseñas](active-directory-aadconnectsync-implement-password-synchronization.md) de Azure AD para proporcionar a los usuarios una única credencial para acceder a los servicios locales y en la nube, otras requieren que las contraseñas, incluso aquellas en forma de hash, permanezcan dentro de los límites internos de la organización.

La autenticación de paso a través de Azure AD proporciona una solución sencilla para estos clientes. Se asegura de que la validación de contraseñas para servicios de Azure AD se realice en su directorio Active Directory local. Las contraseñas se pueden validar sin necesidad de poseer una compleja infraestructura de red ni de almacenar las contraseñas locales en la nube de forma alguna.

Cuando se combina con la opción [Inicio de sesión único](active-directory-aadconnect-sso.md), los usuarios no necesitan escribir su contraseña para iniciar sesión en Azure AD ni otros servicios en la nube. Esta característica proporciona a estos clientes una experiencia realmente integrada en sus máquinas corporativas.

![Autenticación de paso a través](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

La autenticación de paso a través se puede configurar mediante Azure AD Connect y utiliza un agente local simple que escucha las solicitudes de validación de contraseña. El agente se puede implementar fácilmente en varios equipos para proporcionar una alta disponibilidad y equilibrio de carga. Puesto que todas las comunicaciones son exclusivamente salientes, no es necesario disponer de una arquitectura de red perimetral (DMZ) ni instalar el conector en una arquitectura de red perimetral. Los requisitos del equipo para el conector son los siguientes:

- Windows Server 2012 R2 o superior
- Unión a un dominio en el bosque en el que se validan los usuarios

>[!NOTE]
>Pueden admitirse entornos de varios bosques si hay relaciones de confianza entre los bosques y el enrutamiento de sufijos de nombre está configurado correctamente.

## <a name="supported-clients-in-the-preview"></a>Clientes compatibles en la versión preliminar
La autenticación de paso a través es compatible mediante clientes basados en explorador web y clientes de Office compatibles con la [autenticación moderna](https://aka.ms/modernauthga). Para los clientes incompatibles, como los clientes de Office heredados o Exchange Active Sync (es decir, clientes de correo electrónico nativos en dispositivos móviles), se recomienda a los usuarios que utilicen el equivalente a la autenticación moderna. Estos clientes no solo permiten la autenticación de paso a través, sino también que se aplique el acceso condicional, como la autenticación multifactor.

Actualmente no se admite la autenticación de paso a través para clientes que usen Windows 10 y se hayan unido a Azure AD. No obstante, los clientes pueden utilizar la sincronización de contraseña como reserva automática para Windows 10, además de para los clientes heredados.

>[!NOTE]
>Durante la versión preliminar, se habilita la sincronización de contraseña de forma predeterminada cuando se selecciona la autenticación de paso a través como opción de inicio de sesión en Azure AD Connect. Esta configuración se puede deshabilitar en la página Opciones de Azure AD Connect.

## <a name="how-azure-ad-pass-through-authentication-works"></a>Cómo funciona la autenticación de paso a través de Azure AD
Cuando un usuario escribe su nombre de usuario y contraseña en la página de inicio de sesión de Azure AD, Azure AD coloca ambos en la cola del conector local que corresponda para la validación. A continuación, uno de los conectores locales disponibles recupera el nombre de usuario y la contraseña y valida estas credenciales con Active Directory. La validación se produce a través de API de Windows estándar, de manera similar al modo en que los Servicios de federación de Active Directory validan la contraseña.

Después, el controlador de dominio local evalúa la solicitud y devuelve una respuesta al conector, que, a su vez, la devuelve a Azure AD. Azure AD evalúa entonces la respuesta y responde al usuario según corresponda, por ejemplo, emitiendo un token o solicitando la autenticación multifactor. En el diagrama se muestran los diversos pasos:

![Autenticación de paso a través](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="azure-ad-pass-through-prerequisites"></a>Requisitos previos de paso a través de Azure AD
Antes de poder habilitar y usar la autenticación de paso a través de Azure AD, debe disponer de lo siguiente:

- Azure AD Connect
- Un inquilino de Azure AD del que sea administrador global.

>[!NOTE]
>Se recomienda que la cuenta sea una cuenta de administrador solo en la nube, para que pueda administrar la configuración del inquilino en caso de que sus servicios locales generen errores o no estén disponibles.

- Un servidor con Windows Server 2012 R2 o posterior en el que ejecutar Azure AD Connect. Esta máquina debe pertenecer al mismo bosque que los usuarios que se validan.
- Si tiene más de un bosque que contenga usuarios que se van a validar con Azure AD, los bosques deben tener relaciones de confianza entre ellos.
- El valor UserPrincipalName local debe utilizarse como nombre de usuario de Azure AD.
- Un segundo servidor con Windows Server 2012 R2 o posterior en el que ejecutar un segundo conector para obtener una alta disponibilidad y equilibrio de carga. A continuación se incluyen instrucciones sobre cómo implementar este conector.
- Si hay un firewall entre el conector y Azure AD, asegúrese de lo siguiente:
    - Si está habilitado el filtrado para direcciones URL, asegúrese de que el conector puede comunicarse con las siguientes direcciones URL:
        -  \*.msappproxy.net
        -  \*.servicebus.windows.net.  
        -  El conector también establece conexión en conexiones IP directas con los [intervalos de IP del centro de datos de Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653).
    - Asegúrese de que el firewall no realiza la inspección de SSL mientras el conector usa certificados de cliente para comunicarse con Azure AD.
    - Asegúrese de que el conector puede realizar solicitudes HTTPS (TCP) a Azure AD en los siguientes puertos.

|Número de puerto|Descripción
| --- | ---
|80|Permite el tráfico HTTP saliente para la validación de seguridad, como listas de revocación de certificados SSL.
|443|    Habilita la autenticación de usuario en Azure AD.
|8080/443|    Habilita la secuencia de arranque del conector y la actualización automática del conector.
|9090|    Habilita el registro de conector (solo es necesario para el proceso de registro del conector).
|9091|    Habilita la renovación automática de certificados de confianza del conector.
|9352, 5671|    Habilita la comunicación entre el conector y el servicio de Azure AD para las solicitudes entrantes.
|9350|    [Opcional] Posibilita un mejor rendimiento de las solicitudes entrantes.
|10100–10120|    Habilita las respuestas del conector a Azure AD.

Si el firewall fuerza el tráfico según los usuarios que se originan, abra estos puertos para el tráfico procedente de  los servicios de Windows que se ejecutan como un servicio de red. Además, asegúrese de habilitar el puerto 8080 para NT Authority\System.

## <a name="enabling-pass-through-authentication"></a>Habilitación de la autenticación de paso a través
La autenticación de paso a través de Azure AD se habilita por medio de Azure AD Connect. Al habilitar la autenticación de paso a través, se implementa el primer conector en el mismo servidor que Azure AD Connect. Al instalar Azure AD Connect, seleccione una instalación personalizada y elija Autenticación de paso a través en la página de opciones de inicio de sesión. Para más información, consulte [Instalación personalizada de Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

![Inicio de sesión único](./media/active-directory-aadconnect-sso/sso3.png)

De forma predeterminada, el primer conector de autenticación de paso a través se instala en el servidor de Azure AD Connect. Debe implementar un segundo conector en otro equipo para asegurarse de disponer de una alta disponibilidad y de equilibrio de carga para las solicitudes de autenticación.

Para implementar el segundo conector, siga las instrucciones siguientes:

### <a name="step-1-install-the-connector-without-registration"></a>Paso 1: Instalar el conector sin registro

1.    Descargue el [conector](https://go.microsoft.com/fwlink/?linkid=837580) más reciente.
2.    Abra un símbolo del sistema como administrador.
3.    Ejecute el siguiente comando en el que el modificador /q significa instalación desatendida (la instalación no le pide que acepte el contrato de licencia para el usuario final).

```
AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
```

### <a name="step-2-register-the-connector-with-azure-ad-for-pass-through-authentication"></a>Paso 2: Registrar el conector con Azure AD para la autenticación de paso a través

1.    Abra una ventana de PowerShell como administrador.
2.    Vaya a **C:\Archivos de programa\Microsoft AAD App Proxy Connector** y ejecute el script.  
`.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Feature PassthroughAuthentication`
3.    Cuando se le solicite, escriba las credenciales de su cuenta de administrador de inquilinos de Azure AD.

## <a name="troubleshooting-pass-through-authentication"></a>Solución de problemas de la autenticación de paso a través
Al solucionar problemas de autenticación de paso a través, existen diversas categorías de problemas que pueden producirse. Según el tipo de problema, es posible que deba buscar soluciones en distintos lugares.

Para errores relacionados con el conector, puede consultar el registro de eventos de la máquina del conector en **Application and Service Logs\Microsoft\AadApplicationProxy\Connector\Admin**. Si es necesario, puede disponer de registros más detallados si ve los registros de análisis y depuración, y habilita el registro de sesiones del conector. No se recomienda la ejecución con este registro habilitado de forma predeterminada, y el contenido solo es visible una vez que el registro se deshabilita.

También es posible encontrar más información en los registros de seguimiento del conector, en **C:\Programdata\Microsoft\Microsoft AAD Application Proxy Connector\Trace**. Estos registros también indican la razón por la que se ha producido un error en la autenticación de paso a través para un usuario determinado, como la entrada siguiente, que incluye el código de error 1328:

```
    ApplicationProxyConnectorService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

Puede obtener los detalles del error si inicia un símbolo del sistema y ejecuta el siguiente comando (reemplace "1328" por el número de error de la solicitud):

`Net helpmsg 1328`

El resultado es similar a la siguiente respuesta:

![Autenticación de paso a través](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

Si está habilitado el registro de auditoría, también es posible encontrar información adicional en los registros de seguridad de los controladores de dominio. La siguiente sería una consulta simple para obtener las solicitudes de autenticación del conector:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe')]]</Select>
    </Query>
    </QueryList>
```

A continuación, se detallan otros errores notificados en la pantalla de inicio de sesión de Azure AD, junto con los pasos de resolución correspondientes.

|Error|Description|Resolución
| --- | --- | ---
|AADSTS80001|No es posible conectarse a Active Directory.|Asegúrese de que los equipos de conector están unidos a un dominio y se pueden conectar a Active Directory.  
|AADSTS8002|Se ha agotado el tiempo de espera al conectarse a Active Directory.|Compruebe que Active Directory está disponible y que responde a las solicitudes del conector.
|AADSTS80004|El nombre de usuario transferido al conector no era válido.|Asegúrese de que el usuario esté intentando iniciar sesión con el nombre de usuario correcto.
|AADSTS80005|La validación encontró una excepción WebException impredecible|Se trata de un problema transitorio. Vuelva a intentarlo. Si continúa, póngase en contacto con el soporte técnico de Microsoft.
|AADSTS80007|Error al establecer comunicación con Active Directory.|Compruebe los registros del conector para más información y verifique que Active Directory está funcionando según lo previsto.



<!--HONumber=Jan17_HO5-->



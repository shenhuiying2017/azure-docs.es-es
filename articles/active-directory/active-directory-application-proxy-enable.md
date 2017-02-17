---
title: "Habilitación del proxy de la aplicación de Azure AD | Microsoft Docs"
description: "Active el proxy de aplicación en el Portal de Azure clásico e instale los conectores del proxy inverso."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: c7186f98-dd80-4910-92a4-a7b8ff6272b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/19/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 67e50a09d9863fe081115c0f303ac63b2b6113f2
ms.openlocfilehash: 32a9069f8572ca5c01af9e80dbe92e88727375aa


---
# <a name="enable-application-proxy-in-the-azure-portal"></a>Habilitación del proxy de aplicación en el Portal de Azure
En este artículo se explican los pasos para habilitar el proxy de aplicación de Microsoft Azure AD en el directorio en la nube de Azure AD.

Si no sabe cómo puede ayudarle el proxy de aplicación, consulte [Provisión de acceso remoto seguro a aplicaciones locales](active-directory-application-proxy-get-started.md).

## <a name="application-proxy-prerequisites"></a>Requisitos previos del proxy de la aplicación
Antes de poder habilitar y usar los servicios del proxy de la aplicación, debe tener lo siguiente:

* Una [suscripción Basic o Premium a Microsoft Azure AD](active-directory-editions.md) y un directorio de Azure AD del que sea administrador global.
* Un servidor en el que se ejecute Windows Server 2012 R2, Windows 8.1, o una versión superior, y en el que se pueda instalar el conector del proxy de la aplicación. El servidor envía solicitudes a los servicios del proxy de aplicación en la nube y necesita una conexión HTTPS a las aplicaciones que va a publicar.
  
  * Para habilitar el inicio de sesión único en las aplicaciones publicadas, esta máquina debe estar unida al mismo dominio de AD que las aplicaciones que va a publicar.
* Si hay un firewall en la ruta de acceso, es preciso asegurarse de que está abierto con el fin de que el conector pueda realizar solicitudes HTTPS (TCP) al proxy de la aplicación. El conector utiliza estos puertos, junto con los subdominios que forman parte de los dominios de alto nivel: msappproxy.net y servicebus.windows.net. Asegúrese de abrir los siguientes puertos al tráfico **saliente** :
  
  | Número de puerto | Descripción |
  | --- | --- |
  | 80 |Habilita el tráfico HTTP saliente para la validación de seguridad |
  | 443 |Habilita la autenticación de usuario en Azure AD (solo es necesario para el proceso de registro del conector) |
  | 10100–10120 |Habilita las respuestas LOB HTTP enviadas de nuevo hacia el servidor proxy |
  | 9352, 5671 |Habilita la comunicación entre el conector hacia el servicio de Azure para las solicitudes entrantes |
  | 9350 |Opcional, para habilitar un mejor rendimiento para las solicitudes entrantes. |
  | 8080 |Habilita la secuencia de arranque del conector y la actualización automática del conector |
  | 9090 |Habilita el registro de conector (solo es necesario para el proceso de registro del conector) |
  | 9091 |Habilita la renovación automática de certificados de confianza del conector |
  
    Si el firewall fuerza el tráfico según los usuarios que se originan, abra estos puertos para el tráfico procedente de  los servicios de Windows que se ejecutan como un servicio de red. Además, asegúrese de habilitar el puerto 8080 para NT Authority\System.
* Si su organización usa servidores proxy para conectarse a Internet, eche un vistazo a la entrada de blog [Working with existing on-premises proxy servers](https://blogs.technet.microsoft.com/applicationproxyblog/2016/03/07/working-with-existing-on-prem-proxy-servers-configuration-considerations-for-your-connectors/) (Trabajo con servidores proxy locales existentes) para más información sobre cómo configurarlos.

## <a name="step-1-enable-application-proxy-in-azure-ad"></a>Paso 1: Habilitar el proxy de la aplicación en Azure AD
1. Inicie sesión como administrador en el [Portal de Azure clásico](https://manage.windowsazure.com/).
2. Vaya a Active Directory y seleccione el directorio en el que desea habilitar el proxy de la aplicación.
   
    ![Active Directory (icono)](./media/active-directory-application-proxy-enable/ad_icon.png)
3. Seleccione **Configurar** en la página del directorio y desplácese hacia abajo hasta **Proxy de la aplicación**.
4. Cambie **Habilitar servicios de proxy de la aplicación para este directorio** a **Habilitado**.
   
    ![Habilitación del proxy de la aplicación](./media/active-directory-application-proxy-enable/app_proxy_enable.png)
5. Seleccione **Descargar ahora**. Esto le conducirá a **Azure AD Application Proxy Connector Download**(Descarga del conector del proxy de aplicación de Azure AD). Lea y acepte los términos de licencia y haga clic en **Descargar** para guardar el archivo de Windows Installer (.exe) del conector.

## <a name="step-2-install-and-register-the-connector"></a>Paso 2: Instalar y registrar el conector
1. Ejecute **AADApplicationProxyConnectorInstaller.exe** en el servidor que preparó según los requisitos previos.
2. Siga las instrucciones del asistente para instalar.
3. Durante la instalación se le pedirá que registre el conector en el proxy de aplicación de su inquilino de Azure AD.
   
   * Proporcione sus credenciales de administrador global de Azure AD. Su inquilino de administrador global puede ser diferente de sus credenciales de Microsoft Azure.
   * Asegúrese de que el administrador que registra el conector está en el mismo directorio donde habilitó el servicio Proxy de aplicación. Por ejemplo, si el dominio del inquilino es contoso.com, el administrador debe ser admin@contoso.com o cualquier otro alias de ese dominio.
   * Si **Configuración de seguridad mejorada de IE** está establecido en **Activado** en el servidor donde va a instalar el conector, la pantalla de registro podría bloquearse. Siga las instrucciones del mensaje de error para permitir el acceso. Asegúrese de que Internet Explorer Enhanced Security está desactivado.
   * Si el registro del conector no funciona, consulte [Solucionar problemas de Proxy de aplicación](active-directory-application-proxy-troubleshoot.md).  
4. Cuando finalice la instalación, se agregan dos nuevos servicios a su servidor:
   
   * **Conector de Proxy de aplicación de Microsoft AAD** habilita la conectividad
     
     * **Actualizador del Conector de Proxy de aplicación de Microsoft AAD** es un servicio de actualización automatizada que comprueba periódicamente las nuevas versiones del conector y lo actualiza según sea necesario.
     
     ![Servicios de conector del proxy de la aplicación (captura de pantalla)](./media/active-directory-application-proxy-enable/app_proxy_services.png)
5. Haga clic en **Finalizar** en la ventana de instalación.

Para más información sobre los conectores, consulte [Understand Azure AD Application Proxy connectors](application-proxy-understand-connectors.md) (Descripción de los conectores del proxy de aplicación de Azure AD). 

Si desea tener alta disponibilidad, debe implementar al menos dos conectores. Para implementar más conectores, repita los pasos 2 y 3. Cada conector debe estar registrado de manera independiente.

Si desea desinstalar el conector, desinstale el servicio de conector y el servicio de actualizador. Reinicie el equipo para quitar completamente el servicio.

## <a name="next-steps"></a>Pasos siguientes
Ya está listo para la [publicación de aplicaciones mediante el proxy de aplicación](active-directory-application-proxy-publish.md).

Si tiene aplicaciones que están en redes independientes o en diferentes ubicaciones, puede usar grupos de conectores para organizar los distintos conectores en unidades lógicas. Obtenga más información sobre cómo [trabajar con conectores de Proxy de aplicación](active-directory-application-proxy-connectors.md).




<!--HONumber=Feb17_HO1-->



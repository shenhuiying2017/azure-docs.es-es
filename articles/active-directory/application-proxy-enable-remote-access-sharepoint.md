---
title: "Habilitación del acceso remoto a SharePoint con el Proxy de aplicación de Azure AD | Microsoft Docs"
description: "Se explican los conceptos básicos acerca de los conectores del Proxy de aplicación de Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 8436238546515b66b58b31673d54586e4a20f50f
ms.openlocfilehash: 86f10c04368d1c382939b418c6909ca807b3bf5a


---

# <a name="enable-remote-access-to-sharepoint-with-azure-ad-app-proxy"></a>Habilitación del acceso remoto a SharePoint con el Proxy de aplicación de Azure AD

En este artículo se describe cómo integrar un servidor SharePoint local en el Proxy de aplicación de Azure AD.

> [!NOTE]
> Proxy de aplicación es una característica que solo está disponible si actualizó a la edición Premium o Basic de Azure Active Directory. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).
> 

##<a name="prerequisites"></a>Requisitos previos

En este artículo se da por supuesto que ya tiene SharePoint 2013 o una versión más reciente instalada y en ejecución en su entorno. Además, tenga en cuenta los siguientes requisitos previos:

* SharePoint incluye compatibilidad nativa con Kerberos. Por lo tanto, los usuarios tengan acceso a sitios internos de forma remota a través del Proxy de aplicación de Azure AD pueden suponer que tienen a su disposición una experiencia de inicio de sesión único sin interrupciones.

* Tendrá que realizar algunos cambios de configuración en el servidor de SharePoint. Se recomienda usar un entorno de ensayo. Así, podrá realizar actualizaciones en el servidor de ensayo en primer lugar y, a continuación, facilitar un ciclo de prueba antes de pasar a producción.

* Se supone que ya ha configurado SSL para SharePoint, ya que se requiere SSL en la dirección URL publicada. Debe tener habilitado SSL en el sitio interno para asegurarse de que los vínculos se envían/asignan correctamente. Si no ha configurado SSL, vea [Configure SSL for SharePoint 2013](https://blogs.msdn.microsoft.com/fabdulwahab/2013/01/20/configure-ssl-for-sharepoint-2013) (Configuración de SSL para SharePoint 2013), que proporciona instrucciones para la configuración de SSL. Además, asegúrese de que la máquina de conector confía en el certificado que emite. (No es necesario que sea un certificado emitido públicamente).

##<a name="steps-to-set-up-sharepoint"></a>Pasos para configurar SharePoint

Siga estos pasos para habilitar el acceso remoto a SharePoint con el Proxy de aplicación de Azure AD:

**Parte 1: Configurar el inicio de sesión único (SSO)**

  * Paso A. Asegúrese de que el servidor de SharePoint se ejecuta como una cuenta de servicio.
  * Paso B. Configure SharePoint para Kerberos.
  * Paso C. Configure un nombre de entidad de seguridad de servicio (SPN) para la cuenta que se asigna a SharePoint.
  * Paso D. Asegúrese de que el conector está configurado como un delegado de confianza en SharePoint.

**Parte 2: Habilitar el acceso remoto seguro**

 * Publique la granja de SharePoint en el Proxy de aplicación de Azure AD.

**Parte 3: Comprobar que SharePoint conoce la dirección URL externa**

 * Establezca asignaciones de acceso alternativas en SharePoint.

### <a name="part-1-set-up-single-sign-on-sso-to-sharepoint"></a>Parte 1: Configurar el inicio de sesión único (SSO) en SharePoint

Nuestros clientes desean la mejor experiencia de SSO en sus aplicaciones de back-end (el servidor de SharePoint en este caso). En este escenario habitual de Azure AD, el usuario se autenticará solo una vez, ya que no se le pedirá que se autentique de nuevo.

Para las aplicaciones locales que requieren o utilizan la autenticación de Windows, esto puede lograrse mediante el protocolo de autenticación Kerberos y una característica denominada delegación limitada de Kerberos (KCD). La KCD, cuando está configurada, permite que el conector del proxy de aplicación obtenga un vale/token de Windows para un usuario determinado, incluso si el usuario no ha iniciado sesión en Windows directamente. Para obtener más información acerca de KCD, vea [Introducción a la delegación limitada de Kerberos](https://technet.microsoft.com/en-us/library/jj553400.aspx).

Siga los pasos siguientes para configurar esta característica en un servidor de SharePoint.

**Paso A: Asegúrese de que SharePoint se ejecuta bajo una cuenta de servicio, no sistema local, servicio local o servicio de red**

Lo primero que debe hacer es asegurarse de que SharePoint se está ejecutando bajo una cuenta de servicio definido. Es necesario para que podamos adjuntar nombres de entidad de seguridad de servicio (SPN) a una cuenta válida. Los SPN son la forma que utiliza el protocolo de Kerberos para distinguir los diferentes servicios. Y los necesitará más adelante para configurar KCD.

Para asegurarse de que los sitios se ejecutan bajo una cuenta de servicio definido, haga lo siguiente:

1. Abra el sitio de **Administración central de SharePoint 2013**.
2. Vaya a **Seguridad** y elija **Configurar cuentas de servicio**.
3. Seleccione **Grupo de aplicaciones web – SharePoint – 80**. Las opciones pueden ser ligeramente diferentes según el nombre del grupo web, o si usa SSL de forma predeterminada.

  ![Conectores del Proxy de aplicación de Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-service-web-application.png)

4. Si **Seleccione una cuenta para este componente** está establecido en **Servicio Local** o **Servicio de red**, tendrá que crear una cuenta. Si no es así, ya habrá terminado y puede ir al paso siguiente. 
5. Elija **Registrar una nueva cuenta administrada**. Una vez creada la cuenta, debe configurar el **Grupo de aplicaciones web** para poder usar la cuenta.

> [!NOTE]
Debe tener una cuenta de AD previamente creada para el servicio. Se recomienda que permita un cambio de contraseña automático. Para obtener más detalles sobre el conjunto completo de pasos y la solución de problemas, consulte [Configurar el cambio de contraseña automático en SharePoint 2013](https://technet.microsoft.com/EN-US/library/ff724280.aspx). 

**Paso B: Configure SharePoint para Kerberos**

Usamos KCD para realizar el inicio de sesión único (SSO) en el servidor de SharePoint, y esto solo funciona con Kerberos. 

Para configurar el sitio de SharePoint para la autenticación de Kerberos:

1. Abra el sitio de **Administración central de SharePoint 2013**.
2. Vaya a **Administración de aplicaciones	**, elija **Administrar aplicaciones web** y seleccione el sitio de SharePoint. En este ejemplo, es **SharePoint – 80**.

  ![Conectores del Proxy de aplicación de Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-manage-web-applications.png)
  
3. Haga clic en **Proveedores de autenticación** en la barra de herramientas.
4. En el cuadro **Proveedores de autenticación**, haga clic en **Zona predeterminada** para ver la configuración.
5. En el cuadro **Editar autenticación**, desplácese hacia abajo hasta que vea **Tipos de autenticación de notificaciones** y asegúrese de que están activadas las opciones **Habilitar autenticación de Windows** y **Autenticación integrada de Windows**. 
6. En el cuadro desplegable, asegúrese de que está activado **Negociar (Kerberos)**.

  ![Conectores del Proxy de aplicación de Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-service-edit-authentication.png)

7. En la parte inferior del cuadro **Editar autenticación**, haga clic en **Guardar**.

**Paso C: Configure un SPN para la cuenta de servicio de SharePoint**

Antes de configurar KCD, es necesario identificar el servicio de SharePoint que se ejecuta como la cuenta de servicio que ha configurado anteriormente. Para ello, configure un nombre de entidad de seguridad de servicio (SPN). Para obtener más información, consulte [Service Principal Names](https://technet.microsoft.com/en-us/library/cc961723.aspx) (Nombres de entidad de seguridad de servicio).

El formato de SPN es:

```
<service class>/<host>:<port>
```

_service class_ es un nombre único para el servicio. Para SharePoint, se utiliza HTTP.

_host_ es el nombre de dominio completo o el nombre de Netbios del host en el que se está ejecutando el servicio. En el caso de un sitio de SharePoint, puede que necesite ser la dirección URL del sitio, según la versión de IIS que esté usando.

_port_ es opcional. Si el FQDN del servidor de SharePoint es:

```
sharepoint.demo.o365identity.us
```

El SPN será: 

```
HTTP/ sharepoint.demo.o365identity.us demo
```

Además, quizá deba establecer también el SPN para sitios específicos en el servidor. Para obtener más información, consulte [Configuración de la autenticación Kerberos](https://technet.microsoft.com/en-us/library/cc263449(v=office.12).aspx). Preste especial atención a la sección "Creación de nombres de entidad de seguridad de servicio para las aplicaciones web con la autenticación Kerberos". 

Para ello, lo más fácil es seguir los formatos de SPN que ya pueden estar presentes para su sitio. Copie los SPN para registrar en la cuenta de servicio. Para ello, siga estos pasos:

1. Vaya al sitio con el SPN de otro equipo. 
 Al hacerlo, el conjunto pertinente de vales de Kerberos se almacenan en la caché del equipo. Estos vales contienen el SPN del sitio de destino al que ha navegado. Podemos extraer el SPN de ese sitio mediante una herramienta denominada [Klist](http://web.mit.edu/kerberos/krb5-devel/doc/user/user_commands/klist.html).
 
2. En una ventana de comandos que se ejecute en el mismo contexto que el usuario que tuvo acceso al sitio en el explorador, ejecute el siguiente comando: 
```
Klist
```
3. Devolverá el conjunto de SPN del servicio de destino. En este ejemplo, el valor destacado es el SPN que se necesitaba:

  ![Conectores del Proxy de aplicación de Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-target-service.png)
  
4. Ahora que tiene el SPN, debe asegurarse de que esté configurado correctamente en la cuenta de servicio establecida anteriormente para la aplicación web. Siga los pasos descritos en la sección siguiente.

**Establecimiento del SPN**

Para establecer el SPN, ejecute el comando siguiente desde el símbolo del sistema como administrador del dominio.

```
setspn -S http/sharepoint.demo.o365identity.us demo\sp_svc
```

Este comando establece el SPN para la cuenta de servicio de SharePoint que se ejecuta como _demo\sp_svc_.

No olvide reemplazar _http/sharepoint.demo.o365identity.us_ por el SPN para el servidor y _demo\sp_svc_ por la cuenta de servicio de su entorno. El comando setspn buscará el SPN antes de agregarlo. En este caso, quizá vea un error que informa de la existencia de un **valor SPN duplicado**. Si aparece este error, asegúrese de que el valor está asociado a la cuenta de servicio.

Puede comprobar que se agregó el SPN mediante la ejecución del comando Setspn con la opción -l. Para obtener más información acerca de la herramienta Setspn, consulte [Setspn](https://technet.microsoft.com/en-us/library/cc731241.aspx).

**Paso D: Asegúrese de que se confía en los conectores para delegar en SharePoint**

En este paso, configurará KCD para que el servicio de Proxy de aplicación de Azure AD pueda delegar identidades de usuario en el servicio de SharePoint. Para ello, habilite el conector del Proxy de aplicación para que pueda recuperar los vales de Kerberos para los usuarios que se han autenticado en Azure AD. A continuación, ese servidor pasará el contexto a la aplicación de destino, o SharePoint en este caso. 

Para configurar KCD, deberá repetir los pasos siguientes para cada máquina de conector:

1. Inicie sesión como administrador en un controlador de dominio y, a continuación, abra **Usuarios y equipos de Active Directory**.
2. Busque el equipo en el que se ejecuta el conector. En este ejemplo, se trata del mismo servidor de SharePoint.
3. Haga doble clic en el equipo y, a continuación, en la pestaña **Delegación**.
4. Asegúrese de que la configuración de las delegaciones está establecida en **Confiar en este equipo para la delegación solo a los servicios especificados** y, a continuación, seleccione **Usar cualquier protocolo de autenticación**.

  ![Conectores del Proxy de aplicación de Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-delegation-box.png)
  
5. Ahora debe agregar el SPN que creó anteriormente para la cuenta de servicio. Haga clic en el botón **Agregar**, luego en **Usuarios y equipos** y busque la cuenta que creó anteriormente.

  ![Conectores del Proxy de aplicación de Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-users-computers.png)

 Debería ver una lista de los SPN entre los que puede elegir. Debe agregar el que estableció anteriormente. 
6. Seleccione ese elemento y haga clic en **Aceptar**. Haga clic en **Aceptar** de nuevo para guardar el cambio.

### <a name="part-2-enable-remote-access-to-sharepoint"></a>Parte 2: Habilitar el acceso remoto en SharePoint

Ahora que ha habilitado SharePoint para Kerberos y ha configurado KCD, está listo para configurar el inicio de sesión único (SSO) en SharePoint. A continuación, desde el conector, puede publicar SharePoint para el acceso remoto a través del Proxy de aplicación de Azure AD.

**Publicación en SharePoint con el Proxy de aplicación de Azure AD**

Para llevar a cabo los pasos siguientes, debe ser un miembro del rol de administrador global dentro de la cuenta de Azure Active Directory de su organización.

1. Inicie sesión en el Portal de administración de Azure (https://manage.windowsazure.com) y busque su inquilino de Azure AD.
2. Haga clic en **Aplicaciones** y luego en **Agregar**.
3. Seleccione **Publicar una aplicación que estará accesible desde fuera de la red**. Si no ve esta opción, asegúrese de que tiene configurado Azure AD Básico o Premium en el inquilino.
4. En el cuadro que aparece, realice cada una de las opciones como se indica a continuación: 
 * **Nombre**: cualquier valor que desee, por ejemplo _SharePoint_.
 * **Dirección URL interna**: esta es la dirección URL del sitio de SharePoint a nivel interno, como https://SharePoint/. En este ejemplo, asegúrese de usar https.
 * **Método de autenticación previa**: seleccione _Azure Active Directory_.

  ![Conectores del Proxy de aplicación de Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-add-application.png)

5. Una vez publicada la aplicación, haga clic en la pestaña **Configurar**.
6. Desplácese hasta la opción **Traducir URL en encabezados**. El valor predeterminado es _SÍ_, cámbielo a _NO_. 

 SharePoint utiliza el valor _Encabezado host_ para buscar el sitio, y también genera vínculos basándose en este valor. De este modo se garantiza que cualquier vínculo que genere SharePoint sea una dirección URL publicada que se ha configurado correctamente para usar la dirección URL externa. Al establecer el valor en _SÍ_ se permite también que el conector reenvíe la solicitud a la aplicación de back-end. Sin embargo, si se establece en _NO_, el conector no enviará el nombre de host interno y, en su lugar, envía el encabezado host como dirección URL publicada para la aplicación de back-end.

 Además, para asegurarse de que SharePoint acepta esta dirección URL, debe completar una configuración más en el servidor de SharePoint. Lo hará en la sección siguiente.

7. Cambie el **Método de autenticación interno** a _Integrado en Windows_. Si su inquilino de Azure AD utiliza un UPN en la nube diferente al local, no olvide actualizar también el valor de **Identidad de inicio de sesión delegada**.
8. Establezca el **SPN de la aplicación interno** en el valor indicado anteriormente. Por ejemplo, _http/sharepoint.demo.o365identity.us_.
9. Ahora puede asignar la aplicación a los usuarios de destino.

La aplicación debería tener un aspecto similar al siguiente:

  ![Conectores del Proxy de aplicación de Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-internal-application-spn.png)

###<a name="part-3-ensure-sharepoint-knows-about-the-external-url"></a>Parte 3: Comprobar que SharePoint conoce la dirección URL externa

El último paso que debe completar es asegurarse de que SharePoint puede encontrar el sitio en función de la dirección URL externa, de manera que represente los vínculos basándose en dicha dirección URL externa. Para ello, configure las asignaciones de acceso alternativas para SharePoint.

**Configuración de un nombre alternativo para el sitio de SharePoint**

1. Abra el sitio de **Administración central de SharePoint 2013**.
2. En **Configuración del sistema**, seleccione **Configurar asignaciones de acceso alternativas**. 

 Se abrirá el cuadro **Asignaciones de acceso alternativas**.

  ![Conectores del Proxy de aplicación de Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-alternate-access1.png)

3. En la lista desplegable situada junto a **Colección de asignaciones de acceso alternativas**, seleccione **Change Alternative Access Mapping Collection** (Cambiar colección de asignaciones de acceso alternativas).
4. Seleccione el sitio, por ejemplo **SharePoint – 80**.

  ![Conectores del Proxy de aplicación de Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-alternate-access2.png)

5. Puede agregar la dirección URL publicada como una dirección URL interna o una dirección URL pública. Este ejemplo se utiliza una **dirección URL pública** como extranet.
6. Haga clic en **Editar direcciones URL públicas** en la ruta de acceso de **Extranet** y, a continuación, escriba la ruta de acceso en la que se publica la aplicación, como se muestra en el paso anterior. Por ejemplo, _https://sharepoint-iddemo.msappproxy.net_.

  ![Conectores del Proxy de aplicación de Azure AD](./media/application-proxy-remote-sharepoint/remote-sharepoint-alternate-access3.png)

7. Haga clic en **Guardar**. 

 Ahora puede acceder al sitio de SharePoint externamente mediante el Proxy de aplicación de Azure AD.

##<a name="next-steps"></a>Pasos siguientes

[Provisión de acceso remoto seguro a aplicaciones locales](active-directory-application-proxy-get-started.md)<br>
[Descripción de los conectores del Proxy de aplicación de Azure AD](application-proxy-understand-connectors.md)<br>
[Publishing SharePoint 2016 and Office Online Server with Azure AD Application Proxy](https://blogs.technet.microsoft.com/dawiese/2016/06/09/publishing-sharepoint-2016-and-office-online-server-with-azure-ad-application-proxy/) (Publicación de SharePoint 2016 y Office Online Server con el Proxy de aplicación de Azure AD)









<!--HONumber=Feb17_HO1-->



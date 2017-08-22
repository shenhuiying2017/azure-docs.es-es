---
title: "Habilitar el acceso remoto a SharePoint con el Proxy de aplicación de Azure AD | Microsoft Docs"
description: "Explica los conceptos básicos sobre cómo integrar un servidor de SharePoint local con el proxy de aplicación de Azure AD."
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
ms.date: 07/21/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 22aa82e5cbce5b00f733f72209318c901079b665
ms.openlocfilehash: 97eeec3b3936bcbef6ac3966b890332901bcb153
ms.contentlocale: es-es
ms.lasthandoff: 07/24/2017

---

# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Habilitar el acceso remoto a SharePoint con el proxy de aplicación de Azure AD

En este artículo se describe cómo integrar un servidor de SharePoint local con el proxy de aplicación de Azure Active Directory (Azure AD).

Para habilitar el acceso remoto a SharePoint con el proxy de aplicación de Azure AD, siga los pasos de las secciones de este artículo.

## <a name="prerequisites"></a>Requisitos previos

En este artículo se da por supuesto que ya tiene SharePoint 2013 o una versión más reciente instalada en su entorno. Además, tenga en cuenta los siguientes requisitos previos:

* SharePoint incluye compatibilidad nativa con Kerberos. Por tanto, los usuarios que tengan acceso a sitios internos de forma remota a través del proxy de aplicación de Azure AD pueden suponer que tienen a su disposición una experiencia de inicio de sesión único (SSO) sin interrupciones.

* Tiene que realizar algunos cambios de configuración en el servidor de SharePoint. Se recomienda usar un entorno de ensayo. Así, podrá realizar actualizaciones en el servidor de ensayo en primer lugar y, después, facilitar un ciclo de pruebas antes de pasar a producción.

* Se supone que ya configuró SSL para SharePoint, dado que se requiere SSL en la dirección URL publicada. Debe tener habilitado SSL en el sitio interno para asegurarse de que los vínculos se envían o asignan correctamente. Si no configuró SSL, vea [Configure SSL for SharePoint 2013 (Configurar SSL para SharePoint 2013)](https://blogs.msdn.microsoft.com/fabdulwahab/2013/01/20/configure-ssl-for-sharepoint-2013) para obtener instrucciones. Además, asegúrese de que la máquina de conector confía en el certificado que emite. (No es necesario que sea un certificado emitido públicamente).

## <a name="step-1-set-up-single-sign-on-to-sharepoint"></a>Parte 1: Configurar el inicio de sesión único en SharePoint

Nuestros clientes quieren la mejor experiencia de SSO en sus aplicaciones de back-end, en este caso, el servidor de SharePoint. En este escenario habitual de Azure AD, el usuario se autentica solo una vez, ya que no se le pedirá que se autentique de nuevo.

Para las aplicaciones locales que requieren o usan la autenticación de Windows, el SSO puede lograrse mediante el protocolo de autenticación Kerberos y una característica denominada delegación limitada de Kerberos (KCD). La KCD, cuando está configurada, permite que el conector del proxy de aplicación obtenga un vale o token de Windows para un usuario, incluso si el usuario no inició sesión en Windows directamente. Para obtener más información acerca de KCD, vea [Introducción a la delegación limitada de Kerberos](https://technet.microsoft.com/library/jj553400.aspx).

Para configurar KCD para un servidor de SharePoint, use los procedimientos de las siguientes secciones secuenciales:

### <a name="ensure-that-sharepoint-is-running-under-a-service-account"></a>Asegurarse de que SharePoint se ejecuta como una cuenta de servicio

En primer lugar, asegúrese de que SharePoint se ejecuta bajo una cuenta de servicio definida, no de sistema local, servicio local o servicio de red. Para ello, es necesario que pueda asociar nombres de entidad de servicio (SPN) a una cuenta válida. Los SPN son la forma que utiliza el protocolo de Kerberos para distinguir los diferentes servicios. Y necesitará la cuenta más adelante para configurar KCD.

Para asegurarse de que los sitios se ejecutan en una cuenta de servicio definido, sita estos pasos:

1. Abra el sitio de **Administración central de SharePoint 2013**.
2. Vaya a **Seguridad** y seleccione **Configurar cuentas de servicio**.
3. Seleccione **Grupo de aplicaciones web - SharePoint - 80**. Las opciones pueden ser ligeramente diferentes según el nombre del grupo web o si el grupo web usa SSL de forma predeterminada.

  ![Opciones para configurar una cuenta de servicio](./media/application-proxy-remote-sharepoint/remote-sharepoint-service-web-application.png)

4. Si **Seleccione una cuenta para este componente** está establecido en **Servicio Local** o **Servicio de red**, tendrá que crear una cuenta. Si no es así, ya habrá terminado y puede ir a la sección siguiente.
5. Haga clic en **Registrar una nueva cuenta administrada**. Una vez creada la cuenta, debe configurar el **Grupo de aplicaciones web** antes de poder usar la cuenta.

> [!NOTE]
Debe tener una cuenta de Azure AD previamente creada para el servicio. Se recomienda que permita un cambio de contraseña automático. Para más información sobre el conjunto completo de pasos y la solución de problemas, vea [Configurar el cambio de contraseña automático en SharePoint 2013](https://technet.microsoft.com/library/ff724280.aspx).

### <a name="configure-sharepoint-for-kerberos"></a>Configurar SharePoint para Kerberos

Se usa KCD para realizar el inicio de sesión único en el servidor de SharePoint, y esto solo funciona con Kerberos.

Para configurar el sitio de SharePoint para la autenticación de Kerberos:

1. Abra el sitio de **Administración central de SharePoint 2013**.
2. Vaya a **Administración de aplicaciones**, haga clic en **Administrar aplicaciones web** y seleccione el sitio de SharePoint. En este ejemplo, es **SharePoint – 80**.

  ![Selección del sitio de SharePoint](./media/application-proxy-remote-sharepoint/remote-sharepoint-manage-web-applications.png)

3. Haga clic en **Proveedores de autenticación** en la barra de herramientas.
4. En el cuadro **Proveedores de autenticación**, haga clic en **Zona predeterminada** para ver la configuración.
5. En el cuadro de diálogo **Editar autenticación**, desplácese hacia abajo hasta que vea **Tipos de autenticación de notificaciones** y asegúrese de que están activadas las opciones **Habilitar autenticación de Windows** y **Autenticación integrada de Windows**.
6. En el cuadro desplegable, asegúrese de que está seleccionada la opción **Negociar (Kerberos)**.

  ![Cuadro de diálogo Editar autenticación](./media/application-proxy-remote-sharepoint/remote-sharepoint-service-edit-authentication.png)

7. En la parte inferior del cuadro de diálogo **Editar autenticación**, haga clic en **Guardar**.

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>Establecer un nombre de entidad de servicio para la cuenta de servicio de SharePoint

Antes de configurar KCD, es necesario identificar el servicio de SharePoint que se ejecuta como la cuenta de servicio que configuró. Para ello se establece un SPN. Para obtener más información, consulte [Service Principal Names](https://technet.microsoft.com/library/cc961723.aspx) (Nombres de entidad de seguridad de servicio).

El formato de SPN es:

```
<service class>/<host>:<port>
```

En el formato de SPN:

* _service class_ es un nombre único para el servicio. Para SharePoint, se usa **HTTP**.

* _host_ es el nombre de dominio completo o el nombre de NetBIOS del host en el que se está ejecutando el servicio. En un sitio de SharePoint, es posible que este texto tenga que ser la dirección URL del sitio, según la versión de IIS que esté usando.

* _port_ es opcional.

Si el FQDN del servidor de SharePoint es:

```
sharepoint.demo.o365identity.us
```

El SPN será:

```
HTTP/ sharepoint.demo.o365identity.us demo
```

Es posible que también tenga que establecer el SPN para sitios específicos en el servidor. Para más información, vea [Configuración de la autenticación Kerberos](https://technet.microsoft.com/library/cc263449(v=office.12).aspx). Preste especial atención a la sección "Creación de nombres de entidad de seguridad de servicio para las aplicaciones web con la autenticación Kerberos".

La forma más fácil de establecer SPN es seguir los formatos de SPN que ya pueden estar presentes para los sitios. Copie los SPN para registrar en la cuenta de servicio. Para ello, siga estos pasos:

1. Vaya al sitio con el SPN desde otro equipo.
 Al hacerlo, el conjunto pertinente de vales Kerberos se almacenan en la caché del equipo. Estos vales contienen el SPN del sitio de destino al que ha navegado.

2. Se puede extraer el SPN de ese sitio mediante una herramienta denominada [Klist](http://web.mit.edu/kerberos/krb5-devel/doc/user/user_commands/klist.html). En una ventana de comandos que se ejecute en el mismo contexto que el usuario que tuvo acceso al sitio en el explorador, ejecute el siguiente comando:
```
Klist
```
Klist devuelve después el conjunto de SPN de destino. En este ejemplo, el valor resaltado es el SPN que se necesitaba:

  ![Resultados del ejemplo de Klist](./media/application-proxy-remote-sharepoint/remote-sharepoint-target-service.png)

4. Ahora que tiene el SPN, debe asegurarse de que esté configurado correctamente en la cuenta de servicio establecida anteriormente para la aplicación web. Ejecute el comando siguiente desde el símbolo del sistema como administrador del dominio:

 ```
 setspn -S http/sharepoint.demo.o365identity.us demo\sp_svc
 ```

 Este comando establece el SPN para la cuenta de servicio de SharePoint que se ejecuta como _demo\sp_svc_.

 Reemplace _http/sharepoint.demo.o365identity.us_ por el SPN para el servidor y _demo\sp_svc_ por la cuenta de servicio del entorno. El comando Setspn busca el SPN antes de agregarlo. En este caso, es posible que vea un error de **valor SPN duplicado**. Si aparece este error, asegúrese de que el valor está asociado a la cuenta de servicio.

Puede comprobar que se agregó el SPN mediante la ejecución del comando Setspn con la opción -l. Para más información sobre este comando, vea [Setspn](https://technet.microsoft.com/library/cc731241.aspx).

### <a name="ensure-that-the-connector-is-set-as-a-trusted-delegate-to-sharepoint"></a>Asegurarse de que el conector está configurado como un delegado de confianza en SharePoint

Configure KCD para que el servicio de proxy de aplicación de Azure AD pueda delegar identidades de usuario en el servicio de SharePoint. Para ello se habilita el conector del proxy de aplicación para recuperar los vales Kerberos para los usuarios que autenticaron en Azure AD. A continuación, ese servidor pasa el contexto a la aplicación de destino, o a SharePoint en este caso.

Para configurar KCD, repita los pasos siguientes para cada equipo de conexión:

1. Inicie sesión como administrador en un controlador de dominio y, después, abra **Usuarios y equipos de Active Directory**.
2. Busque el equipo en el que se ejecuta el conector. En este ejemplo, se trata del mismo servidor de SharePoint.
3. Haga doble clic en el equipo y, después, en la pestaña **Delegación**.
4. Asegúrese de que la configuración de delegación está establecida en **Confiar en este equipo para la delegación solo a los servicios especificados** y después seleccione **Usar cualquier protocolo de autenticación**.

  ![Configuración de delegación](./media/application-proxy-remote-sharepoint/remote-sharepoint-delegation-box.png)

5. Haga clic en el botón **Agregar**, haga clic en **Usuarios o equipos** y busque la cuenta de servicio.

  ![Agregar el SPN para la cuenta de servicio](./media/application-proxy-remote-sharepoint/remote-sharepoint-users-computers.png)

6. En la lista de los SPN, seleccione el que creó anteriormente para la cuenta de servicio.
7. Haga clic en **Aceptar**. Haga clic en **Aceptar** de nuevo para guardar los cambios.

## <a name="step-2-enable-remote-access-to-sharepoint"></a>Parte 2: Habilitar el acceso remoto a SharePoint

Después de habilitar SharePoint para Kerberos y configurar KCD, está listo para configurar el inicio de sesión único en SharePoint. Después, desde el conector, puede publicar la granja de SharePoint para el acceso remoto a través del proxy de aplicación de Azure AD.

Para realizar los pasos siguientes, debe ser un miembro del rol de administrador global dentro de la cuenta de Azure Active Directory de la organización.

1. Inicie sesión en [Azure Portal](https://manage.windowsazure.com) y busque el inquilino de Azure AD.
2. Haga clic en **Aplicaciones** y luego en **Agregar**.
3. Seleccione **Publicar una aplicación que estará accesible desde fuera de la red**. Si no ve esta opción, asegúrese de que tiene configurado Azure AD Básico o Premium en el inquilino.
4. Realice cada una de las opciones como se indica a continuación:
 * **Nombre**: use el valor que quiera, por ejemplo **SharePoint**.
 * **Dirección URL interna**: esta es la dirección URL del sitio de SharePoint a nivel interno, como **https://SharePoint/**. En este ejemplo, asegúrese de usar **https**.
 * **Método de autenticación previa**: seleccione **Azure Active Directory**.

  ![Opciones para agregar una aplicación](./media/application-proxy-remote-sharepoint/remote-sharepoint-add-application.png)

5. Después de publicar la aplicación, haga clic en la pestaña **Configurar**.
6. Desplácese hasta la opción **Traducir URL en encabezados**. El valor predeterminado es **SÍ**. Cámbielo a **NO**.

 SharePoint usa el valor _Encabezado de host_ para buscar el sitio. También genera vínculos basándose en este valor. El efecto es garantizar que cualquier vínculo que genere SharePoint sea una dirección URL publicada que está configurada correctamente para usar la dirección URL externa. Al establecer el valor en **SÍ** se permite también que el conector reenvíe la solicitud a la aplicación de back-end. Pero establecer el valor en **NO** significa que el conector no enviará el nombre de host interno. En su lugar, el conector envía el encabezado de host como la dirección URL publicada a la aplicación de back-end.

 Además, para asegurarse de que SharePoint acepta esta dirección URL, debe completar una configuración más en el servidor de SharePoint. Lo hará en la sección siguiente.

7. Cambie **Método de autenticación interno** por **Autenticación integrada de Windows**. Si el inquilino de Azure AD usa un UPN en la nube diferente al local, no olvide actualizar también el valor de **Identidad de inicio de sesión delegada**.
8. Establezca el **SPN de la aplicación interno** en el valor establecido anteriormente. Por ejemplo, use **http/sharepoint.demo.o365identity.us**.
9. Asigne la aplicación a los usuarios de destino.

La aplicación debería tener una apariencia similar al ejemplo siguiente:

  ![Aplicación terminada](./media/application-proxy-remote-sharepoint/remote-sharepoint-internal-application-spn.png)

## <a name="step-3-ensure-that-sharepoint-knows-about-the-external-url"></a>Paso 3: Asegurarse de que SharePoint conoce la dirección URL externa

El último paso es asegurarse de que SharePoint puede encontrar el sitio en función de la dirección URL externa, de manera que represente los vínculos basándose en esa dirección URL externa. Para ello, configure las asignaciones de acceso alternativas para el sitio de SharePoint.

1. Abra el sitio de **Administración central de SharePoint 2013**.
2. En **Configuración del sistema**, seleccione **Configurar asignaciones de acceso alternativas**.

 Se abrirá el cuadro **Asignaciones de acceso alternativas**.

  ![Cuadro Asignaciones de acceso alternativas](./media/application-proxy-remote-sharepoint/remote-sharepoint-alternate-access1.png)

3. En la lista desplegable situada junto a **Colección de asignaciones de acceso alternativas**, seleccione **Change Alternative Access Mapping Collection** (Cambiar colección de asignaciones de acceso alternativas).
4. Seleccione el sitio, por ejemplo **SharePoint - 80**.

  ![Selección de un sitio](./media/application-proxy-remote-sharepoint/remote-sharepoint-alternate-access2.png)

5. Puede agregar la dirección URL publicada como una dirección URL interna o una dirección URL pública. En este ejemplo se usa una dirección URL pública como extranet.
6. Haga clic en **Editar direcciones URL públicas** en la ruta de acceso de **Extranet** y, después, escriba la ruta de acceso para la aplicación publicada, como en el paso anterior. Por ejemplo, escriba **https://sharepoint-iddemo.msappproxy.net**.

  ![Escribir la ruta de acceso](./media/application-proxy-remote-sharepoint/remote-sharepoint-alternate-access3.png)

7. Haga clic en **Guardar**.

 Ahora puede tener acceso al sitio de SharePoint externamente mediante el proxy de aplicación de Azure AD.

## <a name="next-steps"></a>Pasos siguientes

- [Provisión de acceso remoto seguro a aplicaciones locales](active-directory-application-proxy-get-started.md)
- [Descripción de los conectores del Proxy de aplicación de Azure AD](application-proxy-understand-connectors.md)
- [Publishing SharePoint 2016 and Office Online Server with Azure AD Application Proxy](https://blogs.technet.microsoft.com/dawiese/2016/06/09/publishing-sharepoint-2016-and-office-online-server-with-azure-ad-application-proxy/) (Publicación de SharePoint 2016 y Office Online Server con el proxy de aplicación de Azure AD)


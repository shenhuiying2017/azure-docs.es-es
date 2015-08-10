<properties
	pageTitle="Publicación de aplicaciones con el Proxy de aplicación de Azure AD"
	description="Describe cómo publicar aplicaciones locales mediante el Proxy de aplicación de Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="06/02/2015"
	ms.author="rkarlin"/>


# Publicación de aplicaciones mediante el proxy de aplicación de Azure AD

> [AZURE.NOTE]Proxy de aplicación es una característica que solo está disponible si actualizó a la edición Premium o Basic de Azure Active Directory. Para obtener más información, consulte [Ediciones de Azure Active Directory](https://msdn.microsoft.com/library/azure/dn532272.aspx).

Después de habilitar el Proxy de aplicación de Microsoft Azure AD, puede publicar las aplicaciones para que los usuarios de fuera de la red privada puedan tener acceso a ellas.

Esta sección le guiará por los pasos necesarios para publicar aplicaciones que se ejecutan en la red local para las que desea habilitar el acceso remoto seguro desde fuera de la red.

> [AZURE.NOTE]Para comprobar que el conector funciona correctamente, la primera aplicación que publique debe ser cualquier sitio accesible desde dentro de su red privada, para tener la seguridad de que los usuarios pueden tener acceso a él desde Internet, antes de publicar una aplicación real.

## Publicación de una aplicación mediante el Asistente

1. Abra el explorador que prefiera y vaya al Portal de administración de Azure.
2. En el panel izquierdo del Portal de administración de Azure, haga clic en la ficha Active Directory.
3. Haga clic en el directorio en el que ha habilitado el Proxy de aplicación y para el que desea publicar una aplicación (por ejemplo, Wingtip Toys).
4. Haga clic en la ficha **Aplicaciones** y después haga clic en el botón **Agregar** situado en la parte inferior de la pantalla.![Agregar aplicación](http://i.imgur.com/wEeZ7IR.png)
5. En el cuadro de diálogo ¿Qué desea hacer?, haga clic en **Publicar una aplicación que será accesible desde fuera de la red**. ![Nueva aplicación que será accesible desde fuera de la red](http://i.imgur.com/Wj3vkyD.png)

6. Siga las instrucciones que aparecen en pantalla para facilitar la información siguiente sobre su aplicación:![Propiedades de la aplicación](http://i.imgur.com/MkPNc93.png)

**Configuración** | **Detalles**
---|---
URL externa | Esta es la dirección URL del servicio en la nube que se utiliza para tener acceso a la aplicación desde fuera de la red privada. La dirección URL se genera automáticamente a partir del nombre que haya facilitado y el sufijo msappproxy.net.
Método de autenticación previa | <p>Establezca el tipo de método de autenticación previa que quiera que use la aplicación:</p><p>a. Microsoft Azure Active Directory (Microsoft Azure AD): establece el método de autenticación previa en Microsoft Azure Active Directory (Microsoft Azure AD). Cuando un usuario intente tener acceso a una aplicación, Proxy de aplicación lo redirigirá para que inicie sesión con Azure AD, lo que autenticará al usuario y garantizará que tenga los permisos necesarios para el directorio y la aplicación.</p><p>b. Acceso directo: no se realiza la autenticación previa.</p>
Protocolo URL externo | <p>De manera predeterminada, las aplicaciones se publican usando el protocolo HTTPS.</p> <p>Para habilitar HTTP para una aplicación interna, debe establecer Método de autenticación previa en Acceso directo y entonces podrá cambiar el Protocolo URL externo de HTTPS a HTTP.</p> <p>Tenga en cuenta que publicar aplicaciones mediante el uso de HTTP puede crear problemas de seguridad para la aplicación y sus usuarios.</p> <p>Puede establecer un dominio personalizado en lugar de utilizar el valor predeterminado msappproxy.net. Para obtener más información, siga leyendo.</p>
Dirección URL interna | Se trata de la dirección URL interna que usa el conector de Proxy de aplicación para obtener acceso a la aplicación internamente. Debe ser la dirección URL de la aplicación publicada que se utiliza para tener acceso a la aplicación desde dentro de la red privada. Se trata de una dirección URL válida sin ruta de acceso y sin espacios ni símbolos.

Para finalizar al asistente, haga clic en la marca de verificación de la parte inferior de la pantalla. La aplicación ya está definida en Azure AD.



## Asignación de usuarios y grupos a la aplicación

1. Para las aplicaciones con autenticación previa, debe asignar los usuarios y grupos que tendrán acceso a la aplicación. <p>Para las aplicaciones que son de acceso directo, el acceso está disponible para todos los usuarios. Sin embargo, para que el usuario vea la aplicación en su lista de aplicaciones, debe asignar la aplicación a ese usuario.
2. Tras finalizar el Asistente para agregar aplicaciones, se muestra la página Inicio rápido de Proxy de aplicación. Para asignar los usuarios, haga clic en **Asignar usuarios**. ![Pantalla de inicio rápido de Proxy de aplicación](http://i.imgur.com/OmuWElV.png)
3. Seleccione cada usuario o grupo que desea asignar a esta aplicación y haga clic en **Asignar**. 

> [AZURE.NOTE]Para las aplicaciones de Autenticación de Windows integrada, puede asignar solo a los usuarios y grupos que se sincronizan desde Active Directory local. A los usuarios que inicien sesión con una cuenta de Microsoft y a los invitados no se les pueden asignar aplicaciones publicadas con Proxy de aplicación de Azure Active Directory. Asegúrese de que los usuarios que asigne inician sesión con sus credenciales en el mismo dominio que la aplicación que está publicando.

## Configuración avanzada

1. Puede modificar las aplicaciones publicadas o configurar opciones avanzadas, como SSO en aplicaciones locales, desde la página **Configurar**.![Configuración avanzada](http://i.imgur.com/FhbRvoq.png)

2. Seleccione la aplicación y haga clic en **Configurar**. Están disponibles las siguientes opciones:

**Configuración** | **Detalles**
---|---
Nombre | Escriba un nombre descriptivo para la aplicación
URL externa | Esta es la dirección URL del servicio en la nube que se utiliza para tener acceso a la aplicación desde fuera de la red privada. La dirección URL se genera automáticamente a partir del nombre que haya facilitado y el sufijo**msappproxy.net.**
Método de autenticación previa | Establezca el tipo de método de autenticación previa que desea que utilice la aplicación:<p>Active Directory: establece el método de autenticación previa en Active Directory. Cuando un usuario intente tener acceso a una aplicación, Proxy de aplicación lo redirigirá para que inicie sesión Proxy de aplicación, lo que autenticará al usuario y garantizará que tenga los permisos necesarios para el directorio y la aplicación.</p><p> Acceso directo: no se realiza la autenticación previa.</p>
URL externa | De manera predeterminada, las aplicaciones se publican usando el protocolo HTTPS. Para habilitar HTTP para una aplicación interna, debe establecer Método de autenticación previa en Acceso directo y entonces podrá cambiar el Protocolo URL externo de HTTPS a HTTP. Tenga en cuenta que publicar aplicaciones mediante el uso de HTTP puede crear problemas de seguridad para la aplicación y sus usuarios. Puede establecer un dominio personalizado en lugar de usar el dominio predeterminado proporcionado (msappproxy.net). Para obtener más información, consulte Trabajo con dominios personalizados.
Dirección URL interna | Se trata de la dirección URL interna que usa el conector de Proxy de aplicación para obtener acceso a la aplicación internamente. Debe ser la dirección URL de la aplicación publicada que se utiliza para tener acceso a la aplicación desde dentro de la red privada. Se trata de una dirección URL válida sin ruta de acceso y sin espacios ni símbolos.
Traducir URL en encabezados | Para las aplicaciones (como por ejemplo algunas configuraciones de SharePoint) que requieren que los encabezados de host HTTP no se traduzcan, establezca este parámetro en No. Así se deshabilitará la traducción de los encabezados de solicitud y respuesta.
Método de autenticación interno | Si utiliza el Proxy de aplicación para la autenticación previa, puede establecer un método de autenticación interno para permitir a los usuarios beneficiarse de inicio de sesión único (SSO) para esta aplicación. <p>Seleccione **Autenticación de Windows integrada (IWA)** si su aplicación utiliza IWA y podrá configurar la delegación de limitada de Kerberos (KCD) para habilitar SSO para esta aplicación. <p>Seleccione **Ninguno** si su aplicación no utiliza IWA. <p>Las aplicaciones que usan IWA deben configurarse mediante KCD; en caso contrario, el Proxy de aplicación no podrá publicar estas aplicaciones. <p>Para obtener más información, consulte SSO para aplicaciones IWA locales mediante KCD con Proxy de aplicación
SPN de la aplicación interno | Este es el nombre principal de servicio (SPN) de la aplicación interna como está configurado en el Proxy de aplicación local. El conector del Proxy de aplicación utiliza el SPN para captar los tokens de Kerberos para las aplicaciones que usan la delegación limitada de Kerberos (KCD). <p> Para obtener más información, consulte SSO para aplicaciones IWA locales mediante KCD con Proxy de aplicación

Después de publicar las aplicaciones mediante el Proxy de aplicación de Azure Active Directory, estas aparecen en la lista de aplicaciones en Azure AD y se pueden administrar desde ahí. Si deshabilita los servicios de Proxy de aplicación después de publicar aplicaciones, estas no se eliminan, pero ya no estarán accesibles desde fuera de la red privada. Para ver una aplicación y asegurarse de que es accesible, haga doble clic en el nombre de la aplicación. Si se deshabilita el servicio de Proxy de aplicación y la aplicación no está disponible, aparece un mensaje de advertencia en la parte superior de la pantalla. Para eliminar una aplicación, selecciónela en la lista y, a continuación, haga clic en **Eliminar**.

## Recursos adicionales

* [Sobre la delegación limitada de Kerberos](http://technet.microsoft.com/library/cc995228.aspx)

<!---HONumber=July15_HO5-->
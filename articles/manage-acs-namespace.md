<properties linkid="manage-services-manage-acs" urlDisplayName="Manage ACS" pageTitle="Access Control Service - Azure service management" metaKeywords="" description="Learn how to manage your Azure Access Control Service (ACS) using certificates and keys." metaCanonical="" services="active-directory" documentationCenter="" title="Managing Your ACS Namespace" authors="" solutions="" manager="" editor="" />

Administración del espacio de nombres ACS
=========================================

En este tema de describen las tareas de administración que es recomendable realizar con regularidad a fin de que las aplicaciones que usan el Servicio de control de acceso (ACS) de Azure continúen funcionando correctamente y sin interrupciones. Estas tareas de administración son las siguientes:

1.  Importante: realice un seguimiento de la caducidad y realice la sustitución de certificados, claves y contraseńas utilizados por el espacio de nombres ACS, las aplicaciones de usuarios de confianza, las identidades de servicio, los proveedores de identidades y la cuenta del Servicio de administración de ACS. Consulte las Instrucciones de administración de certificados y claves a continuación para obtener más información.

2.  Revise los proveedores de identidades, las identidades de servicio, las reglas y los administradores del portal y elimine los que están obsoletos.

Para obtener más información acerca de ACS, consulte [Access Control Service 2.0](http://msdn.microsoft.com/en-us/library/gg429786.aspx).

Instrucciones de administración de certificados y claves
--------------------------------------------------------

Por motivos de seguridad, se garantiza la caducidad de los certificados y las claves utilizados en ACS. Es importante hacer un seguimiento de las fechas de caducidad, a fin de que estos certificados y claves puedan renovarse.

Los pasos de alto nivel para sustituir un certificado de firma de tokens (clave simétrica o certificado X.509) o un certificado de descifrado de tokens son:

1.  Configure el nuevo certificado o la nueva clave en ACS como una clave "secundaria", junto con el certificado o la clave existentes que van a caducar.
2.  Notifique a los asociados que usan el servicio que necesitan actualizar las claves correspondientes antes de un plazo concreto.
3.  Los asociados deben actualizar el certificado o la clave correspondientes de los usuarios de confianza o de los proveedores de identidades. Por ejemplo, importe los metadatos de WS-Federation actualizados para el espacio de nombres ACS que contiene el nuevo certificado de validación de firma de tokens, o bien configure manualmente la clave simétrica en la configuración de la aplicación.
4.  Cuando todas las aplicaciones se hayan actualizado (o después de que haya transcurrido un plazo), marque el certificado o la clave nuevos como principal en la configuración de ACS.
5.  Tras un período de gracia razonable, elimine el certificado o la clave anteriores de la configuración de ACS.

Los pasos de alto nivel para sustituir los certificados de cifrado de tokens son:

1.  El usuario o sus asociados actualizan el certificado o la clave correspondientes que se usan para el descifrado de tokens en las aplicaciones de usuarios de confianza.
2.  Configure el nuevo certificado de cifrado en ACS, junto con el certificado existente que va a caducar.
3.  Elimine el certificado de cifrado anterior.

Los pasos de alto nivel para sustituir las claves del servicio de administración o de identidad de servicio son:

1.  Configure el certificado o la clave nuevos en ACS, junto con el certificado o la clave existentes que van a caducar.
2.  El usuario o sus asociados actualizan el certificado o la clave correspondientes que se usan para las solicitudes de token en las aplicaciones cliente.
3.  Después de que se hayan actualizado todos los clientes (o después de un período de gracia razonable), elimine la clave o el certificado anteriores.

Cuando un certificado o una clave caducan, ACS dejará de emitir tokens, lo que impedirá que el usuario de confianza opere con normalidad. ACS ignorará los certificados y las claves caducados, causando excepciones efectivamente como si no se hubiera configurado ningún certificado o clave en primer lugar. En las siguientes secciones encontrará información de cada certificado y clave administrada por ACS, el modo de renovarlos y cómo reconocer si han caducado y necesitan renovarse.

-   Use la sección Certificates and Keys del portal de administración de ACS para administrar certificados y claves relacionados con el espacio de nombres de servicio y las aplicaciones de usuarios de confianza. Para obtener más información acerca de estos tipos de credenciales, consulte [Certificados y claves](http://msdn.microsoft.com/en-us/library/gg185932.aspx).
-   Use la sección Service identities del portal de administración de ACS para administrar credenciales (certificados, claves o contraseńas) relacionadas con identidades de servicio. Para obtener más información acerca de las identidades de servicio, consulte [Identidades de servicio](http://msdn.microsoft.com/en-us/library/gg185945.aspx).
-   Use la sección Management Service del portal de administración de ACS para administrar credenciales (certificados, claves o contraseńas) relacionadas con las cuentas del Servicio de administración de ACS. Para obtener más información acerca del Servicio de administración de ACS, consulte [Servicio de administración de ACS](http://msdn.microsoft.com/en-us/library/gg185972.aspx).

Hay algunos tipos de certificados y claves que no aparecen en el portal de administración de ACS. En particular para los proveedores de identidades de WS-Federation como AD FS, debe comprobar previamente la validez de los certificados que los proveedores de identidades usan. Actualmente, los certificados disponibles a través de los metadatos de los proveedores de identidades de WS-Federation no aparecen en el portal de administración de ACS. Para comprobar la validez de los certificados, debe usar el servicio de administración para inspeccionar las fechas de validez y caducidad para las propiedades StartDate y EndDate de [IdentityProviderKey](http://msdn.microsoft.com/en-us/library/hh124084.aspx). Cuando el certificado o una clave caducan y, por tanto, dejan de ser válidos, ACS empezará a lanzar [Códigos de error de ACS](http://msdn.microsoft.com/en-us/library/gg185949.aspx) como excepciones específicos de cada certificado o clave. Consulte las siguientes secciones para conocer los códigos de error específicos.

Puede actualizar los certificados y las claves mediante programación con el [Servicio de administración de ACS](http://msdn.microsoft.com/en-us/library/gg185972.aspx). Plantéese revisar la muestra de código KeyManagement disponible para descarga como parte del [Ejemplo de código: Servicio de administración](http://msdn.microsoft.com/en-us/library/gg185970.aspx).

Certificados y claves disponibles
---------------------------------

En la siguiente lista se muestran los certificados y las claves disponibles que se usan en ACS y de cuyas fechas de caducidad cabe realizar un seguimiento:

-   Certificados de firma de tokens
-   Claves de firma de tokens
-   Certificados de cifrado de tokens
-   Certificados de descifrado de tokens
-   Credenciales de identidad de servicio
-   Credenciales de la cuenta del Servicio de administración de ACS
-   Certificados de cifrado y firma del proveedor de identidades

En el resto de este tema se habla de manera detallada acerca de cada certificado y clave.

Certificados de firma de tokens
-------------------------------

ACS firma todos los tokens de seguridad que emite. Se utilizan los certificados X.509 para la firma cuando se crea una aplicación que consume los tokens de SAMS enviados por ACS.

Puede administrar los certificados de firma de tokens a través de la sección Certificates and Keys del portal de administración de ACS.

**Para administrar certificados de firma de tokens**

1.  Abra un explorador de Internet y visite el Portal de administración de Azure (<http://go.microsoft.com/fwlink/?LinkID=129428>).

2.  Inicie sesión en el sitio web con un Windows Live ID. Si no tiene un Windows Live ID, haga clic en Sign up para crear uno.

3.  Después de iniciar sesión con el Windows Live ID, se le redirigirá a la página del Portal de administración. En la parte inferior izquierda de la página, haga clic en **Service Bus and Access Control**.

    ![](./media/manage-acs-namespace/ACS1.png)

4.  Para lanzar el portal de administración de ACS, haga clic en **Control de acceso** en el árbol del lado izquierdo, seleccione el espacio de nombres del servicio ACS que desea configurar y, a continuación, haga clic en el botón **Servicio de control de acceso** de la barra de herramientas en la parte superior de la página.

    ![](./media/manage-acs-namespace/ACS2.png)

    En este punto, la pantalla debe presentar la siguiente apariencia:

    ![](./media/manage-acs-namespace/ACS3.png)

5.  Haga clic en **Certificates and Keys** en el árbol del lado izquierdo en la sección Service Settings.

    ![](./media/manage-acs-namespace/ACS4.png)

    En este punto, la pantalla debe presentar la siguiente apariencia:

    ![](./media/manage-acs-namespace/ACS5.png)

6.  En la sección Token Signing, use el botón Add para configurar el nuevo certificado en ACS como una clave "secundaria", junto con el certificado existente que va a caducar.

7.  Notifique a los asociados que usan el servicio que necesitan actualizar las claves correspondientes antes de un plazo concreto.

8.  Los asociados deben actualizar el certificado correspondiente de los usuarios de confianza o de los proveedores de identidades. Por ejemplo, importe los metadatos de WS-Federation actualizados para el espacio de nombres ACS que contiene el nuevo certificado de validación de firma de tokens, o bien configure manualmente la clave simétrica en la configuración de la aplicación.

9.  Cuando todas las aplicaciones se hayan actualizado (o después de que haya transcurrido un plazo), marque el nuevo certificado como principal en la configuración de ACS.

10. Después de un período de gracia razonable, use el botón Delete en la sección Token Signing de la página Certificates and Keys para eliminar el certificado anterior de la configuración de ACS.

Para obtener más información, consulte [Certificados y claves](http://msdn.microsoft.com/en-us/library/gg185932.aspx).

Cuando expiren los certificados de firma, recibirá los siguientes errores cuando intente solicitar un token:

<table><tr><td><b>Código de error</b></td>
<td><b>Mensaje</b></td>
<td><b>Acción necesaria para corregir el mensaje</b></td>
</tr>
<tr>
<td>ACS50004</td>
<td>No hay ningún certificado de firma X.509 principal configurado. Se requiere un certificado de firma para SAML.</td>
<td>Si el usuario de confianza elegido usa SAML como tipo de token, compruebe que haya un certificado X.509 configurado para el usuario de confianza o el espacio de nombres de servicio. El certificado debe estar establecido en principal y debe encontrarse dentro del período de validez.</td></tr>
</table>

Clave de firma de tokens
------------------------

ACS firma todos los tokens de seguridad que emite. Las claves de firma simétricas de 256 bits se utilizan al construir una aplicación que consume tokens de SWT enviados por ACS.

Puede administrar las claves de firma de tokens a través de la sección Certificates and Key del portal de administración de ACS.

**Para administrar la clave de firma de tokens**

1.  Abra un explorador de Internet y visite el Portal de administración de Azure (<http://go.microsoft.com/fwlink/?LinkID=129428>).

2.  Inicie sesión en el sitio web con un Windows Live ID. Si no tiene un Windows Live ID, haga clic en Sign up para crear uno.

3.  Después de iniciar sesión con el Windows Live ID, se le redirigirá a la página del Portal de administración. En la parte inferior izquierda de la página, haga clic en **Service Bus and Access Control**.

    ![](./media/manage-acs-namespace/ACS1.png)

4.  Para lanzar el portal de administración de ACS, haga clic en **Control de acceso** en el árbol del lado izquierdo, seleccione el espacio de nombres del servicio ACS que desea configurar y, a continuación, haga clic en el botón **Servicio de control de acceso** de la barra de herramientas en la parte superior de la página.

    ![](./media/manage-acs-namespace/ACS2.png)

    En este punto, la pantalla debe presentar la siguiente apariencia:

    ![](./media/manage-acs-namespace/ACS3.png)

5.  Haga clic en **Certificates and Keys** en el árbol del lado izquierdo en la sección Service Settings.

    ![](./media/manage-acs-namespace/ACS4.png)

    En este punto, la pantalla debe presentar la siguiente apariencia:

    ![](./media/manage-acs-namespace/ACS5.png)

6.  En la sección Token Signing, use el botón Add para configurar la nueva clave en ACS como una clave "secundaria", junto con la clave existente que va a caducar.

7.  Notifique a los asociados que usan el servicio que necesitan actualizar las claves correspondientes antes de un plazo concreto.

8.  Los asociados deben actualizar la clave correspondiente de los usuarios de confianza o de los proveedores de identidades. Por ejemplo, importe los metadatos de WS-Federation actualizados para el espacio de nombres ACS que contiene el nuevo certificado de validación de firma de tokens, o bien configure manualmente la clave simétrica en la configuración de la aplicación.

9.  Cuando todas las aplicaciones se hayan actualizado (o después de que haya transcurrido un plazo), marque la nueva clave como principal en la configuración de ACS.

10. Después de un período de gracia razonable, use el botón Delete en la sección Token Signing de la página Certificates and Keys para eliminar la clave anterior de la configuración de ACS.

Para obtener más información, consulte [Certificados y claves](http://msdn.microsoft.com/en-us/library/gg185932.aspx).

Cuando expiren las claves de firma, recibirá los siguientes errores cuando intente solicitar un token:


<table><tr><td><b>Código de error</b></td>
<td><b>Mensaje</b></td>
<td><b>Acción necesaria para corregir el mensaje</b></td>
</tr>
<tr>
<td>ACS50003</td>
<td>No hay ninguna clave de firma simétrica principal configurada. Para SWT, se requiere una clave de firma simétrica.</td>
<td>Si el usuario de confianza elegido usa SWT como tipo de token, compruebe que haya una clave simétrica configurada para el usuario de confianza o el espacio de nombres de servicio, y que la clave esté establecida como principal y se encuentre dentro del período de validez.</td></tr>
</table>

Certificados de cifrado de tokens
---------------------------------

Se requiere el cifrado de tokens si una aplicación de usuarios de confianza es un servicio web que utiliza tokens de prueba de posesión en el protocolo WS-Trust; en otros casos, el cifrado de tokens es opcional.

Puede administrar los certificados de cifrado de tokens a través de la sección Certificates and Keys del portal de administración de ACS.

**Para administrar certificados de cifrado de tokens**

1.  Abra un explorador de Internet y visite el Portal de administración de Azure (<http://go.microsoft.com/fwlink/?LinkID=129428>).

2.  Inicie sesión en el sitio web con un Windows Live ID. Si no tiene un Windows Live ID, haga clic en Sign up para crear uno.

3.  Después de iniciar sesión con el Windows Live ID, se le redirigirá a la página del Portal de administración. En la parte inferior izquierda de la página, haga clic en **Service Bus and Access Control**.

    ![](./media/manage-acs-namespace/ACS1.png)

4.  Para lanzar el portal de administración de ACS, haga clic en **Control de acceso** en el árbol del lado izquierdo, seleccione el espacio de nombres del servicio ACS que desea configurar y, a continuación, haga clic en el botón **Servicio de control de acceso** de la barra de herramientas en la parte superior de la página.

    ![](./media/manage-acs-namespace/ACS2.png)

    En este punto, la pantalla debe presentar la siguiente apariencia:

    ![](./media/manage-acs-namespace/ACS3.png)

5.  Haga clic en **Certificates and Keys** en el árbol del lado izquierdo en la sección Service Settings.

    ![](./media/manage-acs-namespace/ACS4.png)

    En este punto, la pantalla debe presentar la siguiente apariencia:

    ![](./media/manage-acs-namespace/ACS7.png)

6.  El usuario o sus asociados actualizan el certificado o la clave correspondientes que se usan para el descifrado de token en las aplicaciones de usuarios de confianza.
7.  Use el botón Add para configurar el nuevo certificado de cifrado en ACS, junto con el certificado existente que va a caducar.
8.  Use el botón Delete para eliminar el certificado de cifrado anterior.

Para obtener más información, consulte [Certificados y claves](http://msdn.microsoft.com/en-us/library/gg185932.aspx).

Cuando expiren los certificados de cifrado, recibirá los siguientes errores cuando intente solicitar un token:

<table><tr><td><b>Código de error</b></td>
<td><b>Mensaje</b></td>
<td><b>Acción necesaria para corregir el mensaje</b></td>
</tr>
<tr>
<td>ACS50005</td>
<td>El cifrado del token es obligatorio, pero no hay ningún certificado de cifrado configurado para el usuario de confianza.</td>
<td>Deshabilite el cifrado de token para el usuario de confianza elegido o cargue un certificado X.509 para usar para el cifrado del token.</td></tr>
</table>

Certificados de descifrado de tokens
------------------------------------

ACS puede aceptar tokens cifrados de los proveedores de identidades de WS-Federation (por ejemplo, AD FS 2.0). Un certificado X.509 hospedado en ACS se usa para el descifrado.

Puede administrar los certificados de descifrado de tokens a través de la sección Certificates and Keys del portal de administración de ACS.

**Para administrar certificados de descifrado de tokens**

1.  Abra un explorador de Internet y visite el Portal de administración de Azure (<http://go.microsoft.com/fwlink/?LinkID=129428>).

2.  Inicie sesión en el sitio web con un Windows Live ID. Si no tiene un Windows Live ID, haga clic en Sign up para crear uno.

3.  Después de iniciar sesión con el Windows Live ID, se le redirigirá a la página del Portal de administración. En la parte inferior izquierda de la página, haga clic en **Service Bus and Access Control**.

    ![](./media/manage-acs-namespace/ACS1.png)

4.  Para lanzar el portal de administración de ACS, haga clic en **Control de acceso** en el árbol del lado izquierdo, seleccione el espacio de nombres del servicio ACS que desea configurar y, a continuación, haga clic en el botón **Servicio de control de acceso** de la barra de herramientas en la parte superior de la página.

    ![](./media/manage-acs-namespace/ACS2.png)

    En este punto, la pantalla debe presentar la siguiente apariencia:

    ![](./media/manage-acs-namespace/ACS3.png)

5.  Haga clic en **Certificates and Keys** en el árbol del lado izquierdo en la sección Service Settings.

    ![](./media/manage-acs-namespace/ACS4.png)

    En este punto, la pantalla debe presentar la siguiente apariencia:

    ![](./media/manage-acs-namespace/ACS9.png)

6.  En la sección Token decryption, use el botón Add para configurar el nuevo certificado en ACS como una clave "secundaria", junto con el certificado existente que va a caducar.

7.  Notifique a los asociados que usan el servicio que necesitan actualizar las claves correspondientes antes de un plazo concreto.

8.  Los asociados deben actualizar el certificado correspondiente de los usuarios de confianza o de los proveedores de identidades. Por ejemplo, importe los metadatos de WS-Federation actualizados para el espacio de nombres ACS que contiene el nuevo certificado de validación de firma de tokens, o bien configure manualmente la clave simétrica en la configuración de la aplicación.

9.  Cuando todas las aplicaciones se hayan actualizado (o después de que haya transcurrido un plazo), marque el nuevo certificado como principal en la configuración de ACS.

10. Después de un período de gracia razonable, use el botón Delete en la sección Token Signing de la página Certificates and Keys para eliminar el certificado anterior de la configuración de ACS.

Para obtener más información, consulte [Certificados y claves](http://msdn.microsoft.com/en-us/library/gg185932.aspx).

Cuando expiren los certificados de descifrado, recibirá los siguientes errores cuando intente solicitar un token:

<table><tr><td><b>Código de error</b></td>
<td><b>Mensaje</b></td>
</tr>
<tr>
<td>ACS10001</td>
<td>Error al procesar el encabezado SOAP.</td>
</tr>
<tr><td>ACS20001</td>
<td>Error al procesar una respuesta de inicio de sesión de WS-Federation.</td></tr>
</table>

Credenciales de identidad de servicio
-------------------------------------

Las identidades de servicio son credenciales configuradas globalmente para el espacio de nombres ACS que permiten a las aplicaciones o los clientes autenticarse directamente en ACS y recibir un token. Hay tres tipos de credenciales con los que se puede asociar una identidad de servicio de ACS con clave simétrica, contraseńa y certificado X.509.

Puede administrar las credenciales de identidad de servicio a través de la página Service identities del portal de administración de ACS.

**Para administrar las credenciales de identidad de servicio**

1.  Abra un explorador de Internet y visite el Portal de administración de Azure (<http://go.microsoft.com/fwlink/?LinkID=129428>).

2.  Inicie sesión en el sitio web con un Windows Live ID. Si no tiene un Windows Live ID, haga clic en Sign up para crear uno.

3.  Después de iniciar sesión con el Windows Live ID, se le redirigirá a la página del Portal de administración. En la parte inferior izquierda de la página, haga clic en **Service Bus and Access Control**.

    ![](./media/manage-acs-namespace/ACS1.png)

4.  Para lanzar el portal de administración de ACS, haga clic en **Control de acceso** en el árbol del lado izquierdo, seleccione el espacio de nombres del servicio ACS que desea configurar y, a continuación, haga clic en el botón **Servicio de control de acceso** de la barra de herramientas en la parte superior de la página.

    ![](./media/manage-acs-namespace/ACS2.png)

    En este punto, la pantalla debe presentar la siguiente apariencia:

    ![](./media/manage-acs-namespace/ACS3.png)

5.  Haga clic en **Service identities** en el árbol del lado izquierdo en la sección Service Settings.

    ![](./media/manage-acs-namespace/ACS11.png)

6.  Haga clic en la identidad de servicio que desea editar.

    ![](./media/manage-acs-namespace/ACS112.png)

7.  En la sección Credentials, use el botón Add para configurar el certificado o la clave nuevos en ACS, junto con el certificado o la clave existentes que van a caducar.

8.  El usuario o sus asociados actualizan el certificado o la clave correspondientes que se usan para las solicitudes de token en las aplicaciones cliente.

9.  Después de que se hayan actualizado todos los clientes (o después de un período de gracia razonable), use el botón Delete para eliminar el certificado o la clave anteriores.

Para obtener más información, consulte [Identidades de servicio](http://msdn.microsoft.com/en-us/library/gg185945.aspx).

A continuación se indica la excepción que ACS lanzará si las credenciales han caducado:

<table><tr><td><b>Credencial</b></td><td><b>Código de error</b></td>
<td><b>Mensaje</b></td><td><b>Acción necesaria para corregir el mensaje</b></td>
</tr>
<tr>
<td>Clave simétrica, contraseńa</td>
<td>ACS50006</td>
<td>Error al comprobar la firma. (El mensaje puede contener más detalles).</td>
<td></td>
</tr>
<tr><td>Certificado X.509</td>
<td>ACS50016</td>
<td>El certificado X.509 con el sujeto "&lt;nombre del="" sujeto="" certificado=""&gt;" y la huella digital "&lt;huella digital="" del="" certificado=""&gt;" no coincide con ningún certificado configurado.</td>
<td>Asegúrese de que el certificado solicitado se haya cargado en ACS.</td>
</tr>
</table>

Para comprobar y actualizar las fechas de caducidad de las claves simétricas o la contraseńa, o bien para cargar el nuevo certificado como credenciales de identidad de servicio, siga las instrucciones descritas en [Procedimiento: Agregar identidades de servicio con un certificado X.509, una contraseńa o una clave simétrica](http://msdn.microsoft.com/en-us/library/gg185924.aspx). Lista de credenciales de identidad de servicio disponible en la página Edit Service Identity.

Credenciales del servicio de administración
-------------------------------------------

El Servicio de administración de ACS es un componente clave de ACS que le permite administrar y configurar mediante programación los valores de un espacio de nombres ACS. Existen tres tipos de credenciales con las que se puede asociar la cuenta del Servicio de administración de ACS. Son la clave simétrica, la contraseńa y un certificado X.509.

Puede administrar las credenciales del servicio de administración a través de la página Management service del portal de administración de ACS.

**Para administrar las credenciales del Servicio de administración de ACS**

1.  Abra un explorador de Internet y visite el Portal de administración de Azure (<http://go.microsoft.com/fwlink/?LinkID=129428>).

2.  Inicie sesión en el sitio web con un Windows Live ID. Si no tiene un Windows Live ID, haga clic en Sign up para crear uno.

3.  Después de iniciar sesión con el Windows Live ID, se le redirigirá a la página del Portal de administración. En la parte inferior izquierda de la página, haga clic en **Service Bus and Access Control**.

    ![](./media/manage-acs-namespace/ACS1.png)

4.  Para lanzar el portal de administración de ACS, haga clic en **Control de acceso** en el árbol del lado izquierdo, seleccione el espacio de nombres del servicio ACS que desea configurar y, a continuación, haga clic en el botón **Servicio de control de acceso** de la barra de herramientas en la parte superior de la página.

    ![](./media/manage-acs-namespace/ACS2.png)

    En este punto, la pantalla debe presentar la siguiente apariencia:

    ![](./media/manage-acs-namespace/ACS3.png)

5.  Haga clic en **Management service** en el árbol del lado izquierdo en la sección Administration.

    ![](./media/manage-acs-namespace/ACS14.png)

6.  Haga clic en la cuenta del servicio de administración.

    ![](./media/manage-acs-namespace/ACS15.png)

7.  En la sección Credentials, use el botón Add para configurar el certificado o la clave nuevos en ACS, junto con el certificado o la clave existentes que van a caducar.

8.  El usuario o sus asociados actualizan el certificado o la clave correspondientes que se usan para las solicitudes de token en las aplicaciones cliente.

9.  Después de que se hayan actualizado todos los clientes (o después de un período de gracia razonable), use el botón Delete para eliminar el certificado o la clave anteriores.

Para obtener más información, consulte [Servicio de administración de ACS](http://msdn.microsoft.com/en-us/library/gg185972.aspx).

ACS lanzará las siguientes excepciones si las credenciales han caducado:

<table><tr><td><b>Credencial</b></td><td><b>Código de error</b></td>
<td><b>Mensaje</b></td><td><b>Acción necesaria para corregir el mensaje</b></td>
</tr>
<tr>
<td>Clave simétrica, contraseńa</td>
<td>ACS50006</td>
<td>Error al comprobar la firma. (El mensaje puede contener más detalles).</td>
<td></td>
</tr>
<tr><td>Certificado X.509</td>
<td>ACS50016</td>
<td>El certificado X.509 con el sujeto "" y la huella digital "" no coincide con ningún certificado configurado.</td>
<td>Asegúrese de que el certificado solicitado se haya cargado en ACS.</td>
</tr>
</table>

La lista de las credenciales de la cuenta del Servicio de administración de ACS está disponible en la página Edit Management Service Account del portal de administración de ACS.

Certificado del proveedor de identidades de WS-Federation
---------------------------------------------------------

El certificado del proveedor de identidades de WS-Federation está disponible a través de sus metadatos. Al configurar el proveedor de identidades de WS-Federation, como AD FS, se configura el certificado de firma de WS-Federation a través de los metadatos de WS-Federation disponibles mediante URL o como un archivo; lea [Proveedores de identidades de WS-Federation](http://msdn.microsoft.com/en-us/library/gg185933.aspx) y [Procedimiento: Configurar AD FS 2.0 como proveedor de identidades](http://msdn.microsoft.com/en-us/library/gg185961.aspx) para obtener más información. Una vez configurado el proveedor de identidades de WS-Federation en ACS, utilice el servicio de administración de ACS para consultar la validez de sus certificados. Tenga en cuenta que para cada carga consecutiva de los metadatos a través del portal de administración de ACS o del servicio de administración de ACS, se sustituirán las claves.

A continuación se indican las excepciones que ACS lanzará si el certificado ha caducado:

<table><tr><td><b>Código de error</b></td>
<td><b>Mensaje</b></td>
</tr>
<tr>
<td>ACS10001</td>
<td>Error al procesar el encabezado SOAP.</td>
</tr>
<tr><td>ACS20001</td>
<td>Error al procesar una respuesta de inicio de sesión de WS-Federation.</td></tr>
<tr><td>ACS50006</td><td>Error al comprobar la firma. (El mensaje puede contener más detalles).
</td></tr>
</table>



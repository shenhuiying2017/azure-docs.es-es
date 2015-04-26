Las nuevas API de Twitter v1.1 requieren que la aplicación se autentique para obtener acceso a los recursos. Primero, tendrá que obtener las credenciales necesarias para solicitar el acceso mediante OAuth 2.0. A continuación, las almacenará de forma segura en la configuración de aplicaciones para el servicio móvil.

1. Si aún no lo ha hecho, siga los pasos que se describen en el tema <a href="../articles/mobile-services-how-to-register-twitter-authentication.md/" target="_blank">Registro de las aplicaciones para el inicio de sesión en Twitter con Servicios móviles</a>. 

  	Twitter genera las credenciales necesarias para permitirle obtener acceso a las API de Twitter v1.1. Puede obtener estas credenciales en el sitio web para desarrolladores de Twitter. 

2. Navegue hasta el sitio web para <a href="http://go.microsoft.com/fwlink/p/?LinkId=268300" target="_blank">desarrolladores de Twitter</a> (en inglés), inicie sesión con las credenciales de la cuenta de Twitter y seleccione la aplicación de Twitter.

3. En la pestaña **Claves y tokens de acceso** de la aplicación, anote los siguientes valores:

	+ **Consumer key**
	+ **Consumer secret**
	+ **Access token**
	+ **Access token secret**

4. Inicie sesión en el [Portal de administración de Microsoft Azure], haga clic en **Servicios móviles** y, a continuación, haga clic en el servicio móvil.

5. Haga clic en la pestaña **Identidad**, escriba los valores **Clave de consumidor** y **Secreto de consumidor** obtenidos de Twitter y haga clic en **Guardar**. 

	![](./media/mobile-services-register-twitter-access/mobile-identity-tab-twitter-only.png)

2. Haga clic en la pestaña **Configurar**, desplácese a **Configuración de aplicaciones** y especifique un par **nombre** y **valor** para cada uno de los siguientes valores que ha obtenido en el sitio de Twitter y, a continuación, haga clic en **Guardar**.

	+ `TWITTER_ACCESS_TOKEN`
	+ `TWITTER_ACCESS_TOKEN_SECRET`

	![](./media/mobile-services-register-twitter-access/mobile-schedule-job-app-settings.png)

	De esta forma, se almacena el acceso a Twitter en la configuración de aplicaciones. Al igual que sucede con las credenciales de consumidor en la pestaña **Identidad**, las credenciales de acceso también se almacenan cifradas en la configuración de aplicaciones y puede obtener acceso a ellas en los scripts de servidor sin codificarlas de forma rígida en el archivo de script. Para obtener más información, vea [Configuración de aplicación].

<!-- URLs. -->
[Referencia del script del servidor de servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
[WindowsAzure.com]: http://www.windowsazure.com/
[Portal de administración de Microsoft Azure]: https://manage.windowsazure.com/
[Registro de aplicaciones para el inicio de sesión en Twitter con Servicios móviles]: ../articles/mobile-services-how-to-register-twitter-authentication.md
[Desarrolladores de Twitter]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Configuración de aplicaciones]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
[Configuración de aplicación]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

<!--HONumber=49-->
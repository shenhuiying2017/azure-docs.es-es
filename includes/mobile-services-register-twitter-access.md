

Las nuevas API de Twitter v1.1 requieren que la aplicación se autentique para obtener acceso a los recursos. Primero, tendrá que obtener las credenciales necesarias para solicitar el acceso mediante OAuth 2.0. A continuación, las almacenará de forma segura en la configuración de aplicaciones para el servicio móvil.

1. Si no lo ha hecho ya, lleve a cabo los pasos que se describen en el tema <a href="/es-es/documentation/articles/mobile-services-how-to-register-twitter-authentication/" target="_blank">Registro de las aplicaciones para el inicio de sesión en Twitter con Servicios móviles</a>. 
  
  	Twitter genera las credenciales necesarias para permitirle obtener acceso a las API de Twitter v1.1. Puede obtener estas credenciales en el sitio web para desarrolladores de Twitter. 

2. Desplácese hasta el sitio web para <a href="http://go.microsoft.com/fwlink/p/?LinkId=268300" target="_blank">desarrolladores de Twitter</a> (en inglés), inicie sesión con las credenciales de la cuenta de Twitter, diríjase a **My Applications** y seleccione la aplicación de Twitter.

    ![](./media/mobile-services-register-twitter-access/mobile-twitter-my-apps.png)

3. En la pestaña **Detalles** de la aplicación, anote los siguientes valores:

	+ **Clave de consumidor**
	+ **Secreto de consumidor**
	+ **Token de acceso**
	+ **Secreto de token de acceso**

	![](./media/mobile-services-register-twitter-access/mobile-twitter-app-secrets.png)

4. Inicie sesión en el [Portal de administración de Microsoft Azure], haga clic en **Servicios móviles** y, a continuación, haga clic en el servicio móvil.

5. Haga clic en la pestaña **Identidad**, escriba los valores **Clave de consumidor** y **Secreto de consumidor** obtenidos de Twitter y haga clic en **Guardar**. 

	![](./media/mobile-services-register-twitter-access/mobile-identity-tab-twitter-only.png)

2. Haga clic en la pestaña **Configure**, desplácese a **Configuración de aplicaciones** y especifique un par **nombre** y **valor** para cada uno de los siguientes valores que ha obtenido en el sitio de Twitter y, a continuación, haga clic en **Save**.

	+ `TWITTER_ACCESS_TOKEN`
	+ `TWITTER_ACCESS_TOKEN_SECRET`

	![](./media/mobile-services-register-twitter-access/mobile-schedule-job-app-settings.png)

	De esta forma, se almacena el acceso a Twitter en la configuración de aplicaciones. Al igual que sucede con las credenciales de consumidor en la pestaña **Identidad**, las credenciales de acceso también se almacenan cifradas en la configuración de aplicaciones y puede obtener acceso a ellas en los scripts de servidor sin codificarlas de forma rígida en el archivo de script. Para obtener más información, consulte [Configuración de aplicaciones].

<!-- URLs. -->
[Referencia del script de servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
[WindowsAzure.com]: http://azure.microsoft.com/
[Portal de administración de Microsoft Azure]: https://manage.windowsazure.com/
[Registro de las aplicaciones para el inicio de sesión en Twitter con Servicios móviles]: /es-es/documentation/articles/mobile-services-how-to-register-twitter-authentication
[Desarrolladores de Twitter]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Configuración de aplicaciones]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7<!--HONumber=42-->

<properties title="Azure RemoteApp FAQ" pageTitle="Preguntas más frecuentes sobre Azure RemoteApp" description="Obtenga información sobre Azure RemoteApp." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="mbaldwin" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/12/2014" ms.author="elizapo" ms.manager="mbaldwin" />

#Preguntas más frecuentes sobre Azure RemoteApp
Hemos recibido las siguientes preguntas acerca de Azure RemoteApp. ¿Tiene alguna otra? Visite los [Foros de RemoteApp](https://social.msdn.microsoft.com/Forums/azure/es-es/home?forum=AzureRemoteApp) y pregúntenos lo que quiera saber.

##¿Qué es Azure RemoteApp?##


- **¿Qué es Azure RemoteApp?** RemoteApp es un servicio de Azure que ofrece la funcionalidad del RemoteApp local de Microsoft, respaldado por Servicios de Escritorio remoto de Azure. RemoteApp ayuda a proporcionar acceso remoto y seguro a aplicaciones desde diferentes dispositivos de usuario. Lea más sobre [Azure RemoteApp](http://azure.microsoft.com/es-es/documentation/articles/remoteapp-whatis/).
- **¿Cuáles son los dos tipos de opciones de implementación?** Hay dos opciones de implementación de RemoteApp (o colecciones): híbrida y nube. Averigüe cuál de estas [opciones de implementación](http://azure.microsoft.com/es-es/documentation/articles/remoteapp-whatis/) se adapta mejor a su organización.

##Configuraciones admitidas##


- **¿Se admiten las aplicaciones personalizadas de línea de negocio (LOB)?** Sí, para usar una aplicación personalizada en Azure RemoteApp, cree una [imagen de plantilla personalizada](http://azure.microsoft.com/es-es/documentation/articles/remoteapp-create-custom-image/)y, luego, cárguela en la colección de RemoteApp.
- **¿Funcionará mi aplicación LOB personalizada en Azure RemoteApp?** La mejor forma de comprobarlo es probándola. Revise los [requisitos de compatibilidad de la aplicación](http://www.microsoft.com/es-es/download/details.aspx?id=18704) y deseleccione [Centro de compatibilidad de escritorio remoto](http://www.rdcompatibility.com/compatibility/default.aspx).
- **¿Qué método de implementación (nube o híbrida) es mejor para mi organización?** Las colecciones híbridas proporcionan una experiencia más completa si quiere integración completa con inicio de sesión único (SSO) y conectividad de red local segura. Las colecciones de nube proporcionan una manera fácil y ágil para aislar la implementación mediante el uso de varios métodos de autenticación. Lea más sobre la [opciones de implementación](http://azure.microsoft.com/es-es/documentation/articles/remoteapp-whatis/).
- **La colección híbrida requiere una red virtual. ¿Podemos usar nuestra red existente?** No en este momento, pero sabemos que quiere hacerlo. Mientras estamos trabajando en esto, puede conectar la red virtual existente a la red virtual de Azure RemoteApp siguiendo [estas instrucciones](http://blogs.msdn.com/b/rds/archive/2014/07/21/how-to-link-azure-remoteapp-to-an-existing-vnet.aspx).
- **¿Puedo usar una nube o una máquina virtual existente como plantilla para mi colección de RemoteApp?** No en este momento, pero esta es una solicitud muy popular en nuestro [sitio de comentarios](http://feedback.azure.com/forums/247748-azure-remoteapp/suggestions/5923679-allow-creation-of-custom-hybrid-image-completely-w).
- **Tenemos SQL u otra base de datos de forma local o en Azure. ¿Qué tipo de implementación debemos usar?** Depende de dónde esté la base de datos SQL o back-end. Si la base de datos está en una red privada, use la colección híbrida. Si la base de datos está expuesta en Internet y permite conexiones cliente para conectarse a ella, puede usar la colección en la nube.
- **¿Qué sucede con la asignación de unidades, USB y puerto serie, uso compartido del portapapeles y redirección de impresora?** Todas esas características se admiten en Azure RemoteApp. Redirección de impresoras y uso compartido del portapapeles se habilita de forma predeterminada. [Póngase en contacto con nosotros](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20enable%20Redirection%20request) para habilitar la asignación de unidades o USB o la redirección de puertos serie. (Estamos trabajando para agregar esta compatibilidad en el portal, pero todavía no está disponible).
- **¿Qué ocurre con la autenticación? ¿Qué métodos son compatibles?** La colección en la nube admite cuentas de Microsoft y cuentas de Azure Active Directory, que son también cuentas de Office 365. La colección híbrida solo admite cuentas de Azure Active Directory que se han sincronizado (con una herramienta como DirSync) desde una implementación de Windows Server Active Directory; en concreto, sincronizadas con la opción de sincronización de contraseña o sincronizadas con la federación Servicios de federación de Active Directory (ADFS) configurada. También puede configurar [Multi-Factor Authentication (MFA)](http://azure.microsoft.com/es-es/documentation/services/multi-factor-authentication/).

	**Nota:** los usuarios de Azure Active Directory deben ser del inquilino al que está asociada la suscripción. (Puede ver y modificar su suscripción en la ficha **Configuración** del portal. Consulte [Cambio del inquilino de Azure Active Directory que usa RemoteApp](http://msdn.microsoft.com/es-es/3d6c4fd1-c981-4c57-9402-59fe31b11883) para obtener más información.)

- **¿Por qué no puedo ofrecer acceso a mi cuenta de Azure Active Directory?** Los usuarios de Azure Active Directory deben ser del directorio que está asociado a la suscripción. Puede ver o modificar dicho directorio en la ficha Configuración del portal. Consulte [Cambio del inquilino de Azure Active Directory que usa RemoteApp](http://msdn.microsoft.com/es-es/3d6c4fd1-c981-4c57-9402-59fe31b11883) para obtener más información.
- **¿Qué dispositivos y sistemas operativos admiten las aplicaciones cliente?** Las aplicaciones cliente están disponibles para Windows 8.1, Windows 8, Windows 7 Service Pack 1, iOS, Mac OS X, Windows RT, dispositivos Android y Windows Phone. También se admite la vista previa de Windows 10.
 
	[Descargar](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx) ahora un cliente de RemoteApp.
- **¿Azure RemoteApp admite clientes ligeros?** Sí, se admiten los siguientes clientes ligeros de Windows Embedded:
	- Windows Embedded Standard 7 con Service Pack 1
	- Windows Embedded POSReady7 
	- Windows Embedded Thin PC 
	- Windows Embedded 8.1 Industry

- **¿Qué versión de Windows Server es compatible con el host de sesión de Escritorio remoto (RDSH)?** Windows Server 2012 R2.

##Soporte y comentarios

- **¿Puedo probar este servicio de forma gratuita?** Sí, hay una versión de prueba gratuita de 30 días. Tras la finalización de la prueba, puede realizar la transición a una cuenta pagada (que puede usar en producción) o dejar de usar el servicio. Comience una prueba gratuita, vaya a [manage.windowsazure.com](http://manage.windowsazure.com) - crear una nueva instancia de RemoteApp. Con la versión de prueba gratuita, puede crear dos instancias de RemoteApp con 10 usuarios por instancia. Recuerde que esta versión de prueba solo dura 30 días.
- **¿Cuál es el plan de soporte para RemoteApp?** El soporte para administración de suscripciones y facturación se proporciona sin coste alguno. El soporte técnico está disponible a través de los [planes de servicio de Azure](http://azure.microsoft.com/support/plans/). También puede obtener soporte técnico gratuito de la comunidad a través de nuestro [foro de discusión de Azure](http://social.msdn.microsoft.com/Forums/windowsazure/es-es/home?forum=AzureRemoteApp).
- **¿Cuánto cuesta RemoteApp?** Eche un vistazo a los [Detalles de precios de Azure RemoteApp](http://azure.microsoft.com/es-es/pricing/details/remoteapp/).
- **¿Cómo puedo enviar comentarios?** Visite el [foro de comentarios](http://feedback.azure.com/forums/247748-azure-remoteapp).
- **¿A quién puedo dirigirme para obtener más información acerca de Azure RemoteApp?** Además de a nuestro [foro de discusión](http://social.msdn.microsoft.com/Forums/windowsazure/es-es/home?forum=AzureRemoteApp), que es un buen sitio para publicar preguntas, puede unirse al [seminario web semanal Preguntar a los expertos](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html), donde se habla de todas las cosas RemoteApp.
- **¿Existe documentación sobre RemoteApp?** Nos alegra que haya preguntado esto. Además del contenido de ayuda en el espacio de ayuda del portal (haga clic en **?** en cualquier página del portal), los artículos siguientes están disponibles para mostrarle todo acerca de RemoteApp:
	- **Primeros pasos:**
		- [¿Qué es RemoteApp?](http://azure.microsoft.com/es-es/documentation/articles/remoteapp-whatis/)
		- [¿Cuáles son las imágenes de plantilla de RemoteApp?](http://azure.microsoft.com/es-es/documentation/articles/remoteapp-images/)
		- [¿Cómo funciona la concesión de licencias?](http://azure.microsoft.com/es-es/documentation/articles/remoteapp-licensing/)
		- [¿Cómo funcionan conjuntamente RemoteApp y Office?](http://azure.microsoft.com/es-es/documentation/articles/remoteapp-o365/)
	- **Implementación:**
		- [Crear una imagen de plantilla personalizada](http://azure.microsoft.com/es-es/documentation/articles/remoteapp-create-custom-image/)
		- [Crear una colección híbrida](http://azure.microsoft.com/es-es/documentation/articles/remoteapp-create-hybrid-deployment/)
		- [Crear una colección en la nube](http://azure.microsoft.com/es-es/documentation/articles/remoteapp-create-cloud-deployment/)
		- [Configurar Azure Active Directory para RemoteApp](http://azure.microsoft.com/es-es/documentation/articles/remoteapp-ad/)
		- [Publicar una aplicación en RemoteApp](http://azure.microsoft.com/es-es/documentation/articles/remoteapp-publish/)
	- **Administrar:**
		- [Agregar usuarios](http://azure.microsoft.com/es-es/documentation/articles/remoteapp-user/)
		- [Prácticas recomendadas para configurar y usar RemoteApp](http://azure.microsoft.com/es-es/documentation/articles/remoteapp-bestpractices/)	

	Vídeos También tenemos una serie de vídeos acerca de RemoteApp. Algunos proporcionan una introducción ([Introducción a Azure RemoteApp](http://azure.microsoft.com/es-es/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/)) y otros le guiarán a través de la implementación ([Implementación en la nube](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be) e [Implementación híbrida](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be)). Écheles un vistazo.


<!--HONumber=35.2-->

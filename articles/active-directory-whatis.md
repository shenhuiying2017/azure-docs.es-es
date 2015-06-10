<properties
	pageTitle="¿Qué es Azure Active Directory?"
	description="Usar Azure Active Directory para ampliar sus identidades locales existentes en la nube o desarrollar aplicaciones integradas de Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/11/2015"
	ms.author="curtand"/>


# ¿Qué es Azure Active Directory?


[¿Cómo funciona?](active-directory-works.md)<br> [Introducción](active-directory-get-started.md)<br> [Pasos siguientes](active-directory-next-steps.md)<br> [Más información](active-directory-learn-map.md)

Azure Active Directory (Azure AD) ofrece una manera fácil para que su empresa amplíe sus inversiones locales para aprovechar los servicios en la nube, con la flexibilidad y la seguridad que Active Directory siempre ha proporcionado. Se trata de una plataforma en la nube que soluciona problemas de la era de la nube ampliando la infraestructura de identidades que ya tiene, sin que sea necesario mover nada. Puede continuar aprovechando sus inversiones existentes y capacidades locales mientras aprovecha Azure AD para poner administrar identidades y el acceso en la nube.

Con Azure AD, puede controlar de forma central el acceso a aplicaciones y recursos, agregar fácilmente recursos existentes (servicios en la nube o aplicaciones locales) e integrar las aplicaciones que está desarrollando. -Los usuarios puedan obtener capacidades de autoservicio y acceso de inicio de sesión único a todas sus aplicaciones, por lo que no necesitan saber dónde encontrar cada aplicación ni recordar una contraseña independiente. -Su empresa puede colaborar en la nube con socios comerciales, proveedores y clientes con negocios que puede invitar a sitios de SharePoint (o participar en sus sitios de SharePoint).

Con Azure AD, puede administrar todo desde un mismo lugar y hacer que ese control se refleje en la nube y en un entorno local. No se duplica la administración; Azure AD se conecta directamente a lo que ya tiene. [Más información](active-directory-aadconnect.md).




> [AZURE.NOTE]Para usar Azure Active Directory, necesita una cuenta de Azure. Si aún no tiene ninguna, puede [registrarse para obtener una cuenta de Azure gratuita](http://azure.microsoft.com/pricing/free-trial/).


## ¿Qué puede hacer Azure Active Directory por mí?

Dado que es un servicio completo, Azure AD proporciona distintas a personas de distintos roles dentro de una organización.

- Si usted se encarga de tomar decisiones empresariales, use Azure AD le permite disponer de recursos móviles y aprovechar la potencia de las aplicaciones en nube con la confianza de que se cumplan los requisitos de gobernanza.
- Si es un proveedor de servicios, use Azure AD para solucionar fácilmente todas sus necesidades de identidades y acceso, conectando los servicios a las soluciones de identidad existentes de los clientes mientras ofrece capacidad de contacto con los clientes de Microsoft Azure y Office 365. Azure AD también puede hacer frente a todas sus necesidades de acceso del área de operaciones, por lo que, tanto si es interno como externo, puede estar seguro de que las personas adecuadas tendrán los derechos de acceso correctos.
- Si es profesional de TI, use Azure AD para aumentar el control y la visibilidad de las operaciones a la "velocidad de la nube". Con Azure AD, sabrá qué personas están haciendo uso y podrá autorizarles a través de ofertas de autoservicio. <br> <br>

![][1]

##Nueve aspectos principales que debe saber sobre Azure AD

### Administración de contraseñas de Active Directory desde cualquier lugar

Use Azure Active Directory para permitir a los usuarios administrar sus contraseñas de Active Directory o Azure Active Directory desde cualquier dispositivo y  cualquier ubicación. Un administrador puede administrar directivas de contraseña y notificación, además de ver la actividad de auditoría detallada de las operaciones de restablecimiento de estas contraseñas en el momento en que se producen. [Más información sobre la administración de contraseñas de Azure ](http://aka.ms/ssproverview)

### Establecimiento de reglas de acceso a recursos en la nube

Puede establecer directivas y reglas que controlen quién tiene acceso, y en qué condiciones, a aplicaciones y recursos en la nube. Por ejemplo, puede requerir autenticación multifactor (MFA) y administrar el acceso basándose en el dispositivo o ubicación. [Obtenga más información acerca de Azure MFA.](multi-factor-authentication.md).

### Inicio de sesión único en cualquier aplicación

Azure Active Directory proporciona un inicio de sesión único seguro en aplicaciones locales y en la nube como Microsoft Office 365 y miles de aplicaciones SaaS como Salesforce, Workday, DocuSign, ServiceNow y Box. [Más información sobre aplicaciones SaaS](http://azure.microsoft.com/marketplace/active-directory/).

### Funciona con cualquier dispositivo

Los usuarios pueden iniciar aplicaciones desde un panel de acceso personalizado basado en web, la aplicación móvil, Office 365 o portales de empresa personalizados con sus credenciales de trabajo existentes, y disfrutar de la misma experiencia independientemente de si trabajan con dispositivos Windows, Mac OS X, Android o iOS.

### Establecimiento de reglas de  acceso externo

Los usuarios externos pueden conectarse a la red local al otro lado de un servidor de seguridad mediante el proxy de aplicación integrado. Todas estas reglas y directivas, incluido Multi-factor Authentication, se pueden aplicar para el acceso a aplicaciones en la nube o a aplicaciones locales heredadas mediante el proxy de aplicación (sin necesidad de volver a escribirlos o exponerlos directamente en Internet). [Obtenga más información sobre del proxy de aplicación de Azure AD.](https://msdn.microsoft.com/library/azure/dn768219.aspx)

### Supervisión del acceso de los usuarios

Por último, Azure AD proporciona información sobre lo que está ocurriendo en su organización a su alcance. Con los informes y el análisis avanzados, obtendrá información exclusiva sobre el acceso de los usuarios. Por ejemplo, mediante la detección de aplicaciones, puede averiguar qué aplicaciones se utilizan activamente en su organización. [Obtenga más información sobre la detección de aplicaciones en la nube de Azure AD](https://appdiscovery.azure.com/).

### Informes, auditorías y análisis avanzados
Azure AD proporciona una cantidad de informes de uso y acceso diario para los administradores ([informes de Azure AD](active-directory-view-access-usage-reports.md)) y ofrece informes personalizados y exploración profunda de datos mediante la API de informes ([API de informes de Azure AD](active-directory-reporting-api-getting-started.md)). Las ediciones de pago de Azure AD incluyen aún más informes, como la auditoría de acciones con privilegios ([auditoría de Azure AD](active-directory-view-access-usage-reports.md)).

### Una experiencia sencilla y segura para todos los usuarios

Ejemplos: - Los usuarios obtienen una experiencia sencilla y colocan sus perfiles, aplicaciones y su capacidad para administrar su acceso a los recursos en un solo lugar, sin necesidad de entrenamiento especializado. La autenticación multifactor, las aplicaciones SaaS, las herramientas híbridas y las capacidades de autoservicio están listas.

- Los administradores tienen acceso al portal de administración de Azure AD y Windows PowerShell para la administración integral.

- Los desarrolladores tienen un conjunto coherente de API de REST y fácil acceso a interfaces de publicación y consumo.

### Elija su modelo

Azure AD se distribuye en tres modelos:

- La edición gratuita, que es simplemente un servicio de directorios en la nube.
- La edición básica, que es un servicio de directorios en la nube que también proporciona acceso a aplicaciones SaaS.
- La edición Premium, que es una solución de servicios de directorio administrados integral, controlada por reglas y de autoservicio.

Para comprobar las características que se describen aquí, active la [prueba gratuita de Azure](http://azure.microsoft.com/trial/get-started-active-directory/).

[Obtenga más información acerca de las ediciones de Azure AD](active-directory-editions.md)


## Recursos adicionales

* [Inicio de sesión en Azure como una organización](sign-up-organization.md)
* [Identidad de Azure](fundamentals-identity.md)

<!--Image references-->
[1]: ./media/active-directory-whatis/Azure_Active_Directory.png

<!---HONumber=58-->
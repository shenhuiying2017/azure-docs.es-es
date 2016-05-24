<properties
	pageTitle="P+F de Azure Active Directory | Microsoft Azure"
	description="P+F de Azure Active Directory"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/20/2016"
	ms.author="markusvi"/>

# P+F de Azure Active Directory

Azure Active Directory (Azure AD) es un conjunto integral de funcionalidades de administración de identidad y acceso basado en la nube que proporciona identidad como servicio (IDaaS). Como proveedor de IDaaS, Azure AD permite controlar "*quién puede hacer qué*" en la red de equipos. Además, Azure AD proporciona inicio de sesión único (SSO) para las aplicaciones. Con SSO, puede mejorar considerablemente la experiencia de inicio de sesión de los usuarios. Para más información, consulte [¿Qué es Azure Active Directory?](active-directory-whatis.md).



## Acceso a Azure y Azure Active Directory

****P: ¿Por qué no encuentro Azure AD en el Portal de Azure (http://portal.azure.com)?**)?

**R:** La experiencia de administración de Azure AD sigue estando disponible en el Portal de Azure clásico (`http://manage.windowsazure.com`). El plan es publicar una versión preliminar de las nuevas experiencias de administración integrada en el Portal de Azure para septiembre. Para obtener la información más reciente sobre las versiones, consulte el [blog del equipo de Active Directory](https://blogs.technet.microsoft.com/ad/) .


---

****P: ¿Por qué obtengo "No subscriptions found" (No se encontraron suscripciones) cuando intento acceder a Azure AD en el Portal de Azure clásico (http://manage.windowsazure.com)?**)?

**R:** Necesita permisos en una suscripción de Azure, si tiene Office 365 o Azure AD de pago vaya a [http://aka.ms/accessAAD](http://aka.ms/accessAAD) para la activación en un paso; de lo contrario, debe activar una [prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/) completa o una suscripción de pago.

---

**P: ¿Cuál es la relación entre AD de Azure, Office 365 y Azure?**

**R:** Microsoft Azure es una plataforma informática abierta y flexible en la nube para empresas con la infraestructura necesaria para crear, implementar y administrar aplicaciones y servicios a través de una red mundial de centros de datos administrados por Microsoft. Azure proporciona, por ejemplo, soluciones de software como servicio (SaaS) como Office 365. Office 365 es un grupo de suscripciones de software y servicios que ofrece software de productividad y servicios en línea relacionados. Azure complementa Office 365 con un conjunto integral de funcionalidades de administración de identidad y acceso basado en la nube que proporciona identidad como servicio (IDaaS). Si dispone de una suscripción a Office 365, tendrá también Azure AD en ejecución automáticamente en Azure.


---

**P: ¿Qué características de Azure AD están disponibles de forma gratuita?**

**R:** Todas las características comunes de una suscripción de Azure están disponibles de forma gratuita. Para una lista exhaustiva de características, consulte [Ediciones de Azure Active Directory](active-directory-editions.md/#common-features).



---

P: **¿Cómo conecto mi directorio local a Azure AD?**

R: Puede conectar su directorio local a Azure AD mediante **Azure AD Connect**. Para más detalles, consulte [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).


---

**P: ¿Cómo se configura el SSO entre mi directorio local y las aplicaciones de nube?**

**R:** Solo tiene que configurar el SSO entre el directorio local y Azure AD. Podrá ampliar la cobertura del SSO a su entorno local mediante la implementación de la federación con la sincronización de contraseña o de ADFS, siempre y cuando se administren las aplicaciones en la nube con Azure AD. En **Azure AD Connect** se incluyen las dos opciones. Para más detalles, consulte [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).
  


---

**P: ¿Me ayuda Azure AD a administrar la infraestructura local?**

**R:** Efectivamente. Premium Edition de Azure AD viene con **Connect Health**. Azure AD Connect Health le ayuda a supervisar y a comprender mejor su infraestructura de identidad local y los servicios de sincronización. Para más detalles, consulte [Supervisión de la infraestructura de identidad local y los servicios de sincronización en la nube](active-directory-aadconnect-health.md).



## Administración de contraseñas

**P: ¿Puedo usar la escritura diferida de contraseñas de Azure AD sin sincronización de contraseñas? (Es decir, me gustaría usar SSPR de Azure AD con la escritura diferida de contraseñas, pero no quiero que mi contraseñas se almacenen en la nube)**

**R:** No es necesario sincronizar las contraseñas de AD con Azure AD para habilitar la escritura diferida. En un entorno federado, el SSO Azure AD se basa en el directorio local para autenticar al usuario. En este caso no se necesita la contraseña local para realizar el seguimiento en Azure AD.

---

**P: ¿Cuánto se tarda en escribir una contraseña en diferido en AD local?**

**R:** Las contraseñas se escriben en diferido en tiempo real. Para más detalles, consulte [Introducción a la administración de contraseñas](active-directory-passwords-getting-started.md).


---

**P: ¿Puedo usar la escritura diferida de contraseñas con las que administra un administrador?**

**R:** Sí, si tiene la escritura diferida de contraseñas habilitada, las operaciones de contraseña realizadas por un administrador se escriben de manera diferida en el entorno local. Para ver más respuestas a preguntas relativas a las contraseñas, consulte [Preguntas más frecuentes sobre la administración de contraseñas](active-directory-passwords-faq.md).



## Acceso a las aplicaciones


**P: ¿Dónde puedo encontrar una lista de las aplicaciones preintegradas en Azure AD y sus funcionalidades?**

**R:** Azure AD cuenta con más de 2600 preintegradas de Microsoft, proveedores de servicios de aplicaciones o asociados. Para una lista exhaustiva de las aplicaciones preintegradas, consulte [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).


---

**P: ¿Qué ocurre si no encuentro la aplicación que necesito en Marketplace de Azure AD?**

**R:** Con Azure AD Premium, puede agregar y configurar cualquier aplicación. Además, puede, en función de las funcionalidades de la aplicación y sus preferencias, configurar el SSO y el aprovisionamiento automatizado. Para obtener información, consulte:

- [Configuración del inicio de sesión único en aplicaciones que no están en la Galería de aplicaciones de Azure Active Directory](active-directory-saas-custom-apps.md)
- [Uso de SCIM para habilitar el aprovisionamiento automático de usuarios y grupos de Azure Active Directory a aplicaciones](active-directory-scim-provisioning.md) 



---

**P: ¿Puedo agregar aplicaciones que ejecuto de manera local?**

**R:** El Proxy de la aplicación de Azure AD permite acceder fácilmente a las aplicaciones web locales, del mismo modo que a las aplicaciones SaaS en Azure Active Directory, sin necesidad de una red privada virtual o de cambios en la infraestructura de red. Para más detalles, consulte [Provisión de acceso remoto seguro a aplicaciones locales](active-directory-application-proxy-get-started.md).


--- 

**P: ¿Cómo solicito MFA para que los usuarios accedan a una aplicación determinada?**

**R:** Con el acceso condicional de Azure AD puede asignar una directiva de acceso único a cada aplicación. En la directiva, puede solicitar MFA siempre o cuando los usuarios no estén conectados a la red local. Para más detalles, consulte [Protección del acceso a Office 365 y otras aplicaciones conectadas a Azure Active Directory](active-directory-conditional-access.md).

<!---HONumber=AcomDC_0518_2016-->
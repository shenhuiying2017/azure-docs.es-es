<properties 
	pageTitle="Monitorización de la infraestructura de identidad local en la nube" 
	description="En esta página se describe qué es Azure AD Connect Health y por qué debería usarlo." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Monitorización de la infraestructura de identidad local en la nube

Azure AD Connect Health le ayuda a supervisar y a comprender mejor su infraestructura de identidad local. Ofrece la capacidad de ver alertas, rendimiento, patrones de uso, ajustes de configuración; le permite mantener una conexión confiable a Office 365 y mucho más. Esto se logra mediante un agente instalado en los servidores de destino. Para obtener más información, consulte [Azure AD Connect Health en MSDN.](https://msdn.microsoft.com/library/azure/dn906722.aspx)

![Qué es Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnecthealth2.png)


Esta información se presenta en el portal de Azure AD Connect Health. Mediante el portal de Azure AD Connect Health puede ver alertas, supervisión del rendimiento y análisis de uso. Esta información se presenta en un mismo lugar, muy fácil de usar, para que no tenga que perder tiempo en la búsqueda de la información que necesita.

![Qué es Azure AD Connect Health](./media/active-directory-aadconnect-health/usage.png)

Las actualizaciones futuras de Azure AD Connect Health incluirá la supervisión adicional y la información sobre otros componentes de identidad y servicios, como servicios de Azure AD Connect Sync. Por tanto, se proporciona un único panel mediante el modo de identidad, al permitir un entorno más sólido, mantenido e integrado que los usuarios pueden aprovechar para aumentar su capacidad de hacer su trabajo.

Para obtener más información, consulte [Azure AD Connect Health en MSDN.](https://msdn.microsoft.com/library/azure/dn906722.aspx)


![Qué es Azure AD Connect Health](./media/active-directory-aadconnect-health/logo1.png)




## Por qué usar Azure AD Connect Health

La integración de directorios locales con Azure AD hace que los usuarios sean más productivos al proporcionar una identidad común para acceder tanto a los recursos en la nube como a los locales. Sin embargo, esta integración conlleva el desafío de garantizar que este entorno esté bien mantenido para que los usuarios puedan acceder de manera confiable a los recursos tanto a nivel local como en la nube desde cualquier dispositivo. Azure AD Connect Health proporciona un sencillo enfoque basado en la nube para supervisar y obtener información sobre la infraestructura de identidad local, que se utiliza para acceder a Office 365 o a otras aplicaciones de Azure AD. Es tan sencillo como instalar un agente en cada uno de los servidores de identidad locales.

Azure AD Connect Health para AD FS es compatible con AD FS 2.0 en Windows Server 2008/2008 R2, AD FS en Windows Server 2012/2012 R2. También se incluyen los servidores Proxy de AD FS o Proxy de aplicación web que proporcionan compatibilidad de autenticación para el acceso a la extranet. Azure AD Connect Health para AD FS proporciona el siguiente conjunto de capacidades clave:

- Ver y realizar acciones sobre las alertas para un acceso confiable a las aplicaciones protegidas de AD FS, como Azure AD
- Notificaciones de correo electrónico para alertas críticas
- Ver datos de rendimiento para determinar la planificación de capacidad
- Vistas detalladas de los patrones de inicio de sesión de AD FS para determinar las anomalías o establecer líneas de base para la planificación de capacidad



----------------------------------------------------------------------------------------------------------
## Descargar el agente de Azure AD Connect Health

Para empezar a utilizar Azure AD Connect Health, puede descargar la versión más reciente del agente aquí: [Descargar agente de Azure AD Connect Health](http://go.microsoft.com/fwlink/?LinkID=518973). Asegúrese de que ha agregado el servicio de Marketplace antes de instalar los agentes.

----------------------------------------------------------------------------------------------------------





**Recursos adicionales**

* [Azure AD Connect Health en MSDN](https://msdn.microsoft.com/library/azure/dn906722.aspx)


 
<!--HONumber=62-->
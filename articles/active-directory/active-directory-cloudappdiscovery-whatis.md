<properties 
	pageTitle="¿Cómo puedo detectar aplicaciones en la nube no sancionadas que se usan dentro de mi organización?" 
	description="En este tema se describe qué es Cloud App Discovery y por qué debería usarlo." 
	services="active-directory" 
	documentationCenter="" 
	authors="markusvi" 
	manager="swadhwa" 
	editor="lisatoft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/23/2015" 
	ms.author="markusvi"/>

# ¿Cómo puedo detectar aplicaciones en la nube no sancionadas que se usan dentro de mi organización?

En las empresas modernas, los departamentos de TI a menudo no son conscientes de todas las aplicaciones en la nube que usan los usuarios para realizar su trabajo. Por consiguiente, los administradores suelen tener problemas junto con el acceso no autorizado a los datos corporativos, posible pérdida de datos y otros riesgos de seguridad inherentes a las aplicaciones. Dado que no saben cuántas o qué aplicaciones se usan, incluso comenzar a crear un plan para tratar estos riesgos parece desalentador.

Puede solucionar estos problemas mediante Cloud App Discovery. Cloud App Discovery es una característica Premium de Azure Active Directory que permite detectar aplicaciones en la nube usadas por los empleados de la organización.


**Con Cloud App Discovery, puede:**

* Detectar aplicaciones en uso y medir el uso por número de usuarios, volumen de tráfico o número de solicitudes web a la aplicación. 
* Identificar a los usuarios que están usando una aplicación. 
* Exportar datos para análisis sin conexión de adición. 
* Dar prioridad a las aplicaciones para poner bajo control de TI e integrar aplicaciones fácilmente para habilitar el inicio de sesión único y la administración de usuarios. 

Con Cloud App Discovery, la parte de la recuperación de datos se realiza mediante agentes que se ejecutan en equipos de sus entornos. La información de uso de aplicaciones que capturan los agentes se envía a través de un canal seguro y cifrado al servicio Cloud App Discovery. El servicio Cloud App Discovery evalúa los datos y genera informes que puede usar para analizar su entorno.


<center>![How Cloud App Discovery Works](./media/active-directory-cloudappdiscovery/cad01.png)</center>

##Pasos siguientes


* Para obtener más información acerca cómo funciona Cloud App Discovery, vea [Introducción a Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx). 
* Para obtener información sobre consideraciones sobre privacidad y seguridad, consulte [Consideraciones de seguridad y privacidad de Cloud App Discovery](active-directory-cloudappdiscovery-security-and-privacy-considerations.md). 
* Para obtener información sobre la implementación del agente de Cloud App Discovery en un entorno empresarial con: 
 * Administración de directivas de grupo de Active Directory, vea [Guía de implementación de directivas de grupo de Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx). 
 * Microsoft System Center Configuration Manager, vea [Guía de implementación de System Center de Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx) 
 * Servidores proxy con puertos personalizados, vea [Configuración del registro de Cloud App Discovery para servidores proxy con puertos personalizados](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md) 





**Recursos adicionales**


* [Cloud App Discovery: registro de cambios del agente](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
* [Cloud App Discovery: preguntas más frecuentes](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx)

<!---HONumber=July15_HO5-->
<properties 
	pageTitle="Habilitación del directorio para la administración híbrida con Azure AD Connect." 
	description="Aquí se describe qué es Azure AD Connect y por qué debería usarlo." 
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
	ms.date="06/22/2015" 
	ms.author="billmath"/>

# Habilitación del directorio para la administración híbrida con Azure AD Connect

<div class="dev-center-tutorial-selector sublanding">
<a href="../active-directory-aadconnect/" title="¿Qué es?" class="current">¿Qué es?</a> <a href="../active-directory-aadconnect-how-it-works/" title="¿Cómo funciona?">¿Cómo funciona?</a> <a href="../active-directory-aadconnect-get-started/" title="Introducción">Introducción</a> <a href="../active-directory-aadconnect-whats-next/" title="¿Qué sigue?">¿Qué sigue?</a> <a href="../active-directory-aadconnect-learn-more/" title="Más información">Más información</a>
</div>


Hoy en día, los usuarios desean tener acceso a las aplicaciones tanto a nivel local como en la nube. Desean poder hacerlo desde cualquier dispositivo, ya sea un equipo portátil, un teléfono inteligente o una tableta. Para que esto ocurra, usted y su organización deben poder proporcionar una forma para que los usuarios tengan acceso a estas aplicaciones; sin embargo pasar completamente a la nube no es siempre una opción.

<center>![What is Azure AD Connect](./media/active-directory-aadconnect/arch.png)</center>

Con la introducción de Azure Active Directory Connect, proporcionar acceso a estas aplicaciones y pasar a la nube nunca ha sido tan fácil. Azure AD Connect ofrece las siguientes ventajas:

- Los usuarios pueden iniciar sesión con una identidad común en la nube y localmente. No es necesario recordar varias contraseñas o cuentas y los administradores no tienen que preocuparse por la sobrecarga adicional que pueden suponer varias cuentas.
- Una única herramienta y experiencia guiada para la conexión de sus directorios locales con Azure Active Directory. Una vez instalado, el asistente implementa y configura todos los componentes necesarios para preparar la integración y ejecución de directorios, incluidos los servicios de sincronización, la sincronización de contraseñas o AD FS y los requisitos previos, como el módulo de Azure AD PowerShell.



<center>![What is Azure AD Connect](./media/active-directory-aadconnect/azuread.png)</center>




## ¿Por qué usar Azure AD Connect? 

la integración de directorios locales con Azure AD hace que los usuarios sean más productivos proporcionando una identidad común para tener acceso a recursos de nube y locales. Con esta integración, los usuarios y las organizaciones pueden aprovechar lo siguiente:
	
* Las organizaciones pueden proporcionar a los usuarios una identidad híbrida común entre servicios en la nube o locales al aprovechar Windows Server Active Directory y, a continuación, conectarse a Active Directory de Azure. 
* Los administradores pueden proporcionar acceso condicional basado en el recurso de la aplicación, dispositivo e identidad de usuario, ubicación de red y autenticación multifactor.
* Los usuarios pueden aprovechar su identidad común mediante cuentas de Azure AD a Office 365, Intune, aplicaciones SaaS y aplicaciones de otros fabricantes.  
* Los desarrolladores pueden crear aplicaciones que aprovechan el modelo de identidad común, la integración de aplicaciones en Active Directory local o en Azure para aplicaciones basadas en la nube

Azure AD Connect facilita esta integración y simplifica la administración de su infraestructura de identidad en la nube y local.



----------------------------------------------------------------------------------------------------------
## Descarga de Azure AD Connect

Para empezar a utilizar Azure AD Connect puede descargar la versión más reciente mediante [Descarga de Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771).

----------------------------------------------------------------------------------------------------------








 

<!---HONumber=62-->
<properties
	pageTitle="Creación de una regla sencilla para configurar pertenencias dinámicas para un grupo | Microsoft Azure"
	description="Explica cómo crear una regla sencilla para configurar pertenencias dinámicas para un grupo."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="femila"/>


# Creación de una regla sencilla para configurar pertenencias dinámicas para un grupo

**Para habilitar la pertenencia dinámica para un grupo determinado, siga estos pasos:**

1. En el Portal de administración de Azure, en la pestaña **Grupos**, seleccione el grupo que desea editar, y, a continuación, en la pestaña **Configurar** de este grupo, establezca el conmutador **Habilitar pertenencia dinámica** en **Sí**.


2. Ahora puede configurar una sola regla sencilla para el grupo que controlará cómo funciona la pertenencia dinámica para este grupo. Asegúrese de que el botón de radio **Agregar usuarios donde** está seleccionado y, a continuación, seleccione una propiedad de usuario en el menú desplegable (por ejemplo, department, jobTitle, etc.).

3. A continuación, seleccione una condición (Not Equals, Equals, Not Starts With, Starts With, Not Contains, Contains, Not Match, Match), y por último, especifique un valor para la propiedad del usuario seleccionado. Por ejemplo, si un grupo se asigna a una aplicación SaaS y habilita las pertenencias dinámicas para este grupo estableciendo una regla mediante la cual **Agregar usuarios donde** se establece para un puesto de trabajo (jobTitle) con la condición Equals(-eq)Sales Rep, todos los usuarios de su directorio de Azure AD cuyos puestos de trabajo se hayan configurado en Sales Rep tendrán acceso a la aplicación SaaS.

A continuación presentamos algunos temas que proporcionarán información adicional acerca de Azure Active Directory.

* [Administración del acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)

* [¿Qué es Azure Active Directory?](active-directory-whatis.md)

* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=July15_HO4-->
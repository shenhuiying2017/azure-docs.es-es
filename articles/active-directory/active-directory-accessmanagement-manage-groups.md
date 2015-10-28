<properties

	pageTitle="Managing security groups in Azure Active Directory | Microsoft Azure"
	description="Covers how to sign up for Azure and first steps you can try with Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="femila"/>


#Administración de grupos de seguridad en Azure Active Directory


##Creación y administración de un grupo de seguridad

**Para crear un grupo desde el Portal de administración de Azure**

1. En el Portal de administración, haga clic en Active Directory y, a continuación, haga clic en el nombre del directorio de su organización.
2. Haga clic en la pestaña **Grupos**.
3. En la página Grupos, haga clic en **Agregar grupo**.
4. En la ventana **Agregar grupo**, especifique el nombre y la descripción de un grupo.
5. Esta tarea puede realizarse mediante el portal de la cuenta de Office 365, el portal de cuentas de Windows Intune o el portal de administración de Azure, dependiendo de los servicios a los que se haya suscrito su organización. Para obtener más información acerca del uso de portales para administrar su directorio de Azure Active Directory, consulte Administración de su directorio de Azure AD.

## Asignación o eliminación de usuarios en un grupo de seguridad

**Para agregar un miembro a un grupo desde el Portal de administración de Azure**

1. En el Portal de administración, haga clic en Active Directory y, a continuación, haga clic en el nombre del directorio de su organización.
2. Haga clic en la pestaña **Grupos**.
3. En la página **Grupos**, haga clic en el nombre del grupo al que desea agregar miembros. De forma predeterminada, aparece la pestaña **Miembros** del grupo seleccionado.
4. En la página del grupo, haga clic en**Agregar miembros**.
5. En la página **Agregar miembros**, haga clic en el nombre del usuario o el grupo que desea agregar como miembro de este grupo y asegúrese de que este nombre se agrega al panel Seleccionado.


**Para quitar un miembro de un grupo desde el Portal de administración de Azure**

1. En el Portal de administración, haga clic en Active Directory y, a continuación, haga clic en el nombre del directorio de su organización.
2. Haga clic en la pestaña **Grupos**.
3. En la página Grupos, haga clic en el nombre del grupo del que desea quitar miembros.
4. En la página del grupo, haga clic en la pestaña **Miembros**.
5. En la página de ese grupo, haga clic en el nombre del miembro que desea quitar de este grupo y, a continuación, haga clic en**Quitar**.
6. Verifique que desea quitar este miembro del grupo haciendo clic en**Sí** como respuesta a la pregunta de comprobación de la acción.


##Uso de una regla para administrar dinámicamente los miembros de un grupo de seguridad
**Para habilitar la pertenencia dinámica para un grupo determinado, siga estos pasos:**

1. En el Portal de administración de Azure, en la pestaña **Grupos**, seleccione el grupo que desea editar, y, a continuación, en la pestaña **Configurar** de este grupo, establezca el conmutador **Habilitar pertenencia dinámica** en **Sí**.
2. Ahora puede configurar una sola regla sencilla para el grupo que controlará cómo funciona la pertenencia dinámica para este grupo. Asegúrese de que el botón de radio **Agregar usuarios donde** está activado y, a continuación, seleccione una propiedad de usuario en el menú desplegable (por ejemplo, department, jobTitle, etc.). 
3. A continuación, seleccione una condición (Not Equals, Equals, Not Starts With, Starts With, Not Contains, Contains, Not Match, Match), y por último, especifique un valor para la propiedad del usuario seleccionado.
4. Por ejemplo, si un grupo se asigna a una aplicación SaaS (para más información, vea Asignación de acceso para un grupo a una aplicación SaaS en Azure AD) y habilita las pertenencias dinámicas para este grupo estableciendo una regla mediante la cual los usuarios Agregar usuarios donde se establece para un puesto de trabajo (jobTitle) con la condición Equals(-eq)Sales Rep, todos los usuarios de su directorio de Azure AD cuyos puestos de trabajo se hayan configurado en Sales Rep tendrán acceso a la aplicación SaaS.

A continuación presentamos algunos temas que proporcionarán información adicional acerca de Azure Active Directory.

* [Administración del acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)

* [¿Qué es Azure Active Directory?](active-directory-whatis.md)

* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=Oct15_HO3-->
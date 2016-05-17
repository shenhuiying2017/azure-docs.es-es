<properties

	pageTitle="Managing groups in Azure Active Directory | Microsoft Azure"
	description="How to create and manage groups to manage Azure resource access using Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/18/2016"
	ms.author="curtand"/>


# Administración de grupos en Azure Active Directory

Una de las principales características de Azure Active Directory (Azure AD) es la capacidad para administrar el acceso a los recursos. Estos recursos pueden ser objetos en el directorio, o bien recursos externos al directorio, como las aplicaciones SaaS, servicios de Azure, sitios de SharePoint o recursos locales. Además, el propietario de un recurso puede asignar acceso a un recurso a un grupo de Azure AD. Esto concede a los miembros de ese grupo acceso al recurso. A continuación, el propietario del grupo administra la pertenencia al grupo. El propietario del recurso delega efectivamente en el propietario del grupo el permiso para asignar usuarios a sus recursos.

## ¿Cómo se crea un grupo?

**Para crear un grupo**

Esta tarea puede realizarse mediante el portal de cuentas de Office 365, el portal de cuentas de Windows Intune o el portal de Azure, en función de los servicios a los que se haya suscrito su organización. Para obtener más información acerca del uso de portales para administrar su directorio de Azure Active Directory, consulte [Administración de su directorio de Azure AD](active-directory-administer.md).

1. En el [Portal de Azure clásico](https://manage.windowsazure.com), seleccione **Active Directory** y luego el nombre del directorio de su organización.

2. Seleccione la pestaña **Grupos**.

3. Seleccione **Agregar grupo**.

4. En la ventana **Agregar grupo**, especifique el nombre y la descripción de un grupo.


## Asignación o eliminación de usuarios individuales de un grupo de seguridad

**Para agregar un usuario individual a un grupo**

1. En el [Portal de Azure clásico](https://manage.windowsazure.com), seleccione **Active Directory** y luego el nombre del directorio de su organización.

2. Seleccione la pestaña **Grupos**.

3. Abra el grupo al que desea agregar miembros. De forma predeterminada, aparece la pestaña **Miembros** del grupo seleccionado.

4. Seleccione **Agregar miembros**.

5. En la página **Agregar miembros**, seleccione el nombre del usuario o el grupo que quiere agregar como miembro de este grupo y asegúrese de que este nombre se agrega al panel **Seleccionado**.


**Para quitar un usuario individual de un grupo**

1. En el [Portal de Azure clásico](https://manage.windowsazure.com), seleccione **Active Directory** y luego el nombre del directorio de su organización.

2. Seleccione la pestaña **Grupos**.

3. Abra el grupo del que desea quitar miembros.

4. Seleccione la pestaña **Miembros**, elija el nombre del miembro que quiere quitar de este grupo y haga clic en **Quitar**.

6. En el símbolo del sistema, confirme que desea quitar este miembro del grupo.


## ¿Cómo puedo administrar la pertenencia de un grupo dinámicamente?

En Azure AD, puede configurar fácilmente una regla sencilla (es decir, una regla que solo realiza una comparación) para determinar qué usuarios van a ser miembros del grupo. Por ejemplo, si se asigna un grupo a una aplicación SaaS, y configura una regla para agregar los usuarios que tienen el puesto de trabajo "Representante de ventas", todos los usuarios de su directorio de Azure AD con ese puesto de trabajo tendrán acceso a esta aplicación SaaS.

> [AZURE.NOTE] Puede configurar una regla de pertenencia dinámica a grupos de seguridad o en grupos de Office 365. En este momento, no se admiten las pertenencias a grupos anidadas para la asignación basada en grupos a aplicaciones.
>
> La pertenencia dinámica a grupos requiere que haya una licencia de Azure AD Premium asignada
> 
> - Al administrador que administra la regla en un grupo
> - A todos los usuarios seleccionados por la regla para que sean miembros del grupo

**Para habilitar la pertenencia dinámica para un grupo**

1. En el [Portal de Azure clásico](https://manage.windowsazure.com), seleccione **Active Directory** y luego el nombre del directorio de su organización.

2. Seleccione la pestaña **Grupos** y abra el grupo que desea editar.

3. Seleccione la pestaña **Configurar** y establezca **Habilitar pertenencia dinámica** en **Sí**.

4. Configure una sola regla sencilla para el grupo que controlará cómo funciona la pertenencia dinámica para este grupo. Asegúrese de que la opción **Agregar usuarios donde** está seleccionada y, a continuación, seleccione una propiedad de usuario en la lista (por ejemplo, department, jobTitle, etc.).

5. A continuación, seleccione una condición (Not Equals, Equals, Not Starts With, Starts With, Not Contains, Contains, Not Match, Match), y por último, especifique un valor para la propiedad del usuario seleccionado.

Para aprender a crear reglas *avanzadas* (reglas que pueden contener varias comparaciones) para la pertenencia dinámica a grupos, consulte [Uso de atributos para crear reglas avanzadas](active-directory-accessmanagement-groups-with-advanced-rules.md).

## Información adicional

Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [Administración del acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)

* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)

* [¿Qué es Azure Active Directory?](active-directory-whatis.md)

* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0511_2016-->
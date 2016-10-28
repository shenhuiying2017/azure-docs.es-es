<properties
	pageTitle="Eliminación de asignaciones de usuario o grupo de una aplicación empresarial en la versión preliminar de Azure Active Directory | Microsoft Azure"
	description="Cómo eliminar la asignación del acceso de un usuario o grupo de una aplicación empresarial en Azure Active Directory"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/12/2016"
	ms.author="curtand"/>


# Eliminación de asignaciones de usuario o grupo de una aplicación empresarial en la versión preliminar de Azure Active Directory

Es fácil evitar que se asigne acceso a un usuario o grupo a una de sus aplicaciones empresariales en la versión preliminar de Azure Active Directory (Azure AD). [¿Qué es la versión preliminar?](active-directory-preview-explainer.md) Debe tener los permisos adecuados para administrar la aplicación empresarial. En la versión preliminar actual, debe ser administrador global del directorio.

## ¿Cómo se puede eliminar una asignación de grupo o usuario?

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.

2. Seleccione **Más servicios**, escriba **Azure Active Directory** en el cuadro de texto y, después, presione **Intro**.

3. En la hoja **Azure Active Directory - *nombreDelDirectorio*** (es decir, la hoja de Azure AD del directorio que está administrando), seleccione **Enterprise applications** (Aplicaciones empresariales).

	![Apertura de Enterprise apps (Aplicaciones empresariales)](./media/active-directory-coreapps-remove-assignment-user-azure-portal/open-enterprise-apps.png)

4. En la hoja **Enterprise applications** (Aplicaciones empresariales), seleccione **Todas las aplicaciones**. Verá una lista de las aplicaciones que puede administrar.

5. En la hoja **Enterprise applications (Aplicaciones empresariales) - Todas las aplicaciones**, seleccione una aplicación.

6. En la hoja ***nombreDeLaAplicación*** (es decir, la hoja con el nombre de la aplicación seleccionada en el título), seleccione **Usuarios y grupos**.

	![Selección de usuarios o grupos](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-app-users.png)

7. En la hoja ***nombreDeLaAplicación*** **- Asignación de grupos y usuarios**, seleccione uno de los varios usuarios o grupos y, luego, seleccione el comando **Quitar**. Confirme la decisión en el símbolo del sistema.

	![Selección del comando Eliminar](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-users.png)

## Pasos siguientes

- [Ver todos mis grupos](active-directory-groups-view-azure-portal.md)
- [Asignar un usuario o grupo a una aplicación empresarial](active-directory-coreapps-assign-user-azure-portal.md)
- [Deshabilitar los inicios de sesión de los usuarios de una aplicación empresarial](active-directory-coreapps-disable-app-azure-portal.md)
- [Cambiar el nombre o el logotipo de una aplicación empresarial](active-directory-coreapps-change-app-logo-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->
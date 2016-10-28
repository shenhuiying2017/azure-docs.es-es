<properties
	pageTitle="Asignación de un usuario o un grupo a una aplicación empresarial en la versión preliminar de Azure Active Directory | Microsoft Azure"
	description="Procedimiento para seleccionar una aplicación empresarial para asignarla a un usuario o un grupo en la versión preliminar de Azure Active Directory"
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

# Asignación de un usuario o un grupo a una aplicación empresarial en la versión preliminar de Azure Active Directory

Asignar un usuario o un grupo a una aplicación empresarial de la versión preliminar de Azure Active Directory (Azure AD) es fácil. [¿Qué hay en la versión preliminar?](active-directory-preview-explainer.md) Para administrar la aplicación empresarial, debe tener los permisos adecuados. En la versión preliminar actual, debe ser administrador global del directorio.

## ¿Cómo se asigna a los usuarios acceso a una aplicación empresarial?

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.

2. Seleccione **Más servicios**, escriba Azure Active Directory en el cuadro de texto y, después, presione **Entrar**.

3. En la hoja **Azure Active Directory - *nombre del directorio*** (es decir, la hoja de Azure AD del directorio que está administrando), seleccione **Enterprise applications** (Aplicaciones empresariales).

  ![Apertura de aplicaciones empresariales](./media/active-directory-coreapps-assign-user-azure-portal/open-enterprise-apps.png)

4. En la hoja **Enterprise applications** (Aplicaciones empresariales), seleccione **Todas las aplicaciones**. Verá una lista de las aplicaciones que puede administrar.

5. En la hoja **Enterprise applications (Aplicaciones empresariales) - Todas las aplicaciones**, seleccione una aplicación.

6. En la hoja ***nombre de la aplicación*** (es decir, la hoja con el nombre de la aplicación seleccionada en el título), seleccione **Usuarios y grupos**.

  ![Selección del comando Todas las aplicaciones](./media/active-directory-coreapps-assign-user-azure-portal/select-app-users.png)

7. En la hoja ***nombre de la aplicación*** **- User & Group Assignment (Asignación de usuarios y grupos)**, seleccione el comando **Agregar**.

8. En la hoja **Add Assignment** (Agregar asignación), seleccione **Usuarios y grupos**.

  ![Asignación de un usuario o un grupo a la aplicación](./media/active-directory-coreapps-assign-user-azure-portal/assign-users.png)

9. En la hoja **Usuarios y grupos**, seleccione uno o varios usuarios o grupos de la lista y luego seleccione el botón **Seleccionar** en la parte inferior de la hoja.

10. En la hoja **Add Assignment** (Agregar asignación), seleccione **Rol**. A continuación, en la hoja **Seleccionar rol**, seleccione un rol para aplicar a los usuarios o grupos seleccionados y luego seleccione el botón **Aceptar** en la parte inferior de la hoja.

11. En la hoja **Add Assignment** (Agregar asignación), seleccione el botón **Asignar** en la parte inferior de la hoja. Los usuarios o grupos asignados tendrán definidos los permisos por el rol seleccionado para esta aplicación empresarial.

## Pasos siguientes

- [Ver todos mis grupos](active-directory-groups-view-azure-portal.md)
- [Quitar una asignación de usuario o grupo de una aplicación empresarial](active-directory-coreapps-remove-assignment-user-azure-portal.md)
- [Deshabilitar los inicios de sesión de los usuarios de una aplicación empresarial](active-directory-coreapps-disable-app-azure-portal.md)
- [Cambiar el nombre o el logotipo de una aplicación empresarial](active-directory-coreapps-change-app-logo-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->
<properties
	pageTitle="Creación de un nuevo grupo en la versión preliminar de Azure Active Directory | Microsoft Azure"
	description="Procedimiento para crear un grupo en Azure Active Directory y agregar usuarios (miembros) al grupo"
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


# Creación de un nuevo grupo en la versión preliminar de Azure Active Directory

En este artículo se explica cómo crear y rellenar un nuevo grupo en la versión preliminar de Azure Active Directory (Azure AD). [¿Qué hay en la versión preliminar?](active-directory-preview-explainer.md) Use un grupo para realizar tareas de administración, como asignar licencias o permisos a un número de usuarios o dispositivos a la vez.

## ¿Cómo se crea un grupo?

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.

2. Seleccione **Más servicios**, escriba **Usuarios y grupos** en el cuadro de texto y, después, presione **Entrar**.

  ![Apertura de Administración de usuarios](./media/active-directory-groups-create-azure-portal/search-user-management.png)

3. En la hoja **Usuarios y grupos**, seleccione **Todos los grupos**.

  ![Apertura de la hoja Grupos](./media/active-directory-groups-create-azure-portal/view-groups-blade.png)

4. En la hoja **Usuarios y grupos - Todos los grupos**, seleccione el comando **Agregar**.

  ![Selección del comando Agregar](./media/active-directory-groups-create-azure-portal/add-group-command.png)

5. En la hoja **Grupo**, agregue un nombre y una descripción para el nuevo grupo.

6. Para seleccionar los miembros que se agregarán al grupo, seleccione **Asignado** en el cuadro **Membership type** (Tipo de pertenencia a grupos) y luego seleccione **Miembros**. Para obtener más información sobre cómo administrar la pertenencia de un grupo de forma dinámica, consulte el artículo sobre el [uso de atributos para crear reglas avanzadas de pertenencias a grupo](active-directory-groups-dynamic-membership-azure-portal.md).

  ![Selección de miembros para agregar](./media/active-directory-groups-create-azure-portal/select-members.png)

5. En la hoja **Miembros**, seleccione uno o varios usuarios o dispositivos que quiera agregar al grupo y, luego, haga clic en el botón **Seleccionar** situado en la parte inferior de la hoja para agregarlos. El cuadro **Usuario** filtra la visualización en función de si coincide lo que ha escrito con cualquier parte del nombre de un usuario o dispositivo. No se aceptan caracteres comodín en el cuadro.

6. Cuando termine de agregar miembros al grupo, seleccione **rear** en la hoja **Grupo**.

  ![Creación de la confirmación de grupo](./media/active-directory-groups-create-azure-portal/create-group-confirmation.png)




## Información adicional

Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [Consulta de los grupos existentes](active-directory-groups-view-azure-portal.md)
* [Administración de la configuración de un grupo](active-directory-groups-settings-azure-portal.md)
* [Administración de miembros de un grupo](active-directory-groups-members-azure-portal.md)
* [Administración de la pertenencia a un grupo](active-directory-groups-membership-azure-portal.md)
* [Administración de reglas dinámicas para los usuarios de un grupo](active-directory-groups-dynamic-membership-azure-portal.md)

<!---HONumber=AcomDC_0928_2016-->
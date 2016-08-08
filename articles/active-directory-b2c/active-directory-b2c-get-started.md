<properties
	pageTitle="Azure Active Directory B2C: creación de un inquilino de Azure Active Directory B2C | Microsoft Azure"
	description="Un tema sobre cómo crear un inquilino de Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.topic="article"
    ms.devlang="na"
	ms.date="07/24/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C: creación de un inquilino de Azure AD B2C

Para empezar a usar Microsoft Azure Active Directory (Azure AD) B2C, siga los tres pasos descritos en este artículo.

## Paso 1: Registrarse para obtener una suscripción de Azure

Si ya tiene una suscripción de Azure, omita este paso. Si no es así, regístrese para conseguir [una suscripción de Azure](../active-directory/sign-up-organization.md) y acceder a Azure AD B2C.

## Paso 2: Crear un inquilino de Azure AD B2C

Use los pasos siguientes para crear un nuevo inquilino de Azure AD B2C. Actualmente, las características B2C no pueden activarse en los inquilinos existentes.

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/) como administrador de la suscripción. Esta cuenta es la misma cuenta profesional o educativa o la misma cuenta Microsoft que usó para registrarse en Azure.
2. Haga clic en **Nuevo** > **Servicios de aplicaciones** > **Active Directory** > **Directorio** > **Creación personalizada**.

    ![Captura de pantalla del proceso de inicio de creación de un inquilino](./media/active-directory-b2c-get-started/new-directory.png)

3. Elija el **nombre**, el **nombre de dominio** y el **país o región** del inquilino.
4. Active la opción que dice **Este es un directorio B2C**.
5. Haga clic en la marca de verificación para completar la acción.

    ![Captura de pantalla de la marca de verificación para crear un directorio B2C](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. El inquilino se crea y aparecerá en la extensión de Active Directory. También se crea un administrador global del inquilino. Puede agregar otros administradores globales según sea necesario.

    > [AZURE.IMPORTANT]
	Si planea usar el inquilino B2C para una aplicación de producción, lea el artículo sobre [escala de producción frente a inquilinos de B2C de versión preliminar](active-directory-b2c-reference-tenant-type.md).

## Paso 3: Ir a la hoja de características B2C en el Portal de Azure

1. Vaya a la extensión de Active Directory en la barra de navegación del lado izquierdo.
2. Busque su inquilino en la pestaña **Directorio** y haga clic en él.
3. Haga clic en la pestaña **Configurar**.
4. Haga clic en el vínculo **Administrar la configuración B2C** de la sección **Administración B2C**.

    ![Captura de pantalla de la configuración de directorios para B2C](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

5. El Portal de Azure con la hoja de características B2C se abrirá en una nueva pestaña o ventana del explorador.

    > [AZURE.IMPORTANT]
    El inquilino será accesible desde el Portal de Azure en unos 2-3 minutos. Este problema se soluciona al volver a realizar estos pasos en un tiempo. En caso contrario, póngase en contacto con el equipo de soporte técnico.

6. Ancle esta hoja al panel de inicio para facilitar el acceso (la herramienta Anclar está marcada en rojo en la esquina superior derecha de la hoja de características).

    ![Captura de pantalla de la hoja de características B2C](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE]
    En el [Portal de Azure clásico](https://manage.windowsazure.com/) puede administrar usuarios y grupos, la configuración del autoservicio de restablecimiento de contraseña y las características de personalización de marca corporativa de su inquilino.

## Fácil acceso a la hoja de características B2C en el Portal de Azure

Para mejorar la detectabilidad, hemos agregado un acceso directo a la hoja de características de B2C en el Portal de Azure.

1. Inicie sesión en el Portal de Azure como administrador global de su inquilino de B2C. Si ya inició sesión en otro inquilino, cambie de inquilino (en la esquina superior derecha).
2. Haga clic en **Examinar** en el panel de navegación de la izquierda.
3. Haga clic en **Azure AD B2C** para tener acceso a la hoja de características de B2C.

    ![Captura de pantalla de la hoja de características de B2C](./media/active-directory-b2c-get-started/b2c-browse.png)

## Pasos siguientes

Aprenda cómo registrar una aplicación con Azure AD B2C y cómo crear una aplicación de inicio rápido; para ello, consulte [Azure Active Directory B2C: registro de la aplicación](active-directory-b2c-app-registration.md).

<!---HONumber=AcomDC_0727_2016-->
---
title: "Tutorial: Integración de Azure Active Directory con SpaceIQ | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SpaceIQ."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5b55ae29-491f-401f-9299-d3a6b64a1b99
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: jeedes
ms.openlocfilehash: 939cc4215ca8822cee376dfb13d5f6d1b8be1d35
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-spaceiq"></a>Tutorial: Integración de Azure Active Directory con SpaceIQ

En este tutorial, aprenderá a integrar SpaceIQ con Azure Active Directory (Azure AD).

La integración de SpaceIQ con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a SpaceIQ.
- Puede permitir que los usuarios inicien sesión automáticamente en SpaceIQ (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con SpaceIQ, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en SpaceIQ

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de SpaceIQ desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-spaceiq-from-the-gallery"></a>Incorporación de SpaceIQ desde la galería
Para configurar la integración de SpaceIQ en Azure AD, tendrá que agregar SpaceIQ desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar SpaceIQ desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **SpaceIQ**, seleccione **SpaceIQ** en el panel de resultados y haga clic en el botón **Agregar** para agregar la aplicación.

    ![SpaceIQ en la lista de resultados](./media/active-directory-saas-spaceiq-tutorial/tutorial_spaceiq_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con SpaceIQ con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de SpaceIQ para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de SpaceIQ.

Para establecer la relación de vínculo, en SpaceIQ asigne el valor de **nombre de usuario** de Azure AD como valor del **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con SpaceIQ, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de SpaceIQ](#create-a-spaceiq-test-user)**: para tener un homólogo de Britta Simon en SpaceIQ que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en su aplicación SpaceIQ.

**Para configurar el inicio de sesión único de Azure AD con SpaceIQ, siga estos pasos:**

1. En Azure Portal, en la página de integración de la aplicación **SpaceIQ**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-spaceiq-tutorial/tutorial_spaceiq_samlbase.png)

3. En la sección **Dominio y direcciones URL de SpaceIQ**, lleve a cabo los pasos siguientes:

    ![Información acerca del inicio de sesión único de dominio y direcciones URL de SpaceIQ](./media/active-directory-saas-spaceiq-tutorial/tutorial_spaceiq_url.png)

    a. En el cuadro de texto **Identificador**, escriba la dirección URL: `https://api.spaceiq.com`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://api.spaceiq.com/saml/<instanceid>/callback`.

    > [!NOTE] 
    > Actualice estos valores con la dirección URL de respuesta y el identificador reales, que se explican más adelante en el tutorial.
 
4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-spaceiq-tutorial/tutorial_spaceiq_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-spaceiq-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de SpaceIQ**, haga clic en **Configurar SpaceIQ** para abrir la ventana **Configurar inicio de sesión**. Copie el **identificador de entidad de SAML** de la **sección de referencia rápida**.

    ![Configuración de SpaceIQ](./media/active-directory-saas-spaceiq-tutorial/tutorial_spaceiq_configure.png) 

7.  Abra una nueva ventana del explorador e inicie sesión en el entorno de SpaceIQ como administrador.

8. Una vez que haya iniciado sesión, haga clic en la pieza de puzzle en la esquina superior derecha, y luego haga clic en **"Integraciones"**

    ![Configuración de cuenta](./media/active-directory-saas-spaceiq-tutorial/setting1.png) 

9. En **All PROVISIONING & SSO** (Todos los aprovisionamientos y SSO), haga clic en el icono de **Azure** para agregar una instancia de Azure como IDP.

    ![Icono de SAML](./media/active-directory-saas-spaceiq-tutorial/setting2.png)

10. En el cuadro de diálogo **SSO**, realice los pasos siguientes:

    ![Configuración de la autenticación SAML](./media/active-directory-saas-spaceiq-tutorial/setting3.png)

    a. En el cuadro **SAML Issuer URL** (Dirección URL del emisor de SAML), pegue el valor de **identificador de entidad de SAML** que copió de la ventana de configuración de la aplicación Azure AD.
    
    b. Copie el valor de **dirección URL de punto de conexión de devolución de llamada SAML (solo lectura)** y péguelo en el cuadro **URL de respuesta** en Azure Portal, en la sección **Dominio y direcciones URL** de SpaceIQ.
    
    c. Copie el valor de **URI de audiencia de SAML (solo lectura)** y péguelo en el cuadro **Identificador** en Azure Portal, en la sección **Dominio y direcciones URL** de SpaceIQ.

    d. Abra el certificado descargado en el Bloc de notas, copie el contenido y péguelo en el cuadro **Certificado X.509**.
    
    e. Haga clic en **Guardar**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-spaceiq-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-spaceiq-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-spaceiq-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-spaceiq-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
  
### <a name="create-a-spaceiq-test-user"></a>Creación de un usuario de prueba SpaceIQ

En esta sección, creará un usuario llamado Britta Simon en SpaceIQ. Trabaje con el [equipo de soporte técnico de SpaceIQ](mailto:eng@spaceiq.com) para agregar los usuarios en la plataforma de SpaceIQ. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a SpaceIQ.

![Asignación del rol de usuario][200] 

**Para asignar Britta Simon a SpaceIQ, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **SpaceIQ**.

    ![Vínculo a SpaceIQ en la lista de aplicaciones](./media/active-directory-saas-spaceiq-tutorial/tutorial_spaceiq_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de SpaceIQ del panel de acceso, debería iniciar sesión automáticamente en su aplicación SpaceIQ.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_203.png


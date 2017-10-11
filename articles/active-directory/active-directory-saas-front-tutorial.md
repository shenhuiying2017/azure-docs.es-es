---
title: "Tutorial: Integración de Azure Active Directory con Front | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Front."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 88270b6d-2571-434a-b139-b6ccc3a2b19f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.openlocfilehash: d936bc50a66ac2a3c17038ff08351edf9902c99f
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-front"></a>Tutorial: integración de Azure Active Directory con Front

En este tutorial, obtendrá información sobre cómo integrar Front con Azure Active Directory (Azure AD).

Integrar Front con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Front.
- Puede permitir que los usuarios inicien sesión automáticamente en Front (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Front, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Front

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Front desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-front-from-the-gallery"></a>Adición de Front desde la galería
Para configurar la integración de Front en Azure AD, deberá agregar Front desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Front desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Front**, seleccione **Front** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Front en la lista de resultados](./media/active-directory-saas-front-tutorial/tutorial_front_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Front con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD necesita saber cuál es el usuario homólogo de Front para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Front.

Para establecer la relación de vínculo, en Front, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Front, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de usuario de prueba de Front](#create-a-front-test-user)**: para tener un homólogo de Britta Simon en Front que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Front.

**Para configurar el inicio de sesión único de Azure AD con Front, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Front**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-front-tutorial/tutorial_front_samlbase.png)

3. En la sección **Dominio y direcciones URL de Front**, si quiere configurar la aplicación en modo iniciado por **IDP**:

    ![Configurar inicio de sesión único](./media/active-directory-saas-front-tutorial/tutorial_front_url1.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.frontapp.com`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.frontapp.com/sso/saml/callback`.

4. Active **Mostrar configuración avanzada de URL**, si quiere configurar la aplicación en modo iniciado por **SP**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-front-tutorial/tutorial_front_url2.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.frontapp.com`.
     
    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con el identificador, la dirección URL de respuesta y la dirección URL de inicio de sesión reales que se explican más adelante en el tutorial, o póngase en contacto con el [equipo de soporte técnico al cliente de Front](mailto:support@frontapp.com) para obtener estos valores. 

5. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-front-tutorial/tutorial_front_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-front-tutorial/tutorial_general_400.png)
    
7. En la sección **Configuración de Front**, haga clic en **Configurar Front** para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-front-tutorial/tutorial_front_configure.png) 

8. Inicie la sesión en el inquilino de Front como administrador.

9. Vaya a **Settings (cog icon at the bottom of the left sidebar) > Preferences** (Configuración (icono de engranaje en la parte inferior de la barra lateral izquierda) > Preferencias).
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-front-tutorial/tutorial_front_000.png)

10. Haga clic en el vínculo **Inicio de sesión único** .
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-front-tutorial/tutorial_front_001.png)

11. Seleccione **SAML** en la lista desplegable de **Single Sign On** (Inicio de sesión único).
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-front-tutorial/tutorial_front_002.png)

12. En el cuadro de texto **Entry Point** (Punto de entrada), coloque el valor de **Dirección URL del servicio de inicio de sesión único** del Asistente para configuración de aplicaciones de Azure AD.
    
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-front-tutorial/tutorial_front_003.png)

13. Abra el archivo descargado de **certificado (Base64)** en el Bloc de notas, copie su contenido en el Portapapeles y luego péguelo en el cuadro de texto **Certificado de firma**.
    
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-front-tutorial/tutorial_front_004.png)

14. En la sección **Service provider settings** (configuración del proveedor de servicios), siga estos pasos:

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-front-tutorial/tutorial_front_005.png)

    a. Copie el valor de **Entity ID** (Identificador de entidad) y péguelo en el cuadro de texto **Identificador**, que se encuentra en la sección **Dominio y direcciones URL de Front** de Azure Portal.

    b. Copie el valor de **ACS URL** y péguelo en el cuadro de texto **URL de inicio de sesión**, que se encuentra en la sección **Dominio y direcciones URL de Front** de Azure Portal.
    
15. Haga clic en el botón **Guardar** .

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-front-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-front-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-front-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-front-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-front-test-user"></a>Creación de un usuario de prueba de Front

En esta sección, creará un usuario llamado Britta Simon en Front. Trabaje con el [equipo de soporte técnico al cliente de Front](mailto:support@frontapp.com) para agregar los usuarios a la plataforma de Front. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Front.

![Asignación del rol de usuario][200] 

**Para asignar la usuaria Britta Simon a Front, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Front**.

    ![Vínculo a Front en la lista de aplicaciones](./media/active-directory-saas-front-tutorial/tutorial_front_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Front en el panel de acceso, debería iniciar sesión automáticamente en su aplicación de Front. 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-front-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-front-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-front-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-front-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-front-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-front-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-front-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-front-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-front-tutorial/tutorial_general_203.png


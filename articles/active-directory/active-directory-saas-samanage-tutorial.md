---
title: "Tutorial: integración de Azure Active Directory con Samanage | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Samanage."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: f0db4fb0-7eec-48c2-9c7a-beab1ab49bc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: c54dbe407145a29a712acc3c0fb549a38ac26bed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>Tutorial: integración de Azure Active Directory con Samanage

En este tutorial, obtendrá información sobre cómo integrar Samanage con Azure Active Directory (Azure AD).

La integración de Samanage con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Samanage.
- Puede habilitar que los usuarios inicien sesión automáticamente en Samanage (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Samanage, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Samanage

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Samanage desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-samanage-from-the-gallery"></a>Adición de Samanage desde la galería
Para configurar la integración de Samanage en Azure AD, deberá agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Samanage desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Samanage**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_search.png)

5. En el panel de resultados, seleccione **Samanage** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Samanage con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Samanage para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Samanage.

Para establecer la relación de vínculo, en Samanage, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Samanage, es preciso completar los siguientes bloques de creación:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba en Samanage](#creating-a-samanage-test-user)**: para tener un homólogo de Britta Simon en Samanage que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Samanage.

**Para configurar el inicio de sesión único de Azure AD con Samanage, siga estos pasos:**

1. En Azure Portal, en la página de integración de la aplicación **Samanage**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_samlbase.png)

3. En la sección **Dominio y direcciones URL de Samanage**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<Company Name>.samanage.com/saml_login/<Company Name>`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con la dirección URL de inicio de sesión y el identificador reales, que se explican más adelante en el tutorial. Para más información, póngase en contacto con [equipo de soporte al cliente de Samanage](https://www.samanage.com/support).    
 
4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-samanage-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Samanage**, haga clic en **Configurar Samanage** para abrir la ventana **Configurar inicio de sesión**. Copie los valores de **Sign-Out URL (Dirección URL de cierre de sesión) y SAML Entity ID (Identificador de entidad de SAML)** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_configure.png) 

7. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de Samanage como administrador.

8. Haga clic en **Dashboard** (Panel) y seleccione **Setup** (Configuración) en el panel de navegación izquierdo.
   
    ![Panel](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "Panel")

9. Haga clic en **Inicio de sesión único**.
   
    ![Inicio de sesión único](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_002.png "Inicio de sesión único")

10. Navegue hasta la sección **Login using SAML** (Iniciar sesión con SAML), realice los pasos siguientes:
   
    ![Inicio de sesión con SAML](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_003.png "Inicio de sesión con SAML")
 
    a. Haga clic en **Enable Single Sign-On with SAML**(Habilitar inicio de sesión único con SAML).  
 
    b. En el cuadro de texto **Identity provider URL** (Dirección URL del proveedor de identidad), pegue el valor del **identificador de entidad de SAML** que ha copiado de Azure Portal.    
 
    c. Confirme que la **dirección URL de inicio de sesión** coincide con la **dirección URL de inicio de sesión** de la sección **Dominio y direcciones URL de Samanage**  en Azure Portal.
 
    d. En el cuadro de texto **Logout URL** (Dirección URL de cierre de sesión), pegue el valor de la **dirección URL de cierre de sesión** que copió de Azure Portal.
 
    e. En el cuadro de texto **SAML Issuer** (Emisor de SAML) escriba el URI del identificador de la aplicación establecido en el proveedor de identidades.
 
    f. En el Bloc de notas, abra el certificado codificado en base 64 descargado de Azure Portal, copie su contenido en el Portapapeles y péguelo en el cuadro de texto **Paste your Identity Provider x.509 Certificate below** (Pegue a continuación el certificado x.509 del proveedor de identidades).
 
    g. Haga clic en **Create users if they do not exist in Samanage**(Crear usuarios si no existen en Samanage).
 
    h. Haga clic en **Update**(Actualizar).

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
 
### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-samanage-test-user"></a>Creación de un usuario de prueba en Samanage

Para permitir que los usuarios de Azure AD inicien sesión en Samanage, tienen que aprovisionarse en Samanage.  
En el caso de Samanage, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en su sitio de la compañía de Samanage como administrador.

2. Haga clic en **Dashboard** (Panel) y seleccione **Setup** (Configuración) en el panel de navegación izquierdo.
   
    ![Instalación](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "Instalación")

3. Haga clic en la pestaña **Usuarios** .
   
    ![Usuarios](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_006.png "Usuarios")

4. Haga clic en **Nuevo usuario**.
   
    ![Nuevo usuario](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_007.png "Nuevo usuario")

5. Escriba el **nombre** y la **dirección de correo electrónico** de la cuenta de Azure Active Directory que desea aprovisionar y haga clic en **Create user** (Crear usuario).
   
    ![Creación de usuarios](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_008.png "Creación de usuarios")
   
   >[!NOTE]
   >El titular de la cuenta de Azure Active Directory recibirá un mensaje de correo y seguirá un vínculo para confirmar su cuenta antes de que se active. Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Samanage que proporcione Samanage para aprovisionar cuentas de usuario de Azure Active Directory.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, concederá acceso a Britta Simon a Samanage para que use el inicio de sesión único de Azure.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Samanage, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Samanage**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Samanage en el panel de acceso, debería iniciar sesión automáticamente en la aplicación Samanage.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_203.png


---
title: "Tutorial: Integración de Azure Active Directory con Zscaler Private Access (ZPA) | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Zscaler Private Access (ZPA)."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 83711115-1c4f-4dd7-907b-3da24b37c89e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jeedes
ms.openlocfilehash: 9ac65791e8d50d5e5da5d36adee14cd0e88a3da8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-zpa"></a>Tutorial: Integración de Azure Active Directory con Zscaler Private Access (ZPA)

En este tutorial, aprenderá a integrar Zscaler Private Access (ZPA) con Azure Active Directory (Azure AD).

La integración de Zscaler Private Access (ZPA) con Azure AD ofrece las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Zscaler Private Access (ZPA)
- Puede permitir que los usuarios inicien sesión automáticamente en Zscaler Private Access (ZPA) (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el Portal de administración de Azure.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Zscaler Private Access (ZPA), necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único de Zscaler Private Access (ZPA)


> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Zscaler Private Access (ZPA) desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>Adición de Zscaler Private Access (ZPA) desde la galería
Para configurar la integración de Zscaler Private Access (ZPA) con Azure AD, debe agregar Zscaler Private Access (ZPA) desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Zscaler Private Access (ZPA) desde la galería, realice los siguientes pasos:**

1. En el panel de navegación izquierdo del **[Portal de administración de Azure](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Haga clic en el botón **Agregar** situado en la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Zscaler Private Access (ZPA)**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_001.png)

5. En el panel de resultados, seleccione **Zscaler Private Access (ZPA)** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con Zscaler Private Access (ZPA) con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD necesita saber cuál es el usuario homólogo en Zscaler Private Access (ZPA) para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado en Zscaler Private Access (ZPA).

Para ello, se asigna el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Zscaler Private Access (ZPA).

Para configurar y probar el inicio de sesión único de Azure AD con Zscaler Private Access (ZPA), es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Zscaler Private Access (ZPA)](#creating-a-zscaler-private-access-(zpa)-test-user)**: para tener un homólogo de Britta Simon en Zscaler Private Access (ZPA) que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en el Portal de administración de Azure y configurará el inicio de sesión único en la aplicación Zscaler Private Access (ZPA).

**Para configurar el inicio de sesión único en Azure AD con Zscaler Private Access (ZPA), realice los pasos siguientes:**

1. En el Portal de administración de Azure, en la página de integración de aplicaciones de **Zscaler Private Access (ZPA)**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo**, seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_300.png)
    
3. En la sección **Dominio y direcciones URL de Zscaler Private Access (ZPA)**, lleve a cabo los pasos siguientes:
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_01.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`.

    b. En el cuadro de texto **Identificador**, escriba: `https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE] 
    > Tenga en cuenta que estos no son valores reales. Tendrá que actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Aquí le recomendamos que utilice el valor único de URL en el identificador. Póngase en contacto con el [equipo de soporte técnico de Zscaler Private Access (ZPA)](https://help.zscaler.com/zpa-submit-ticket) para obtener estos valores.

4. En la sección **Certificado de firma de SAML**, haga clic en **Crear nuevo certificado**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_400.png)   

5. En el cuadro de diálogo **Crear nuevo certificado**, haga clic en el icono del calendario y seleccione una valor en **Fecha de expiración**. Luego haga clic en el botón **Guardar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_500.png)

6. En la sección **Certificado de firma de SAML**, seleccione **Make new certificate active** (Activar el nuevo certificado) y haga clic en el botón **Guardar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_02.png)

7. En la ventana emergente **Rollover certificate** (Certificado de sustitución), haga clic en **Aceptar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_600.png)

8. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_03.png) 

9. En otra ventana del explorador web, inicie sesión en el sitio de la compañía de Zscaler Private Access (ZPA) como administrador.

10. Vaya a **Administrator** (Administrador) y haga clic en **Idp Configuration** (Configuración de Idp).

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_04.png)

11. En la sección **Idp Configuration** (Configuración de IDP), haga clic en **Add New IDP Configuration** (Agregar nueva configuración de IDP).

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_05.png)

12. En la sección **New IDP Configuration** (Nueva configuración de IDP), realice los siguientes pasos:

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_06.png)

    a. Haga clic en **Select File** (Seleccionar archivo) y cargue su archivo de metadatos descargado.

    b. Haga clic en el botón **Guardar** .
    


### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de administración de Azure llamado Britta Simon.

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-zscalerprivateaccess-tutorial/create_aaduser_01.png) 

2. Vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios** para mostrar la lista de usuarios.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-zscalerprivateaccess-tutorial/create_aaduser_02.png) 

3. En la parte superior del diálogo, haga clic en **Agregar** para abrir el diálogo **Usuario**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-zscalerprivateaccess-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-zscalerprivateaccess-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**. 



### <a name="creating-a-zscaler-private-access-zpa-test-user"></a>Creación de un usuario de prueba de Zscaler Private Access (ZPA)

En esta sección, creará un usuario llamado Britta Simon en Zscaler Private Access (ZPA). Trabaje con el [equipo de soporte técnico de Zscaler Private Access (ZPA)](https://help.zscaler.com/zpa-submit-ticket) para agregar los usuarios a la plataforma de Zscaler Private Access (ZPA).


### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, permitirá que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Zscaler Private Access (ZPA).

![Asignar usuario][200] 

**Para asignar a Britta Simon a Zscaler Private Access (ZPA), realice los siguientes pasos:**

1. En el Portal de administración de Azure, abra la vista de aplicaciones, vaya a la vista de directorio y seleccione **Aplicaciones empresariales**. Después, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Zscaler Private Access (ZPA)**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_50.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    


### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Zscaler Private Access (ZPA) en el Panel de acceso, debe iniciar sesión automáticamente en la aplicación Zscaler Private Access (ZPA).


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_203.png
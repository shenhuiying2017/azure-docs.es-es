---
title: "Tutorial: Integración de Azure Active Directory con KnowBe4 Security Awareness Training | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y KnowBe4 Security Awareness Training."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: b80d2212-cc5f-4adb-836c-570640810c39
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 3b18737112a8aef101fab7fac1904f7c2e194d64
ms.contentlocale: es-es
ms.lasthandoff: 06/29/2017


---
# <a name="tutorial-azure-active-directory-integration-with-knowbe4-security-awareness-training"></a>Tutorial: Integración de Azure Active Directory con KnowBe4 Security Awareness Training

En este tutorial, aprenderá a integrar KnowBe4 Security Awareness Training con Azure Active Directory (Azure AD).

La integración de KnowBe4 Security Awareness Training con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a KnowBe4 Security Awareness Training
- Puede permitir que los usuarios inicien sesión automáticamente en KnowBe4 Security Awareness Training (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con KnowBe4 Security Awareness Training, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción de KnowBe4 Security Awareness Training con el inicio de sesión único habilitado

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de KnowBe4 Security Awareness Training desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-knowbe4-security-awareness-training-from-the-gallery"></a>Incorporación de KnowBe4 Security Awareness Training desde la galería
Para configurar la integración de KnowBe4 Security Awareness Training en Azure AD, es preciso agregar KnowBe4 Security Awareness Training desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar KnowBe4 Security Awareness Training desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **KnowBe4 Security Awareness Training**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-KnowBe4-tutorial/tutorial_knowbe4sat_search.png)

5. En el panel de resultados, seleccione **KnowBe4 Security Awareness Training** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-KnowBe4-tutorial/tutorial_knowbe4sat_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con KnowBe4 Security Awareness Training con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de KnowBe4 Security Awareness Training para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de KnowBe4 Security Awareness Training.

Para establecer la relación de vínculo, en KnowBe4 Security Awareness Training, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con KnowBe4 Security Awareness Training, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de KnowBe4 Security Awareness Training](#creating-a-knowbe4-security-awareness-training-test-user)**: para tener un homólogo de Britta Simon en KnowBe4 Security Awareness Training que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación KnowBe4 Security Awareness Training.

**Para configurar el inicio de sesión único de Azure AD con KnowBe4 Security Awareness Training, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **KnowBe4 Security Awareness Training**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-KnowBe4-tutorial/tutorial_knowbe4sat_samlbase.png)

3. En la sección **Dominio y direcciones URL de KnowBe4 Security Awareness Training**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-KnowBe4-tutorial/tutorial_knowbe4sat_url.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.KnowBe4.com/auth/saml/<instancename>`.

    > [!NOTE] 
    > Este valor no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de soporte técnico de KnowBe4 Security Awareness Training](mailto:support@KnowBe4.com) para obtener dicho valor. 
 

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (sin procesar)** y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-KnowBe4-tutorial/tutorial_knowbe4sat_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-KnowBe4-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de KnowBe4 Security Awareness Training**, haga clic en **Configurar KnowBe4 Security Awareness Training** para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-KnowBe4-tutorial/tutorial_knowbe4sat_configure.png) 

7. Para configurar el inicio de sesión único en **KnowBe4 Security Awareness Training**, es preciso enviar los valores descargados de **Certificado (sin procesar)**, **Sign-Out URL (Dirección URL de cierre de sesión), SAML Entity ID (Identificador de entidad de SAML) y SAML Single Sign-On Service URL (Dirección URL del servicio de inicio de sesión único de SAML)** al [equipo de soporte técnico de KnowBe4 Security Awareness Training ](mailto:support@KnowBe4.com).

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-KnowBe4-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-KnowBe4-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-KnowBe4-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-KnowBe4-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-knowbe4-security-awareness-training-test-user"></a>Creación de un usuario de prueba de KnowBe4 Security Awareness Training

El objetivo de esta sección es crear un usuario llamado Britta Simon en KnowBe4 Security Awareness Training. KnowBe4 Security Awareness Training admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para usted en esta sección. Al intentar acceder a KnowBe4 Security Awareness Training, si aún no existe, se crea un usuario. 

>[!NOTE]
>Si necesita crear un usuario manualmente, es preciso que se ponga en contacto con el [equipo de soporte técnico de KnowBe4 Security Awareness Training](mailto:support@KnowBe4.com).
> 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, concederá acceso a Britta Simon a nowBe4 Security Awareness Training para que use el inicio de sesión único de Azure.

![Asignar usuario][200] 

**Para asignar a Britta Simon a KnowBe4 Security Awareness Training, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **KnowBe4 Security Awareness Training**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-KnowBe4-tutorial/tutorial_knowbe4sat_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.
  
Al hacer clic en el icono de KnowBe4 Security Awareness Training en el panel de acceso, debería iniciar sesión automáticamente en la aplicación KnowBe4 Security Awareness Training.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-KnowBe4-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-KnowBe4-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-KnowBe4-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-KnowBe4-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-KnowBe4-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-KnowBe4-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-KnowBe4-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-KnowBe4-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-KnowBe4-tutorial/tutorial_general_203.png



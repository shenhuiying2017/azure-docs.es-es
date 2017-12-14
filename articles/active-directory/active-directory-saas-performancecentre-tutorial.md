---
title: "Tutorial: Integración de Azure Active Directory con PerformanceCentre | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y PerformanceCentre."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 65288c32-f7e6-4eb3-a6dc-523c3d748d1c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: eea4393b7f051b2cbd3366d504ccd7cb75ee80a5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>Tutorial: Integración de Azure Active Directory con PerformanceCentre

En este tutorial, aprenderá a integrar PerformanceCentre con Azure Active Directory (Azure AD).

La integración de PerformanceCentre con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a PerformanceCentre.
- Puede permitir que los usuarios inicien sesión automáticamente en PerformanceCentre (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con PerformanceCentre, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en PerformanceCentre

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de PerformanceCentre desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-performancecentre-from-the-gallery"></a>Incorporación de PerformanceCentre desde la galería
Para configurar la integración de PerformanceCentre en Azure AD, deberá agregar PerformanceCentre desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar PerformanceCentre desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **PerformanceCentre**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_search.png)

5. En el panel de resultados, seleccione **PerformanceCentre** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con PerformanceCentre con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de PerformanceCentre para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de PerformanceCentre.

Para establecer la relación de vínculo, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario** de PerformanceCentre.

Para configurar y probar el inicio de sesión único de Azure AD con PerformanceCentre, debe completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de PerformanceCentre](#creating-a-performancecentre-test-user)**: el objetivo es tener un homólogo de Britta Simon en PerformanceCentre que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación PerformanceCentre.

**Para configurar el inicio de sesión único de Azure AD con PerformanceCentre, realice los pasos siguientes:**

1. En la página de integración de la aplicación **PerformanceCentre** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_samlbase.png)

3. En la sección **Dominio y direcciones URL de PerformanceCentre**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `http://companyname.performancecentre.com/saml/SSO`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `http://companyname.performancecentre.com`

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con [equipo de atención al cliente de PerformanceCentre](https://www.performancecentre.com/contact-us/) para obtener estos valores. 

4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-performancecentre-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de PerformanceCentre**, haga clic en **Configurar PerformanceCentre** para abrir la ventana **Configurar inicio de sesión**. Copie los valores de **identificador de entidad de SAML y dirección URL del servicio de inicio de sesión único de SAML** de la **sección de referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_configure.png) 

7. Inicie sesión en su sitio de la empresa **PerformanceCentre** como administrador.

8. En la pestaña en el lado izquierdo, haga clic en **Configurar**.
   
    ![Inicio de sesión único de Azure AD ][10]

9. En la pestaña en el lado izquierdo, haga clic en **Varios** y, después, en **Inicio de sesión único**.
   
    ![Inicio de sesión único de Azure AD ][11]

10. En **Protocolo**, seleccione **SAML**.
   
    ![Inicio de sesión único de Azure AD ][12]

11. Abra el archivo de metadatos descargado en el Bloc de notas, copie el contenido, péguelo en el cuadro de texto **Metadatos del proveedor de identidades** y haga clic en **Guardar**.
   
    ![Inicio de sesión único de Azure AD ][13]

12. Compruebe que los valores de **Dirección URL de base de entidad** y **Dirección URL de Id. de entidad** son correctos.
    
     ![Inicio de sesión único de Azure AD ][14]

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-performancecentre-test-user"></a>Creación de un usuario de prueba de PerformanceCentre

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en PerformanceCentre.

**Para crear un usuario llamado Britta Simon en PerformanceCentre, realice los pasos siguientes:**

1. Inicie sesión en su sitio de la empresa PerformanceCentre como administrador.

2. En el menú de la izquierda, haga clic en **Interrelate** (Interrelacionar) y, a continuación, en **Create Participant** (Crear participante).
   
    ![Crear usuario][400]

3. En el cuadro de diálogo **Interrelate - Create Participant** (Interrelacionar- Crear participante), realice los pasos siguientes:
   
    ![Crear usuario][401]
    
    a. Escriba los atributos necesarios para Britta Simon en los cuadros de texto correspondientes.
    
    >[!IMPORTANT]
    >El atributo del nombre del usuario de Britta en PerformanceCentre debe ser el mismo que el nombre del usuario en Azure AD.
    
    b. Seleccione **Administrador de cliente** como **Elegir rol**.
    
    c. Haga clic en **Guardar**. 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a PerformanceCentre.

![Asignar usuario][200] 

**Para asignar un usuario llamado Britta Simon a PerformanceCentre, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **PerformanceCentre**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.  

Al hacer clic en el icono de PerformanceCentre en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación de PerformanceCentre.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_04.png
[10]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_10.png

[100]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_203.png
[400]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_12.png


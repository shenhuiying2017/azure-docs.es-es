---
title: "Tutorial: integración de Azure Active Directory con Lesson.ly | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Lesson.ly."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8c9dc6e6-5d85-4553-8a35-c7137064b928
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 01e4395d5b2baaaf130f7a0fa7aaa29d611559b7
ms.sourcegitcommit: 4256ebfe683b08fedd1a63937328931a5d35b157
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/23/2017
---
# <a name="tutorial-azure-active-directory-integration-with-lessonly"></a>Tutorial: Integración de Azure Active Directory con Lesson.ly

En este tutorial se aprende a integrar Lesson.ly con Azure Active Directory (Azure AD).

Integrar Lesson.ly con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Lesson.ly.
- Puede permitir que los usuarios inicien sesión automáticamente en Lesson.ly (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Lesson.ly, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Lesson.ly

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de Lesson.ly desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-lessonly-from-the-gallery"></a>Adición de Lesson.ly desde la galería
Para configurar la integración de Lesson.ly en Azure AD, es preciso agregar Lesson.ly desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Lesson.ly desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![APLICACIONES][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![APLICACIONES][3]

4. En el cuadro de búsqueda, escriba **Lesson.ly**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-lessonly-tutorial/tutorial_lesson.ly_search.png)

5. En el panel de resultados, seleccione **Lesson.ly** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-lessonly-tutorial/tutorial_lesson.ly_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección se configura y prueba el inicio de sesión único de Azure AD con Lesson.ly con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Lesson.ly para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Lesson.ly.

Para establecer la relación de vínculo, en Lesson.ly, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Lesson.ly, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Lesson.ly](#creating-a-lessonly-test-user)**: para tener un homólogo de Britta Simon en Lesson.ly vinculado a la representación de usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección se habilita el inicio de sesión único de Azure AD en Azure Portal y se configura el inicio de sesión único en la aplicación Lesson.ly.

**Para configurar el inicio de sesión único de Azure AD con Lesson.ly, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Lesson.ly**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-lessonly-tutorial/tutorial_lesson.ly_samlbase.png)

3. En la sección **Dominio y direcciones URL de Lesson.ly**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-lessonly-tutorial/tutorial_lesson.ly_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón:
    | |
    |--|
    | `https://<companyname>.lesson.ly/signin`|
    | `https://<companyname>.lessonly.com/signin`|

    >[!NOTE]
    >Cuando se hace referencia a un nombre genérico, debe reemplazar **nombreDeCompañía** por un nombre real.
    
    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón:
    | |
    |--|
    | `https://<companyname>.lesson.ly/auth/saml/metadata`|
    | `https://<companyname>.lessonly.com/auth/saml/metadata`|

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Contacte con el [equipo de soporte técnico de cliente de Lesson.ly](mailto:dev@lessonly.com) para obtener estos valores. 

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-lessonly-tutorial/tutorial_lesson.ly_certificate.png)

5. La aplicación Lesson.ly espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los **atributos del token de SAML**. La siguiente captura de pantalla le muestra un ejemplo de esto.

    ![Configurar inicio de sesión único](./media/active-directory-saas-lessonly-tutorial/tutorial_lessonly_06.png)
           
6. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo del token de SAML como muestra la imagen anterior y realice los siguientes pasos:

    | Nombre del atributo   | Valor de atributo |
    | ---------------  | ----------------|
    | urn:oid:2.5.4.42 |user.givenname |
    | urn:oid:2.5.4.4  |user.surname |
    | urn:oid:0.9.2342.19200300.100.1.3 |user.mail |
    | urn:oid:1.3.6.1.4.1.5923.1.1.1.10 |user.objectid |

    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-lessonly-tutorial/tutorial_attribute_04.png)

    ![Configurar inicio de sesión único](./media/active-directory-saas-lessonly-tutorial/tutorial_attribute_05.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.
    
    d. Haga clic en **Aceptar**.     

7. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-lessonly-tutorial/tutorial_general_400.png)

8. En la sección **Configuración de Lesson.ly**, haga clic en **Configurar Lesson.ly** para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-lessonly-tutorial/tutorial_lesson.ly_configure.png)

9. Para configurar el inicio de sesión único en **Lesson.ly**, es preciso enviar el **Certificado (Base64)** descargado, la **dirección URL de cierre de sesión, el identificador de identidad de SAML y la dirección URL del servicio de inicio de sesión único de SAML** al [equipo de soporte técnico de Lesson.ly](mailto:dev@lessonly.com).

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-lessonly-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="creating-a-lessonly-test-user"></a>Creación de un usuario de prueba de Lesson.ly

El objetivo de esta sección es crear un usuario llamado a Britta Simon en Lesson.ly. Lesson.ly admite el aprovisionamiento Just-In-Time, que está habilitado de manera predeterminada.

No hay ningún elemento de acción para usted en esta sección. Durante un intento de acceder a Lesson.ly se creará un nuevo usuario, si aún no existe.

> [!NOTE]
> Si necesita crear un usuario manualmente, es preciso que contacte con el [equipo de soporte técnico de Lesson.ly](mailto:dev@lessonly.com).

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección se habilita a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Lesson.ly.

![Asignar usuario][200] 

**Para asignar Britta Simon a Lesson.ly, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Lesson.ly**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-lessonly-tutorial/tutorial_lesson.ly_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Lesson.ly en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Lesson.ly.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-lessonly-tutorial/tutorial_general_203.png


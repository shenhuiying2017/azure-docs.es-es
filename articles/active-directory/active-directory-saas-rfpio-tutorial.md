---
title: "Tutorial: Integración de Azure Active Directory con RFPIO | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y RFPIO."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 87187076-7b50-4247-814f-f217b052703f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 57bb448f1bb858d15a7ecaad5a9ba797f5d7d85e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>Tutorial: Integración de Azure Active Directory con RFPIO

En este tutorial, aprenderá a integrar RFPIO con Azure Active Directory (Azure AD).

La integración de RFPIO con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a RFPIO.
- Puede permitir que los usuarios inicien sesión automáticamente en RFPIO (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con RFPIO, necesita los siguientes elementos:

- Una suscripción de Azure AD.
- Una suscripción habilitada para el inicio de sesión único en RFPIO

> [!NOTE]
> No se recomienda usar un entorno de producción para probar los pasos de este tutorial.

Para probar los pasos de este tutorial, siga estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de RFPIO desde la galería
2. Configuración y prueba del inicio de sesión único de Azure AD

## <a name="add-rfpio-from-the-gallery"></a>Adición de RFPIO desde la galería
Para configurar la integración de RFPIO en Azure AD, es preciso agregar dicha solución desde la galería a la lista de aplicaciones SaaS administradas.

### <a name="to-add-rfpio-from-the-gallery"></a>Para agregar RFPIO desde la galería

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, seleccione el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, seleccione el botón **Nueva aplicación** en la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **RFPIO**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_search.png)

5. En el panel de resultados, seleccione **RFPIO** y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con RFPIO con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es la relación entre el usuario homólogo de RFPIO y un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario correspondiente de RFPIO.

Para establecer la relación de vínculo, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario** de RFPIO.

Para configurar y probar el inicio de sesión único de Azure AD con RFPIO, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)**: el objetivo es permitir que los usuarios usen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: el objetivo es probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de RFPIO](#creating-a-rfpio-test-user)**: el objetivo es tener un homólogo de Britta Simon en RFPIO que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: el objetivo es permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: el objetivo es comprobar que la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación RFPIO.

**Para configurar el inicio de sesión único de Azure AD con RFPIO, realice los pasos siguientes:**

1. En la página de integración de la aplicación **RFPIO** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_samlbase.png)

3. Vaya a la sección **Dominio y direcciones URL de RFPIO**, si quiere configurar la aplicación en modo iniciado por **IDP**:

    ![Configurar inicio de sesión único](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_url.png)

    a. En el cuadro de texto **Identificador**, escriba la dirección URL: `https://www.rfpio.com`

    ![Configurar inicio de sesión único](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_url1.png)

    b. Active **Mostrar configuración avanzada de URL**.

    c. En el cuadro de texto **Estado de la retransmisión**, escriba un valor de cadena. Póngase en contacto con el [equipo de soporte técnico de RFPIO](https://www.rfpio.com/contact/) para obtener este valor. 

4. Active **Mostrar configuración avanzada de URL**. Si quiere volver a configurar la aplicación en modo iniciado por **SP**: 

    ![Configurar inicio de sesión único](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_url2.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL similar a la siguiente: `https://www.app.rfpio.com`

5. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-rfpio-tutorial/tutorial_general_400.png)

7. En otra ventana del explorador web, inicie sesión en el sitio web de **RFPIO** como administrador.

8. Haga clic en la lista desplegable de la esquina inferior izquierda.

    ![Configurar inicio de sesión único](./media/active-directory-saas-rfpio-tutorial/app1.png)

9. Haga clic en **Organization Settings** (Configuración de la organización). 

    ![Configurar inicio de sesión único](./media/active-directory-saas-rfpio-tutorial/app2.png)

10. Haga clic en **FEATURES & INTEGRATION** (CARACTERÍSTICAS E INTEGRACIÓN).

    ![Configurar inicio de sesión único](./media/active-directory-saas-rfpio-tutorial/app4.png)

11. En **SAML SSO Configuration** (Configuración del inicio de sesión único de SAML), haga clic en **Edit** (Editar).

    ![Configurar inicio de sesión único](./media/active-directory-saas-rfpio-tutorial/app3.png)

12. En esta sección, realice las siguientes acciones:

    ![Configurar inicio de sesión único](./media/active-directory-saas-rfpio-tutorial/app5.png)
    
    a. Copie el contenido del archivo **XML de metadatos descargado** y péguelo en el campo **identity configuration** (Configuración de identidad).

    > [!NOTE]
    >Para copiar el contenido del archivo **XML de metadatos** descargado, use **Notepad++** o un **Editor XML** adecuado. 

    b. Haga clic en **Validar**.

    c. Después de hacer clic en **Validate** (Validar), mueva **SAML(Enabled)** (SAML [Habilitado]) a activado.

    d. Haga clic en **Enviar**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-rfpio-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-rfpio-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-rfpio-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-rfpio-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-rfpio-test-user"></a>Creación de un usuario de prueba de RFPIO

Para permitir que los usuarios de Azure AD inicien sesión en RFPIO, deben aprovisionarse en dicha solución.  
En el caso de RFPIO, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Regístrese en el sitio de la compañía de RFPIO como administrador.

2. Haga clic en la lista desplegable de la esquina inferior izquierda.

    ![Configurar inicio de sesión único](./media/active-directory-saas-rfpio-tutorial/app1.png)

3. Haga clic en **Organization Settings** (Configuración de la organización). 

    ![Configurar inicio de sesión único](./media/active-directory-saas-rfpio-tutorial/app2.png)

4. Haga clic en **TEAM MEMBERS** (MIEMBROS DE EQUIPO).

    ![Configurar inicio de sesión único](./media/active-directory-saas-rfpio-tutorial/app6.png)

5. Haga clic en **ADD MEMBERS** (AGREGAR MIEMBROS).

    ![Configurar inicio de sesión único](./media/active-directory-saas-rfpio-tutorial/app7.png)

6. Vaya a la sección **Add New Members** (Agregar nuevos miembros). Realice las siguientes acciones:

    ![Configurar inicio de sesión único](./media/active-directory-saas-rfpio-tutorial/app8.png)

    a. Escriba la **dirección de correo electrónico** en el campo **Enter one email per line** (Escribir un correo electrónico por línea).

    b. Seleccione **Role** (Role) de acuerdo con sus requisitos.

    c. Haga clic en **ADD MEMBERS** (AGREGAR MIEMBROS).
        
    > [!NOTE]
    > El titular de la cuenta de Azure Active Directory recibirá un mensaje de correo y seguirá un vínculo para confirmar su cuenta antes de que se active.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a RFPIO.

![Asignar usuario][200] 

**Para asignar a Britta Simon a RFPIO, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **RFPIO**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de RFPIO en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación RFPIO.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo la integración de aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_203.png


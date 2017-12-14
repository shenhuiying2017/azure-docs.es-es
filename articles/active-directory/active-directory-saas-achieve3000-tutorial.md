---
title: "Tutorial: Integración de Azure Active Directory con Achieve3000 | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Achieve3000."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 83a83d07-ff9c-46c4-b5ba-25fe2b2cd003
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: jeedes
ms.openlocfilehash: 03a6eb581dae88e9789b73c04d5424c0ed14cf62
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-achieve3000"></a>Tutorial: Integración de Azure Active Directory con Achieve3000

En este tutorial, aprenderá a integrar Achieve3000 con Azure Active Directory (Azure AD).

La integración de Achieve3000 con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Achieve3000.
- Puede permitir que los usuarios inicien sesión automáticamente en Achieve3000 (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Achieve3000, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción que permita el inicio de sesión único en Achieve3000

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Agregar Achieve3000 desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-achieve3000-from-the-gallery"></a>Agregar Achieve3000 desde la galería
Para configurar la integración de Achieve3000 en Azure AD, será preciso que agregue Achieve3000 desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Achieve3000 desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Achieve3000**, seleccione **Achieve3000** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Achieve3000 en la lista de resultados](./media/active-directory-saas-achieve3000-tutorial/tutorial_achieve3000_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Achieve3000 con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Achieve3000 para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Achieve3000.

Para establecer la relación de vínculo, en Achieve3000, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Achieve3000, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Achieve3000](#create-an-achieve3000-test-user)**: para tener un homólogo de Britta Simon en Achieve3000 que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Achieve3000.

**Para configurar el inicio de sesión único de Azure AD con Achieve3000, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Achieve3000**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-achieve3000-tutorial/tutorial_achieve3000_samlbase.png)

3. En la sección **Achieve3000 Domain and URLs** (Dominios y direcciones URL de Achieve3000), lleve a cabo los pasos siguientes:

    ![Información acerca del inicio de sesión único de dominio y direcciones URL de Achieve3000](./media/active-directory-saas-achieve3000-tutorial/tutorial_achieve3000_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://saml.achieve3000.com/district/<District Identifier>`.

    b. En el cuadro de texto **Identificador**, escriba el valor `achieve3000-saml`.

    > [!NOTE] 
    > El valor de la dirección URL de inicio de sesión no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de soporte técnico de Achieve3000](https://www.achieve3000.com/contact-us/) para obtener este valor. 

4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-achieve3000-tutorial/tutorial_achieve3000_certificate.png) 

5. La aplicación Achieve3000 espera el valor de **studentID** único de la notificación en el identificador de nombre. El cliente puede asignar el valor correcto de la notificación del identificador de nombre. En este caso, asignamos **user.mail** para la demostración. Sin embargo, debe asignar el valor correcto según el identificador único.   

    ![Configurar el atributo de inicio de sesión único](./media/active-directory-saas-achieve3000-tutorial/tutorial_achieve3000_attribute.png)

6. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo token de SAML como muestra la imagen y siga estos pasos:
    
    | Nombre del atributo | Valor de atributo |
    | ------------------- | -------------------- |    
    | studentID               | user.mail |

    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar la adición del inicio de sesión único](./media/active-directory-saas-achieve3000-tutorial/tutorial_officespace_04.png)

    ![Configurar la adición del atributo del inicio de sesión único](./media/active-directory-saas-achieve3000-tutorial/tutorial_attribute_05.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.
    
    d. Haga clic en **Aceptar**.

7. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-achieve3000-tutorial/tutorial_general_400.png)
    
8. Para configurar el inicio de sesión único en el lado de **Achieve3000**, debe enviar el archivo **XML de metadatos** descargado al [equipo de soporte técnico de Achieve3000](https://www.achieve3000.com/contact-us/). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-achieve3000-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-achieve3000-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-achieve3000-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-achieve3000-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-an-achieve3000-test-user"></a>Crear un usuario de prueba de Achieve3000

En esta sección, creará un usuario llamado Britta Simon en Achieve3000. Colabore con el [equipo de soporte técnico de Achieve3000](https://www.achieve3000.com/contact-us/) para agregar a los usuarios a la plataforma Achieve3000. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único. 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Achieve3000.

![Asignación del rol de usuario][200] 

**Para asignar a Britta Simon a Achieve3000, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Achieve3000**.

    ![Vínculo a Achieve3000 en la lista de aplicaciones](./media/active-directory-saas-achieve3000-tutorial/tutorial_achieve3000_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Achieve3000 en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Achieve3000.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-achieve3000-tutorial/tutorial_general_203.png


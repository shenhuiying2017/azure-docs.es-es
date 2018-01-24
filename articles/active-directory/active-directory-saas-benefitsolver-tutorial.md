---
title: "Tutorial: Integración de Azure Active Directory con Benefitsolver | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Benefitsolver."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 333394c1-b5a7-489c-8f7b-d1a5b4e782ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2017
ms.author: jeedes
ms.openlocfilehash: d23f1c4d6613d525695ed222e0d84736b9007833
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="tutorial-azure-active-directory-integration-with-benefitsolver"></a>Tutorial: Integración de Azure Active Directory con Benefitsolver

En este tutorial, aprenderá a integrar Benefitsolver con Azure Active Directory (Azure AD).

La integración de Benefitsolver con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Benefitsolver.
- Puede permitir que los usuarios inicien sesión automáticamente en Benefitsolver (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Benefitsolver, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en Benefitsolver

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Benefitsolver desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-benefitsolver-from-the-gallery"></a>Incorporación de Benefitsolver desde la galería
Para configurar la integración de Benefitsolver en Azure AD, deberá agregar Benefitsolver desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Benefitsolver desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Benefitsolver**, seleccione **Benefitsolver** en el panel de resultados y haga clic en el botón **Agregar** para agregar la aplicación.

    ![Benefitsolver en la lista de resultados](./media/active-directory-saas-benefitsolver-tutorial/tutorial_benefitsolver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Benefitsolver utilizando un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD tiene que saber cuál es el usuario homólogo de Benefitsolver para un usuario de Azure AD. Es decir, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Benefitsolver.

Para establecer la relación de vínculo, en Benefitsolver, asigne el valor de **nombre de usuario** de Azure AD como valor del **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Benefitsolver, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba en Benefitsolver](#create-a-benefitsolver-test-user)**: para tener un homólogo de Britta Simon en Benefitsolver que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Benefitsolver.

**Para configurar el inicio de sesión único de Azure AD con Benefitsolver, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Benefitsolver**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-benefitsolver-tutorial/tutorial_benefitsolver_samlbase.png)

3. En la sección **Dominio y direcciones URL de Benefitsolver**, lleve a cabo los pasos siguientes:

    ![Información acerca del inicio de sesión único de dominio y direcciones URL de Benefitsolver](./media/active-directory-saas-benefitsolver-tutorial/tutorial_benefitsolver_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `http://<companyname>.benefitsolver.com`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.benefitsolver.com/saml20`

    c. En el cuadro de texto **URL de respuesta**, escriba la siguiente dirección URL: `https://www.benefitsolver.com/benefits/BenefitSolverView?page_name=single_signon_saml`

    > [!NOTE] 
    > Estos valores no son reales. Actualícelos con la dirección URL de inicio de sesión, el identificador y la dirección URL de respuesta reales. Póngase en contacto con el [equipo de soporte técnico de Benefitsolver](https://www.businessolver.com/contact) para obtener estos valores.

4. La aplicación Benefitsolver espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los **atributos del token de SAML** .

    ![Sección del atributo Benefitsolver](./media/active-directory-saas-benefitsolver-tutorial/tutorial_attribute.png)

5. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo token de SAML como muestra la imagen y siga estos pasos:
    
    | Nombre del atributo| Valor de atributo|
    |---------------|----------------|
    | ClientID | Para obtener este valor tiene que acudir al [equipo de soporte técnico de Benefitsolver](https://www.businessolver.com/contact).|
    | ClientKey | Para obtener este valor tiene que acudir al [equipo de soporte técnico de Benefitsolver](https://www.businessolver.com/contact).|
    | LogoutURL | Para obtener este valor tiene que acudir al [equipo de soporte técnico de Benefitsolver](https://www.businessolver.com/contact).|
    | EmployeeID | Para obtener este valor tiene que acudir al [equipo de soporte técnico de Benefitsolver](https://www.businessolver.com/contact).|

    a. Haga clic en Agregar atributo para abrir el cuadro de diálogo Agregar atributo.

    ![Sección del atributo Benefitsolver](./media/active-directory-saas-benefitsolver-tutorial/tutorial_attribute_04.png)
    
    ![Sección del atributo Benefitsolver](./media/active-directory-saas-benefitsolver-tutorial/tutorial_attribute_05.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.
    
    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.
    
    d. Haga clic en **Aceptar**.

6. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-benefitsolver-tutorial/tutorial_benefitsolver_certificate.png) 

7. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-benefitsolver-tutorial/tutorial_general_400.png)

8. Para configurar el inicio de sesión único en el lado de **Benefitsolver**, es preciso enviar el archivo **XML de metadatos** descargado al [equipo de soporte técnico de Benefitsolver](https://www.businessolver.com/contact).

    > [!NOTE]
    > El equipo de soporte técnico de Benefitsolver es el que tiene que realizar la configuración real de SSO. Cuando SSO se haya habilitado en su suscripción recibirá una notificación.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-benefitsolver-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-benefitsolver-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-benefitsolver-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-benefitsolver-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-benefitsolver-test-user"></a>Creación de un usuario de prueba de Benefitsolver

Para permitir que los usuarios de Azure AD inicien sesión en Benefitsolver, deben aprovisionarse a Benefitsolver. En el caso de Benefitsolver, los datos de empleados en la aplicación se rellenan a través de un archivo Census de su sistema HRIS (normalmente por la noche).

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Benefitsolver para aprovisionar cuentas de usuario de AAD.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Benefitsolver.

![Asignación de rol de usuario][200] 

**Para asignar Britta Simon a Benefitsolver, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Benefitsolver**.

    ![Vínculo a Benefitsolver en la lista de aplicaciones](./media/active-directory-saas-benefitsolver-tutorial/tutorial_benefitsolver_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Benefitsolver en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Benefitsolver.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-benefitsolver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-benefitsolver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-benefitsolver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-benefitsolver-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-benefitsolver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-benefitsolver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-benefitsolver-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-benefitsolver-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-benefitsolver-tutorial/tutorial_general_203.png


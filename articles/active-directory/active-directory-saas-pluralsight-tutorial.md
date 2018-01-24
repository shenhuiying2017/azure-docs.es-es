---
title: "Tutorial: integración de Azure Active Directory con Pluralsight | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Pluralsight."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4c3f07d2-4e1f-4ea3-9025-c663f1f2b7b4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: jeedes
ms.openlocfilehash: 62d148d78d9f98b6a3ddf1259177936b3976aeab
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/22/2017
---
# <a name="tutorial-azure-active-directory-integration-with-pluralsight"></a>Tutorial: Integración de Azure Active Directory con Pluralsight

En este tutorial, aprenderá a integrar Pluralsight con Azure Active Directory (Azure AD).

Integrar Pluralsight con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Pluralsight.
- Puede permitir que los usuarios inicien sesión automáticamente en Pluralsight (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Pluralsight, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Pluralsight

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Pluralsight desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-pluralsight-from-the-gallery"></a>Incorporación de Pluralsight desde la galería
Para configurar la integración de Pluralsight en Azure AD, deberá agregar Pluralsight desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Pluralsight desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Pluralsight**, seleccione **Pluralsight** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Pluralsight en la lista de resultados](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Pluralsight con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Pluralsight para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Pluralsight.

Para establecer la relación de vínculo, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario** de Pluralsight.

Para configurar y probar el inicio de sesión único de Azure AD con Pluralsight, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Pluralsight](#create-a-pluralsight-test-user)**: el objetivo es tener un homólogo de Britta Simon en Pluralsight que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación PurelyHR.

**Para configurar el inicio de sesión único de Azure AD con Pluralsight, siga estos pasos:**

1. En la página de integración de la aplicación **Pluralsight** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_samlbase.png)

3. En la sección **Dominio y direcciones URL de Pluralsight**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de Pluralsight](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<instancename>.pluralsight.com/sso/<companyname>`.

    b. En el cuadro de texto **Identificador**, escriba la dirección URL: `www.pluralsight.com`

    c. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<instancename>.pluralsight.com/sp/ACS.saml2`.
     
    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de atención al cliente de Pluralsight](mailto:support@pluralsight.com) para obtener estos valores. 

4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_certificate.png) 

5. El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en Azure Portal y configurarlo en la aplicación Pluralsight.

    La aplicación Pluralsight espera las aserciones de SAML en un formato específico, lo cual requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de pantalla le muestra un ejemplo de esto.

    ![Configurar inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_attribute.png)

    >[!NOTE]
    >También puede agregar el atributo **"Id. exclusivo"** con el valor correspondiente como EmployeeID o cualquier otro aspecto que se adapte a su organización. Tenga en cuenta que no es un atributo obligatorio, pero puede agregarlo para identificar al usuario único. 

6. Para agregar los **atributos de token de SAML**requeridos, para cada fila mostrada en la tabla siguiente, realice los pasos que se indican a continuación:
   
   | Nombre del atributo | Valor de atributo |
   | ---| --- |
   | Nombre |user.givenname |
   | Apellido |user.surname |
   | Email |user.mail |
   
   a. Haga clic en **add user attribute** to open the **Agregar atributo de usuario** .
    
     ![Configurar inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_addattribute.png)
  
   b. En el cuadro de texto **Nombre de atributo** , escriba el nombre de atributo que se muestra para la fila.
  
   c. En la lista **Valor de atributo** , seleccione el valor de atributo que se muestra para esa fila.
  
   d. Haga clic en **Aceptar**.    

7. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_400.png)

8. Para configurar el inicio de sesión único para la aplicación, póngase en contacto con el equipo de [servicios profesionales de Pluralsight](mailTo:professionalservices@pluralsight.com) y proporcione el archivo de metadatos descargado.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-pluralsight-test-user"></a>Creación de un usuario de prueba de Pluralsight

El objetivo de esta sección es crear un usuario llamado Britta Simon en Pluralsight. Colabore con el [equipo de soporte técnico de Pluralsight](mailto:support@pluralsight.com) para agregar los usuarios en la cuenta de Pluralsight.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Pluralsight.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a Pluralsight, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Pluralsight**.

    ![Vínculo a Pluralsight en la lista de aplicaciones](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Pluralsight en el panel de acceso, debería iniciar sesión automáticamente en su aplicación de Pluralsight.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_203.png


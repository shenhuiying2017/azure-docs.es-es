---
title: "Tutorial: Integración de Azure Active Directory con RedBrick Health | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y RedBrick Health."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26290c65-9aa3-42ab-8ba5-901b14dc8e73
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: jeedes
ms.openlocfilehash: 598592d87cf6471a431dab89d19c5e8beb48e661
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="tutorial-azure-active-directory-integration-with-redbrick-health"></a>Tutorial: Integración de Azure Active Directory con RedBrick Health

En este tutorial, aprenderá a integrar RedBrick Health con Azure Active Directory (Azure AD).

La integración de RedBrick Health con Azure AD ofrece las ventajas siguientes:

- En Azure AD, puede controlar quién tiene acceso a RedBrick Health.
- Puede habilitar que los usuarios inicien sesión automáticamente en RedBrick Health (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con RedBrick Health, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción a RedBrick Health con el inicio de sesión único habilitado

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de RedBrick Health desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-redbrick-health-from-the-gallery"></a>Adición de RedBrick Health desde la galería
Para configurar la integración de RedBrick Health en Azure AD, será preciso que agregue RedBrick Health desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar RedBrick Health desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **RedBrick Health**, seleccione **RedBrick Health** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![RedBrick Health en la lista de resultados](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_redbrickhealth_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con RedBrick Health con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de RedBrick Health para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de RedBrick Health.

Para establecer la relación de vínculo, en RedBrick Health, asigne el valor del **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con RedBrick Health, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de RedBrick Health](#create-a-redbrick-health-test-user)**: para tener un homólogo de Britta Simon en RedBrick Health que esté vinculado a la representación del usuario de Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación RedBrick Health.

**Para configurar el inicio de sesión único de Azure AD con RedBrick Health, siga este procedimiento:**

1. En la página de integración de la aplicación **RedBrick Health** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_redbrickhealth_samlbase.png)

3. En la sección **RedBrick Health and URLs** (Dominio y direcciones URL de RedBrick Health), lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de RedBrick Health](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_redbrickhealth_url.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL como: `http://www.redbrickhealth.com`
    
    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL:`https://sso-intg.redbrickhealth.com/sp/ACS.saml2`
    
    Para un entorno de producción: `https://sso.redbrickhealth.com/sp/ACS.saml2`

    c. Haga clic en **Mostrar configuración avanzada de URL**.
    
    ![Información de dominio y direcciones URL de inicio de sesión único de RedBrick Health](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_redbrickhealth_url1.png)

    d. En el cuadro de texto **Estado de la retransmisión**, escriba una dirección URL que siga este patrón: `https://api-sso2.redbricktest.com/identity/sso/nbound?target=https://vanity9-sso2.redbrickdev.com/portal&connection=<companyname>conn1`
    
    > [!NOTE] 
    > El valor de Estado de la retransmisión no es real. Actualice este valor con el estado de retransmisión real. Póngase en contacto con el [equipo de soporte técnico de RedBrick Health](https://home.redbrickhealth.com/contact/) para obtener este valor.

4. La aplicación RedBrick Health espera las aserciones de SAML en un formato específico, lo cual requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. Estas notificaciones son específicas del cliente y dependen de sus requisitos. Las siguientes notificaciones opcionales son un ejemplo que puede configurar para la aplicación. Puede administrar los valores de estos atributos en la sección **Atributos de usuario** de la página de integración de aplicaciones.

    ![Configurar inicio de sesión único](./media/active-directory-saas-redbrickhealth-tutorial/attribute.png)

5. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo Token SAML como muestra la imagen anterior y realice los siguientes pasos:

    | Nombre del atributo | Valor de atributo |
    | ---------------| ----------------|
    | nombre de entidad de seguridad | ********** |
    | ID. DE CLIENTE | ********** |
    | id. del participante | ********** |
    
    > [!NOTE]
    > Estos valores son solo para referencia. Debe definir los atributos según los requisitos de su organización. Póngase en contacto con el [equipo de soporte técnico de RedBrick Health](https://home.redbrickhealth.com/contact/) para obtener más información sobre las notificaciones necesarias.
    
    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_attribute_04.png)
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_attribute_05.png)
    
    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.
    
    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.

    d. Deje **Espacio de nombres** en blanco.
    
    e. Haga clic en **Aceptar**.

6. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_redbrickhealth_certificate.png) 

7. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_400.png)

8. En la sección **RedBrick Health Configuration** (Configuración de RedBrick Health), haga clic en **Configure RedBrick Health** (Configurar RedBrick Health) para abrir la ventana **Configurar inicio de sesión**. Copie el valor de **Identificador de entidad de SAML** de la **sección Referencia rápida**

    ![Configuración de RedBrick Health](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_redbrickhealth_configure.png) 

9. Para configurar el inicio de sesión único en **RedBrick Health**, debe enviar el **certificado (Base64)** descargado y el **identificador de entidad de SAML** al [equipo de soporte técnico de RedBrick Health](https://home.redbrickhealth.com/contact/). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-redbrickhealth-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-redbrickhealth-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-redbrickhealth-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-redbrickhealth-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
  
### <a name="create-a-redbrick-health-test-user"></a>Crear un usuario de prueba de RedBrick Health

En esta sección, creará el usuario Britta Simon en RedBrick Health. Trabaje con el [equipo de soporte técnico de RedBrick Health](https://home.redbrickhealth.com/contact/) para agregar los usuarios en la plataforma de RedBrick Health. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único. 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a RedBrick Health.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a RedBrick Health, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **RedBrick Health**.

    ![Vínculo a RedBrick Health en la lista de aplicaciones](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_redbrickhealth_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de RedBrick Health en el Panel de acceso, se debería iniciar sesión automáticamente en la aplicación RedBrick Health.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_203.png


---
title: "Tutorial: Integración de Azure Active Directory con Mercer BenefitsCentral (MBC) | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Mercer BenefitsCentral (MBC)."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3788b28c-49aa-4208-9acd-630362008e89
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2017
ms.author: jeedes
ms.openlocfilehash: dfc963be9d087c6ad1c56dcdaf922ff5dbf5b284
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-mercer-benefitscentral-mbc"></a>Tutorial: Integración de Azure Active Directory con Mercer BenefitsCentral (MBC)

En este tutorial, aprenderá a integrar Mercer BenefitsCentral (MBC) con Azure Active Directory (Azure AD).

La integración de Mercer BenefitsCentral (MBC) con Azure AD proporciona las siguientes ventajas:

- En Azure AD puede controlar quién tiene acceso a Mercer BenefitsCentral (MBC).
- Puede permitir que los usuarios inicien sesión automáticamente en Mercer BenefitsCentral (MBC) mediante el proceso de inicio de sesión único y con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Mercer BenefitsCentral (MBC), necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción a Mercer BenefitsCentral (MBC) que tenga la opción de inicio de sesión único

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Agregar Mercer BenefitsCentral (MBC) desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-mercer-benefitscentral-mbc-from-the-gallery"></a>Agregar Mercer BenefitsCentral (MBC) desde la galería
Para configurar la integración de Mercer BenefitsCentral (MBC) en Azure AD, será preciso que agregue Mercer BenefitsCentral (MBC) desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Mercer BenefitsCentral (MBC) desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Mercer BenefitsCentral (MBC)**, seleccione **Mercer BenefitsCentral (MBC)** del panel de resultados y, a continuación, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Mercer BenefitsCentral (MBC) en la lista de resultados](./media/active-directory-saas-mercerhrs-tutorial/tutorial_mercerhrs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Mercer BenefitsCentral (MBC) usando un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Mercer BenefitsCentral (MBC) para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Mercer BenefitsCentral (MBC).

Para establecer la relación de vínculo, en Mercer BenefitsCentral (MBC) asigne el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Mercer BenefitsCentral (MBC), es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Crear un usuario de prueba de Mercer BenefitsCentral (MBC)](#create-a-mercer-benefitscentral-mbc-test-user)**: para tener un homólogo de Britta Simon en Mercer BenefitsCentral (MBC) que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Mercer BenefitsCentral (MBC).

**Para configurar el inicio de sesión único de Azure AD con Mercer BenefitsCentral (MBC), realice los pasos siguientes:**

1. En la página de integración de la aplicación **Mercer BenefitsCentral (MBC)** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-mercerhrs-tutorial/tutorial_mercerhrs_samlbase.png)

3. En la sección **Dominio y direcciones URL de Mercer BenefitsCentral (MBC)**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de Mercer BenefitsCentral (MBC)](./media/active-directory-saas-mercerhrs-tutorial/tutorial_mercerhrs_url.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `stg.mercerhrs.com/saml2.0`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://ssous-stg.mercerhrs.com/SP2/Saml2AssertionConsumer.aspx`.

    > [!NOTE] 
    > El valor de la dirección URL de respuesta no es real. Actualice este valor con la dirección URL de respuesta real. Póngase en contacto con el [equipo de soporte técnico de Mercer BenefitsCentral (MBC)](https://www.mercer.com/contact-us.html) para obtener este valor.

4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-mercerhrs-tutorial/tutorial_mercerhrs_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Mercer BenefitsCentral (MBC)**, haga clic en **Configurar Mercer BenefitsCentral (MBC)** para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de servicio de inicio de sesión único de SAML** de la sección **Referencia rápida**.

    ![Configuración de Mercer BenefitsCentral (MBC)](./media/active-directory-saas-mercerhrs-tutorial/tutorial_mercerhrs_configure.png) 

7. Para configurar el inicio de sesión único en **Mercer BenefitsCentral (MBC)**, es preciso enviar los datos descargados de **XML de metadatos** y **Dirección URL del servicio de inicio de sesión único de SAML** al [equipo de soporte técnico de Mercer BenefitsCentral (MBC)](https://www.mercer.com/contact-us.html). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-mercerhrs-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-mercerhrs-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-mercerhrs-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-mercerhrs-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
  
### <a name="create-a-mercer-benefitscentral-mbc-test-user"></a>Crear un usuario de prueba de Mercer BenefitsCentral (MBC)

En esta sección, se crea un usuario denominado Britta Simon en Mercer HRS. Póngase en contacto con el [equipo de soporte técnico de Mercer BenefitsCentral (MBC)](https://www.mercer.com/contact-us.html) para agregar usuarios en la plataforma de Mercer HRS. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, concederá acceso a Britta Simon a Mercer BenefitsCentral (MBC) para que use el inicio de sesión único de Azure.

![Asignación del rol de usuario][200] 

**Para asignar a Britta Simon a Mercer BenefitsCentral (MBC), realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Mercer BenefitsCentral (MBC)**.

    ![Vínculo de Mercer BenefitsCentral (MBC) en la lista de aplicaciones](./media/active-directory-saas-mercerhrs-tutorial/tutorial_mercerhrs_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Mercer BenefitsCentral (MBC) que se encuentra en el panel de acceso, iniciará sesión automáticamente en la aplicación Mercer BenefitsCentral (MBC).
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mercerhrs-tutorial/tutorial_general_203.png


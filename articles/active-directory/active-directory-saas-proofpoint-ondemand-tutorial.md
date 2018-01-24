---
title: "Tutorial: Integración de Azure Active Directory con Proofpoint on Demand | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Proofpoint on Demand."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 773e7f7d-ec31-411b-860d-6a6633335d43
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: jeedes
ms.openlocfilehash: 55479406487bf445c5f449b13663c0bfaee751fd
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="tutorial-azure-active-directory-integration-with-proofpoint-on-demand"></a>Tutorial: Integración de Azure Active Directory con Proofpoint on Demand

En este tutorial, aprenderá a integrar Proofpoint on Demand con Azure Active Directory (Azure AD).

La integración de Proofpoint on Demand con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Proofpoint on Demand.
- Puede permitir que los usuarios inicien sesión automáticamente en Proofpoint on Demand (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Proofpoint on Demand, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Proofpoint on Demand

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de Proofpoint on Demand desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-proofpoint-on-demand-from-the-gallery"></a>Adición de Proofpoint on Demand desde la galería
Para configurar la integración de Proofpoint on Demand en Azure AD, deberá agregar Proofpoint on Demand desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Proofpoint on Demand desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Proofpoint on Demand**, seleccione **Proofpoint on Demand** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Proofpoint on Demand en la lista de resultados](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Proofpoint on Demand con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Proofpoint on Demand para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Proofpoint on Demand.

Para establecer la relación de vínculo, en Proofpoint on Demand, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Proofpoint on Demand, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Proofpoint on Demand](#create-a-proofpoint-on-demand-test-user)**, para tener un homólogo de Britta Simon en Proofpoint on Demand que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Proofpoint on Demand.

**Para configurar el inicio de sesión único de Azure AD con Proofpoint on Demand, realice los pasos siguientes:**

1. En la página de integración de la aplicación **Proofpoint on Demand** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_samlbase.png)

3. En la sección **Dominio y direcciones URL de Proofpoint on Demand**, realice los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de Proofpoint on Demand](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<hostname>.pphosted.com/ppssamlsp_hostname`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<hostname>.pphosted.com/ppssamlsp`

    c. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<hostname>.pphosted.com:portnumber/v1/samlauth/samlconsumer`.
    
    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de Proofpoint on Demand](https://www.proofpoint.com/us/support-services) para obtener estos valores.

5. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_400.png)
    
7. En la sección **Configuración de Proofpoint on Demand**, haga clic en **Configurar Proofpoint on Demand** para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configuración de Proofpoint on Demand](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_configure.png) 

8. Para configurar el inicio de sesión único en **Proofpoint on Demand**, es preciso enviar el **certificado (Base64)** descargado, **la dirección URL de cierre de sesión, el identificador de entidad de SAML y la dirección URL del servicio de inicio de sesión único de SAML** al [equipo de atención al cliente de Proofpoint on Demand](https://www.proofpoint.com/us/support-services). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-proofpoint-on-demand-test-user"></a>Creación de un usuario de prueba de Proofpoint on Demand

En esta sección, creará un usuario llamado Britta Simon en Proofpoint on Demand. Trabaje con el [equipo de atención al cliente de Proofpoint on Demand](https://www.proofpoint.com/us/support-services) para agregar usuarios a la plataforma Proofpoint on Demand.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Proofpoint on Demand.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a Proofpoint on Demand, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Proofpoint on Demand**.

    ![Vínculo de Proofpoint on Demand en la lista de aplicaciones](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono Proofpoint on Demand del Panel de acceso, debería iniciar sesión automáticamente en su aplicación Proofpoint on Demand.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_203.png


---
title: "Tutorial: Integración de Azure Active Directory con Proofpoint on Demand | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Proofpoint on Demand."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 773e7f7d-ec31-411b-860d-6a6633335d43
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 7aa746b8c6f4b747a7dca0e9bf75e61b2707b7e1


---
# <a name="tutorial-azure-active-directory-integration-with-proofpoint-on-demand"></a>Tutorial: Integración de Azure Active Directory con Proofpoint on Demand
En este tutorial, aprenderá a integrar Proofpoint on Demand con Azure Active Directory (Azure AD).

La integración de Proofpoint on Demand con Azure AD proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a Proofpoint on Demand.
* Puede permitir que los usuarios inicien sesión automáticamente en Proofpoint on Demand (inicio de sesión único o SSO) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con Proofpoint on Demand, se necesitan los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción de inicio de sesión único de Proofpoint on Demand

Para probar los pasos de este tutorial, siga estas recomendaciones:

* No use su entorno de producción, salvo que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba.

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Proofpoint on Demand desde la galería.
2. Configuración y prueba del inicio de sesión único en Azure AD.

## <a name="add-proofpoint-on-demand-from-the-gallery"></a>Adición de Proofpoint on Demand desde la galería
Para configurar la integración de Proofpoint on Demand en Azure AD, deberá agregar Proofpoint on Demand desde la galería a la lista de aplicaciones SaaS administradas.

1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
    ![Icono de Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, en la vista de directorio, haga clic en **APLICACIONES** en el menú superior.
   
    ![Elemento de menú APLICACIONES][2]
4. Haga clic en **AGREGAR** en la parte inferior de la página.
   
    ![Botón AGREGAR][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Elegir la adición de una aplicación de la galería][4]
6. En el cuadro de búsqueda, escriba **Proofpoint in Demand**.
   
    ![Cuadro en que se escribe "Proofpoint on Demand"](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_01.png)
7. En el panel de resultados, seleccione **Proofpoint on Demand** y haga clic en **Completar** para agregar la aplicación.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Proofpoint on Demand con un usuario de prueba llamado Britta Simon.

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Proofpoint on Demand para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Proofpoint on Demand.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Proofpoint on Demand.

Para configurar y probar el inicio de sesión único de Azure AD con Proofpoint on Demand, es preciso completar los siguientes procedimientos:

1. [Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on), para permitir que los usuarios utilicen esta característica.
2. [Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user), para probar el inicio de sesión único de Azure AD con Britta Simon.
3. [Creación de un usuario de prueba de Proofpoint on Demand](#creating-a-proofpoint-ondemand-test-user), para tener un homólogo de Britta Simon en Proofpoint on Demand que esté vinculado a la representación de ella en Azure AD.
4. [Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user), para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. [Prueba del inicio de sesión único](#testing-single-sign-on): para comprobar que la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
En esta sección, se habilita el inicio de sesión único de Azure AD en el portal clásico y se configura el inicio de sesión único en la aplicación Proofpoint on Demand.

1. En el portal clásico, en la página de integración de aplicaciones de **Proofpoint on Demand**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Botón "Configurar inicio de sesión único"][6]
2. En la página **¿Cómo desea que los usuarios inicien sesión en Proofpoint on Demand?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
    ![Botón de opción "Inicio de sesión único de Microsoft Azure AD"](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_03.png)
3. En la página **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
    ![Página "Configurar las opciones de la aplicación" con los cuadros rellenos](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_04.png)
   
    a. En el cuadro **URL DE INICIO DE SESIÓN** escriba la dirección URL en la que los usuarios inician sesión en su aplicación de Proofpoint on Demand. Utilice el siguiente patrón: **https://\<nombre de host\>.pphosted.com/ppssamlsp_hostname**
   
    b. En el cuadro **IDENTIFICADOR**, escriba la dirección URL mediante el siguiente patrón: **https://\<nombre de host/>.pphosted.com/ppssamlsp**
   
    c. En el cuadro **URL DE RESPUESTA**, escriba la dirección URL mediante el siguiente patrón: **https://\<nombre de host/>.pphosted.com:portnumber/v1/samlauth/samlconsumer**
   
    d. Haga clic en **Siguiente**.
4. En la página **Configurar inicio de sesión único en Proofpoint on Demand**, siga estos pasos:
   
    ![Página "Configurar inicio de sesión único en Proofpoint a petición" con el botón "Descargar certificado"](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_05.png)
   
    a. Haga clic en **Descargar certificado**y después guarde el archivo en el equipo.
   
    b. Haga clic en **Next**.
5. Para configurar el SSO para su aplicación, póngase en contacto con el equipo de soporte técnico de Proofpoint on Demand y proporcione lo siguiente:
   
    • El certificado descargado
   
    • El identificador de entidad
   
    La dirección URL de inicio de sesión único de SAML
6. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
   
    ![Casilla que confirma que ha configurado el inicio de sesión único][10]
7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
   
    ![Página de confirmación][11]

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba llamado Britta Simon en el portal clásico.

![El usuario de prueba en la lista de usuarios][20]

1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
    ![Icono de Active Directory](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_09.png)
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **USUARIOS**.
   
    ![Elemento de menú USUARIOS](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_03.png)
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **AGREGAR USUARIO**.
   
    ![Botón AGREGAR USUARIO](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_04.png)
5. En la página **Proporcione información sobre este usuario** siga estos pasos:  ![Página "Proporcione información sobre este usuario" con los cuadros rellenos](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_05.png)
   
    a. En el cuadro **TIPO DE USUARIO**, seleccione **Nuevo usuario de la organización**.
   
    b. En el cuadro **NOMBRE DE USUARIO**, escriba **BrittaSimon**.
   
    c. Haga clic en **Siguiente**.
6. En la página del **perfil del usuario**, siga estos pasos: ![La página de "perfil de usuario" con los cuadros rellenos](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_06.png)
   
   a. En el cuadro **NOMBRE**, escriba **Britta**.  
   
   b. En el cuadro **APELLIDOS**, escriba **Britta**.
   
   c. En el cuadro **NOMBRE PARA MOSTRAR**, escriba **Britta Simon**.
   
   d. En la lista **ROL**, seleccione **Usuario**.
   
   e. Haga clic en **Next**.
7. En la página **Obtener contraseña temporal**, haga clic en **crear**.
   
    ![Botón para crear una contraseña temporal](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_07.png)
8. En la página **Obtener contraseña temporal**, siga estos pasos:
   
    ![Página "Obtener contraseña temporal" con contraseña](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_08.png)
   
    a. Anote el valor en el campo **NUEVA CONTRASEÑA**.
   
    b. Haga clic en **Completo**.   

### <a name="create-a-proofpoint-on-demand-test-user"></a>Creación de un usuario de prueba de Proofpoint on Demand
En esta sección, creará un usuario llamado Britta Simon en Proofpoint on Demand. Trabaje con el equipo de soporte técnico de Proofpoint on Demand para agregar usuarios en la plataforma de Proofpoint on Demand.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Proofpoint on Demand.

![Información del usuario, que muestra el acceso habilitado a través del método directo][200]

1. En el portal clásico, en la vista de directorio, haga clic en **APLICACIONES** en el menú superior para abrir la vista aplicaciones.
   
    ![Elemento de menú APLICACIONES][201]
2. En la lista de aplicaciones, seleccione **Proofpoint on Demand**.
   
    ![Lista de aplicaciones con Proofpoint on Demand seleccionado](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_50.png)
3. En el menú de la parte superior, haga clic en **USUARIOS**.
   
    ![Elemento de menú USUARIOS][203]
4. En la lista de usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **ASIGNAR**.
   
    ![Botón Asignar][205]

### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único
En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono **Proofpoint on Demand** del Panel de acceso, debería iniciar sesión automáticamente en su aplicación Proofpoint on Demand.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO2-->



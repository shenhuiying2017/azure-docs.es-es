---
title: "Tutorial: Integración de Azure Active Directory con Absorb LMS | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Absorb LMS."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: jeedes
ms.openlocfilehash: 4231b5e72ea0852de344e7484bdf20997243cee6
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Tutorial: Integración de Azure Active Directory con Absorb LMS

En este tutorial, aprenderá a integrar Absorb LMS con Azure Active Directory (Azure AD).

La integración de Absorb LMS con Azure AD ofrece las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Absorb LMS.
- Puede permitir que los usuarios inicien sesión automáticamente en Absorb LMS (mediante el inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si quiere obtener más información sobre la integración de aplicaciones de software como servicio (SaaS) con Azure AD, vea [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Absorb LMS, necesita lo siguiente:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en Absorb LMS

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, siga estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

* Adición de Absorb LMS desde la galería
* Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="add-absorb-lms-from-the-gallery"></a>Agregar Absorb LMS desde la galería
Para configurar la integración de Absorb LMS en Azure AD, será preciso que agregue Absorb LMS desde la galería a la lista de aplicaciones SaaS administradas.

Para agregar Absorb LMS desde la galería, siga estos pasos:

1. En el panel izquierdo de [Azure Portal](https://portal.azure.com), seleccione el icono **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales** > **Todas las aplicaciones**.

    ![Panel Aplicaciones empresariales][2]
    
3. Para agregar una aplicación, haga clic en el botón **Nueva aplicación**.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Absorb LMS**, seleccione **Absorb LMS** en el panel de resultados y haga clic en el botón **Agregar**.

    ![Absorb LMS en la lista de resultados](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Absorb LMS con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Absorb LMS en Azure AD. Es decir, debe establecer una relación de vínculo entre un usuario de Azure AD y el usuario correspondiente en Absorb LMS.

Puede establecer esta relación de vínculo al asignar el valor del *nombre de usuario* en Azure AD como el valor del *nombre de usuario* en Absorb LMS.

Para configurar y probar el inicio de sesión único de Azure AD con Absorb LMS, es preciso completar los bloques de creación que se detallan en las siguientes cinco secciones.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Absorb LMS.

Para configurar el inicio de sesión único de Azure AD con Absorb LMS, siga estos pasos:

1. En la página de integración de la aplicación **Absorb LMS** de Azure Portal, seleccione **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en el cuadro **Modo**, seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_samlbase.png)

3. En la sección **Dominio y direcciones URL de Absorb LMS**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de Absorb LMS](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_url.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL que tenga la siguiente sintaxis: `https://<subdomain>.myabsorb.com/Account/SAML`.

    b. En el cuadro **URL de respuesta**, escriba una dirección URL que tenga la siguiente sintaxis: `https://<subdomain>.myabsorb.com/Account/SAML`.
     
    > [!NOTE] 
    > Estas direcciones URL no son reales. Actualícelas con el identificador y la URL de respuesta reales. Póngase en contacto con el [equipo de soporte técnico de Absorb LMS](https://www.absorblms.com/support) para obtener estos valores. 

4. En la sección **Certificado de firma de SAML**, en la columna **Descargar**, seleccione **XML de metadatos** y, a continuación, guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado de firma](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_certificate.png) 

5. Seleccione **Guardar**.

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-absorblms-tutorial/tutorial_general_400.png)
    
6. En la sección **Configuración de Absorb LMS**, seleccione **Configurar Absorb LMS** para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de cierre de sesión** de la sección **Referencia rápida.**

    ![Panel de configuración de Absorb LMS](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_configure.png) 

7. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de Absorb LMS.

8. Haga clic en el botón **Cuenta** que se encuentra en la esquina superior derecha. 

    ![Botón de cuenta](./media/active-directory-saas-absorblms-tutorial/1.png)

9. En el panel Cuenta, seleccione **Configuración del portal**.

    ![Vínculo de configuración del portal](./media/active-directory-saas-absorblms-tutorial/2.png)
    
10. Seleccione la pestaña **Usuarios** .

    ![La pestaña Usuarios](./media/active-directory-saas-absorblms-tutorial/3.png)

11. Siga estos pasos en la página de configuración de inicio de sesión único:

    ![Página de configuración de inicio de sesión único](./media/active-directory-saas-absorblms-tutorial/4.png)

    a. En el cuadro **Modo**, seleccione **Identity Provider Initiated (Iniciado por el proveedor de identidades)**.

    b. En el Bloc de notas, abra el certificado que descargó desde Azure Portal. Quite las etiquetas **---BEGIN CERTIFICATE---** y **---END CERTIFICATE---**. A continuación, pegue el resto del contenido en el cuadro de texto **Clave**.
    
    c. En el cuadro de texto **Propiedad de id.**, seleccione el atributo adecuado que ha configurado como identificador de usuario en Azure AD. Por ejemplo, si se selecciona *userPrincipalName* en Azure AD, deberá seleccionar **Nombre de usuario**.

    d. En el cuadro **dirección URL de inicio de sesión**, pegue la **dirección URL de acceso de usuario** de la página **Propiedades** de la aplicación en Azure Portal.

    e. En **Dirección URL de cierre de sesión**, pegue el valor de la **dirección URL de cierre de sesión** que copió de la ventana **Configurar inicio de sesión** de Azure Portal.

12. Seleccione la opción **Only Allow SSO Login (Solo permitir inicio de sesión SSO)** y **actívela**.

    ![Opción Only Allow SSO Login (Solo permitir inicio de sesión SSO)](./media/active-directory-saas-absorblms-tutorial/5.png)

13. Seleccione **Guardar.**

> [!TIP]
> Puede leer una versión concisa de estas instrucciones en [Azure Portal](https://portal.azure.com) mientras configura la aplicación. Después de agregar la aplicación desde la sección **Active Directory** > **Aplicaciones empresariales**, seleccione la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Para más información, consulte la [documentación insertada de Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

En esta sección, creará el usuario de prueba Britta Simon en Azure Portal.

![Creación de un usuario de prueba de Azure AD][100]

Haga lo siguiente para crear un usuario de prueba en Azure AD:

1. En el panel izquierdo de Azure Portal, seleccione **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-absorblms-tutorial/create_aaduser_01.png) 

2. Para mostrar una lista de usuarios, seleccione **Usuarios y grupos** > **Todos los usuarios**.
    
    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-absorblms-tutorial/create_aaduser_02.png) 

3. En la parte superior del cuadro de diálogo, seleccione **Agregar**.
 
    ![Botón Agregar](./media/active-directory-saas-absorblms-tutorial/create_aaduser_03.png) 

4. En el cuadro de diálogo **Usuario**, haga lo siguiente:
 
    ![Cuadro de diálogo Usuario](./media/active-directory-saas-absorblms-tutorial/create_aaduser_04.png) 

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico de Britta Simon.

    c. Seleccione la casilla **Mostrar contraseña** y, a continuación, anote el valor que se generó en el cuadro **Contraseña**.

    d. Seleccione **Crear**.

### <a name="create-an-absorb-lms-test-user"></a>Creación de un usuario de prueba de Absorb LMS

Para permitir que los usuarios de Azure AD inicien sesión en Absorb LMS, tienen configurar Absorb LMS.  

En el caso de Absorb LMS, la configuración es una tarea manual.

Para configurar una cuenta de usuario, haga lo siguiente:

1. Inicie sesión como administrador en el sitio de la compañía de Absorb LMS.

2. En el panel izquierdo, seleccione **Usuarios**.

    ![Vínculo de usuarios de Absorb LMS](./media/active-directory-saas-absorblms-tutorial/absorblms_users.png)

3. En el panel **Usuarios**, seleccione **Usuarios**.

    ![Vínculo de usuarios](./media/active-directory-saas-absorblms-tutorial/absorblms_userssub.png)

4. En la lista desplegable **Agregar nuevo**, seleccione **Usuario**.

    ![Lista desplegable Agregar nuevo](./media/active-directory-saas-absorblms-tutorial/absorblms_createuser.png)

5. En la página **Agregar usuario**, siga estos pasos:

    ![página Agregar usuario](./media/active-directory-saas-absorblms-tutorial/user.png)

    a. En el cuadro de texto **Nombre**, escriba el nombre **Britta**.

    b. En el cuadro de texto **Apellido**, escriba el apellido **Simon**.
    
    c. En el cuadro de texto **Nombre de usuario**, escriba el nombre completo **Britta Simon**.

    d. En el cuadro de texto **Contraseña** , escriba la contraseña de Britta Simon.

    e. En el cuadro **Confirmar contraseña** , vuelva a escribir la contraseña.
    
    f. Establezca la opción **Está activo** en **Activo**.  

6. Seleccione **Guardar.**
 
### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará el usuario "Britta Simon" para que use el inicio de sesión único de Azure concediéndole acceso a Absorb LMS.

![Asignación de rol de usuario][200]

Para asignar a Britta Simon a Absorb LMS, siga estos pasos:

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista del directorio y seleccione **Aplicaciones empresariales** > **Todas las aplicaciones**.

    ![Vínculo "Todas las aplicaciones"][201] 

2. En la lista **Aplicaciones**, seleccione **Absorb LMS**.

    ![Vínculo a Absorb LMS en la lista de aplicaciones](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_app.png) 

3. En el panel izquierdo, seleccione **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202] 

4. Seleccione **Agregar** y, después, en el panel **Agregar asignación**, seleccione **Usuarios y grupos**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, en la lista **Usuarios** seleccione **Britta Simon**.

6. En el cuadro de diálogo **Usuarios y grupos**, haga clic en el botón **Seleccionar**.

7. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono **Absorb LMS** del panel de acceso, se inicia sesión automáticamente en la aplicación Absorb LMS. Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](https://msdn.microsoft.com/library/dn308586).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_203.png


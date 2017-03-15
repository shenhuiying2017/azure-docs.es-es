---
title: "Tutorial: integración de Azure Active Directory con Intacct | Microsoft Docs"
description: "Aprenda a usar Intacct con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 20db13c5ba67fcc402db5c692842719dd9a34eb1
ms.openlocfilehash: 0ea77d5f9c5b139ce6f87bc0cdd817166b03c22e
ms.lasthandoff: 03/01/2017

---

# <a name="tutorial-azure-active-directory-integration-with-intacct"></a>Tutorial: integración de Azure Active Directory con Intacct
El objetivo de este tutorial es mostrar la integración de Azure e Intacct.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino de Intacct

Después de completar este tutorial, los usuarios de Azure Active Directory (Azure AD) que ha asignado a Intacct podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Intacct (inicio de sesión iniciado por el proveedor de servicios) o con el [Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

* Habilitación de la integración de aplicaciones para Intacct
* Configuración del inicio de sesión único
* Configuración del aprovisionamiento de usuario
* Asignación de usuarios

![Escenario](./media/active-directory-saas-intacct-tutorial/IC790030.png "Escenario")

## <a name="enable-the-application-integration-for-intacct"></a>Habilitación de la integración de aplicaciones para Intacct
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Intacct.

**Siga estos pasos para habilitar la integración de aplicaciones para Intacct:**

1. En el panel izquierdo del Portal de Azure clásico, haga clic en el icono de Active Directory.

   ![Active Directory](./media/active-directory-saas-intacct-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Aplicaciones**, en el menú superior de la vista de directorios.

   ![Aplicaciones](./media/active-directory-saas-intacct-tutorial/IC700994.png "Aplicaciones")
4. Haga clic en **Agregar** en la parte inferior de la página.

   ![Agregar aplicaciones](./media/active-directory-saas-intacct-tutorial/IC749321.png "Agregar aplicaciones")
5. En la página **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

   ![Agregar una aplicación de la galería](./media/active-directory-saas-intacct-tutorial/IC749322.png "Agregar una aplicación de la galería")
6. En el cuadro de búsqueda, escriba **Intacct**.

   ![Galería de aplicaciones](./media/active-directory-saas-intacct-tutorial/IC790031.png "Galería de aplicaciones")
7. En el panel de resultados, seleccione **Intacct** y luego haga clic en **Completar** para agregar la aplicación.

   ![Intacct](./media/active-directory-saas-intacct-tutorial/IC790032.png "Intacct")

## <a name="configure-single-sign-on"></a>Configurar inicio de sesión único

El objetivo de esta sección es describir cómo se habilita la autenticación de usuarios en Intacct con su cuenta de Azure AD. La federación que se basa en el protocolo SAML se utiliza para esta autenticación.  

Como parte de este procedimiento, se requiere crear un archivo de certificado codificado en base&64;. Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

**Siga estos pasos para configurar el inicio de sesión único:**

1. En el Portal de Azure clásico, en la página de integración de la aplicación **Intacct**, haga clic en **Configurar inicio de sesión único** para abrir la página **Configurar inicio de sesión único**.

   ![Configurar inicio de sesión único](./media/active-directory-saas-intacct-tutorial/IC790033.png "Configurar inicio de sesión único")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Intacct?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

   ![Configurar inicio de sesión único](./media/active-directory-saas-intacct-tutorial/IC790034.png "Configurar inicio de sesión único")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro **URL de inicio de sesión**, escriba su dirección URL que usa el patrón *https://Intacct.com/company* y luego haga clic en **Siguiente**.

   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-intacct-tutorial/IC790035.png "Configurar dirección URL de la aplicación")
4. En la página **Configuración de inicio de sesión único en Intacct**, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.

   ![Configurar inicio de sesión único](./media/active-directory-saas-intacct-tutorial/IC790036.png "Configurar inicio de sesión único")
5. En otra ventana del explorador web, inicie sesión en el sitio de la compañía de Intacct como administrador.
6. Haga clic en la pestaña **Compañía** y luego haga clic en**Información de la compañía**.

   ![Compañía](./media/active-directory-saas-intacct-tutorial/IC790037.png "Compañía")
7. Haga clic en la pestaña **Seguridad** y luego haga clic en **Editar**.

   ![Seguridad](./media/active-directory-saas-intacct-tutorial/IC790038.png "Seguridad")
8. En la sección **Inicio de sesión único (SSO)** , siga estos pasos:

   ![Inicio de sesión único](./media/active-directory-saas-intacct-tutorial/IC790039.png "inicio de sesión único")

   1. Seleccione **Habilitar inicio de sesión único**.
   2. En **Tipo de proveedor de identidades**, seleccione **SAML 2.0**.
   3. En el Portal de Azure clásico, en la página **Configurar inicio de sesión único en Intacct**, copie el valor de **URL del emisor** y péguelo en el cuadro **URL del emisor**.
   4. En el Portal de Azure clásico, en la página **Configurar inicio de sesión único en Intacct**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro **Dirección URL de inicio de sesión**.
   5. Cree un archivo **codificado en base&64;** a partir del certificado descargado. Para más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).      
   6. Abra el certificado codificado en base&64; en el Bloc de notas, copie el contenido del mismo en el Portapapeles y luego péguelo en el cuadro **Certificado**.
   7. Haga clic en **Guardar**.
9. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar la página **Configurar inicio de sesión único**.

   ![Configurar inicio de sesión único](./media/active-directory-saas-intacct-tutorial/IC790040.png "Configurar inicio de sesión único")

## <a name="configure-user-provisioning"></a>Configurar aprovisionamiento de usuarios

Para configurar los usuarios de Azure AD para que puedan iniciar sesión en Intacct, tienen que aprovisionarse en Intacct. Para Intacct, el aprovisionamiento es una tarea manual.

**Para aprovisionar cuentas de usuario, realice estos pasos:**

1. Inicie sesión en su inquilino de **Intacct** .
2. Haga clic en la pestaña **Compañía** y luego haga clic en **Usuarios**.

   ![Usuarios](./media/active-directory-saas-intacct-tutorial/IC790041.png "Usuarios")
3. Haga clic en la pestaña **Agregar**.

   ![Agregar](./media/active-directory-saas-intacct-tutorial/IC790042.png "Agregar")
4. En la sección **Información del usuario** , lleve a cabo estos pasos:

   ![Información del usuario](./media/active-directory-saas-intacct-tutorial/IC790043.png "Información del usuario")

   1. Escriba el **Id. de usuario**, **Apellidos**, **Nombre**, **Dirección de correo electrónico**, **Puesto** y **Teléfono** de una cuenta de Azure AD que quiera aprovisionar en la sección **Información del usuario**.
   2. Seleccione **Privilegios administrativos** de una cuenta de Azure AD que quiera aprovisionar.
   3. Haga clic en **Guardar**. El titular de la cuenta de Azure AD recibe un mensaje de correo y sigue un vínculo para confirmar su cuenta antes de que se active.

>[!NOTE]
>Para aprovisionar cuentas de usuario de Azure AD, puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Intacct ofrecida por Intacct.
>

## <a name="assign-users"></a>Asignar usuarios
Para probar la configuración, es necesario asignar usuarios de Azure AD a Intacct. Una vez asignado un usuario, puede acceder a la aplicación.

**Para asignar usuarios a Intacct, lleve a cabo los siguientes pasos:**

1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **Intacct**, haga clic en **Asignar usuarios**.

   ![Asignar usuarios](./media/active-directory-saas-intacct-tutorial/IC790044.png "Asignar usuarios")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.

   ![Sí](./media/active-directory-saas-intacct-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).


### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, va a configurar a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Intacct.

![Asignar usuario][200]

**Para asignar la usuaria Britta Simon a Intacct., siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio, vaya a **Aplicaciones empresariales** y haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201]

2. En la lista de aplicaciones, seleccione **Intacct.**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_50.png)

3. En el menú **Administrar**, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202]

4. Haga clic en el botón **Agregar** y, después, en **Agregar asignación**, seleccione **Usuarios y grupos**.

    ![Asignar usuario][203]

5. En **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. En **Usuarios y grupos**, haga clic en el botón **Seleccionar**.

7. En **Agregar asignación**, haga clic en el botón **Asignar**.



### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Intacct. en el panel de acceso, debería iniciar sesión automáticamente en su aplicación de Intacct.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_203.png


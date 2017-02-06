---
title: "Tutorial: Integración de Azure Active Directory con Bamboo HR | Microsoft Docs"
description: "Aprenda cómo usar Bamboo HR con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f826b5d2-9c64-47df-bbbf-0adf9eb0fa71
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e675b92ea53c31c014e20735c039db3dbad325cc


---
# <a name="tutorial-azure-active-directory-integration-with-bamboo-hr"></a>Tutorial: Integración de Azure Active Directory con Bamboo HR
El objetivo de este tutorial es mostrar la integración de Azure y BambooHR.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para inicio de sesión único en BambooHR

Después de completar este tutorial, los usuarios de Azure AD que asignó a BambooHR podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de BambooHR (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md)

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para BambooHR
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "Escenario")

## <a name="enabling-the-application-integration-for-bamboohr"></a>Habilitación de la integración de aplicaciones para BambooHR
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones en BambooHR.

### <a name="to-enable-the-application-integration-for-bamboohr-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para BambooHR:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-bamboo-hr-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Aplicaciones](./media/active-directory-saas-bamboo-hr-tutorial/IC700994.png "Aplicaciones")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicaciones](./media/active-directory-saas-bamboo-hr-tutorial/IC749321.png "Agregar aplicaciones")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-bamboo-hr-tutorial/IC749322.png "Agregar una aplicación de la galería")
6. En el **cuadro de búsqueda**, escriba **BambooHR**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-bamboo-hr-tutorial/IC796686.png "Galería de aplicaciones")
7. En el panel de resultados, seleccione **BambooHR** y, luego, haga clic en **Completar** para agregar la aplicación.
   
   ![BambooHR](./media/active-directory-saas-bamboo-hr-tutorial/IC796687.png "BambooHR")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo se habilita la autenticación de usuarios en BambooHR con su cuenta de Azure AD mediante la federación basada en el protocolo SAML.  
Como parte de este procedimiento, es necesario crear un archivo de certificado codificado en base 64.  
Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **BambooHR**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Escenario](./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "Escenario")
2. En la página **¿Cómo desea que los usuarios inicien sesión en BambooHR?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego , haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-bamboo-hr-tutorial/IC796688.png "Configurar inicio de sesión único")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de BambooHR**, escriba la dirección URL usada por los usuarios para iniciar sesión en su aplicación BambooHR (por ejemplo: https://company.bamboohr.com) y haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-bamboo-hr-tutorial/IC796689.png "Configurar dirección URL de la aplicación")
4. En la página **Configuración de inicio de sesión único en BambooHR**, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-bamboo-hr-tutorial/IC796690.png "Configurar inicio de sesión único")
5. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de BambooHR.
6. En la página principal realice los pasos siguientes:
   
   ![Inicio de sesión único](./media/active-directory-saas-bamboo-hr-tutorial/IC796691.png "Inicio de sesión único")
   
   1. Haga clic en **Aplicaciones**.
   2. En el menú de aplicaciones de la izquierda, haga clic en **Inicio de sesión único**.
   3. Haga clic en **Inicio de sesión único de SAML**.
7. En la sección **Inicio de sesión único de SAML** siga estos pasos:
   
   ![Inicio de sesión único SAML](./media/active-directory-saas-bamboo-hr-tutorial/IC796692.png "Inicio de sesión único SAML")
   
   1. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en BambooHR**, copie el valor de **Dirección URL del servicio de inicio de sesión único** y péguelo en el cuadro de texto **URL de inicio de sesión único**.
   2. Cree un archivo **codificado en base 64** a partir del certificado descargado.  
      
      > [!TIP]
      > Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   3. Abra el certificado codificado en base 64 en el Bloc de notas, copie el contenido del mismo en el Portapapeles y, a continuación, péguelo en el cuadro de texto **Certificado X.509** .
   4. Haga clic en **Guardar**.
8. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-bamboo-hr-tutorial/IC796693.png "Configurar inicio de sesión único")
   
   ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en BambooHR, deben aprovisionarse a BambooHR.  
En el caso de BambooHR, el aprovisionamiento es una tarea manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para aprovisionar cuentas de usuario, realice estos pasos:
1. Inicie sesión en su sitio de **BambooHR** como administrador.
2. En la barra de herramientas de la parte superior, haga clic en el icono de **Configuración**.
   
   ![Configuración](./media/active-directory-saas-bamboo-hr-tutorial/IC796694.png "Configuración")
3. Haga clic en **Descripción general**.
4. En el panel de navegación izquierdo, vaya a **Seguridad \> Usuarios**.
5. Escriba el nombre de usuario, la contraseña y la dirección de correo electrónico de una cuenta válida de AAD que desee aprovisionar en los cuadros de texto relacionados.
6. Haga clic en **Guardar**.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de BambooHR para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe asignar los usuarios de Azure AD que quiera que usen su aplicación para concederles acceso a ella.

### <a name="to-assign-users-to-bamboohr-perform-the-following-steps"></a>Para asignar usuarios a BambooHR, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **BambooHR**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-bamboo-hr-tutorial/IC796695.png "Asignar usuarios")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-bamboo-hr-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Dec16_HO4-->



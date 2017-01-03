---
title: "Tutorial: Integración de Azure Active Directory con Benefitsolver | Microsoft Docs"
description: "Aprenda cómo usar Benefitsolver con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: cf4529b1-3fb6-4475-82b7-2ceedcb70b3c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/10/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eb7dce1c0664e34c4946bb08f5b412e1e01c715d


---
# <a name="tutorial-azure-active-directory-integration-with-benefitsolver"></a>Tutorial: Integración de Azure Active Directory con Benefitsolver
El objetivo de este tutorial es mostrar la integración de Azure y Benefitsolver.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para inicio de sesión único en Benefitsolver

Después de completar este tutorial, los usuarios de Azure AD que haya asignado a Benefitsolver podrán realizar inicios de sesión únicos en la aplicación utilizando [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Benefitsolver
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-benefitsolver-tutorial/IC804820.png "Escenario")

## <a name="enabling-the-application-integration-for-benefitsolver"></a>Habilitación de la integración de aplicaciones para Benefitsolver
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para Benefitsolver.

### <a name="to-enable-the-application-integration-for-benefitsolver-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones en Benefitsolver:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-benefitsolver-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Aplicaciones](./media/active-directory-saas-benefitsolver-tutorial/IC700994.png "Aplicaciones")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicaciones](./media/active-directory-saas-benefitsolver-tutorial/IC749321.png "Agregar aplicaciones")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-benefitsolver-tutorial/IC749322.png "Agregar una aplicación de la galería")
6. En el **cuadro de búsqueda**, escriba **Benefitsolver**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-benefitsolver-tutorial/IC804821.png "Galería de aplicaciones")
7. En el panel de resultados, seleccione **Benefitsolver** y, luego, haga clic en **Completar** para agregar la aplicación.
   
   ![Benefitssolver](./media/active-directory-saas-benefitsolver-tutorial/IC804822.png "Benefitssolver")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en Benefitsolver con su cuenta de Azure AD mediante la federación basada en el protocolo SAML.  
La aplicación Benefitsolver espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los **atributos del token de SAML** .  
La siguiente captura de pantalla le muestra un ejemplo de esto.

![Atributos](./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "Atributos")

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Benefitsolver**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-benefitsolver-tutorial/IC804824.png "Configurar inicio de sesión único")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Benefitsolver?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego , haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-benefitsolver-tutorial/IC804825.png "Configurar inicio de sesión único")
3. En la página **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
   ![Configurar las opciones de la aplicación](./media/active-directory-saas-benefitsolver-tutorial/IC804826.png "Configurar las opciones de la aplicación")
   
   1. En el cuadro de texto **URL de inicio de sesión**, escriba **http://azure.benefitsolver.com**.
   2. En el cuadro de texto **URL de respuesta**, escriba **https://www.benefitsolver.com/benefits/BenefitSolverView?page_name=single_signon_saml**.  
   3. Haga clic en **Siguiente**.
4. En la página **Configuración de inicio de sesión único en Benefitsolver**, para descargar los metadatos, haga clic en **Descargar metadatos** y, luego, guarde el archivo de metadatos en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-benefitsolver-tutorial/IC804827.png "Configurar inicio de sesión único")
5. Envíe el archivo de metadatos descargado al equipo de soporte técnico de Benefitsolver.
   
   > [!NOTE]
   > El equipo de soporte técnico de Benefitsolver es el que tiene que realizar la configuración real de SSO.
   > Cuando SSO se haya habilitado en su suscripción recibirá una notificación.
   > 
   > 
6. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-benefitsolver-tutorial/IC804828.png "Configurar inicio de sesión único")
7. En el menú de la parte superior, haga clic en **Atributos** to open the **SAML Token Atributos** .
   
   ![Atributos](./media/active-directory-saas-benefitsolver-tutorial/IC795920.png "Atributos")
8. Para agregar las asignaciones de los atributos necesarios, realice los pasos siguientes:
   
   ![Atributos](./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "Atributos")
   
   | Nombre del atributo | Valor de atributo |
   | --- | --- |
   | ClientID |Para obtener este valor tiene que acudir al equipo de soporte de Benefitsolver. |
   | ClientKey |Para obtener este valor tiene que acudir al equipo de soporte de Benefitsolver. |
   | LogoutURL |Para obtener este valor tiene que acudir al equipo de soporte de Benefitsolver. |
   | EmployeeID |Para obtener este valor tiene que acudir al equipo de soporte de Benefitsolver. |
   
   1. En cada fila de datos de la tabla anterior, haga clic en **agregar atributo de usuario**.
   2. En el cuadro de texto **Nombre de atributo** , escriba el nombre de atributo que se muestra para la fila.
   3. En el cuadro de texto **Valor de atributo** , seleccione el valor de atributo que se muestra para la fila.
   4. Haga clic en **Complete**.
9. Haga clic en **Aplicar cambios**.

## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario
Para permitir que los usuarios de Azure AD inicien sesión en Benefitsolver, deben aprovisionarse a Benefitsolver.  
En el caso de Benefitsolver, los datos de empleados en la aplicación se rellenan a través de un archivo Census de su sistema HRIS (normalmente por la noche).  

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Benefitsolver para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe asignar los usuarios de Azure AD que quiera que usen su aplicación para concederles acceso a ella.

### <a name="to-assign-users-to-benefitsolver-perform-the-following-steps"></a>Para asignar usuarios a Benefitsolver, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **Benefitsolver** haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-benefitsolver-tutorial/IC804829.png "Asignar usuarios")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-benefitsolver-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Jan17_HO1-->



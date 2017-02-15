---
title: "Tutorial: integración de Azure Active Directory con SimpleNexus | Microsoft Docs"
description: "Aprenda cómo usar SimpleNexus con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 89821a05-88e2-4579-b144-0123b2b9cb95
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/12/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 451369e21e7471180b6cd8c77d62b157d0bcddff
ms.openlocfilehash: a4478283ef6ab949cc73322db908347a626c12d8


---
# <a name="tutorial-azure-active-directory-integration-with-simplenexus"></a>Tutorial: Integración de Azure Active Directory con SimpleNexus
El objetivo de este tutorial es mostrar la integración de Azure y SimpleNexus.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para el inicio de sesión único en SimpleNexus

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a SimpleNexus podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de SimpleNexus (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para SimpleNexus
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-simplenexus-tutorial/IC785893.png "Escenario")

## <a name="enabling-the-application-integration-for-simplenexus"></a>Habilitación de la integración de aplicaciones para SimpleNexus
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para SimpleNexus.

### <a name="to-enable-the-application-integration-for-simplenexus-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para SimpleNexus:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-simplenexus-tutorial/IC700993.png "Active Directory")

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Aplicaciones](./media/active-directory-saas-simplenexus-tutorial/IC700994.png "Aplicaciones")

4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Agregar aplicaciones](./media/active-directory-saas-simplenexus-tutorial/IC749321.png "Agregar aplicaciones")

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Agregar una aplicación de la galería](./media/active-directory-saas-simplenexus-tutorial/IC749322.png "Agregar una aplicación de la galería")

6. En el **cuadro de búsqueda**, escriba **simple nexus**.
   
    ![Galería de aplicaciones](./media/active-directory-saas-simplenexus-tutorial/IC785894.png "Galería de aplicaciones")

7. En el panel de resultados, seleccione **SimpleNexus** y, luego, haga clic en **Completar** para agregar la aplicación.
   
    ![Simple Nexus](./media/active-directory-saas-simplenexus-tutorial/IC809578.png "Simple Nexus")
   
## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en SimpleNexus con su cuenta de Azure AD usando el protocolo SAML basado en la federación.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de la aplicación **SimpleNexus**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-simplenexus-tutorial/IC785896.png "Configurar inicio de sesión único")

2. En la página **¿Cómo desea que los usuarios inicien sesión en SimpleNexus?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego, haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-simplenexus-tutorial/IC785897.png "Configurar inicio de sesión único")

3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de inicio de sesión de SimpleNexus**, escriba su dirección URL con el siguiente patrón "*https://simplenexus.com/CompanyName\_login*" y haga clic en **Siguiente**.
   
    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-simplenexus-tutorial/IC786904.png "Configurar dirección URL de la aplicación")

4. En la página **Configurar inicio de sesión único en SimpleNexus**, haga clic en **Descargar metadatos** y, luego, reenvíe el archivo de metadatos al equipo de soporte técnico de SimpleNexus.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-simplenexus-tutorial/IC785899.png "Configurar inicio de sesión único")
   
    > [!NOTE]
    > El inicio de sesión único debe habilitarlo el equipo de soporte técnico de SimpleNexus.
    > 
    > 

5. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-simplenexus-tutorial/IC785900.png "Configurar inicio de sesión único")
   
## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en SimpleNexus, deben aprovisionarse en SimpleNexus.  
En el caso de SimpleNexus, el aprovisionamiento es una tarea manual que realiza el Administrador de inquilinos.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de SimpleNexus ofrecida por SimpleNexus para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-simplenexus-perform-the-following-steps"></a>Para asignar usuarios a SimpleNexus, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de la aplicación **SimpleNexus**, haga clic en **Asignar usuarios**.
   
    ![Asignar usuarios](./media/active-directory-saas-simplenexus-tutorial/IC785901.png "Asignar usuarios")

3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
    ![Sí](./media/active-directory-saas-simplenexus-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Dec16_HO2-->



---
title: "Tutorial: Configuración de Workplace by Facebook para el aprovisionamiento de usuarios | Microsoft Docs"
description: "Obtenga información sobre cómo aprovisionar y cancelar automáticamente el aprovisionamiento de cuentas de usuario de Azure AD Workplace by Facebook."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 77f5f33044b1915fbda7b86c6b07882c0e1e7554
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-configure-workplace-by-facebook-for-user-provisioning"></a>Tutorial: Configuración de Workplace by Facebook para el aprovisionamiento de usuarios

En este tutorial se muestran los pasos necesarios para aprovisionar y cancelar el aprovisionamiento automáticamente las cuentas de usuario de Azure Active Directory (Azure AD) a Workplace by Facebook.

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Workplace by Facebook, necesita lo siguiente:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único (SSO) de Workplace by Facebook

Para probar los pasos de este tutorial, siga estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una oferta de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assign-users-to-workplace-by-facebook"></a>Asignación de usuarios a Workplace by Facebook

Azure AD usa un concepto que se denomina "asignaciones" para determinar qué usuarios deben recibir acceso a determinadas aplicaciones. En el contexto de aprovisionamiento automático de cuentas de usuario, solo se sincronizarán los usuarios y grupos que se han asignado a una aplicación en Azure AD.

Antes de configurar y habilitar el servicio de aprovisionamiento, decida qué usuarios y grupos de Azure AD representan a los usuarios que necesitan acceso a la aplicación Workplace by Facebook. Después, puede asignar estos usuarios a la aplicación de Workplace by Facebook siguiendo las instrucciones de [Asignación de un usuario o un grupo a una aplicación empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

>[!IMPORTANT]
>*   Pruebe la configuración de aprovisionamiento mediante la asignación de un único usuario de Azure AD a Workplace by Facebook. Asigne usuarios y grupos adicionales más tarde.
>*   Al asignar un usuario a Workplace by Facebook, debe seleccionar un rol de usuario válido. El rol Acceso predeterminado no funciona para realizar el aprovisionamiento.

## <a name="enable-automated-user-provisioning"></a>Habilitación del aprovisionamiento automático de usuarios

Esta sección le guiará en el proceso de conexión de Azure AD a la API de aprovisionamiento de cuentas de usuario de Workplace by Facebook. También aprenderá a configurar el servicio de aprovisionamiento para crear, actualizar y deshabilitar cuentas de usuario asignado en Workplace by Facebook. Esto se basa en la asignación de grupos y usuarios en Azure AD.

>[!Tip]
>También puede habilitar el SSO basado en SAML para Workplace by Facebook siguiendo las instrucciones de [Azure Portal](https://portal.azure.com). El inicio de sesión único puede configurarse independientemente del aprovisionamiento automático, aunque estas dos características se complementan entre sí.

### <a name="configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Configuración del aprovisionamiento de cuentas de usuario para Workplace by Facebook en Azure AD

Azure AD admite la capacidad de sincronizar automáticamente los detalles de la cuenta de usuarios asignados a Workplace by Facebook. Esta sincronización automática permite a Workplace by Facebook obtener los datos que necesita para autorizar a los usuarios a acceder, antes de que intenten iniciar sesión por primera vez. También desaprovisiona usuarios de Workplace by Facebook cuando se revoque el acceso en Azure AD.

1. En [Azure Portal](https://portal.azure.com), seleccione **Azure Active Directory**  > **Aplicaciones empresariales** > **Todas las aplicaciones**.

2. Si ya ha configurado Workplace by Facebook para el inicio de sesión único, busque la instancia de Workplace by Facebook mediante el campo de búsqueda. En caso contrario, seleccione **Agregar** y busque **Workplace by Facebook**  en la galería de aplicaciones. Seleccione **Workplace by Facebook** en los resultados de búsqueda y agrégalo a la lista de aplicaciones.

3. Seleccione la instancia de Workplace by Facebook y, después, seleccione la pestaña **Aprovisionamiento**.

4. Establezca el **modo de aprovisionamiento** en **Automático**. 

    ![Captura de pantalla de las opciones de aprovisionamiento de Workplace by Facebook](./media/active-directory-saas-facebook-at-work-provisioning-tutorial/provisioning.png)

5. En la sección **Credenciales de administrador**, escriba el **token del secreto** y la **dirección URL del inquilino** de su administrador de Workplace by Facebook.

6. En Azure Portal, seleccione **Probar conexión** para asegurarse de que Azure AD puede conectarse a la aplicación Workplace by Facebook. Si la conexión no se establece, asegúrese de que la cuenta de Workplace by Facebook tiene permisos de administrador de equipo.

7. Escriba la dirección de correo electrónico de una persona o grupo que debe recibir las notificaciones de error de aprovisionamiento en el campo **Correo electrónico de notificación** y active la casilla.

8. Seleccione **Guardar**.

9. En la sección Asignaciones, seleccione **Synchronize Azure Active Directory Users to Workplace by Facebook** (Sincronizar usuarios de Azure Active Directory con Workplace by Facebook).

10. En la sección **Attribute Mappings** (Asignaciones de atributos), revise los atributos de usuario que se sincronizan entre Azure AD y Workplace by Facebook. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con las cuentas de usuario de Workplace by Facebook con el objetivo de realizar operaciones de actualización. Para confirmar los cambios, seleccione **Guardar**.

11. Para habilitar el servicio de aprovisionamiento de Azure AD para Workplace by Facebook, en la sección **Configuración**, cambie el **Estado de aprovisionamiento** a **Activado**.

12. Seleccione **Guardar**.

Para más información sobre cómo configurar el aprovisionamiento automático, consulte [la documentación de Facebook](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers).

Ahora puede crear una cuenta de prueba. Espere 20 minutos para comprobar que la cuenta se ha sincronizado con Workplace by Facebook.

## <a name="additional-resources"></a>Recursos adicionales

* [Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configuración del inicio de sesión único](active-directory-saas-facebook-at-work-tutorial.md)


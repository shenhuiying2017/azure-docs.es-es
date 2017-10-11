---
title: "Introducción al acceso condicional en Azure Active Directory | Microsoft Docs"
description: "Probar el acceso condicional con una condición de ubicación."
services: active-directory
keywords: acceso condicional a aplicaciones, acceso condicional con Azure AD, acceso seguro a recursos de empresa, directivas de acceso condicional
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/31/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: cd53e8be32d1e98aaf9f72177895871dba69df86
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="get-started-with-conditional-access-in-azure-active-directory"></a>Introducción al acceso condicional en Azure Active Directory

El acceso condicional es una funcionalidad de Azure Active Directory que permite definir las condiciones en las que los usuarios autorizados pueden acceder a las aplicaciones. 

En este tema se proporcionan instrucciones para probar un acceso condicional basado en una condición de ubicación en su entorno.  


## <a name="scenario-description"></a>Descripción del escenario

En muchas organizaciones, un requisito habitual es requerir solo autenticación multifactor para el acceso a las aplicaciones que no se realiza desde la intranet corporativa. Con Azure Active Directory, puede conseguir este objetivo fácilmente mediante la configuración de una directiva de acceso condicional basado en la ubicación. En este tema se proporcionan instrucciones detalladas para configurar esta directiva. La directiva aprovecha las [direcciones IP de confianza](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips) para distinguir entre los intentos de acceso que se realizan desde la intranet corporativa y todas las demás ubicaciones.


## <a name="prerequisites"></a>Requisitos previos

En el escenario descrito en este tema se da por supuesto que está familiarizado con los conceptos que se describen en [Acceso condicional de Azure Active Directory](active-directory-conditional-access-azure-portal.md).

Para probar este escenario, deberá:

- Crear un usuario de prueba 

- Asignar una licencia de Azure AD Premium al usuario de prueba

- Configurar una aplicación administrada y asignarle el usuario de prueba

- Configurar direcciones IP de confianza

Si necesita obtener más información acerca de las direcciones IP de confianza, consulte [Direcciones IP de confianza](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).


## <a name="policy-configuration-steps"></a>Pasos de la configuración de la directiva

**Para configurar la directiva de acceso condicional, haga lo siguiente:**

1. En Azure Portal, en la barra de navegación izquierda, haga clic en **Azure Active Directory**. 

    ![Acceso condicional](./media/active-directory-conditional-access-azure-portal-get-started/01.png)

2. En la hoja **Azure Active Directory**, en la sección **Administrar**, haga clic en **Acceso condicional**.

    ![Acceso condicional](./media/active-directory-conditional-access-azure-portal-get-started/02.png)
 
3. En la hoja **Acceso condicional**, en la barra de herramientas de la parte superior, haga clic en **Agregar** para abrir la hoja **Nuevo**.

    ![Acceso condicional](./media/active-directory-conditional-access-azure-portal-get-started/03.png)

4. En la hoja **Nuevo**, en el cuadro de texto **Nombre**, escriba un nombre para la directiva.

    ![Acceso condicional](./media/active-directory-conditional-access-azure-portal-get-started/04.png)

5. En la sección **Asignación**, haga clic en **Usuarios y grupos**.

    ![Acceso condicional](./media/active-directory-conditional-access-azure-portal-get-started/05.png)

6. En la hoja **Usuarios y grupos**, siga estos pasos:

    ![Acceso condicional](./media/active-directory-conditional-access-azure-portal-get-started/06.png)

    a. Haga clic en **Seleccionar usuarios y grupos**.

    b. Haga clic en **Seleccionar**.

    c. En la hoja **Seleccionar**, seleccione el usuario de prueba y haga clic en **Seleccionar**.

    d. En la hoja **Usuarios y grupos**, haga clic en **Listo**.

7. En la hoja **Nuevo**, en la sección **Asignación**, haga clic en **Aplicaciones en la nube** para abrir la hoja **Aplicaciones en la nube**.

    ![Acceso condicional](./media/active-directory-conditional-access-azure-portal-get-started/07.png)

8. En la hoja **Aplicaciones en la nube**, siga estos pasos:

    ![Acceso condicional](./media/active-directory-conditional-access-azure-portal-get-started/08.png)

    a. Haga clic en **Seleccionar aplicaciones**.

    b. Haga clic en **Seleccionar**.

    c. En la hoja **Seleccionar**, seleccione la aplicación en la nube y haga clic en **Seleccionar**.

    d. En la hoja **Aplicaciones en la nube**, haga clic en **Listo**.

9. En la hoja **Nuevo**, en la sección **Asignación**, haga clic en **Condiciones** para abrir la hoja **Condiciones**.

    ![Acceso condicional](./media/active-directory-conditional-access-azure-portal-get-started/09.png)

10. En la hoja **Condiciones**, haga clic en **Ubicaciones** para abrir la hoja **Ubicaciones**.

    ![Acceso condicional](./media/active-directory-conditional-access-azure-portal-get-started/10.png)

11. En la hoja **Ubicaciones**, siga estos pasos:

    ![Acceso condicional](./media/active-directory-conditional-access-azure-portal-get-started/11.png)

    a. En **Configurar**, haga clic en **Sí**.

    b. En **Incluir**, haga clic en **Todas las ubicaciones**.

    c. Haga clic en **Excluir** y, después, haga clic en **Todas las direcciones IP de confianza**.

    ![Acceso condicional](./media/active-directory-conditional-access-azure-portal-get-started/12.png)

    d. Haga clic en **Done**(Listo).

12. En la hoja **Condiciones**, haga clic en **Listo**.

13. En la hoja **Nuevo**, en la sección **Controles**, haga clic en **Conceder** para abrir la hoja **Conceder**.

    ![Acceso condicional](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. En la hoja **Conceder**, siga estos pasos:

    ![Acceso condicional](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    a. Seleccione **Requerir autenticación multifactor**.

    b. Haga clic en **Seleccionar**.

15. En la hoja **Nuevo**, en **Habilitar directiva**, haga clic en **Activar**.

    ![Acceso condicional](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. En la hoja **Nuevo**, haga clic en **Crear**.


## <a name="testing-the-policy"></a>Prueba de la directiva

Para probar la directiva, debe acceder a la aplicación desde un dispositivo que cumpla estas condiciones: 

1. Tener una dirección IP que esté dentro de las direcciones IP de confianza configuradas. 

1. Tener una dirección IP que no esté dentro de las direcciones IP de confianza configuradas.

La autenticación multifactor solo se requerirá durante un intento de conexión realizado desde un dispositivo que no está dentro de las direcciones IP de confianza configuradas. 


## <a name="next-steps"></a>Pasos siguientes

Para más información sobre el acceso condicional, consulte [Acceso condicional de Azure Active Directory](active-directory-conditional-access-azure-portal.md).


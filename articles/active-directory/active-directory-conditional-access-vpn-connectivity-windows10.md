---
title: "Acceso condicional de Azure Active Directory para la conectividad de redes privadas virtuales (versión preliminar) | Microsoft Docs"
description: "Más información sobre cómo funciona el acceso condicional de Azure Active Directory para la conectividad de redes privadas virtuales. "
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 51a1ee61-3ffe-4f65-b8de-ff21903e1e74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 540d8974ee2c02f80bccf28764b4d0d243e98d85
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-conditional-access-for-virtual-private-network-connectivity-preview"></a>Acceso condicional de Azure Active Directory para la conectividad de redes privadas virtuales (versión preliminar)

Con el [acceso condicional de Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), puede ajustar el modo en que los usuarios autorizados pueden tener acceso a sus recursos. El acceso condicional de Azure AD para la conectividad de redes privadas virtuales (VPN) le permite usar el acceso condicional para proteger las conexiones VPN.


Para configurar el acceso condicional de Azure AD para la conectividad VPN, debe completar los siguientes pasos: 

1.  Configuración del servidor VPN
2.  Configuración del cliente VPN 
3.  Configuración de la directiva de acceso condicional
4.  Comprobación


## <a name="before-you-begin"></a>Antes de empezar

En este tema se da por supuesto que está familiarizado con los temas siguientes:

- [Acceso condicional en Azure Active Directory](active-directory-conditional-access-azure-portal.md)
- [VPN y acceso condicional](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access)

También puede echar un vistazo a [Enhancing remote access in Windows 10 with an automatic VPN profile](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile) (Mejora del acceso remoto en Windows 10 con un perfil VPN automático) para obtener información acerca de cómo Microsoft ha implementado esta característica.   


## <a name="prerequisites"></a>Requisitos previos

Para configurar el acceso condicional de Azure Active Directory para la conectividad de redes privadas virtuales debe tener configurado un servidor VPN. 



## <a name="step-1---configure-your-vpn-server"></a>Paso 1: Configuración del servidor VPN 

El objetivo de este paso es configurar los certificados raíz para la autenticación de VPN con Azure AD. Para configurar el acceso condicional para la conectividad de redes privadas virtuales, debe:

1. Crear un certificado VPN en Azure Portal
2. Descargar el certificado VPN
2. Implementar el certificado en el servidor VPN

El certificado VPN es el emisor que usa Azure AD para firmar los certificados emitidos para los clientes de Windows 10 al autenticarse en Azure AD para la conectividad VPN. Imagine que el token que solicita el cliente de Windows 10 es un certificado que, a continuación, se presenta a la aplicación que, en este caso, es el servidor VPN.

![Acceso condicional](./media/active-directory-conditional-access-vpn-connectivity-windows10/06.png)

En Azure Portal, puede crear dos certificados para administrar las transiciones cuando un certificado está a punto de expirar. Cuando crea un certificado, puede elegir si este es el certificado principal. El certificado principal es el que realmente se usa durante la autenticación para firmar el certificado para la conexión.


**Para crear un certificado VPN:**

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador.

2. En la barra de navegación izquierda, haga clic en **Azure Active Directory**. 

    ![Conectividad VPN](./media/active-directory-conditional-access-vpn-connectivity-windows10/01.png)

3. En la página **Azure Active Directory**, en la sección **Administrar**, haga clic en **Acceso condicional**.

    ![Conectividad VPN](./media/active-directory-conditional-access-azure-portal-get-started/02.png)

4. En la página **Acceso condicional**, en la sección **Administrar**, haga clic en **Conectividad VPN (versión preliminar)**.

    ![Conectividad VPN](./media/active-directory-conditional-access-vpn-connectivity-windows10/03.png)

5. En la página **Conectividad VPN**, haga clic en **Nuevo certificado**.

    ![Conectividad VPN](./media/active-directory-conditional-access-vpn-connectivity-windows10/04.png)

6. En la página **Nuevo**, realice los pasos siguientes:

    ![Conectividad VPN](./media/active-directory-conditional-access-vpn-connectivity-windows10/05.png)

    a. Como **duración**, seleccione **1 año**.

    b. En **Principal**, seleccione **Sí**.

    c. Haga clic en **Crear**.

7. En la página Conectividad VPN, haga clic en **Descargar certificado**.


En este punto, está listo para implementar el certificado recién creado en el servidor VPN. En el servidor VPN, debe agregar el certificado descargado como un *certificado de entidad de certificación raíz de confianza para la autenticación VPN*.

Para implementaciones basadas en RRAS de Windows, en el servidor NPS, debe agregar el certificado raíz en el almacén *Enterprise NTauth* ejecutando los comandos siguientes:

1. `certutil -dspublish <CACERT> RootCA`
2. `certutil -dspublish <CACERT> NtAuthCA`



## <a name="step-2---configure-your-vpn-client"></a>Paso 2: Configuración del cliente VPN 

En este paso, debe configurar el perfil de conectividad del cliente VPN, tal como se describe en [VPN y acceso condicional](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access).


## <a name="step-3---configure-your-conditional-access-policy"></a>Paso 3: Configuración de la directiva de acceso condicional

Esta sección le proporciona instrucciones para configurar la directiva de acceso condicional para la conectividad VPN.

**Para configurar la directiva de acceso condicional:** 

1. En la página **Acceso condicional**, en la barra de herramientas de la parte superior, haga clic en **Agregar**.

    ![Acceso condicional](./media/active-directory-conditional-access-vpn-connectivity-windows10/07.png)

2. En la página **Nuevo**, en el cuadro de texto **Nombre**, escriba un nombre para la directiva, por ejemplo, **Directiva VPN**.

    ![Acceso condicional](./media/active-directory-conditional-access-vpn-connectivity-windows10/08.png)

5. En la sección **Asignación**, haga clic en **Usuarios y grupos**.

    ![Acceso condicional](./media/active-directory-conditional-access-vpn-connectivity-windows10/09.png)

6. En la página **Usuarios y grupos**, siga estos pasos:

    ![Acceso condicional](./media/active-directory-conditional-access-vpn-connectivity-windows10/10.png)

    a. Haga clic en **Seleccionar usuarios y grupos**.

    b. Haga clic en **Seleccionar**.

    c. En la página **Seleccionar**, seleccione el usuario de prueba y haga clic en **Seleccionar**.

    d. En la página **Usuarios y grupos**, haga clic en **Listo**.

7. En la página **Nuevo**, realice los pasos siguientes.

    ![Acceso condicional](./media/active-directory-conditional-access-vpn-connectivity-windows10/11.png)

    a. En la sección **Asignaciones**, haga clic en **Aplicaciones en la nube**.

    b. En la página **Aplicaciones en la nube**, haga clic en **Seleccionar aplicaciones** y, a continuación, haga clic en **Seleccionar**.

    c. En la página **Seleccionar**, en el cuadro de texto **Aplicaciones**, escriba **vpn**.

    d. Seleccione **Servidor VPN**.

    e. Haga clic en **Seleccionar**.


13. En la página **Nuevo**, en la sección **Controles**, haga clic en **Conceder** para abrir la página **Conceder**.

    ![Acceso condicional](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. En la página **Conceder**, siga estos pasos:

    ![Acceso condicional](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    a. Seleccione **Requerir autenticación multifactor**.

    b. Haga clic en **Seleccionar**.

15. En la página **Nuevo**, en **Habilitar directiva**, haga clic en **Activar**.

    ![Acceso condicional](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. En la página **Nuevo**, haga clic en **Crear**.



## <a name="next-steps"></a>Pasos siguientes

Consulte [Enhancing remote access in Windows 10 with an automatic VPN profile](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile) (Mejora del acceso remoto en Windows 10 con un perfil VPN automático) para obtener información acerca de cómo ha implementado Microsoft esta característica.    


---
title: "Acceso condicional de Azure Active Directory para la conectividad de VPN (versión preliminar) | Microsoft Docs"
description: "Más información acerca del funcionamiento del acceso condicional de Azure Active Directory para la conectividad de VPN. "
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 51a1ee61-3ffe-4f65-b8de-ff21903e1e74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 511df58befe9aed4aa65fc6944cae3a8e2c74c2d
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2018
---
# <a name="azure-active-directory-conditional-access-for-vpn-connectivity-preview"></a>Acceso condicional de Azure Active Directory para la conectividad de VPN (versión preliminar)

Con el [acceso condicional de Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), se puede ajustar la forma en que los usuarios autorizados acceden a los recursos. Con el acceso condicional de Azure AD para la conectividad de redes privadas virtuales (VPN) puede ayudar a proteger las conexiones VPN.


Para configurar el acceso condicional para la conectividad VPN, debe seguir estos pasos: 

1.  Configurar el servidor VPN.
2.  Configurar el cliente VPN.
3.  Configurar la directiva de acceso condicional.


## <a name="before-you-begin"></a>Antes de empezar

En este tema se da por supuesto que está familiarizado con los temas siguientes:

- [Acceso condicional en Azure Active Directory](active-directory-conditional-access-azure-portal.md)
- [VPN y acceso condicional](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access)

Consulte [Enhancing remote access in Windows 10 with an automatic VPN profile](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile) (Mejora del acceso remoto en Windows 10 con un perfil VPN automático) para obtener información acerca de la forma en que Microsoft implementa esta característica.   


## <a name="prerequisites"></a>requisitos previos

Para configurar el acceso condicional de Azure Active Directory para la conectividad de VPN debe tener configurado un servidor de VPN. 



## <a name="step-1-configure-your-vpn-server"></a>Paso 1: Configurar el servidor VPN 

En este paso se configuran los certificados raíz para la autenticación de VPN con Azure AD. Para configurar el acceso condicional para la conectividad de VPN, es preciso:

1. Crear un certificado VPN en Azure Portal.
2. Descargar el certificado VPN.
2. Implementar el certificado en un servidor de VPN.

Azure AD usa el certificado VPN para firmar los certificados emitidos para los clientes de Windows 10 al autenticarse en Azure AD para la conectividad de VPN. El token que solicita el cliente de Windows 10 es un certificado que, a continuación, se presenta a la aplicación que, en este caso, es el servidor VPN.

![Descargar certificado para acceso condicional](./media/active-directory-conditional-access-vpn-connectivity-windows10/06.png)

En Azure Portal, puede crear dos certificados para administrar la transición cuando un certificado está a punto de expirar. Al crear un certificado, se puede elegir si es el certificado principal, que se usa durante la autenticación para firmar el certificado para la conexión.

Para crear un certificado VPN:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador global.

2. En la barra de navegación izquierda, haga clic en **Azure Active Directory**. 

    ![Seleccione Azure Active Directory.](./media/active-directory-conditional-access-vpn-connectivity-windows10/01.png)

3. En la página **Azure Active Directory**, en la sección **Administrar**, haga clic en **Acceso condicional**.

    ![Seleccionar Acceso condicional](./media/active-directory-conditional-access-azure-portal-get-started/02.png)

4. En la página **Acceso condicional**, en la sección **Administrar**, haga clic en **Conectividad VPN (versión preliminar)**.

    ![Seleccionar Conectividad VPN](./media/active-directory-conditional-access-vpn-connectivity-windows10/03.png)

5. En la página **Conectividad VPN**, haga clic en **Nuevo certificado**.

    ![Seleccionar nuevo certificado](./media/active-directory-conditional-access-vpn-connectivity-windows10/04.png)

6. En la página **Nuevo**, realice los pasos siguientes:

    ![Seleccionar la duración y principal](./media/active-directory-conditional-access-vpn-connectivity-windows10/05.png)

    a. En **Seleccionar duración**, seleccione **1 año**.

    b. En **Principal**, seleccione **Sí**.

    c. Haga clic en **Create**(Crear).

7. En la página Conectividad VPN, haga clic en **Descargar certificado**.


Ya está listo para implementar el certificado recién creado en un servidor VPN. En el servidor VPN, agregue el certificado descargado como una *CA raíz de confianza para la autenticación de VPN*.

En el caso de las implementaciones realizadas a través de RRAS de Windows en un servidor de NPS, agregue el certificado raíz en el almacén *Enterprise NTauth*, para lo que debe ejecutar los comandos siguientes:

1. `certutil -dspublish <CACERT> RootCA`
2. `certutil -dspublish <CACERT> NtAuthCA`



## <a name="step-2-configure-your-vpn-client"></a>Paso 2: Configurar el cliente VPN 

En este paso se configura el perfil de conectividad del cliente VPN, tal como se describe en [VPN y acceso condicional](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access).


## <a name="step-3-configure-your-conditional-access-policy"></a>Paso 3: Configurar una directiva de acceso condicional

Esta sección le proporciona instrucciones para configurar la directiva de acceso condicional para la conectividad VPN.


1. En la página **Acceso condicional**, en la barra de herramientas de la parte superior, haga clic en **Agregar**.

    ![Seleccione Agregar en la página de acceso condicional](./media/active-directory-conditional-access-vpn-connectivity-windows10/07.png)

2. En la página **Nuevo**, en el cuadro **Nombre**, escriba el nombre de la directiva. Por ejemplo, escriba **VPN policy**.

    ![Agregar el nombre de la directiva en la página de acceso condicional](./media/active-directory-conditional-access-vpn-connectivity-windows10/08.png)

5. En la sección **Asignación**, haga clic en **Usuarios y grupos**.

    ![Seleccionar Usuarios y grupos](./media/active-directory-conditional-access-vpn-connectivity-windows10/09.png)

6. En la página **Usuarios y grupos**, siga estos pasos:

    ![Seleccionar un usuario de prueba](./media/active-directory-conditional-access-vpn-connectivity-windows10/10.png)

    a. Haga clic en **Seleccionar usuarios y grupos**.

    b. Haga clic en **Seleccionar**.

    c. En la página **Seleccionar**, seleccione el usuario de prueba y haga clic en **Seleccionar**.

    d. En la página **Usuarios y grupos**, haga clic en **Listo**.

7. En la página **Nuevo**, realice los pasos siguientes:

    ![Seleccionar aplicaciones en la nube](./media/active-directory-conditional-access-vpn-connectivity-windows10/11.png)

    a. En la sección **Asignaciones**, haga clic en **Aplicaciones en la nube**.

    b. En la página **Aplicaciones en la nube**, haga clic en **Seleccionar aplicaciones** y, a continuación, haga clic en **Seleccionar**.

    c. En la página **Seleccionar**, en el cuadro **Aplicaciones**, escriba **vpn**.

    d. Seleccione **Servidor VPN**.

    e. Haga clic en **Seleccionar**.


13. En la página **Nuevo**, en la sección **Controles**, haga clic en **Conceder** para abrir la página **Conceder**.

    ![Seleccionar Conceder](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. En la página **Conceder**, siga estos pasos:

    ![Seleccionar Requerir autenticación multifactor](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    a. Seleccione **Requerir autenticación multifactor**.

    b. Haga clic en **Seleccionar**.

15. En la página **Nuevo**, en **Habilitar directiva**, haga clic en **Activar**.

    ![Habilitar directiva](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. En la página **Nuevo**, haga clic en **Crear**.



## <a name="next-steps"></a>pasos siguientes

Consulte [Enhancing remote access in Windows 10 with an automatic VPN profile](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile) (Mejora del acceso remoto en Windows 10 con un perfil VPN automático) para obtener información acerca de la forma en que Microsoft implementa esta característica.    


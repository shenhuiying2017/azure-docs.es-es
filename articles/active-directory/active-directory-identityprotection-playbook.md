---
title: "Guía de Azure Active Directory Identity Protection | Microsoft Docs"
description: "Aprenda cómo Azure AD Identity Protection permite limitar la capacidad de un atacante para aprovechar una identidad o un dispositivo en peligro y asegurar una identidad o un dispositivo que antes fue sospechoso o que se sabe que estuvo en peligro."
services: active-directory
keywords: "azure active directory identity protection, detección de aplicaciones en la nube, administración de aplicaciones, seguridad, riesgo, nivel de riesgo, punto vulnerable, directiva de seguridad"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 60836abf-f0e9-459d-b344-8e06b8341d25
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: f4240c9196796c2e83c408271fe81b20842ab722
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="azure-active-directory-identity-protection-playbook"></a>Guía de Azure Active Directory Identity Protection

Esta guía le ayudará a:

* Rellenar los datos en el entorno de Identity Protection mediante la simulación de vulnerabilidades y eventos de riesgo
* Configurar directivas de acceso condicional en función del riesgo y probar el impacto de estas directivas


## <a name="simulating-risk-events"></a>Simulación de eventos de riesgo

En esta sección se indican los pasos para simular los siguientes tipos de eventos de riesgo:

* Inicios de sesión desde direcciones IP anónimas (fácil)
* Inicios de sesión desde ubicaciones desconocidas (moderada)
* Viaje imposible a ubicaciones inusuales (difícil)

Otros eventos de riesgo no se pueden simular de forma segura.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Inicios de sesión desde direcciones IP anónimas

Para obtener más información acerca de este evento de riesgo, vea [Inicios de sesión desde direcciones IP anónimas](active-directory-reporting-risk-events.md#sign-ins-from-anonymous-ip-addresses). 

Para completar el procedimiento siguiente, necesitará:

- [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en) para simular direcciones IP anónimas. Es posible que necesite usar una máquina virtual si su organización restringe el uso de Tor Browser.
- Todavía no hay ninguna cuenta de prueba registrada para la autenticación multifactor.

**Para simular un inicio de sesión desde una IP anónima, realice los siguientes pasos**:

1. Use [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en) para ir a [https://myapps.microsoft.com](https://myapps.microsoft.com).   
2. Escriba las credenciales de la cuenta que desee que aparezcan en el informe **Inicios de sesión desde direcciones IP anónimas** .

El inicio de sesión se mostrará en el panel de Identity Protection en un plazo máximo de 10 o 15 minutos. 

### <a name="sign-ins-from-unfamiliar-locations"></a>Inicios de sesión desde ubicaciones desconocidas

Para obtener más información acerca de este evento de riesgo, vea [Inicios de sesión desde ubicaciones desconocidas](active-directory-reporting-risk-events.md#sign-in-from-unfamiliar-locations). 

Para simular ubicaciones desconocidas, debe iniciar sesión desde una ubicación y un dispositivo desde los que no se haya iniciado sesión en esa cuenta de prueba antes.

El siguiente procedimiento usa:

- Una conexión VPN recién creada, para simular la nueva ubicación.

- Una máquina virtual recién creada, para simular un dispositivo nuevo.

Para completar el procedimiento siguiente, necesitará una cuenta de usuario con:

- Al menos un historial de inicio de sesión de 30 días.
- Autenticación multifactor habilitada.


**Para simular un inicio de sesión desde una ubicación desconocida, realice los siguientes pasos**:

1. Al iniciar sesión en su cuenta de prueba, no supere el desafío MFA.
2. Mediante la VPN nueva, navegue hasta [https://myapps.microsoft.com](https://myapps.microsoft.com) y escriba las credenciales de la cuenta de prueba.
   

El inicio de sesión se mostrará en el panel de Identity Protection en un plazo máximo de 10 o 15 minutos.

### <a name="impossible-travel-to-atypical-location"></a>Viaje imposible a una ubicación inusual

Para obtener más información acerca de este evento de riesgo, vea [Viaje imposible a una ubicación inusual](active-directory-reporting-risk-events.md#impossible-travel-to-atypical-locations). 

Simular la condición de viaje imposible es complicado porque el algoritmo utiliza el aprendizaje automático para descartar falsos positivos, como un viaje imposible desde dispositivos conocidos o inicios de sesión de VPN usadas por otros usuarios del directorio. Además, el algoritmo requiere un historial de inicios de sesión de 14 días y 10 inicios de sesión del usuario antes de empezar a generar eventos de riesgo. Debido a los complejos modelos de aprendizaje automático y a las reglas anteriores, es probable que los pasos siguientes no den lugar a un evento de riesgo. Puede replicar estos pasos para varias cuentas de Azure AD para publicar este evento de riesgo.


**Para simular un viaje imposible a una ubicación inusual, realice los pasos siguientes**:

1. Mediante el explorador estándar, vaya a [https://myapps.microsoft.com](https://myapps.microsoft.com).  
2. Escriba las credenciales de la cuenta para la que desea generar un evento de riesgo de viaje imposible.
3. Cambie el agente de usuario. Puede cambiar el agente de usuario en Internet Explorer desde Herramientas de desarrollo, o bien en Firefox o Chrome con un complemento modificador del agente de usuario.
4. Cambie la dirección IP. Puede cambiar la dirección IP mediante una VPN, un complemento de Tor, o iniciando una máquina nueva en Azure en otro centro de datos.
5. Inicie sesión en [https://myapps.microsoft.com](https://myapps.microsoft.com) con las mismas credenciales que antes y pocos minutos después del inicio de sesión anterior.

El inicio de sesión se mostrará en el panel de Identity Protection entre 2 y 4 horas después.

## <a name="simulating-vulnerabilities"></a>Simulación de puntos vulnerables
Los puntos vulnerables son puntos débiles de un entorno de Azure AD que puede ser aprovechados por un actor perjudicial. Actualmente se exponen 3 tipos de puntos vulnerables en Azure AD Identity Protection que aprovechan otras características de Azure AD. Estos puntos vulnerables se mostrará en el panel de Identity Protection automáticamente una vez configuradas estas características.

* Azure AD [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)
* Azure AD [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md).
* [Privileged Identity Management](active-directory-privileged-identity-management-configure.md)de Azure AD. 


## <a name="testing-security-policies"></a>Prueba de las directivas de seguridad

En esta sección se proporcionan los pasos necesarios para probar el riesgo de usuario y la directiva de seguridad de riesgo de inicio de sesión.


### <a name="user-risk-security-policy"></a>Directiva de seguridad de riesgo del usuario

Para obtener más información, consulte [Directiva de seguridad de riesgo del usuario](active-directory-identityprotection.md#user-risk-security-policy).

![Guía de](./media/active-directory-identityprotection-playbook/02.png "riesgo del usuario")


**Para probar una directiva de seguridad de riesgo del usuario, realice los pasos siguientes**:

1. Inicie sesión en [https://portal.azure.com](https://portal.azure.com) con las credenciales de administrador global para el inquilino.
2. Vaya a **Identity Protection**. 
3. En la página **Azure AD Identity Protection**, haga clic en **Directiva de riesgo de usuario**.
4. En la sección **Asignaciones**, seleccione los usuarios (y grupos) deseados y el nivel de riesgo del usuario.

    ![Guía de](./media/active-directory-identityprotection-playbook/03.png "riesgo del usuario")

5. En la sección Controles, seleccione el control de acceso deseado (por ejemplo, Requerir cambio de contraseña).
5. En **Aplicar directiva**, seleccione **Desactivar**.
6. Eleve el riesgo del usuario de una cuenta de prueba simulando, por ejemplo, uno de los eventos de riesgo varias veces.
7. Espere unos minutos y, después, compruebe que el nivel de usuario para el usuario sea Medio. De lo contrario, simule más eventos de riesgo para el usuario.
8. En **Aplicar directiva**, seleccione **Activar**.
9. Ahora puede probar el acceso condicional basado en riesgos del usuario mediante un inicio de sesión con un usuario con un nivel de riesgo elevado.
    
    

### <a name="sign-in-risk-security-policy"></a>Directiva de seguridad de riesgo de inicio de sesión

Para obtener más información, consulte [Directiva de seguridad de riesgo del usuario](active-directory-identityprotection.md#user-risk-security-policy).

![Guía de](./media/active-directory-identityprotection-playbook/01.png "riesgo de inicio de sesión")


**Para probar la directiva de riesgo de inicio de sesión, realice los pasos siguientes:**

1. Inicie sesión en [https://portal.azure.com ](https://portal.azure.com) con las credenciales de administrador global para el inquilino.

2. Vaya a **Azure AD Identity Protection**.

3. En la página principal **Azure AD Identity Protection**, haga clic en **Directiva de riesgo de inicio de sesión**. 

4. En la sección **Asignaciones**, seleccione los usuarios (y grupos) deseados y el nivel de riesgo de inicio de sesión.

    ![Guía de](./media/active-directory-identityprotection-playbook/04.png "riesgo de inicio de sesión")


5. En la sección **Controles**, seleccione el control de acceso deseado (por ejemplo, **Requerir autenticación multifactor**). 

6. En **Aplicar directiva**, seleccione **Activar**.

7. Haga clic en **Save**(Guardar).

8. Ahora puede probar el acceso condicional basado en riesgo de inicio de sesión mediante el inicio de sesión con una sesión de riesgo (por ejemplo, mediante Tor Browser). 

 




## <a name="see-also"></a>Otras referencias

- [Azure Active Directory Identity Protection](active-directory-identityprotection.md)


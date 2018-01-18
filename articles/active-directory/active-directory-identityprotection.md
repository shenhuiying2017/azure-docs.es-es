---
title: Azure Active Directory Identity Protection | Microsoft Docs
description: "Aprenda cómo Azure AD Identity Protection permite limitar la capacidad de un atacante para aprovechar una identidad o un dispositivo en peligro y asegurar una identidad o un dispositivo que antes fue sospechoso o que se sabe que estuvo en peligro."
services: active-directory
keywords: "azure active directory identity protection, detección de aplicaciones en la nube, administración de aplicaciones, seguridad, riesgo, nivel de riesgo, punto vulnerable, directiva de seguridad"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: e66d033d95efccf53ea2de889b5811fe2eafb76a
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/13/2018
---
# <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

Azure Active Directory Identity Protection es una característica de Azure AD Premium P2 Edition que le permite:

- Detectar posibles vulnerabilidades que afectan a las identidades de la organización

- Configurar respuestas automatizadas a acciones sospechosas detectadas que están relacionadas con las identidades de la organización  

- Investigar incidentes sospechosos y tomar las medidas adecuadas para resolverlos   


## <a name="getting-started"></a>Introducción

Microsoft lleva más de una década con identidades basadas en la nube protegidas. Con Azure Active Directory Identity Protection en su entorno, puede usar los mismos sistemas de protección que Microsoft utiliza para proteger las identidades.

La mayoría de las infracciones de seguridad tienen lugar cuando los atacantes obtienen acceso a un entorno mediante el robo de identidad de un usuario. Con los años, los atacantes son cada vez más eficaces al aprovechar las brechas de otros fabricantes y el uso de ataques de suplantación de identidad sofisticados. Una vez que un atacante obtiene acceso aunque sea a una cuenta de usuario con pocos privilegios, es relativamente sencillo para ellos obtener acceso a recursos importantes de la empresa mediante el desplazamiento lateral.

Como consecuencia de esto, debe:

- Proteger todas las identidades independientemente de su nivel de privilegios

- Evitar de forma proactiva que se haga mal uso de las identidades que están en peligro.

Descubrir las identidades en peligro no es tarea fácil. Azure Active Directory utiliza algoritmos y heurística de aprendizaje automático adaptable para detectar anomalías e incidentes sospechosos que indican identidades que pueden estar en peligro. Con estos datos, Identity Protection genera informes y alertas que le permiten evaluar los problemas y tomar las acciones de corrección o mitigación adecuadas.

Azure Active Directory Identity Protection es más que una herramienta de supervisión e informes. Para proteger las identidades de la organización, puede configurar directivas basadas en riesgos que respondan automáticamente a problemas detectados si se alcanza un nivel de riesgo específico. Estas directivas, además de otros controles de acceso condicional proporcionados por Azure Active Directory y EMS, pueden bloquear automáticamente o iniciar acciones de corrección adaptables que incluyen el restablecimiento de contraseñas y el cumplimiento de la autenticación multifactor.


#### <a name="identity-protection-capabilities"></a>Funcionalidades de Identity Protection

**Detección de vulnerabilidades y cuentas peligrosas:**  

* Proporcionar recomendaciones personalizadas para mejorar la posición de seguridad general al resaltar los puntos vulnerables
* Cálculo de los niveles de riesgo de inicio de sesión
* Calcular los niveles de riesgo del usuario


**Investigación de los eventos de riesgo:**

* Enviar notificaciones de los eventos de riesgo
* Investigar los eventos de riesgo con información pertinente y contextual
* Proporcionar los flujos de trabajo básicos para realizar un seguimiento de las investigaciones
* Proporcionar un fácil acceso a las acciones de corrección, como el restablecimiento de contraseñas

**Directivas de acceso condicional basadas en riesgos:**

* Directiva para mitigar inicios de sesión peligrosos al bloquear inicios de sesión o solicitar desafíos de la autenticación multifactor.
* Directiva para proteger o bloquear cuentas de usuario peligrosas
* Directiva para exigir que los usuarios se registren en la autenticación multifactor



## <a name="identity-protection-roles"></a>Roles de Identity Protection

Para equilibrar la carga de las actividades de administración en torno a su implementación de Identity Protection, puede asignar varios roles. Azure AD Identity Protection admite tres roles de directorio:

| Rol                         | Puede hacer                          | No puede hacer
| :--                          | ---                                |  ---   |
| Administrador global         | Acceso completo a Identity Protection, incorporar Identity Protection| |
| Administrador de seguridad       | Acceso completo a Identity Protection | Incorporar Identity Protection, restablecer contraseñas para un usuario |
| Lector de seguridad              | Acceso de solo lectura a Identity Protection | Incorporar Identity Protection, remediar usuarios, configurar directivas, restablecer contraseñas |




Para obtener más detalles, consulte [Asignación de roles de administrador en Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md)



## <a name="detection"></a>Detección

### <a name="vulnerabilities"></a>Puntos vulnerables

Azure Active Directory Identity Protection analiza la configuración y detecta las vulnerabilidades que pueden afectar a las identidades de usuario. Para conocer más detalles, consulte [Vulnerabilidades detectadas por Azure Active Directory Identity Protection](active-directory-identityprotection-vulnerabilities.md).

### <a name="risk-events"></a>Eventos de riesgo

Azure Active Directory utiliza algoritmos y heurística de aprendizaje automático adaptable para detectar acciones sospechosas que están relacionadas con las identidades de usuario. El sistema crea un registro para cada acción sospechosa detectada. Estos registros se conocen también como eventos de riesgo.  
Para más información, consulte [Azure Active Directory risk events](active-directory-identity-protection-risk-events.md) (Eventos de riesgo de Azure Active Directory).


## <a name="investigation"></a>Investigación
Normalmente su viaje a través de Identity Protection empieza en el panel de Identity Protection.

![Corrección](./media/active-directory-identityprotection/1000.png "Corrección")

El panel proporciona acceso a:

* Informes como **Usuarios marcados en riesgo**, **Eventos de riesgo** y **Vulnerabilidades**.
* Opciones como la configuración de sus **directivas de seguridad**, **notificaciones** y el **registro de la autenticación multifactor**.

Normalmente, es el punto de partida para la investigación, que es el proceso de revisión de las actividades, los registros y otra información pertinente relacionada con un evento de riesgo para decidir si son necesarios pasos de mitigación o de corrección. En la investigación, también se busca averiguar cómo se ha puesto en peligro la identidad, así como comprender cómo se usó la identidad en peligro.

Puede vincular las actividades de investigación a las [notificaciones](active-directory-identityprotection-notifications.md) que Azure Active Directory Identity Protection envía por correo electrónico.

En las secciones siguientes se proporcionan más detalles y los pasos que están relacionados con una investigación.  


## <a name="risky-sign-ins"></a>Inicios de sesión no seguros

Azure Active Directory detecta [tipos de eventos de riesgo](active-directory-reporting-risk-events.md#risk-event-types) en tiempo real y sin conexión. Cada evento de riesgo que se haya detectado durante el inicio de sesión de un usuario contribuye a un concepto lógico, llamado inicio de sesión peligroso. Un inicio de sesión peligroso es un indicador de un intento de inicio de sesión que puede haber realizado alguien que no es el propietario legítimo de una cuenta de usuario.


### <a name="sign-in-risk-level"></a>Nivel de riesgo del inicio de sesión

El nivel de riesgo del inicio de sesión es un indicador (alto, medio o bajo) de la probabilidad que hay de que el legítimo propietario de una cuenta de usuario no haya realizado un intento de inicio de sesión.

### <a name="mitigating-sign-in-risk-events"></a>Mitigación de eventos de riesgo de inicio de sesión

Una mitigación es una acción para limitar la capacidad de un atacante de aprovechar una identidad o un dispositivo en peligro sin restaurar la identidad o el dispositivo a un estado seguro. Una mitigación no resuelve los anteriores eventos de riesgo de inicio de sesión asociados a la identidad o el dispositivo.

Para mitigar los inicios de sesión peligrosos automáticamente, puede configurar directivas de seguridad para inicios de sesión peligrosos. Mediante estas directivas, tenga en cuenta el nivel de riesgo del usuario o el inicio de sesión para bloquear inicios de sesión peligrosos o solicite que el usuario realice la autenticación multifactor. Estas acciones pueden evitar que un atacante pueda aprovechar una identidad robada para causar daños, y pueden darle algún tiempo para proteger la identidad.

### <a name="sign-in-risk-security-policy"></a>Directiva de seguridad de riesgo de inicio de sesión
Una directiva de riesgo de inicio de sesión es una directiva de acceso condicional que evalúa el riesgo de un inicio de sesión concreto y aplica mitigaciones de acuerdo con las reglas y condiciones predefinidas.

![Directiva de riesgo de inicio de sesión](./media/active-directory-identityprotection/1014.png "Directiva de riesgo de inicio de sesión")

Azure AD Identity Protection ayuda a administrar la mitigación de inicios de sesión peligrosos al permitir:

* Configurar los usuarios y grupos a los que se aplica la directiva:

    ![Directiva de riesgo de inicio de sesión](./media/active-directory-identityprotection/1015.png "Directiva de riesgo de inicio de sesión")
* Establecer el umbral de nivel de riesgo del inicio de sesión (bajo, medio o alto) que desencadena la directiva:

    ![Directiva de riesgo de inicio de sesión](./media/active-directory-identityprotection/1016.png "Directiva de riesgo de inicio de sesión")
* Establecer los controles que se aplicará cuando se desencadene la directiva:  

    ![Directiva de riesgo de inicio de sesión](./media/active-directory-identityprotection/1017.png "Directiva de riesgo de inicio de sesión")
* Cambiar el estado de la directiva:

    ![Registro MFA](./media/active-directory-identityprotection/403.png "Registro MFA")
* Revisar y evaluar el impacto de un cambio antes de activarlo:

    ![Directiva de riesgo de inicio de sesión](./media/active-directory-identityprotection/1018.png "Directiva de riesgo de inicio de sesión")

#### <a name="what-you-need-to-know"></a>Lo que necesita saber
Puede configurar una directiva de seguridad de riesgo de inicio de sesión para requerir la autenticación multifactor:

![Directiva de riesgo de inicio de sesión](./media/active-directory-identityprotection/1017.png "Directiva de riesgo de inicio de sesión")

Sin embargo, por seguridad, esta configuración solo funciona para los usuarios que ya se hayan registrado para la autenticación multifactor. Si la condición de requerir la autenticación multifactor la cumple un usuario que aún no se ha registrado para ella, el usuario queda bloqueado.

Como procedimiento recomendado, si desea requerir la autenticación multifactor para los inicios de sesión arriesgados, debe hacer lo siguiente:

1. Habilite la [directiva de registro de la autenticación multifactor](#multi-factor-authentication-registration-policy) para los usuarios afectados.
2. Requiera que los usuarios afectados inicien una sesión sin riesgo para llevar a cabo el registro en MFA.

Tras completar estos pasos, se garantiza que la autenticación multifactor sea necesaria para un inicio de sesión de riesgo.

#### <a name="best-practices"></a>Procedimientos recomendados
Elegir un umbral **Alto** reduce el número de veces que una directiva se desencadena y minimiza el impacto en los usuarios.  

Sin embargo, excluye de la directiva a los usuarios marcados con un riesgo **Bajo** y **Medio**, por lo que es posible que no impida que un atacante aproveche una identidad en peligro.

Al establecer la directiva:

* Excluya a los usuarios que no tengan o no puedan tener la autenticación multifactor.
* Excluya a los usuarios que tengan configuraciones regionales en las que no resulte práctico habilitar la directiva (por ejemplo, no tienen acceso al departamento de soporte técnico).
* Excluya a los usuarios que es probable que generen una gran cantidad de falsos positivos (desarrolladores, analistas de seguridad).
* Utilice un umbral **Alto** durante la puesta en servicio inicial de la directiva, o bien si debe minimizar los desafíos que ven los usuarios finales.
* Use un umbral **Bajo** si su organización requiere un mayor grado de seguridad. Seleccionar un umbral **Bajo** presenta desafíos adicionales de inicio de sesión del usuario, pero aumenta la seguridad.

El valor predeterminado recomendado para la mayoría de las organizaciones es configurar una regla para un umbral **Medio** con el fin de lograr un equilibrio entre la facilidad de uso y la seguridad.

La directiva de riesgo de inicio de sesión:

* Se aplica a todo el tráfico del explorador e inicios de sesión mediante una autenticación moderna.
* No afecta a aplicaciones que utilizan protocolos de seguridad anteriores al deshabilitar el punto de conexión WS-Trust en el IDP federado, como ADFS.

La página **Eventos de riesgo** de la consola de Identity Protection enumera todos los eventos:

* A los que se aplicó esta directiva.
* Puede revisar la actividad y determinar si la acción era apropiada o no.

Para obtener información general de la experiencia de usuario relacionada, consulte:

* [Recuperación de inicios de sesión peligrosos](active-directory-identityprotection-flows.md#risky-sign-in-recovery)
* [Inicios de sesión peligrosos bloqueados](active-directory-identityprotection-flows.md#risky-sign-in-blocked)  
* [Experiencias de inicio de sesión con Azure AD Identity Protection](active-directory-identityprotection-flows.md)  

**Para abrir el cuadro de diálogo de configuración relacionado, siga estos pasos**:

- En la hoja **Azure AD Identity Protection**, en la sección **Configurar**, haga clic en **Directiva de riesgo de inicio de sesión**.

    ![Directiva de riesgo de usuario](./media/active-directory-identityprotection/1014.png "Directiva de riesgo de usuario")



## <a name="users-flagged-for-risk"></a>Usuarios marcados con riesgo

Todos los [eventos de riesgo](active-directory-identity-protection-risk-events.md) activos que detectó Azure Active Directory para un usuario contribuyen a la creación de un concepto lógico que se denomina riesgo de usuario. Un usuario marcado como en peligro es un indicador de una cuenta de usuario que puede haber estado en peligro.

![Usuarios marcados con riesgo](./media/active-directory-identityprotection/1200.png)


### <a name="user-risk-level"></a>Nivel de riesgo del usuario

El nivel de riesgo de un usuario es una indicación (Alto, Medio o Bajo) de la probabilidad de que se haya puesto en peligro la identidad del usuario. Se calcula en función de los eventos de riesgo del usuario que están asociados a una identidad del usuario.

El estado de un evento de riesgo es **Activo** o **Cerrado**. Solo los eventos de riesgo con el estado **Activo** contribuyen al cálculo del nivel de riesgo del usuario.

El nivel de riesgo del usuario se calcula con las siguientes entradas:

* Eventos de riesgo activos que afectan al usuario
* Nivel de riesgo de estos eventos
* Si se ha tomado alguna acción de corrección

![Riesgos de usuario](./media/active-directory-identityprotection/1031.png "Riesgos de usuario")

Puede utilizar los niveles de riesgo del usuario para crear directivas de acceso condicional para impedir que los usuarios peligrosos inicien sesión u obligarles a cambiar su contraseña de forma segura.

### <a name="closing-risk-events-manually"></a>Cierre manual de eventos de riesgo

En la mayoría de los casos, realizará las acciones de corrección, como restablecer una contraseña segura, para cerrar automáticamente los eventos de riesgo. Sin embargo, esto no siempre es posible.  
Por ejemplo, esto sucede cuando:

* Un usuario con eventos de riesgo en estado Activo se ha eliminado.
* Una investigación revela que el usuario legítimo ha realizado un evento de riesgo notificado.

Dado que los eventos de riesgo que tienen el estado **Activo** contribuyen al cálculo de riesgo del usuario, puede que tenga que reducir manualmente un nivel de riesgo al cerrar manualmente los eventos de riesgo.  
Durante el curso de la investigación, puede elegir realizar cualquiera de estas acciones para cambiar el estado de un evento de riesgo:

![Acciones](./media/active-directory-identityprotection/34.png "Acciones")

* **Resolver** : si, después de investigar un evento de riesgo, ha tomado una acción de corrección adecuada fuera de Identity Protection y cree que se debe considerar el evento de riesgo cerrado, marque el evento como resuelto. Los eventos resueltos establecerán el estado del evento de riesgo en Cerrado y ya no contribuirá al riesgo del usuario.
* **Marcar como falso positivo** : en algunos casos, puede investigar un evento de riesgo y detectar que se ha marcado incorrectamente como peligroso. Para ayudar a reducir el número de dichos casos, puede marcar el evento de riesgo como falso positivo. Esto ayudará a los algoritmos de aprendizaje automático a mejorar la clasificación de eventos similares en el futuro. El estado de los eventos con falsos positivos es **Cerrado** y ya no contribuyen al riesgo del usuario.
* **Omitir** : si no se ha tomado ninguna acción de corrección, pero desea que el evento de riesgo se quite de la lista activa, puede seleccionar Omitir para un evento de riesgo, cuyo estado será Cerrado. Los eventos omitidos no contribuyen al riesgo del usuario. Esta opción solo debe utilizarse en circunstancias inusuales.
* **Reactivar**: los eventos de riesgo que se cierran manualmente (al elegir **Resolver**, **Falso positivo** u **Omitir**) se pueden reactivar, lo que vuelve a establecer el estado del evento en **Activo**. Los eventos de riesgo reactivados contribuyen al cálculo del nivel de riesgo del usuario. Los eventos de riesgo cerrados mediante una corrección (como el restablecimiento de una contraseña segura) no se pueden reactivar.

**Para abrir el cuadro de diálogo de configuración relacionado**:

1. En la hoja **Azure AD Identity Protection**, en **Investigar**, haga clic en **Eventos de riesgo**.

    ![Restablecimiento manual de la contraseña](./media/active-directory-identityprotection/1002.png "Restablecimiento manual de la contraseña")
2. En la lista de **eventos de riesgo** , haga clic en uno de ellos.

    ![Restablecimiento manual de la contraseña](./media/active-directory-identityprotection/1003.png "Restablecimiento manual de la contraseña")
3. En la hoja del riesgo, haga clic con el botón derecho en un usuario.

    ![Restablecimiento manual de la contraseña](./media/active-directory-identityprotection/1004.png "Restablecimiento manual de la contraseña")

### <a name="closing-all-risk-events-for-a-user-manually"></a>Cierre manual de todos los eventos de riesgo de un usuario
En lugar de cerrar manualmente los eventos de riesgo de un usuario de uno en uno, Azure Active Directory Identity Protection también proporciona un método para cerrar todos los eventos de un usuario con un solo clic.

![Acciones](./media/active-directory-identityprotection/2222.png "Acciones")

Al hacer clic en **Dismiss all events**(Descartar todos los eventos), se cierran todos los eventos y el usuario afectado deja de correr riesgo.

### <a name="remediating-user-risk-events"></a>Corrección de eventos de riesgo del usuario

Una corrección es una acción para proteger una identidad o un dispositivo que antes fue sospechoso o que se sabe que estuvo en peligro. Una acción de corrección restaura la identidad o el dispositivo a un estado seguro y resuelve los anteriores eventos de riesgo asociados a la identidad o el dispositivo.

Para corregir los eventos de riesgo del usuario, puede:

* Realizar un restablecimiento de contraseña segura para corregir manualmente los eventos de riesgo del usuario.
* Configurar una directiva de seguridad de riesgo del usuario para mitigar o corregir automáticamente los eventos de riesgo del usuario.
* Recrear la imagen del dispositivo infectado.  

#### <a name="manual-secure-password-reset"></a>Restablecimiento manual de una contraseña segura
Restablecer una contraseña segura es una corrección eficaz para muchos de los eventos de riesgo y, cuando se realiza, estos eventos de riesgo cierran y vuelven a calcular automáticamente el nivel de riesgo del usuario. Puede utilizar el panel de Identity Protection para iniciar el restablecimiento de la contraseña de un usuario en peligro.

El cuadro de diálogo relacionado proporciona dos métodos diferentes de restablecer una contraseña:

**Restablecer contraseña**: seleccione **Require user to reset password** (Solicitar al usuario que restablezca la contraseña) para permitir que el usuario se recupere de forma automática si se ha registrado para la autenticación multifactor. Durante el siguiente inicio de sesión del usuario, el usuario debe resolver un desafío de autenticación multifactor correctamente y, a continuación, debe cambiar la contraseña. Esta opción no está disponible si la cuenta de usuario aún no tiene registrada la autenticación multifactor.

**Contraseña temporal**: seleccione **Generar una contraseña temporal** para invalidar inmediatamente la contraseña existente y crear una temporal para el usuario. Envíe la nueva contraseña temporal a una dirección de correo electrónico alternativa para el usuario o el administrador del usuario. Dado que la contraseña es temporal, se le pedirá al usuario que cambie la contraseña al iniciar sesión.

![Directiva](./media/active-directory-identityprotection/1005.png "Directiva")

**Para abrir el cuadro de diálogo de configuración relacionado, siga estos pasos**:

1. En la hoja **Azure AD Identity Protection**, haga clic en **Usuarios marcados en riesgo**.

    ![Restablecimiento manual de la contraseña](./media/active-directory-identityprotection/1006.png "Restablecimiento manual de la contraseña")
2. En la lista de usuarios, seleccione un usuario que tenga al menos un evento de riesgo.

    ![Restablecimiento manual de la contraseña](./media/active-directory-identityprotection/1007.png "Restablecimiento manual de la contraseña")
3. En la hoja del usuario, haga clic en **Reset password**(Restablecer contraseña).

    ![Restablecimiento manual de la contraseña](./media/active-directory-identityprotection/1008.png "Restablecimiento manual de la contraseña")

### <a name="user-risk-security-policy"></a>Directiva de seguridad de riesgo del usuario
Una directiva de seguridad de riesgo del usuario es una directiva de acceso condicional que evalúa el nivel de riesgo de un usuario específico y aplica acciones de corrección y mitigación según reglas y condiciones predefinidas.

![Directiva de riesgo de usuario](./media/active-directory-identityprotection/1009.png "Directiva de riesgo de usuario")

Azure AD Identity Protection ayuda a administrar la mitigación y corrección de usuarios marcados como de riesgo al permitirle:

* Configurar los usuarios y grupos a los que se aplica la directiva:

    ![Directiva de riesgo de usuario](./media/active-directory-identityprotection/1010.png "Directiva de riesgo de usuario")
* Establecer el umbral de nivel de riesgo del usuario (bajo, medio o alto) que desencadena la directiva:

    ![Directiva de riesgo de usuario](./media/active-directory-identityprotection/1011.png "Directiva de riesgo de usuario")
* Establecer los controles que se aplicará cuando se desencadene la directiva:

    ![Directiva de riesgo de usuario](./media/active-directory-identityprotection/1012.png "Directiva de riesgo de usuario")
* Cambiar el estado de la directiva:

    ![Directiva de riesgo de usuario](./media/active-directory-identityprotection/403.png "Registro MFA")
* Revisar y evaluar el impacto de un cambio antes de activarlo:

    ![Directiva de riesgo de usuario](./media/active-directory-identityprotection/1013.png "Directiva de riesgo de usuario")

Elegir un umbral **Alto** reduce el número de veces que una directiva se desencadena y minimiza el impacto en los usuarios.
Sin embargo, excluye de la directiva a los usuarios marcados en riesgo **Bajo** y **Medio**, por lo que es posible que no se protejan las identidades o los dispositivos bajo sospecha o con certeza de haber estado en peligro.

Al establecer la directiva:

* Excluya a los usuarios que es probable que generen una gran cantidad de falsos positivos (desarrolladores, analistas de seguridad).
* Excluya a los usuarios que tengan configuraciones regionales en las que no resulte práctico habilitar la directiva (por ejemplo, no tienen acceso al departamento de soporte técnico).
* Utilice un umbral **Alto** durante la puesta en servicio inicial de la directiva, o bien si debe minimizar los desafíos que ven los usuarios finales.
* Utilice un umbral **Bajo** si su organización requiere un mayor grado de seguridad. Seleccionar un umbral **Bajo** presenta desafíos adicionales de inicio de sesión del usuario, pero aumenta la seguridad.

El valor predeterminado recomendado para la mayoría de las organizaciones es configurar una regla para un umbral **Medio** con el fin de lograr un equilibrio entre la facilidad de uso y la seguridad.

Para obtener información general de la experiencia de usuario relacionada, consulte:

* [Flujo de recuperación de cuentas en peligro](active-directory-identityprotection-flows.md#compromised-account-recovery).  
* [Flujo de cuentas en peligro bloqueadas](active-directory-identityprotection-flows.md#compromised-account-blocked).  

**Para abrir el cuadro de diálogo de configuración relacionado, siga estos pasos**:

- En la hoja **Azure AD Identity Protection**, en la sección **Configurar**, haga clic en **Directiva de riesgo de usuario**.

    ![Directiva de riesgo de usuario](./media/active-directory-identityprotection/1009.png "Directiva de riesgo de usuario")

### <a name="mitigating-user-risk-events"></a>Mitigación de los eventos de riesgo del usuario
Los administradores pueden establecer una directiva de seguridad de riesgo del usuario para bloquear a los usuarios al iniciar sesión en función del nivel de riesgo.

El bloqueo de un inicio de sesión:

* Evita la generación de nuevos eventos de riesgo del usuario para el usuario afectado.
* Permite a los administradores corregir manualmente los eventos de riesgo que afectan a la identidad del usuario y restaurarla a un estado seguro.



## <a name="multi-factor-authentication-registration-policy"></a>Directiva de registro de la autenticación multifactor
Azure Multi-Factor Authentication es un método que se usa para comprobar la identidad del usuario que requiere usar algo más que un nombre de usuario y una contraseña. Proporciona una segunda capa de seguridad a los inicios de sesión y transacciones de los usuarios.  
Se recomienda requerir Azure Multi-Factor Authentication en los inicios de sesión de usuario por los siguientes motivos:

* Ofrece autenticación segura con una gama de opciones de comprobación sencillas.
* Desempeña un papel fundamental en la preparación de su organización para protegerse y recuperarse frente a cuentas comprometidas.

![Directiva de riesgo de usuario](./media/active-directory-identityprotection/1019.png "Directiva de riesgo de usuario")

Para obtener más información, consulte [Qué es Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)

Azure AD Identity Protection ayuda a administrar la puesta en servicio del registro de autenticación multifactor al configurar una directiva que le permite:

* Configurar los usuarios y grupos a los que se aplica la directiva:

    ![Directiva de MFA](./media/active-directory-identityprotection/1020.png "Directiva de MFA")
* Establecer los controles que se aplicará cuando se desencadene la directiva:  

    ![Directiva de MFA](./media/active-directory-identityprotection/1021.png "Directiva de MFA")
* Cambiar el estado de la directiva:

    ![Directiva de MFA](./media/active-directory-identityprotection/403.png "Directiva de MFA")
* Ver el estado actual del registro:

    ![Directiva de MFA](./media/active-directory-identityprotection/1022.png "Directiva de MFA")

Para obtener información general de la experiencia de usuario relacionada, consulte:

* [Flujo de registro de autenticación multifactor](active-directory-identityprotection-flows.md#multi-factor-authentication-registration).  
* [Experiencias de inicio de sesión con Azure AD Identity Protection](active-directory-identityprotection-flows.md).  

**Para abrir el cuadro de diálogo de configuración relacionado, siga estos pasos**:

- En la hoja **Azure AD Identity Protection**, en la sección **Configurar**, haga clic en **Registro para autenticación multifactor**.

    ![Directiva de MFA](./media/active-directory-identityprotection/1019.png "Directiva de MFA")

## <a name="next-steps"></a>pasos siguientes
* [Channel 9: Azure AD and Identity Show: Identity Protection Preview (Channel 9: Presentación de Azure AD e Identity: versión preliminar de Identity Protection)](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

* [Habilitación de Azure Active Directory Identity Protection](active-directory-identityprotection-enable.md)

* [Vulnerabilities detected by Azure Active Directory Identity Protection (Vulnerabilidades detectadas por Azure Active Directory Identity Protection)](active-directory-identityprotection-vulnerabilities.md)

* [Eventos de riesgo de Azure Active Directory](active-directory-identity-protection-risk-events.md)

* [Azure Active Directory Identity Protection notifications (Notificaciones de Azure Active Directory Identity Protection)](active-directory-identityprotection-notifications.md)

* [Azure Active Directory Identity Protection playbook (Guía de Azure Active Directory Identity Protection)](active-directory-identityprotection-playbook.md)

* [Azure Active Directory Identity Protection glossary (Glosario de Azure Active Directory Identity Protection)](active-directory-identityprotection-glossary.md)

* [Experiencias de inicio de sesión con Azure AD Identity Protection](active-directory-identityprotection-flows.md)

* [Azure Active Directory Identity Protection: desbloqueo de usuarios](active-directory-identityprotection-unblock-howto.md)

* [Introducción a Azure Active Directory Identity Protection y Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)

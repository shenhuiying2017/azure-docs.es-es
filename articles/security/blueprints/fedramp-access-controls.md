---
title: 'Azure Blueprint Automation de FedRAMP: control de acceso'
description: 'Aplicaciones web para FedRAMP: control de acceso'
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: f7e6cd8f-b2df-4db6-8332-de97d86c5281
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: a7a840b338af3aa6c6a71f2db3cff1e36a8a6794
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/23/2017
---
# <a name="access-control-ac"></a>Control de acceso (CA)

> [!NOTE]
> NIST y los Estados Unidos definen estos controles. Departamento de Comercio como parte de publicación especial de NIST 800-53 Revisión 4. Consulte el NIST 800-53 Rev. 4 para obtener información sobre procedimientos de pruebas y guía para cada control.

## <a name="nist-800-53-control-ac-1"></a>NIST 800-53 Control AC-1

#### <a name="access-control-policy-and-procedures"></a>Procedimientos y directiva de control de acceso

**AC-1** La organización desarrolla, documenta y difunde a [Asignación: personal o roles definidos por la organización] una directiva de control de acceso que aborda el propósito, el ámbito, los roles, las responsabilidades, el compromiso de la administración, la coordinación entre las entidades de la organización y el cumplimiento; y los procedimientos para facilitar la implementación de la directiva de control de acceso y los controles de acceso asociados; y revisa y actualiza la directiva de control de acceso actual [Asignación: frecuencia definida por la organización], y los procedimientos de control de acceso [Asignación: frecuencia definida por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | La directiva de control de acceso de nivel empresarial y los procedimientos del cliente pueden ser suficientes para abordar este control. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-2a"></a>NIST 800-53 Control AC-2.a

#### <a name="account-management"></a>Administración de cuentas

**AC-2.a** La organización identifica y selecciona los siguientes tipos de cuentas del sistema de información para respaldar las funciones empresariales y las misiones de la organización: [Asignación: tipos de cuentas del sistema de información definidos por el usuario].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta solución de Azure Blueprint se basa en los tipos de cuentas del sistema siguientes y los implementa: usuarios de Azure Active Directory (usados para implementar la solución y administrar el acceso a los recursos de Azure), usuarios del sistema operativo Windows (administrados por Active Directory) y cuenta de servicio de SQL Server. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-2b"></a>NIST 800-53 Control AC-2.b

#### <a name="account-management"></a>Administración de cuentas

**AC-2.b** La organización asigna administradores de cuentas para las cuentas del sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de asignar los administradores a las cuentas identificadas en AC-02.a. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-2c"></a>NIST 800-53 Control AC-2.c

#### <a name="account-management"></a>Administración de cuentas

**AC-2.c** La organización establece las condiciones de pertenencia a grupos y roles.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de establecer los criterios de pertenencia a roles y grupos para los tipos de cuenta controlados por el cliente (vea AC-02.a). |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-2d"></a>NIST 800-53 Control AC-2.d

#### <a name="account-management"></a>Administración de cuentas

**AC-2.d** La organización especifica los usuarios autorizados del sistema de información, la pertenencia a grupos y roles, las autorizaciones de acceso (es decir, los privilegios) y otros atributos (según sea necesario) para cada cuenta.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente puede confiar en un proceso establecido de autorización de la cuenta a nivel empresarial. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-2e"></a>NIST 800-53 Control AC-2.e

#### <a name="account-management"></a>Administración de cuentas

**AC-2.e** La organización requiere aprobaciones por parte de [Asignación: personal o roles definidos por la organización] de las solicitudes para crear cuentas del sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente puede confiar en un proceso establecido de autorización de la cuenta a nivel empresarial. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-2f"></a>NIST 800-53 Control AC-2.f

#### <a name="account-management"></a>Administración de cuentas

**AC-2.f** La organización crea, habilita, modifica, deshabilita y quita las cuentas del sistema de información de acuerdo con [Asignación: condiciones o procedimientos definidos por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente puede confiar en un proceso establecido de administración de cuentas a nivel empresarial. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-2g"></a>NIST 800-53 Control AC-2.g

#### <a name="account-management"></a>Administración de cuentas

**AC-2.g** La organización supervisa el uso de las cuentas del sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta solución de Azure Blueprint implementa el panel de Identity and Access de la solución Security and Audit de OMS. Este panel permite a los administradores de cuentas supervisar el uso de las cuentas del sistema de información. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-2h"></a>NIST 800-53 Control AC-2.h

#### <a name="account-management"></a>Administración de cuentas

**AC-2.h** La organización notifica a los administradores de cuentas cuándo las cuentas ya no son necesarias, cuándo finalizan o se transfieren los usuarios y cuándo se producen cambios que hay que saber o un uso individual del sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los procedimientos de control de acceso de nivel empresarial del cliente pueden establecer un proceso para notificar al administrador de cuentas apropiado cuándo una cuenta ya no es necesaria. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-2i"></a>NIST 800-53 Control AC-2.i

#### <a name="account-management"></a>Administración de cuentas

**AC-2.i** La organización autoriza el acceso al sistema de información en función de una autorización de acceso válida, el uso previsto del sistema y otros atributos, según las necesidades de la organización o de las funciones empresariales o misiones asociadas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los procedimientos de control de acceso de nivel empresarial del cliente pueden establecer un proceso de autorización de acceso. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-2j"></a>NIST 800-53 Control AC-2.j

#### <a name="account-management"></a>Administración de cuentas

**AC-2.j** La organización revisa el cumplimiento de las cuentas con los requisitos de administración de cuentas [Asignación: frecuencia definida por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de revisar las cuentas controladas por el cliente con la frecuencia necesaria, a fin de determinar si las cuentas cumplen todos los requisitos de la organización. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-2k"></a>NIST 800-53 Control AC-2.k

#### <a name="account-management"></a>Administración de cuentas

**AC-2.k** La organización establece un proceso para volver a emitir las credenciales de cuentas de grupos o compartidas (si se implementaron) cuando se quitan usuarios del grupo.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los procedimientos de control de acceso de nivel empresarial del cliente pueden establecer un proceso para administrar las credenciales de las cuentas de grupos. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-2-1"></a>NIST 800-53 Control AC-2 (1)

#### <a name="account-management--automated-system-account-management"></a>Administración de cuentas | Administración automatizada de cuentas del sistema

**AC-2 (1)** La organización emplea mecanismos automatizados para admitir la administración de cuentas del sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta solución de Azure Blueprint implementa el panel de Identity and Access de la solución Security and Audit de OMS. Este panel permite a los administradores de cuentas supervisar el uso de las cuentas del sistema de información. Se puede configurar OMS para enviar alertas cuando se sospecha de alguna actividad inusual o cuando se producen otros eventos predefinidos. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-2-2"></a>NIST 800-53 Control AC-2 (2)

#### <a name="account-management--removal-of-temporary--emergency-accounts"></a>Administración de cuentas | Eliminación de cuentas de emergencia/temporales

**AC-2 (2)** El sistema de información automáticamente [Selección: quita; deshabilita] cuentas temporales y de emergencias después de [Asignación: período de tiempo definido por la organización para cada tipo de cuenta].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta solución de Azure Blueprint no implementa cuentas temporales o de emergencia. Si no se deshabilita manualmente, el controlador de dominio implementado deshabilita automáticamente todas las cuentas inactivas después de treinta y cinco días. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-2-3"></a>NIST 800-53 Control AC-2 (3)

#### <a name="account-management--disable-inactive-accounts"></a>Administración de cuentas | Deshabilitar cuentas inactivas

**AC-2 (3)** El sistema de información deshabilita automáticamente las cuentas inactivas después de [Asignación: período de tiempo definido por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El controlador de dominio implementado por esta solución de Azure Blueprint está configurado para deshabilitar todas las cuentas de usuario después de treinta y cinco días de inactividad. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-2-4"></a>NIST 800-53 Control AC-2 (4)

#### <a name="account-management--automated-audit-actions"></a>Administración de cuentas | Acciones de auditoría automatizada

**AC-2 (4)** El sistema de información realiza auditorías automáticas de las acciones de creación, modificación, habilitación, deshabilitación y eliminación de cuentas y las notifica a [Asignación: personal o roles definidos por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta solución de Azure Blueprint implementa los siguientes tipos de cuentas del sistema: usuarios de Azure Active Directory, usuarios del sistema operativo Windows y cuenta de servicio de SQL Server. Las acciones de administración de cuentas de Azure Active Directory generan un evento en el registro de actividad de Azure, y las acciones de administración de cuentas a nivel de sistema operativo generan un evento en el registro del sistema. Estos registros los recopila Log Analytics y se almacenan en el repositorio de OMS. Se puede configurar OMS par que envíe alertas cuando se producen eventos predefinidos.  |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-2-5"></a>NIST 800-53 Control AC-2 (5)

#### <a name="account-management--inactivity-logout"></a>Administración de cuentas | Cierre de sesión por inactividad

**AC-2 (5)** La organización requiere que los usuarios cierren sesión cuando se detecta [Asignación: período de tiempo definido por la organización de inactividad prevista o descripción de cuándo cerrar sesión].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | La directiva de control de acceso de nivel empresarial del cliente puede establecer una directiva por la que los usuarios deben cerrar sesión cuando prevén que van a estar inactivos durante un período de tiempo o por otros factores. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-2-7a"></a>NIST 800-53 Control AC-2 (7).a

#### <a name="account-management--role-based-schemes"></a>Administración de cuentas | Esquemas basados en roles

**AC-2 (7).a** La organización establece y administra las cuentas de usuario con privilegios según un esquema de acceso basado en roles que organiza en roles el acceso al sistema de información y sus privilegios.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta solución de Azure Blueprint implementa los siguientes tipos de cuentas del sistema: usuarios de Azure Active Directory, usuarios del sistema operativo Windows y cuenta de servicio de SQL Server. Los privilegios de las cuentas de Azure Active Directory se implementan mediante un control de acceso basado en roles con la asignación de usuarios a roles; los privilegios de las cuentas de Active Directory se implementan mediante un control de acceso basado en roles con la asignación de usuarios a grupos de seguridad. El cliente puede extender estos esquemas basados en roles para cumplir las necesidades de la misión. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-2-7b"></a>NIST 800-53 Control AC-2 (7).b

#### <a name="account-management--role-based-schemes"></a>Administración de cuentas | Esquemas basados en roles

**AC-2 (7).b** La organización supervisa las asignaciones de roles con privilegios.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta solución de Azure Blueprint implementa el panel de Identity and Access de la solución Security and Audit de OMS. Este panel permite a los administradores de cuentas supervisar el uso de las cuentas del sistema de información. Esta solución se puede consultar para informar de las asignaciones de roles con privilegios. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-2-7c"></a>NIST 800-53 Control AC-2 (7).c

#### <a name="account-management--role-based-schemes"></a>Administración de cuentas | Esquemas basados en roles

**AC-2 (7).c** La organización realiza [Asignación: acciones definidas por la organización] cuando ya no son adecuadas las asignaciones de roles con privilegios.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de realizar acciones en cuentas controladas por el cliente cuando las asignaciones de roles con privilegios ya no son adecuadas. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-2-9"></a>NIST 800-53 Control AC-2 (9)

#### <a name="account-management--restrictions-on-use-of-shared--group-accounts"></a>Administración de cuentas | Restricciones de uso de cuentas de grupo o compartidas

**AC-2 (9)** La organización solo admite el uso de cuentas de grupo o compartidas que cumplen [Asignación: condiciones definidas por la organización para establecer cuentas de grupos o compartidas].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | No se habilita ninguna cuenta compartida o de grupo en los recursos implementados por esta instancia de Azure Blueprint. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-2-10"></a>NIST 800-53 Control AC-2 (10)

#### <a name="account-management--shared--group-account-credential-termination"></a>Administración de cuentas | Finalización de credenciales de cuentas de grupo o compartidas

**AC-2 (10)** El sistema de información finaliza las credenciales de cuentas de grupo o compartidas cuando los miembros dejan el grupo.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | No se habilita ninguna cuenta compartida o de grupo en los recursos implementados por esta instancia de Azure Blueprint. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-2-11"></a>NIST 800-53 Control AC-2 (11)

#### <a name="account-management--usage-conditions"></a>Administración de cuentas | Condiciones de uso

**AC-2 (11)** El sistema de información exige [Asignación: condiciones de uso o circunstancias definidas por la organización] para [Asignación: cuentas del sistema de información definidas por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint implementa un controlador de dominio al que se unen todas las máquinas virtuales implementadas. Se puede establecer una directiva de grupo en Active Directory y configurarla para implementar restricciones para horas del día u otras condiciones de uso de las cuentas. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-2-12a"></a>NIST 800-53 Control AC-2 (12).a

#### <a name="account-management--account-monitoring--atypical-usage"></a>Administración de cuentas | Supervisión o uso inusual de cuentas

**AC-2 (12).a** La organización supervisa el [Asignación: uso inusual definido por la organización] de las cuentas del sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta solución de Azure Blueprint implementa el panel de Identity and Access de la solución Security and Audit de OMS. Este panel permite a los administradores de cuentas supervisar los intentos de acceso a los recursos implementados. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-2-12b"></a>NIST 800-53 Control AC-2 (12).b

#### <a name="account-management--account-monitoring--atypical-usage"></a>Administración de cuentas | Supervisión o uso inusual de cuentas

**AC-2 (12).b** La organización notifica el uso inusual de las cuentas del sistema de información a [Asignación: roles o personal definidos por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta solución de Azure Blueprint implementa el panel de Identity and Access de la solución Security and Audit de OMS. Este panel permite a los administradores de cuentas supervisar los intentos de acceso a los recursos implementados. Se puede configurar esta solución para enviar alertas cuando se sospecha de alguna actividad inusual o cuando se producen otros eventos predefinidos. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-2-13"></a>NIST 800-53 Control AC-2 (13)

#### <a name="account-management--disable-accounts-for-high-risk-individuals"></a>Administración de cuentas | Deshabilitar cuentas de usuarios de alto riesgo

**AC-2 (13)** La organización deshabilita las cuentas de usuarios que plantean un riesgo importante durante el [Asignación: período de tiempo definido por la organización] en el que se detecta el riesgo.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los procedimientos y la directiva de control de acceso de nivel empresarial del cliente pueden establecer condiciones para deshabilitar cuentas de usuarios que plantean un riesgo importante para la organización. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-3"></a>NIST 800-53 Control AC-3

#### <a name="access-enforcement"></a>Aplicación de acceso

**AC-3** El sistema de información aplica autorizaciones aprobadas de acceso lógico a los recursos del sistema e informativos en función de directivas de control de acceso aplicables.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta solución de Azure Blueprint aplica autorizaciones de acceso lógico mediante el control de acceso basado en roles que aplica Azure Active Directory a través de la asignación de usuarios a roles y que aplica Active Directory mediante la asignación de usuarios a grupos de seguridad y controles en el nivel del sistema operativo Windows. Los roles de Azure Active Directory asignados a usuarios o grupos controlan el acceso lógico a recursos de Azure en los niveles de recurso, grupo o suscripción. Los grupos de seguridad de Active Directory controlan el acceso lógico a los recursos y funciones en el nivel del sistema operativo. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-4"></a>NIST 800-53 Control AC-4

#### <a name="information-flow-enforcement"></a>Aplicación del flujo de información

**AC-4** El sistema de información aplica autorizaciones aprobadas de control del flujo de información dentro del sistema y entre sistemas interconectados en función de [Asignación: directivas de control del flujo de información definidas por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint aplica restricciones al flujo de información mediante el uso de grupos de seguridad de red aplicados a las subredes en que los recursos se implementan, a Application Gateway y al equilibrador de carga. Los grupos de seguridad de red garantizan el control del flujo de información entre los recursos en función de reglas aprobadas. Application Gateway y el equilibrador de carga enrutan el tráfico de forma dinámica a recursos específicos en función de roles aprobados. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-4-8"></a>NIST 800-53 Control AC-4 (8)

#### <a name="information-flow-enforcement--security-policy-filters"></a>Aplicación del flujo de información | Filtros de directivas de seguridad

**AC-4 (8)** El sistema de información exige el control del flujo de información mediante el uso de [Asignación: filtros de directivas de seguridad definidos por la organización] como base para las decisiones de control de flujo para [Asignación: flujos de información definidos por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de aplicar el control del flujo de información en los recursos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-4-21"></a>NIST 800-53 Control AC-4 (21)

#### <a name="information-flow-enforcement--physical--logical-separation-of-information-flows"></a>Aplicación del flujo de información | Separación física o lógica de los flujos de información

**AC-4 (21)** El sistema de información separa los flujos de información de forma lógica o física con [Asignación: técnicas o mecanismos definidos por la organización] para realizar [Asignación: separaciones necesarias definidas por la organización por tipos de información].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de separar los flujos de información en los recursos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-5a"></a>NIST 800-53 Control AC-5.a

#### <a name="separation-of-duties"></a>Separación de obligaciones

**AC-5.a** La organización separa [Asignación: obligaciones de las personas definidas por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de la separación de las obligaciones entre las cuentas controladas por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-5b"></a>NIST 800-53 Control AC-5.b

#### <a name="separation-of-duties"></a>Separación de obligaciones

**AC-5.b** La organización documenta la separación de las obligaciones de las personas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de documentar la separación de las obligaciones entre las cuentas controladas por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-5c"></a>NIST 800-53 Control AC-5.c

#### <a name="separation-of-duties"></a>Separación de obligaciones

**AC-5.c** La organización define las autorizaciones de acceso al sistema de información para respaldar la separación de obligaciones.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint implementa el control de acceso basado en rol, que puede configurarse para separar obligaciones en función de los requisitos de la organización. Los privilegios de las cuentas de Azure Active Directory se implementan mediante un control de acceso basado en roles con la asignación de usuarios a roles; los privilegios de las cuentas de Active Directory se implementan mediante un control de acceso basado en roles con la asignación de usuarios a grupos de seguridad. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-6"></a>NIST 800-53 Control AC-6

#### <a name="least-privilege"></a>Privilegios mínimos

**AC-6** La organización emplea el principio de privilegios mínimos, permitiendo solo los accesos autorizados de usuarios o procesos que actúan en nombre de los usuarios que resultan necesarios para realizar las tareas asignadas, según las funciones empresariales o las misiones de la organización.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint implementa el control de acceso basado en rol para restringir a los usuarios solo a los privilegios asignados explícitamente. Los privilegios de las cuentas de Azure Active Directory se implementan mediante un control de acceso basado en roles con la asignación de usuarios a roles; los privilegios de las cuentas de Active Directory se implementan mediante un control de acceso basado en roles con la asignación de usuarios a grupos de seguridad.  |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-6-1"></a>NIST 800-53 Control AC-6 (1)

#### <a name="least-privilege--authorize-access-to-security-functions"></a>Privilegios mínimos | Autorizar el acceso a las funciones de seguridad

**AC-6 (1)** La organización autoriza el acceso explícitamente a [Asignación: funciones de seguridad (implementadas en hardware, software y firmware) e información importante para la seguridad definidas por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los procedimientos de control de acceso de nivel empresarial del cliente pueden establecer un proceso de autorización de acceso que engloba el acceso a las funciones de seguridad. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-6-2"></a>NIST 800-53 Control AC-6 (2)

#### <a name="least-privilege--non-privileged-access-for-nonsecurity-functions"></a>Privilegios mínimos | Acceso sin privilegios para las funciones de otra índole

**AC-6 (2)** La organización requiere que los roles o usuarios de las cuentas del sistema de información con acceso a [Asignación: funciones de seguridad o información importante para la seguridad definidas por la organización] usen roles o cuentas sin privilegios para acceder a las funciones de otra índole.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | La directiva de control de acceso de nivel empresarial del cliente puede requerir que los usuarios usen cuentas sin privilegios para acceder a funciones de otra índole. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-6-3"></a>NIST 800-53 Control AC-6 (3)

#### <a name="least-privilege--network-access-to-privileged-commands"></a>Privilegios mínimos | Acceso a la red a los comandos con privilegios

**AC-6 (3)** La organización autoriza el acceso a la red a los [Asignación: comandos con privilegios definidos por la organización] solo para [Asignación: las necesidades operativas apremiantes definidas por la organización] y documenta el motivo de dicho acceso en el plan de seguridad del sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | La directiva de control de acceso de nivel empresarial del cliente puede definir comandos con privilegios a los que se puede acceder a través de una red. Nota: Los clientes no tienen ningún acceso físico a la infraestructura de Azure. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-6-5"></a>NIST 800-53 Control AC-6 (5)

#### <a name="least-privilege--privileged-accounts"></a>Privilegios mínimos | Cuentas con privilegios

**AC-6 (5)** La organización restringe las cuentas con privilegios en el sistema de información a [Asignación: roles o personal definidos por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | La directiva de control de acceso de nivel empresarial del cliente puede definir restricciones de uso de las cuentas con privilegios. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-6-7a"></a>NIST 800-53 Control AC-6 (7).a

#### <a name="least-privilege--review-of-user-privileges"></a>Privilegios mínimos | Revisión de privilegios de usuario

**AC-6 (7).a** La organización revisa [Asignación: la frecuencia definida por la organización] con que los privilegios asignados a [Asignación: clases de usuarios o roles definidos por la organización] validan la necesidad de tales privilegios.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de revisar los privilegios de usuario de las cuentas controladas por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-6-7b"></a>NIST 800-53 Control AC-6 (7).b

#### <a name="least-privilege--review-of-user-privileges"></a>Privilegios mínimos | Revisión de privilegios de usuario

**AC-6 (7).b** La organización reasigna o elimina privilegios, si es necesario, para reflejar correctamente las necesidades empresariales o la misión de la organización.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de reasignar o eliminar los privilegios para las cuentas controladas por el cliente cuando sea oportuno. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-6-8"></a>NIST 800-53 Control AC-6 (8)

#### <a name="least-privilege--privilege-levels-for-code-execution"></a>Privilegios mínimos | Niveles de privilegios para la ejecución de código

**AC-6 (8)** El sistema de información evita que [Asignación: el software definido por la organización] se ejecute con niveles de privilegios más altos de los que disponen los usuarios que ejecutan el software.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint implementa el control de acceso basado en rol para restringir a los usuarios solo a los privilegios asignados explícitamente. Las protecciones a nivel de SO en máquinas virtuales no permiten que el software se ejecute con un nivel de privilegios más alto del que disponen los usuarios que ejecutan el software. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-6-9"></a>NIST 800-53 Control AC-6 (9)

#### <a name="least-privilege--auditing-use-of-privileged-functions"></a>Privilegios mínimos | Auditar el uso de funciones con privilegios

**AC-6 (9)** El sistema de información audita la ejecución de las funciones con privilegios.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint implementa el servicio de Log Analytics en OMS. Las cuentas de almacenamiento de Azure Diagnostics y de las máquinas virtuales implementadas son orígenes conectados a Log Analytics que garantizan la auditoría de la ejecución de las funciones con privilegios. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-6-10"></a>NIST 800-53 Control AC-6 (10)

#### <a name="least-privilege--prohibit-non-privileged-users-from-executing-privileged-functions"></a>Privilegios mínimos | Prohibir que los usuarios sin privilegios ejecuten funciones con privilegios

**AC-6 (10)** El sistema de información evita que los usuarios sin privilegios ejecuten funciones con privilegios para incluir las acciones de deshabilitar, sortear o alterar las medidas o contramedidas de seguridad.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint implementa el control de acceso basado en rol para restringir a los usuarios solo a los privilegios asignados explícitamente.  |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-7a"></a>NIST 800-53 Control AC-7.a

#### <a name="unsuccessful-logon-attempts"></a>Intentos de inicio de sesión incorrectos

**AC-7.a** El sistema de información aplica un límite de [Asignación: número definido por la organización] intentos consecutivos de inicio de sesión no válidos por parte de un usuario durante un [Asignación: período de tiempo definido por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Azure Portal limita los intentos consecutivos de inicios de sesión no válidos por parte de los usuarios. Se aplica una directiva de grupo al nivel del sistema operativo para todas las máquinas virtuales implementadas por esta instancia de Azure Blueprint. La directiva limita los intentos consecutivos de inicios de sesión no válidos por parte de los usuarios a tres como máximo durante quince minutos. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-7b"></a>NIST 800-53 Control AC-7.b

#### <a name="unsuccessful-logon-attempts"></a>Intentos de inicio de sesión incorrectos

**AC-7.b** El sistema de información [Selección: bloquea la cuenta o el nodo durante [Asignación: un período de tiempo definido por la organización]; bloquea la cuenta o el nodo hasta que un administrador lo libera; retrasa la siguiente solicitud de inicio de sesión según [Asignación: el algoritmo de retraso definido por la organización]] automáticamente cuando se ha excedido el número máximo de intentos incorrectos.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Azure Portal bloquea las cuentas después de que los usuarios realizan los intentos consecutivos permitidos de inicios de sesión no válidos. Se aplica una directiva de grupo al nivel del sistema operativo para todas las máquinas virtuales implementadas por esta instancia de Azure Blueprint. La directiva bloquea las cuentas durante tres horas después de que los usuarios han realizado tres intentos consecutivos de inicios de sesión no válidos. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-7-2"></a>NIST 800-53 Control AC-7 (2)

#### <a name="unsuccessful-logon-attempts--purge--wipe-mobile-device"></a>Intentos de inicio de sesión incorrectos | Purgar o borrar el dispositivo móvil

**AC-7 (2)** El sistema de información purga o borra la información de [Asignación: los dispositivos móviles definidos por la organización] según [Asignación: las técnicas o los requisitos de purga o borrado definidos por la organización] después de [Asignación: número definido por la organización] intentos consecutivos de inicios de sesión incorrectos en el dispositivo.

**Responsabilidades:** `Not Applicable`

|||
|---|---|
| **Cliente** | Dispositivos móviles que no están dentro del ámbito de los sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure no admite dispositivos móviles dentro del límite de Azure. Por lo tanto, este control no es aplicable a Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-8a"></a>NIST 800-53 Control AC-8.a

#### <a name="system-use-notification"></a>Notificación de uso del sistema

**AC-8.a** El sistema de información muestra a los usuarios [Asignación: banner o mensajes de notificación del uso del sistema definidos por la organización] antes de conceder acceso al sistema que ofrece avisos de privacidad y seguridad sobre las leyes federales, los decretos ejecutivos, las directivas, las políticas, los reglamentos y las normas aplicables, así como orientación y declaraciones que indican que los usuarios están accediendo a un sistema de información del Gobierno de Estados Unidos; el uso del sistema de información puede supervisarse, registrarse y auditarse; el uno no autorizado del sistema de información está prohibido y sujeto a sanciones penales y civiles; y el uso del sistema de información indica el consentimiento de supervisión y grabación.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint implementa un controlador de dominio al que se unen todas las máquinas virtuales implementadas. Una directiva de grupo implementa una notificación de uso del sistema que se muestra a los usuarios antes de que inicien sesión. Nota: La instancia de Azure Blueprint implementa una notificación de uso del sistema de ejemplo. El cliente debe editar este texto para cumplir los requisitos del organismo regulador o de la organización. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-8b"></a>NIST 800-53 Control AC-8.b

#### <a name="system-use-notification"></a>Notificación de uso del sistema

**AC-8.b** El sistema de información mantiene el banner o mensaje de notificación en la pantalla hasta que el usuario acepta las condiciones de uso y toma medidas explícitas para iniciar sesión o seguir accediendo al sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint implementa un controlador de dominio al que se unen todas las máquinas virtuales implementadas. Una directiva de grupo implementa una notificación de uso del sistema que se muestra a los usuarios antes de que inicien sesión. El usuario debe confirmar la notificación para iniciar sesión. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-8c"></a>NIST 800-53 Control AC-8.c

#### <a name="system-use-notification"></a>Notificación de uso del sistema

**AC-8.c** El sistema de información para sistemas de acceso público muestra [Asignación: condiciones definidas por la organización] de la información de uso del sistema, antes de conceder acceso adicional; muestra referencias, si hay alguna, para supervisar, grabar o auditar que son coherentes con los acuerdos de privacidad para los sistemas que suelen prohibir tales actividades; e incluye una descripción de los usos autorizados del sistema.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de mostrar una notificación de uso del sistema en todos los recursos de acceso público implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-10"></a>NIST 800-53 Control AC-10

#### <a name="concurrent-session-control"></a>Control de sesiones simultáneas

**AC-10** El sistema de información limita el número de sesiones simultáneas de cada [Asignación: tipo de cuenta o cuenta definidos por la organización] a [Asignación: número definido por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Se implementa una directiva de sistema operativo para las máquinas virtuales implementadas por esta instancia de Azure Blueprint. La directiva implementa restricciones de sesiones simultáneas (dos sesiones). |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-11a"></a>NIST 800-53 Control AC-11.a

#### <a name="session-lock"></a>Bloqueo de sesión

**AC-11.a** El sistema de información evita otros accesos al sistema al activar un bloqueo de la sesión después de [Asignación: período de tiempo definido por la organización] de inactividad o al recibir una solicitud de un usuario.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint implementa un controlador de dominio al que se unen todas las máquinas virtuales implementadas. Una directiva de grupo implementa un bloqueo de inactividad para las sesiones de RDP. Los usuarios pueden activar el bloqueo manualmente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-11b"></a>NIST 800-53 Control AC-11.b

#### <a name="session-lock"></a>Bloqueo de sesión

**AC-11.b** El sistema de información mantiene el bloqueo hasta que el usuario restablece el acceso mediante procedimientos establecidos de identificación y autenticación.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint implementa un controlador de dominio al que se unen todas las máquinas virtuales implementadas. Una directiva de grupo implementa un bloqueo de inactividad para las sesiones de RDP. Los usuarios deben volver a autenticarse para desbloquear la sesión.  |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-11-1"></a>NIST 800-53 Control AC-11 (1)

#### <a name="session-lock--pattern-hiding-displays"></a>Bloqueo de sesión | Patrón para ocultar información mostrada

**AC-11 (1)** El sistema de información oculta, mediante el bloque de la sesión, información que antes estaba visible en la pantalla con una imagen de visualización pública.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint implementa un controlador de dominio al que se unen todas las máquinas virtuales implementadas. Una directiva de grupo implementa un bloqueo de inactividad para las sesiones de RDP. El bloqueo de sesión oculta información que antes estaba visible. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-12"></a>NIST 800-53 Control AC-12

#### <a name="session-termination"></a>Finalización de la sesión

**AC-12** El sistema de información finaliza automáticamente una sesión de usuario después [Asignación: eventos desencadenadores o condiciones definidos por la organización que requieren la desconexión de la sesión].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | La configuración del host de sesión de Escritorio remoto de las máquinas virtuales Windows implementadas por esta instancia de Azure Blueprint se puede establecer de tal forma que cumpla los requisitos de finalización de la sesión de la organización. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-12-1a"></a>NIST 800-53 Control AC-12 (1).a

#### <a name="session-termination--user-initiated-logouts--message-displays"></a>Finalización de la sesión | Visualización de mensajes/cierres de sesión iniciados por el usuario

**AC-12 (1).a** El sistema de información ofrece una funcionalidad de cierre de sesiones de comunicaciones iniciadas por el usuario cada vez que se usa la autenticación para acceder a [Asignación: los recursos de información definidos por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los sistemas operativos de las máquinas virtuales y Azure Portal implementados por esta instancia de Azure Blueprint habilitan usos para iniciar un cierre de sesión. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-12-1b"></a>NIST 800-53 Control AC-12 (1).b

#### <a name="session-termination--user-initiated-logouts--message-displays"></a>Finalización de la sesión | Visualización de mensajes/cierres de sesión iniciados por el usuario

**AC-12 (1).b** El sistema de información muestra un mensaje de cierre de sesión explícito a los usuarios en el que se informa de la finalización confiable de sesiones de comunicaciones autenticadas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los sistemas operativos de las máquinas virtuales y Azure Portal implementados por esta instancia de Azure Blueprint habilitan usos para iniciar un cierre de sesión. El proceso de cierre de sesión proporciona una indicación a los usuarios de que la sesión se ha finalizado. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-14a"></a>NIST 800-53 Control AC-14.a

#### <a name="permitted-actions-without-identification-or-authentication"></a>Acciones permitidas sin identificación o autenticación

**AC-14.a** La organización identifica [Asignación: acciones de usuario definidas por la organización] que pueden realizarse en el sistema de información sin identificación o autenticación coherentes con las funciones empresariales o las misiones de la organización.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de identificar las acciones que se pueden realizar en los recursos implementados por el cliente sin identificación o autenticación (por ejemplo, visualizar una página web de acceso público). |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-14b"></a>NIST 800-53 Control AC-14.b

#### <a name="permitted-actions-without-identification-or-authentication"></a>Acciones permitidas sin identificación o autenticación

**AC-14.b** La organización documenta y justifica en el plan de seguridad del sistema de información aquellas acciones de usuario que no requieren identificación o autenticación.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de documentar las acciones de usuario que no requieren identificación o autenticación en los recursos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-17a"></a>NIST 800-53 Control AC-17.a

#### <a name="remote-access"></a>Acceso remoto

**AC-17.a** La organización establece y documenta restricciones de uso, requisitos de configuración y conexión y orientación de implementación para cada tipo de acceso remoto permitido.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | La directiva de control de acceso de nivel empresarial del cliente puede definir restricciones de uso de acceso remoto. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-17b"></a>NIST 800-53 Control AC-17.b

#### <a name="remote-access"></a>Acceso remoto

**AC-17.b** La organización autoriza el acceso remoto al sistema de información antes de permitir dichas conexiones.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los procedimientos de control de acceso de nivel empresarial del cliente pueden establecer un proceso de autorización de acceso remoto. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-17-1"></a>NIST 800-53 Control AC-17 (1)

#### <a name="remote-access--automated-monitoring--control"></a>Acceso remoto | Control y supervisión automatizados

**AC-17 (1)** El sistema de información supervisa y controla los métodos de acceso remoto.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint proporciona acceso remoto al sistema de información a través de Azure Portal, una conexión a Escritorio remoto a través de un JumpBox y una aplicación web implementada por el cliente. Los accesos a través de Azure Portal y las sesiones de Escritorio remoto están sujetos a auditoría y se supervisan a través de OMS. El cliente debe implementar controles de acceso remoto, según sea necesario, a la aplicación web. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-17-2"></a>NIST 800-53 Control AC-17 (2)

#### <a name="remote-access--protection-of-confidentiality--integrity-using-encryption"></a>Acceso remoto | Protección de confidencialidad/integridad mediante cifrado

**AC-17 (2)** El sistema de información implementa mecanismos de criptografía para proteger la confidencialidad e integridad de las sesiones de acceso remoto.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El acceso remoto a los recursos implementados por esta solución de Azure Blueprint, como Azure Portal, la conexión a Escritorio remoto y la puerta de enlace de aplicación web, está protegido mediante TLS. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-17-3"></a>NIST 800-53 Control AC-17 (3)

#### <a name="remote-access--managed-access-control-points"></a>Acceso remoto | Puntos de control de acceso administrados

**AC-17 (3)** El sistema de información enruta todos los acceso remotos a través de [Asignación: número definido por la organización] puntos administrados de control de acceso a la red.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El acceso remoto a la aplicación web nocional implementada por esta instancia de Azure Blueprint se realiza a través de una instancia de Application Gateway. El acceso remoto a todos los demás recursos se realiza a través de un JumpBox. No hay ningún otro punto de conexión de acceso público. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-17-4a"></a>NIST 800-53 Control AC-17 (4).a

#### <a name="remote-access--privileged-commands--access"></a>Acceso remoto | Comandos y acceso con privilegios

**AC-17 (4).a** La organización autoriza la ejecución de comandos y accesos con privilegios a información importante sobre seguridad a través del acceso remoto solo para [Asignación: las necesidades definidas por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | La directiva de control de acceso de nivel empresarial del cliente puede definir comandos con privilegios a los que se puede acceder de forma remota e incluir una justificación. Nota: Los clientes no tienen acceso directo de red a la infraestructura de Azure. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-17-4b"></a>NIST 800-53 Control AC-17 (4).b

#### <a name="remote-access--privileged-commands--access"></a>Acceso remoto | Comandos y acceso con privilegios

**AC-17 (4).b** La organización documenta la justificación de dicho acceso en el plan de seguridad del sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | La directiva de control de acceso de nivel empresarial del cliente puede definir comandos con privilegios a los que se puede acceder de forma remota e incluir una justificación. Nota: Los clientes no tienen acceso directo de red a la infraestructura de Azure. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-17-9"></a>NIST 800-53 Control AC-17 (9)

#### <a name="remote-access--disconnect--disable-access"></a>Acceso remoto | Desconectar o deshabilitar el acceso

**AC-17 (9)** La organización ofrece una funcionalidad para desconectar o deshabilitar rápidamente el acceso al sistema de información durante [Asignación: el período de tiempo definido por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Esta instancia de Azure Blueprint proporciona acceso remoto al sistema de información a través de Azure Portal, una conexión a Escritorio remoto a través de un JumpBox y una aplicación web. Si se deshabilita o elimina una cuenta de Azure Active Directory, el acceso a Azure Portal se desconecta inmediatamente. De forma similar, si se deshabilita o elimina una cuenta a nivel de SO en una máquina virtual, el acceso a Escritorio remoto a través del JumpBox se desconecta inmediatamente. Los clientes deben implementar la desconexión del acceso remoto a la aplicación web. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-18a"></a>NIST 800-53 Control AC-18.a

#### <a name="wireless-access"></a>Acceso inalámbrico

**AC-18.a** La organización establece restricciones de uso, requisitos de configuración y conexión y orientación de implementación para cada tipo de acceso inalámbrico.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | No existe ningún acceso inalámbrico dentro del ámbito de los sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure establece restricciones de uso, requisitos de configuración y conexión y orientación de implementación del acceso inalámbrico a través del estándar de seguridad de red, que prohíbe el uso de conexiones inalámbricas en el entorno de Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-18b"></a>NIST 800-53 Control AC-18.b

#### <a name="wireless-access"></a>Acceso inalámbrico

**AC-18.b** La organización autoriza el acceso inalámbrico al sistema de información antes de permitir dichas conexiones.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | No existe ningún acceso inalámbrico dentro del ámbito de los sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure no permite el acceso inalámbrico en los centros de datos de Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-18-1"></a>NIST 800-53 Control AC-18 (1)

#### <a name="wireless-access--authentication-and-encryption"></a>Acceso inalámbrico | Autenticación y cifrado

**AC-18 (1)** El sistema de información protege el acceso inalámbrico al sistema mediante la autenticación de [Selección (uno o varios): usuarios; dispositivos] y el cifrado.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | No existe ningún acceso inalámbrico dentro del ámbito de los sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure no permite el acceso inalámbrico dentro del entorno de Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-18-3"></a>NIST 800-53 Control AC-18 (3)

#### <a name="wireless-access--disable-wireless-networking"></a>Acceso inalámbrico | Deshabilitar las redes inalámbricas

**AC-18 (3)** La organización deshabilita, cuando no se prevé su uso, las funcionalidades las redes inalámbricas insertadas internamente en los componentes del sistema de información antes de la publicación e implementación.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | No existe ningún acceso inalámbrico dentro del ámbito de los sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure no permite el acceso inalámbrico dentro del entorno de Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-18-4"></a>NIST 800-53 Control AC-18 (4)

#### <a name="wireless-access--restrict-configurations-by-users"></a>Acceso inalámbrico | Restringir las configuraciones de los usuarios

**AC-18 (4)** La organización identifica y autoriza de forma explícita a los usuarios a los que se permite configurar las funcionalidades de redes inalámbricas de forma independiente.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | No existe ningún acceso inalámbrico dentro del ámbito de los sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure no permite el acceso inalámbrico dentro del entorno de Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-18-5"></a>NIST 800-53 Control AC-18 (5)

#### <a name="wireless-access--antennas--transmission-power-levels"></a>Acceso inalámbrico | Niveles de potencia de transmisión/antena

**AC-18 (5)** La organización selecciona antenas de radio y calibra los niveles de potencia de transmisión para reducir la probabilidad de que las señales válidas puedan recibirse fuera de los límites controlados por la organización.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | No existe ningún acceso inalámbrico dentro del ámbito de los sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure no permite el acceso inalámbrico dentro del entorno de Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-19a"></a>NIST 800-53 Control AC-19.a

#### <a name="access-control-for-mobile-devices"></a>Control de acceso para los dispositivos móviles

**AC-19.a** La organización establece restricciones de uso, requisitos de configuración y conexión y orientación de implementación para los dispositivos móviles controlados por la organización.

**Responsabilidades:** `Not Applicable`

|||
|---|---|
| **Cliente** | No existe ningún dispositivo móvil controlado por el cliente dentro del ámbito de los sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure no admite dispositivos móviles dentro del límite de Azure. Por lo tanto, este control no es aplicable a Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-19b"></a>NIST 800-53 Control AC-19.b

#### <a name="access-control-for-mobile-devices"></a>Control de acceso para los dispositivos móviles

**AC-19.b** La organización autoriza la conexión de los dispositivos móviles a los sistemas de información de la organización.

**Responsabilidades:** `Not Applicable`

|||
|---|---|
| **Cliente** | No existe ningún dispositivo móvil controlado por el cliente dentro del ámbito de los sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure no admite dispositivos móviles dentro del límite de Azure. Por lo tanto, este control no es aplicable a Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-19-5"></a>NIST 800-53 Control AC-19 (5)

#### <a name="access-control-for-mobile-devices--full-device--container-based--encryption"></a>Control de acceso para dispositivos móviles | Cifrado total de dispositivo o cifrado de contenedor

**AC-19 (5)** La organización emplea [Selección: el cifrado total de dispositivo; cifrado de contenedor] para proteger la confidencialidad e integridad de la información en [Asignación: los dispositivos móviles definidos por la organización].

**Responsabilidades:** `Not Applicable`

|||
|---|---|
| **Cliente** | No existe ningún dispositivo móvil controlado por el cliente dentro del ámbito de los sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft Azure no admite dispositivos móviles dentro del límite de Azure. Por lo tanto, este control no es aplicable a Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-20a"></a>NIST 800-53 Control AC-20.a

#### <a name="use-of-external-information-systems"></a>Uso de sistemas de información externos

**AC-20.a** La organización establece términos y condiciones, coherentes con las relaciones de confianza establecidas con otras organizaciones que poseen, operan o mantienen sistemas de información externos, permitiendo a los usuarios autorizados acceder al sistema de información desde los sistemas de información externos.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | La directiva de control de acceso de nivel empresarial del cliente puede incluir una cláusula relativa al uso de las ofertas de servicios en la nube según FedRAMP. La Junta de Autorizaciones Conjuntas (JAB) de FedRAMP ha concedido a Azure una autorización provisional para operar (P-ATO), que permite a los organismos gubernamentales adquirir servicios en la nube de Azure para utilizarlos. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-20b"></a>NIST 800-53 Control AC-20.b

#### <a name="use-of-external-information-systems"></a>Uso de sistemas de información externos

**AC-20.b** La organización establece términos y condiciones, coherentes con las relaciones de confianza establecidas con otras organizaciones que poseen, operan o mantienen sistemas de información externos, permitiendo a los usuarios autorizados procesar, almacenar o transmitir información controlada por la organización mediante sistemas de información externos.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | La directiva de control de acceso de nivel empresarial del cliente puede incluir una cláusula relativa al uso de las ofertas de servicios en la nube según FedRAMP. La Junta de Autorizaciones Conjuntas (JAB) de FedRAMP ha concedido a Azure una autorización provisional para operar (P-ATO), que permite a los organismos gubernamentales adquirir servicios en la nube de Azure para utilizarlos. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-20-1"></a>NIST 800-53 Control AC-20 (1)

#### <a name="use-of-external-information-systems--limits-on-authorized-use"></a>Uso de sistemas de información externos | Límites de uso autorizado

**AC-20 (1)** La organización permite a las personas autorizadas usar un sistema de información externo para acceder al sistema de información o procesar, almacenar o transmitir información controlada por la organización solo después de que la organización verifique que se han implementado los controles de seguridad requeridos en el sistema externo, de conformidad con el plan de seguridad y la directiva de seguridad de la información de la organización; también se les puede permitir entablar acuerdos de procesamiento o conexión con el sistema de información aprobado con la entidad de la organización que hospeda el sistema de información externo.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El grupo de tecnología de la información de nivel empresarial del cliente puede verificar si el proveedor de servicios en la nube cumple los requisitos de seguridad de la información de la organización y conceder una aprobación a nivel empresarial para usar las ofertas de servicios en la nube asociadas. La Junta de Autorizaciones Conjuntas (JAB) de FedRAMP ha concedido a Azure una autorización provisional para operar (P-ATO). Azure es evaluado por una organización de evaluación de terceros aprobada por FedRAMP (3PAO) para comprobar el cumplimiento del control de seguridad de FedRAMP y otros requisitos. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-ac-20-2"></a>NIST 800-53 Control AC-20 (2)

#### <a name="use-of-external-information-systems--portable-storage-devices"></a>Uso de sistemas de información externos | Dispositivos de almacenamiento portátiles

**AC-20 (2)** La organización [Selección: restringe; prohíbe] el uso de dispositivos de almacenamiento portátiles controlados por la organización por parte de personas autorizadas en sistemas de información externos.

**Responsabilidades:** `Azure Only`

|||
|---|---|
| **Cliente** | Los clientes no tienen acceso físico a ningún recurso del sistema en los centros de datos de Azure. |
| **Proveedor (Microsoft Azure)** | Microsoft no admite dispositivos de almacenamiento portátiles controlados por el cliente en el entorno de Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-21a"></a>NIST 800-53 Control AC-21.a

#### <a name="information-sharing"></a>Uso compartido de la información

**AC-21.a** La organización facilita el uso compartido de la información al permitir que los usuarios autorizados puedan determinar si las autorizaciones de acceso asignadas al asociado de uso compartido cumplen las restricciones de acceso a la información para [Asignación: circunstancias de uso compartido de la información definidas por la organización en que se requiere discreción por parte del usuario].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | La directiva de control de acceso de nivel empresarial del cliente puede incluir disposiciones sobre el uso compartido de la información. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-21b"></a>NIST 800-53 Control AC-21.b

#### <a name="information-sharing"></a>Uso compartido de la información

**AC-21.b** La organización emplea [Asignación: procesos manuales o mecanismos automatizados definidos por la organización] para ayudar a los usuarios a tomar decisiones sobre colaboración y uso compartido de la información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente puede confiar en una funcionalidad de ayuda para la toma de decisiones sobe el uso compartido de la información a nivel empresarial. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-22a"></a>NIST 800-53 Control AC-22.a

#### <a name="publicly-accessible-content"></a>Contenido de acceso público

**AC-22.a** La organización designa personas autorizadas para publicar información en un sistema de información de acceso público.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los procedimientos de control de acceso de nivel empresarial del cliente pueden designar personas autorizadas para publicar información de acceso público. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-22b"></a>NIST 800-53 Control AC-22.b

#### <a name="publicly-accessible-content"></a>Contenido de acceso público

**AC-22.b** La organización entrena a las personas autorizadas para garantizar que la información de acceso público no contenga información no pública.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente puede confiar en la formación de nivel empresarial de las personas autorizadas para publicar información de acceso público. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-22c"></a>NIST 800-53 Control AC-22.c

#### <a name="publicly-accessible-content"></a>Contenido de acceso público

**AC-22.c** La organización revisa el contenido de la información propuesto antes de publicarlo en un sistema de información de acceso público, a fin de garantizar que no se incluya información no pública.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los procedimientos de control de acceso de nivel empresarial del cliente pueden establecer un proceso de revisión del contenido propuesto para publicarse en un sistema de acceso público. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-ac-22d"></a>NIST 800-53 Control AC-22.d

#### <a name="publicly-accessible-content"></a>Contenido de acceso público

**AC-22.d** La organización revisa el contenido del sistema de información de acceso público para asegurarse de que no contenga información no pública [Asignación: frecuencia definida por la organización] y, en caso de haberla, la elimina.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los procedimientos de control de acceso de nivel empresarial del cliente pueden establecer un proceso de revisión periódica del contenido publicado en sistemas de acceso público. |
| **Proveedor (Microsoft Azure)** | No aplicable |


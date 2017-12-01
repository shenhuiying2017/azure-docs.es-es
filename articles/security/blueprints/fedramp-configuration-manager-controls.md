---
title: "Azure Blueprint Automation de FedRAMP: Administración de configuración"
description: "Aplicaciones web para FedRAMP: Administración de configuración"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 5b953c0d-236f-4b61-b2c5-df2199490c73
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: e93aa430b7150f07210f5d1f37e2027d95334a59
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/23/2017
---
# <a name="configuration-management-cm"></a>Administración de configuración (CM)

> [!NOTE]
> Estos controles los definen el Instituto Nacional de Estándares y Tecnología (NIST) y el Departamento de Comercio de EE. UU. como parte del informe NIST Special Publication 800-53 Revision 4. Consulte el informe NIST 800-53 Rev. 4 para obtener información sobre la guía y los procedimientos de prueba de cada control.

## <a name="nist-800-53-control-cm-1"></a>NIST 800-53 Control CM-1

#### <a name="configuration-management-policy-and-procedures"></a>Procedimientos y directivas de administración de configuración

**CM-1** La organización desarrolla, documenta y difunde a [Asignación: personal o roles definidos por la organización] una directiva de administración de configuración que aborda el propósito, el ámbito, los roles, las responsabilidades, el compromiso de la administración, la coordinación entre las entidades de la organización y el cumplimiento; y los procedimientos para facilitar la implementación de la directiva de administración de configuración y los controles de administración de configuración asociados. Asimismo, revisa y actualiza la directiva de administración de configuración actual [Asignación: frecuencia definida por la organización], y los procedimientos de administración de configuración [Asignación: frecuencia definida por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los procedimientos y la directiva de administración de configuración de nivel empresarial del cliente pueden ser suficientes para abordar este control. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-cm-2"></a>NIST 800-53 Control CM-2

#### <a name="baseline-configuration"></a>Configuración de línea de base

**CM-2** La organización desarrolla, documenta y mantiene, bajo el control de la configuración, una configuración de línea de base actual del sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Las plantillas de Azure Resource Manager y los recursos asociados que componen este programa Azure Blueprint representan una línea de base de configuración como código para la arquitectura implementada. La solución se proporciona mediante GitHub, que puede utilizarse para el control de configuración. La solución incluye una línea de base de Desired State Configuration (DSC) para cada máquina virtual implementada. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-2-1a"></a>NIST 800-53 Control CM-2 (1).a

#### <a name="baseline-configuration--reviews-and-updates"></a>Configuración de línea de base | Revisiones y actualizaciones

**CM-2 (1).a** La organización revisa y actualiza la configuración de línea de base del sistema de información [Asignación: frecuencia definida por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de revisar y actualizar la configuración de línea de base de los recursos implementados por el cliente (incluidos los sistemas operativos, las aplicaciones, las bases de datos y el software). |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-2-1b"></a>NIST 800-53 Control CM-2 (1).b

#### <a name="baseline-configuration--reviews-and-updates"></a>Configuración de línea de base | Revisiones y actualizaciones

**CM-2 (1).b** La organización revisa y actualiza la configuración de línea de base del sistema de información cuando es necesario debido a [Asignación: circunstancias definidas por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de revisar y actualizar la configuración de línea de base de los recursos implementados por el cliente cuando es necesario. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-2-1c"></a>NIST 800-53 Control CM-2 (1).c

#### <a name="baseline-configuration--reviews-and-updates"></a>Configuración de línea de base | Revisiones y actualizaciones

**CM-2 (1).c** La organización revisa y actualiza la configuración de línea de base del sistema de información como parte integral de las actualizaciones e instalaciones de componentes del sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de revisar y actualizar la configuración de línea de base de los recursos implementados por el cliente cuando es necesario. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-2-2"></a>NIST 800-53 Control CM-2 (2)

#### <a name="baseline-configuration--automation-support-for-accuracy--currency"></a>Configuración de línea de base | Automatización para mantener la precisión y la vigencia

**CM-2 (2)** La organización emplea mecanismos automatizados para mantener la configuración de línea de base del sistema de información actualizada, completa, precisa y disponible.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Las plantillas de Azure Resource Manager y los recursos asociados que componen este programa Azure Blueprint representan una línea de base de configuración como código para la arquitectura implementada. La solución se proporciona mediante GitHub, que puede utilizarse para el control de configuración. En Azure Portal, hay disponible un script de automatización para todos los recursos implementados, que proporciona una representación siempre actualizada de esos recursos.  |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-2-3"></a>NIST 800-53 Control CM-2 (3)

#### <a name="baseline-configuration--retention-of-previous-configurations"></a>Configuración de línea de base | Retención de configuraciones anteriores

**CM-2 (3)** La organización conserva [Asignación: versiones anteriores de definidas por la organización de las configuraciones de línea de base del sistema de información] para admitir la reversión.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de conservar las versiones anteriores de las configuraciones de línea de base de los recursos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-2-7a"></a>NIST 800-53 Control CM-2 (7).a

#### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>Configuración de línea de base | Configuración de sistemas, componentes o dispositivos para zonas de alto riesgo

**CM-2 (7).a** La organización emite [Asignación: dispositivos, componentes del sistema o sistemas de información definidos por la organización] con [Asignación: configuraciones definidas por la organización] para personas que viajan a ubicaciones que la organización considera zonas con un riesgo significativo.

**Responsabilidades:** `Not Applicable`

|||
|---|---|
| **Cliente** | No existe ningún dispositivo físico controlado por el cliente dentro del ámbito de sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | El contenido de los clientes de Microsoft Azure nunca se almacena fuera de Microsoft Azure, que está ubicado físicamente en el territorio continental de Estados Unidos. El personal de Microsoft Azure no viaja con dispositivos incluidos en el inventario de Microsoft Azure. Por lo tanto, este control no es aplicable a Microsoft Azure. |


 ### <a name="nist-800-53-control-cm-2-7b"></a>NIST 800-53 Control CM-2 (7).b

#### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>Configuración de línea de base | Configuración de sistemas, componentes o dispositivos para zonas de alto riesgo

**CM-2 (7).b** La organización aplica [Asignación: medidas de seguridad definidas por la organización] en los dispositivos que se devuelven.

**Responsabilidades:** `Not Applicable`

|||
|---|---|
| **Cliente** | No existe ningún dispositivo físico controlado por el cliente dentro del ámbito de sistemas implementados en Azure. |
| **Proveedor (Microsoft Azure)** | El contenido de los clientes de Microsoft Azure nunca se almacena fuera de Microsoft Azure y el personal de Microsoft Azure no viaja con los dispositivos incluidos en el inventario de Microsoft Azure, por lo que este control no es aplicable. |


 ## <a name="nist-800-53-control-cm-3a"></a>NIST 800-53 Control CM-3.a

#### <a name="configuration-change-control"></a>Control de cambios de configuración

**CM-3.a** La organización determina los tipos de cambios en el sistema de información que controla la configuración.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de determinar qué tipos de cambios en los recursos implementados por el cliente (incluidos los sistemas operativos, las aplicaciones, las bases de datos y el software) controla la configuración. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-cm-3b"></a>NIST 800-53 Control CM-3.b

#### <a name="configuration-change-control"></a>Control de cambios de configuración

**CM-3.b** La organización revisa los cambios propuestos controlados por la configuración en el sistema de información, y aprueba o rechaza dichos cambios con consideración explícita para análisis del impacto de seguridad.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de revisar los cambios propuestos controlados por la configuración en los recursos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-cm-3c"></a>NIST 800-53 Control CM-3.c

#### <a name="configuration-change-control"></a>Control de cambios de configuración

**CM-3.c** La organización documenta las decisiones de cambio de configuración asociadas con el sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de documentar los cambios controlados por la configuración asociados con los recursos implementados por el cliente (consulte CM-03.b). |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-cm-3d"></a>NIST 800-53 Control CM-3.d

#### <a name="configuration-change-control"></a>Control de cambios de configuración

**CM-3.d** La organización implementa los cambios controlados por la configuración aprobados en el sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de implementar los cambios controlados por la configuración aprobados en CM-03.b. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-cm-3e"></a>NIST 800-53 Control CM-3.e

#### <a name="configuration-change-control"></a>Control de cambios de configuración

**CM-3.e** La organización conserva los registros de cambios controlados por la configuración en el sistema de información durante [Asignación: período de tiempo definido por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de conservar un registro de cambios controlados por la configuración en los recursos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-cm-3f"></a>NIST 800-53 Control CM-3.f

#### <a name="configuration-change-control"></a>Control de cambios de configuración

**CM-3.f** La organización audita y revisa las actividades asociadas con los cambios controlados por la configuración en el sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de auditar y revisar los cambios de configuración. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-cm-3g"></a>NIST 800-53 Control CM-3.g

#### <a name="configuration-change-control"></a>Control de cambios de configuración

**CM-3.g** La organización coordina y supervisa las actividades de control de cambios de configuración a través de [Asignación: elemento de control de cambios de configuración definido por la organización (por ejemplo, un comité o la junta directiva)] que organice [Selección (una o más): [Asignación: frecuencia definida por la organización]; [Asignación: condiciones de cambio de configuración definidas por la organización]].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de coordinar y supervisar las actividades de control de cambios de configuración. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-3-1a"></a>NIST 800-53 Control CM-3 (1).a

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Control de cambios de configuración | Automatización de documentación, notificación y prohibición de cambios

**CM-3 (1).a** La organización emplea mecanismos automatizados para documentar los cambios propuestos en el sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de emplear mecanismos automatizados para documentar los cambios propuestos (consulte CM-03.b). |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-3-1b"></a>NIST 800-53 Control CM-3 (1).b

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Control de cambios de configuración | Automatización de documentación, notificación y prohibición de cambios

**CM-3 (1).b** La organización emplea mecanismos automatizados para notificar a [Asignación: autoridades de aprobación definidas por la organización] los cambios propuestos en el sistema de información y solicitar la aprobación de dichos cambios.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de emplear un mecanismo automatizado para remitir y solicitar la aprobación de los cambios propuestos en los recursos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-3-1c"></a>NIST 800-53 Control CM-3 (1).c

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Control de cambios de configuración | Automatización de documentación, notificación y prohibición de cambios

**CM-3 (1).c** La organización emplea mecanismos automatizados para resaltar los cambios propuestos en el sistema de información que no se han aprobado ni rechazado durante [Asignación: período de tiempo definido por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de emplear un mecanismo automatizado para resaltar las propuestas de cambios sin revisar. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-3-1d"></a>NIST 800-53 Control CM-3 (1).d

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Control de cambios de configuración | Automatización de documentación, notificación y prohibición de cambios

**CM-3 (1).d** La organización emplea mecanismos automatizados para prohibir cambios en el sistema de información hasta que se reciban las aprobaciones designadas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de emplear un mecanismo automatizado para prohibir la implementación de cambios no aprobados en los recursos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-3-1e"></a>NIST 800-53 Control CM-3 (1).e

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Control de cambios de configuración | Automatización de documentación, notificación y prohibición de cambios

**CM-3 (1).e** La organización emplea mecanismos automatizados para documentar todos los cambios en el sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de emplear un mecanismo automatizado para documentar todos los cambios implementados en los recursos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-3-1f"></a>NIST 800-53 Control CM-3 (1).f

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Control de cambios de configuración | Automatización de documentación, notificación y prohibición de cambios

**CM-3 (1).f** La organización emplea mecanismos automatizados para notificar a [Asignación: personal definido por la organización] cuando se completan los cambios aprobados en el sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de emplear un mecanismo automatizado para proporcionar notificaciones cuando se completan los cambios aprobados en los recursos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-3-2"></a>NIST 800-53 Control CM-3 (2)

#### <a name="configuration-change-control--test--validate--document-changes"></a>Control de cambios de configuración | Prueba, validación y documentación de cambios

**CM-3 (2)** La organización prueba, valida y documenta los cambios en el sistema de información antes de implementarlos en el sistema operativo.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de probar, validar y documentar los cambios en los recursos implementados por el cliente antes de su implementación. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-3-4"></a>NIST 800-53 Control CM-3 (4)

#### <a name="configuration-change-control--security-representative"></a>Control de cambios de configuración | Representante de seguridad

**CM-3 (4)** La organización requiere que un representante de seguridad de la información sea miembro de [Asignación: elemento de control de cambios de configuración definido por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de asignar un representante de seguridad de la información para que sea miembro del elemento de control de cambios definido en CM-03.g. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-3-6"></a>NIST 800-53 Control CM-3 (6)

#### <a name="configuration-change-control--cryptography-management"></a>Control de cambios de configuración | Administración de criptografía

**CM-3 (6)** La organización garantiza que los mecanismos criptográficos utilizados para proporcionar [Asignación: medidas de seguridad definidas por la organización] se controlen mediante la administración de configuración.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de garantizar que los mecanismos criptográficos se controlen mediante la administración de configuración. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-cm-4"></a>NIST 800-53 Control CM-4

#### <a name="security-impact-analysis"></a>Análisis del impacto de seguridad

**CM-4** La organización analiza los cambios en el sistema de información para determinar los posibles impactos de seguridad antes de implementar los cambios.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de analizar los cambios propuestos en los recursos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-4-1"></a>NIST 800-53 Control CM-4 (1)

#### <a name="security-impact-analysis--separate-test-environments"></a>Análisis del impacto de seguridad | Entornos de prueba independientes

**CM-4 (1)** La organización analiza los cambios en el sistema de información en un entorno de prueba independiente antes de su implementación en uno de tipo operativo. Para ello, busca impactos de seguridad debido a errores, puntos débiles, incompatibilidades o actividades delictivas intencionadas.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de analizar los cambios propuestos en los recursos implementados por el cliente en un entorno de prueba antes de su implementación en uno de tipo operativo. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-cm-5"></a>NIST 800-53 Control CM-5

#### <a name="access-restrictions-for-change"></a>Restricciones de acceso para cambios

**CM-5** La organización define, documenta, aprueba y aplica las restricciones de acceso físico y lógico asociadas con los cambios en el sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los privilegios de cuenta de Azure Active Directory se implementan mediante un control de acceso basado en rol y la asignación de usuarios a roles que proporcionan un control estricto sobre qué usuarios pueden ver y controlar los recursos implementados. Los privilegios de la cuenta de Active Directory se implementan mediante un control de acceso basado en rol y la asignación de usuarios a grupos de seguridad. Estos grupos de seguridad controlan las acciones que los usuarios pueden realizar con respecto a la configuración del sistema operativo. El cliente puede extender estos esquemas basados en rol para cumplir las necesidades de la misión. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-5-1"></a>NIST 800-53 Control CM-5 (1)

#### <a name="access-restrictions-for-change--automated-access-enforcement--auditing"></a>Restricciones de acceso para cambios | Automatización de la auditoría y el cumplimiento de acceso

**CM-5 (1)** El sistema de información aplica las restricciones de acceso y permite auditar las acciones de cumplimiento.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los privilegios de cuenta de Azure Active Directory se implementan mediante un control de acceso basado en rol y la asignación de usuarios a roles que proporcionan un control estricto sobre qué usuarios pueden ver y controlar los recursos implementados. Los privilegios de la cuenta de Active Directory se implementan mediante un control de acceso basado en rol y la asignación de usuarios a grupos de seguridad. Estos grupos de seguridad controlan las acciones que los usuarios pueden realizar con respecto a la configuración del sistema operativo. Todos los accesos e intentos de acceso se auditan. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-5-2"></a>NIST 800-53 Control CM-5 (2)

#### <a name="access-restrictions-for-change--review-system-changes"></a>Restricciones de acceso para cambios | Revisión de los cambios del sistema

**CM-5 (2)** La organización revisa los cambios en el sistema de información [Asignación: frecuencia definida por la organización] y [Asignación: circunstancias definidas por la organización] para determinar si se han producido cambios no autorizados.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de revisar los cambios en los recursos implementados por el cliente para determinar si se han producido cambios no autorizados. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-5-3"></a>NIST 800-53 Control CM-5 (3)

#### <a name="access-restrictions-for-change--signed-components"></a>Restricciones de acceso para cambios | Componentes firmados

**CM-5 (3)** El sistema de información evita la instalación de [Asignación: componentes de software y firmware definidos por la organización] sin necesidad de comprobar que el componente se haya firmado digitalmente con un certificado reconocido y aprobado por la organización.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Las máquinas virtuales implementadas por este programa Azure Blueprint implementan Windows AppLocker para especificar qué usuarios pueden instalar o ejecutar determinadas aplicaciones. Además, todas las actualizaciones del sistema operativo Windows están firmadas digitalmente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-5-5a"></a>NIST 800-53 Control CM-5 (5).a

#### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>Restricciones de acceso para cambios | Limitación de los privilegios operativos y de producción

**CM-5 (5).a** La organización limita los privilegios para cambiar los componentes del sistema de información y los datos relacionados con el sistema en un entorno operativo o de producción.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de limitar los privilegios para realizar cambios en entornos operativos o de producción implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-5-5b"></a>NIST 800-53 Control CM-5 (5).b

#### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>Restricciones de acceso para cambios | Limitación de los privilegios operativos y de producción

**CM-5 (5).b** La organización revisa y reevalúa los privilegios [Asignación: frecuencia definida por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de revisar y reevaluar los privilegios definidos en CM-05(05).a. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-cm-6a"></a>NIST 800-53 Control CM-6.a

#### <a name="configuration-settings"></a>Valores de configuración

**CM-6.a** La organización establece y documenta los valores de configuración de los productos de tecnologías de la información empleados en el sistema de información mediante [Asignación: listas de comprobación de configuración de seguridad definidas por la organización] que reflejen el modo más restrictivo en consonancia con los requisitos operativos.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este programa Azure Blueprint incluye una línea de Desired State Configuration (DSC) para cada máquina virtual implementada. Estos scripts de PowerShell declarativos definen y configuran los recursos en los que se aplica. El cliente puede ampliar la línea de base de DSC incluida para los recursos implementados por esta solución para satisfacer las necesidades de la misión. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-cm-6b"></a>NIST 800-53 Control CM-6.b

#### <a name="configuration-settings"></a>Valores de configuración

**CM-6.b** La organización implementa los valores de configuración.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este programa Azure Blueprint incluye una línea de Desired State Configuration (DSC) para cada máquina virtual implementada. Las líneas de base se aplican automáticamente a las máquinas virtuales durante la implementación mediante la extensión de máquina virtual de los scripts personalizados. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-cm-6c"></a>NIST 800-53 Control CM-6.c

#### <a name="configuration-settings"></a>Valores de configuración

**CM-6.c** La organización identifica, documenta y aprueba las desviaciones de los valores de configuración establecidas de [Asignación: componentes del sistema de información definidos por la organización] según [Asignación: requisitos operativos definidos por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de identificar, documentar y aprobar las desviaciones de los valores de configuración establecidos de los recursos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-cm-6d"></a>NIST 800-53 Control CM-6.d

#### <a name="configuration-settings"></a>Valores de configuración

**CM-6.d** La organización supervisa y controla los cambios en los valores de configuración de acuerdo con los procedimientos y las directivas de la organización.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este programa Azure Blueprint implementa DSC de Automation. DSC de Automation alinea las configuraciones de máquina con una configuración específica definida por la organización. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-6-1"></a>NIST 800-53 Control CM-6 (1)

#### <a name="configuration-settings--automated-central-management--application--verification"></a>Valores de configuración | Automatización de la comprobación, aplicación y administración de manera centralizada

**CM-6 (1)** La organización emplea mecanismos automatizados para administrar, aplicar y comprobar los valores de configuración de manera centralizada de [Asignación: componentes del sistema de información definidos por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este programa Azure Blueprint implementa DSC de Azure Automation. DSC de Automation alinea las configuraciones de máquina con una configuración específica definida por la organización y supervisa continuamente los cambios. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-6-2"></a>NIST 800-53 Control CM-6 (2)

#### <a name="configuration-settings--respond-to-unauthorized-changes"></a>Opciones de configuración | Respuesta a cambios no autorizados

**CM-6 (2)** La organización emplea [Asignación: medidas de seguridad definidas por la organización] para responder a cambios no autorizados en [Asignación: valores de configuración definidos por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este programa Azure Blueprint implementa DSC de Azure Automation. DSC de Automation, que forma parte de Operations Management Suite (OMS) de Azure, puede configurarse para generar una alerta o solucionar errores de configuración cuando se detecten. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-cm-7a"></a>NIST 800-53 Control CM-7.a

#### <a name="least-functionality"></a>Funcionalidad mínima

**CM-7.a** La organización configura el sistema de información para proporcionar solo las funcionalidades esenciales.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Los recursos que implementa este programa Azure Blueprint están configurados con el objetivo de proporcionar la funcionalidad mínima de acuerdo con su finalidad prevista. Se incluye una línea de base de Desired State Configuration (DSC) para cada máquina virtual implementada. Estos scripts de PowerShell declarativos definen y configuran los recursos en los que se aplica. El cliente puede ampliar la línea de base de DSC incluida para los recursos implementados por esta solución para limitar aún más la funcionalidad con el fin de satisfacer las necesidades de la misión. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-cm-7b"></a>NIST 800-53 Control CM-7.b

#### <a name="least-functionality"></a>Funcionalidad mínima

**CM-7.b** La organización prohíbe o restringe el uso de los siguientes puertos, protocolos, servicios o funciones: [Asignación: funciones, puertos, protocolos o servicios prohibidos o restringidos definidos por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este programa Azure Blueprint implementa grupos de seguridad de red y de Azure Application Gateway para restringir el uso de puertos y protocolos a solo los necesarios. El cliente puede configurar aún más Application Gateway, los grupos de seguridad de red y las líneas de base de DSC para máquinas virtuales con el fin de restringir el uso de funciones, puertos, protocolos y servicios, y proporcionar por tanto solo la funcionalidad prevista. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-7-1a"></a>NIST 800-53 Control CM-7 (1).a

#### <a name="least-functionality--periodic-review"></a>Funcionalidad mínima | Revisión periódica

**CM-7 (1).a** La organización revisa el sistema de información [Asignación: frecuencia definida por la organización] para identificar las funciones, los puertos, los protocolos y los servicios innecesarios o no seguros.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de revisar los recursos implementados por el cliente (incluidos los sistemas operativos, las aplicaciones, las bases de datos y el software) para identificar puertos, protocolos, funciones y servicios innecesarios o no seguros. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-7-1b"></a>NIST 800-53 Control CM-7 (1).b

#### <a name="least-functionality--periodic-review"></a>Funcionalidad mínima | Revisión periódica

**CM-7 (1).b** La organización deshabilita [Asignación: funciones, puertos, protocolos y servicios definidos por la organización en el sistema de información que se consideran innecesarios o no seguros].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de deshabilitar las funciones, los puertos, los protocolos y los servicios que se consideran innecesarios o no seguros. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-7-2"></a>NIST 800-53 Control CM-7 (2)

#### <a name="least-functionality--prevent-program-execution"></a>Funcionalidad mínima | Evitación de la ejecución de programas

**CM-7 (2)** El sistema de información impide la ejecución de programas de acuerdo con [Selección (una o más): [Asignación: directivas definidas por la organización relativas a las restricciones y el uso de programas de software]; las reglas que autorizan los términos y condiciones de uso de programas de software].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de evitar la ejecución de los programas de acuerdo con las directivas de uso de programas de software definidas por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-7-5a"></a>NIST 800-53 Control CM-7 (5).a

#### <a name="least-functionality--authorized-software--whitelisting"></a>Funcionalidad mínima | Inclusión en listas blancas de software autorizado

**CM-7 (5).a** La organización identifica [Asignación: programas de software definidos por la organización con autorización para ejecutarse en el sistema de información].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de identificar los programas de software autorizados. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-7-5b"></a>NIST 800-53 Control CM-7 (5).b

#### <a name="least-functionality--authorized-software--whitelisting"></a>Funcionalidad mínima | Inclusión en listas blancas de software autorizado

**CM-7 (5).b** La organización emplea una directiva de denegar todo y de permisos por excepción para permitir la ejecución de programas de software autorizados en el sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de emplear una directiva de denegar todo y de permisos por excepción para permitir la ejecución de programas de software autorizados en los recursos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-7-5c"></a>NIST 800-53 Control CM-7 (5).c

#### <a name="least-functionality--authorized-software--whitelisting"></a>Funcionalidad mínima | Inclusión en listas blancas de software autorizado

**CM-7 (5).c** La organización revisa y actualiza los programas de software autorizados [Asignación: frecuencia definida por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de revisar y actualizar la lista de programas de software autorizados. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-cm-8a"></a>NIST 800-53 Control CM-8.a

#### <a name="information-system-component-inventory"></a>Inventario de componentes del sistema de información

**CM-8.a** La organización desarrolla y documenta un inventario de componentes del sistema de información que reflejan el sistema de información actual con precisión; incluye todos los componentes dentro del límite de autorización del sistema de información; se encuentra en el nivel de granularidad que se estima necesario para realizar el seguimiento y generar los informes; e incluye [Asignación: información definida por la organización que se estima necesaria para lograr que la rendición de cuentas en los componentes del sistema de información se realice de forma eficaz].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este programa Azure Blueprint implementa todos los recursos en un grupo de recursos de Azure Resource Manager. Azure Resource Manager proporciona una lista siempre actualizada de los recursos implementados, y puede personalizarse con el fin de etiquetar y agrupar recursos para la administración del inventario. Los recursos implementados por esta solución tienen una etiqueta de recurso específica que se puede asociar con el límite del sistema. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-cm-8b"></a>NIST 800-53 Control CM-8.b

#### <a name="information-system-component-inventory"></a>Inventario de componentes del sistema de información

**CM-8.b** La organización revisa y actualiza el inventario de componentes del sistema de información [Asignación: frecuencia definida por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este programa Azure Blueprint implementa todos los recursos en un grupo de recursos de Azure Resource Manager. Azure Resource Manager proporciona una lista siempre actualizada de los recursos implementados disponibles para su revisión en Azure Portal. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-8-1"></a>NIST 800-53 Control CM-8 (1)

#### <a name="information-system-component-inventory--updates-during-installations--removals"></a>Inventario de componentes del sistema de información | Actualizaciones durante las instalaciones y desinstalaciones

**CM-8 (1)** La organización actualiza el inventario de componentes del sistema de información como una parte integral de las instalaciones y desinstalaciones de componentes, y de las actualizaciones del sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este programa Azure Blueprint implementa todos los recursos en un grupo de recursos de Azure Resource Manager. La hoja de recursos de Azure Portal enumera todos los recursos implementados, lo que proporciona un inventario siempre actualizado cuando se implementan y quitan recursos. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-8-2"></a>NIST 800-53 Control CM-8 (2)

#### <a name="information-system-component-inventory--automated-maintenance"></a>Inventario de componentes del sistema de información | Mantenimiento automatizado

**CM-8 (2)** La organización emplea mecanismos automatizados para ayudar a mantener el inventario de componentes del sistema de información actualizado, completo, preciso y disponible.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este programa Azure Blueprint implementa todos los recursos en un grupo de recursos de Azure Resource Manager. La hoja de recursos de Azure Portal enumera todos los recursos implementados, lo que proporciona un inventario siempre actualizado cuando se implementan y quitan recursos. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-8-3a"></a>NIST 800-53 Control CM-8 (3).a

#### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>Inventario de componentes del sistema de información | Automatización de la detección de componentes no autorizados

**CM-8 (3).a** La organización emplea mecanismos automatizados [Asignación: frecuencia definida por la organización] para detectar la presencia de componentes de hardware, software y firmware no autorizados en el sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de emplear mecanismos automatizados para detectar la presencia de software no autorizado en recursos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-8-3b"></a>NIST 800-53 Control CM-8 (3).b

#### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>Inventario de componentes del sistema de información | Automatización de la detección de componentes no autorizados

**CM-8 (3).b** La organización realiza las siguientes acciones cuando se detectan componentes no autorizados: [Selección (una o más): deshabilita el acceso de red de dichos componentes; aísla los componentes; envía notificaciones [Asignación: personal o roles definidos por la organización]].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de realizar alguna acción cuando se detecta software no autorizado. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-8-4"></a>NIST 800-53 Control CM-8 (4)

#### <a name="information-system-component-inventory--accountability-information"></a>Inventario de componentes del sistema de información | Información de rendición de cuentas

**CM-8 (4)** La organización incluye datos del inventario de componentes en el sistema de información, un medio para identificar por [Selección (una o más): nombre, posición; rol] los individuos responsables de administrar los componentes.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este programa Azure Blueprint implementa todos los recursos en un grupo de recursos de Azure Resource Manager. Las etiquetas de recursos de Azure son pares clave-valor que se pueden emplear para clasificar los recursos con fines de rendición de cuentas o administración. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-8-5"></a>NIST 800-53 Control CM-8 (5)

#### <a name="information-system-component-inventory--no-duplicate-accounting-of-components"></a>Inventario de componentes del sistema de información | Contabilización de componentes duplicados

**CM-8 (5)** La organización comprueba que todos los componentes dentro del límite de autorización del sistema de información no estén duplicados en otros inventarios de componentes del sistema de información.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Este programa Azure Blueprint implementa todos los recursos en un grupo de recursos de Azure Resource Manager. Azure Resource Manager proporciona una lista siempre actualizada de los recursos implementados. Los recursos implementados por esta solución tienen una etiqueta de recurso específica que se puede asociar con el límite del sistema. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-cm-9a"></a>NIST 800-53 Control CM-9.a

#### <a name="configuration-management-plan"></a>Plan de administración de configuración

**CM-9.a** La organización desarrolla, documenta e implementa un plan de administración de configuración para el sistema de información que aborda los roles, las responsabilidades, y los procesos y procedimientos de administración de configuración.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de desarrollar, documentar e implementar un plan de administración de configuración para los recursos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-cm-9b"></a>NIST 800-53 Control CM-9.b

#### <a name="configuration-management-plan"></a>Plan de administración de configuración

**CM-9.b** La organización desarrolla, documenta e implementa un plan de administración de configuración para el sistema de información que establece un proceso con el objetivo de identificar los elementos de configuración de todo el ciclo de vida de desarrollo, así como de administrar la configuración de dichos elementos de configuración.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de desarrollar, documentar e implementar un plan de administración de configuración para los recursos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-cm-9c"></a>NIST 800-53 Control CM-9.c

#### <a name="configuration-management-plan"></a>Plan de administración de configuración

**CM-9.c** La organización desarrolla, documenta e implementa un plan de administración de configuración para el sistema de información que defina los elementos de configuración del sistema de información y que los ponga bajo control de la administración de configuración.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de desarrollar, documentar e implementar un plan de administración de configuración para los recursos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-cm-9d"></a>NIST 800-53 Control CM-9.d

#### <a name="configuration-management-plan"></a>Plan de administración de configuración

**CM-9.d** La organización desarrolla, documenta e implementa un plan de administración de configuración para el sistema de información que proteja el plan de administración de configuración para evitar que se modifique o divulgue.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de desarrollar, documentar e implementar un plan de administración de configuración para los recursos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-cm-10a"></a>NIST 800-53 Control CM-10.a

#### <a name="software-usage-restrictions"></a>Restricciones de uso de software

**CM-10.a** La organización utiliza el software y la documentación asociada de acuerdo con los contratos y las leyes de copyright.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Las licencias de Windows y SQL Server se incluyen para los recursos implementados por este programa Azure Blueprint. Se trata de una característica integrada de Azure. Las organizaciones con contratos de licencia de software existentes pueden plantearse implementar modelos de licencia alternativos. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-cm-10b"></a>NIST 800-53 Control CM-10.b

#### <a name="software-usage-restrictions"></a>Restricciones de uso de software

**CM-10.b** La organización realiza un seguimiento del uso del software y la documentación asociada protegidos por licencias de cantidad con el objetivo de controlar su copia y distribución.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | Las licencias de Windows y SQL Server se incluyen para los recursos implementados por este programa Azure Blueprint. El usuario no tiene que realizar un seguimiento aparte del uso de las licencias. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-cm-10c"></a>NIST 800-53 Control CM-10.c

#### <a name="software-usage-restrictions"></a>Restricciones de uso de software

**CM-10.c** La organización controla y documenta el uso de la tecnología de uso compartido de archivos punto a punto para garantizar que esta funcionalidad no se utilice para la distribución, la exposición, la ejecución o la reproducción sin autorización de obras con copyright.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | No hay ninguna funcionalidad de uso compartido de archivos punto a punto implementada por este programa Azure Blueprint. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-10-1"></a>NIST 800-53 Control CM-10 (1)

#### <a name="software-usage-restrictions--open-source-software"></a>Restricciones de uso de software | Software de código abierto

**CM-10 (1)** La organización establece las siguientes restricciones en el uso de software de código abierto: [Asignación: restricciones definidas por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | La directiva de administración de configuración de nivel empresarial del cliente puede abordar las restricciones en el uso de software de código abierto. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-cm-11a"></a>NIST 800-53 Control CM-11.a

#### <a name="user-installed-software"></a>Software instalado por el usuario

**CM-11.a** La organización establece [Asignación: directivas definidas por la organización] que rigen la instalación de software por parte de los usuarios.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de establecer una directiva que rija la instalación de software por parte de los usuarios en recursos implementados por el cliente. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-cm-11b"></a>NIST 800-53 Control CM-11.b

#### <a name="user-installed-software"></a>Software instalado por el usuario

**CM-11.b** La organización aplica las directivas de instalación de software a través de [Asignación: métodos definidos por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de aplicar las directivas de instalación de software. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ## <a name="nist-800-53-control-cm-11c"></a>NIST 800-53 Control CM-11.c

#### <a name="user-installed-software"></a>Software instalado por el usuario

**CM-11.c** La organización supervisa el cumplimiento de las directivas por [Asignación: frecuencia definida por la organización].

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de supervisar el cumplimiento de los recursos implementados por el cliente con las directivas identificadas en CM-11.a. |
| **Proveedor (Microsoft Azure)** | No aplicable |


 ### <a name="nist-800-53-control-cm-11-1"></a>NIST 800-53 Control CM-11 (1)

#### <a name="user-installed-software--alerts-for-unauthorized-installations"></a>Software instalado por el usuario | Alertas de instalaciones no autorizadas

**CM-11 (1)** El sistema de información envía alertas [Asignación: personal o roles definidos por la organización] cuando se detecta la instalación de software no autorizado.

**Responsabilidades:** `Customer Only`

|||
|---|---|
| **Cliente** | El cliente es responsable de enviar alertas cuando se detecta la instalación de software no autorizado. |
| **Proveedor (Microsoft Azure)** | No aplicable |


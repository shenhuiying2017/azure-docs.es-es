---
title: Glosario de Azure Active Directory Identity Protection | Microsoft Docs
description: Glosario de Azure Active Directory Identity Protection
services: active-directory
keywords: "azure active directory identity protection, detección de aplicaciones en la nube, administración de aplicaciones, seguridad, riesgo, nivel de riesgo, punto vulnerable, directiva de seguridad, glosario"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 833119a5-33d6-4482-adda-fa35218c72c3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 2cf64925cff9a78cf83532a1cfd231f7a1d98304
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Glosario de Azure Active Directory Identity Protection
### <a name="at-risk-user"></a>En riesgo (usuario)
Usuario con uno o más eventos de riesgo activos. 

### <a name="atypical-sign-in-location"></a>Ubicación de inicio de sesión inusual
Inicio de sesión desde una ubicación geográfica que no es la usual para un usuario específico, usuarios similares o el inquilino.

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
Módulo de seguridad de Azure Active Directory que proporciona una vista consolidada de los eventos de riesgo y posibles puntos vulnerables que afectan a las identidades de una organización.

### <a name="conditional-access"></a>Acceso condicional
Directiva para proteger el acceso a los recursos. Las reglas de acceso condicional se almacenan en Azure Active Directory, que las evalúa antes de conceder acceso al recurso.  Reglas de ejemplo son la restricción de acceso según la ubicación del usuario, el estado del dispositivo o el método de autenticación de usuario.

### <a name="credentials"></a>Credenciales
Información que incluye la identificación y prueba de identificación que se usa para obtener acceso a recursos locales y de red. Ejemplos de credenciales son nombres de usuario, contraseñas, tarjetas inteligentes y certificados.

### <a name="event"></a>Evento
Registro de una actividad en Azure Active Directory.

### <a name="false-positive-risk-event"></a>Falso positivo (evento de riesgo)
Estado de un evento de riesgo establecido manualmente por un usuario de Identity Protection, que indica que el evento de riesgo se investigó y marcó incorrectamente como evento de riesgo.

### <a name="identity"></a>Identidad
Persona o entidad que se debe comprobar mediante autenticación, basándose en criterios como contraseñas o certificados.

### <a name="identity-risk-event"></a>Evento de riesgo de identidad
Evento de AAD marcado como anómalo por Identity Protection, que puede indicar que una identidad está en peligro.

### <a name="ignored-risk-event"></a>Omitido (evento de riesgo)
Estado de evento de riesgo establecido manualmente por un usuario de Identity Protection, que indica que el evento de riesgo se cierra sin aplicar una acción de corrección.

### <a name="impossible-travel-from-atypical-locations"></a>Viaje imposible desde ubicaciones inusuales
Evento de riesgo que se desencadena cuando se detectan dos inicios de sesión para el mismo usuario, en el que al menos uno de ellos es desde una ubicación de inicio de sesión inusual y en el que el tiempo entre inicios de sesión es menor que el tiempo mínimo que se tardaría en viajar físicamente entre estas ubicaciones.  

### <a name="investigation"></a>Investigación
Proceso de revisión de las actividades, los registros y otra información pertinente relacionada con un incidente de riesgo para decidir si son necesarios pasos de mitigación o de corrección, y entender si se ha puesto en peligro la identidad y de qué manera, así como comprender cómo se utilizaba la identidad en peligro.

### <a name="leaked-credentials"></a>Credenciales con fugas
Evento de riesgo que se desencadena cuando se encuentran credenciales de usuario actuales (nombre de usuario y contraseña) publicadas en la web oscura por nuestros investigadores.

### <a name="mitigation"></a>Mitigación
Acción para limitar o eliminar la capacidad de un atacante de aprovechar una identidad o un dispositivo en peligro sin restaurar la identidad o el dispositivo a un estado seguro. Una mitigación no resuelve los anteriores eventos de riesgo asociados a la identidad o el dispositivo.

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
Método de autenticación que requiere dos o más métodos de autenticación, que pueden incluir algo que el usuario tiene, como un certificado; algo que el usuario sabe, como nombres de usuario, contraseñas o frases de contraseña; atributos físicos, como una huella digital; y atributos personales, como una firma personal.

### <a name="offline-detection"></a>Detección sin conexión
Detección de anomalías y evaluación del riesgo de un evento como el intento de inicio de sesión después del hecho, para un evento que ya ha ocurrido.

### <a name="policy-condition"></a>Condición de directiva
Parte de una directiva de seguridad que define las entidades (grupos, usuarios, aplicaciones, plataformas de dispositivos, estados de dispositivos, intervalos IP, tipos de cliente) incluidas en la directiva o excluidas de ella.

### <a name="policy-rule"></a>Regla de directiva
Parte de una directiva de seguridad que describe las circunstancias que pueden desencadenar la directiva y las acciones aplicadas cuando se desencadena la directiva.

### <a name="prevention"></a>Prevención
Acción para evitar daños en la organización debidos al uso inapropiado de una identidad o un dispositivo sospechoso o que se sabe que está en peligro. Una acción de prevención no protege la identidad o el dispositivo y no resuelve eventos de riesgo anteriores.

### <a name="privileged-user"></a>Con privilegios (usuario)
Usuario que, en el momento de un evento de riesgo, tenía permisos de administrador permanentes o temporales para uno o más recursos de Azure Active Directory, como un administrador global, administrador de facturación, administrador de servicios, administrador de usuarios y administrador de contraseñas. 

### <a name="real-time"></a>Tiempo real
Ver Detección en tiempo real.

### <a name="real-time-detection"></a>Detección en tiempo real
Detección de anomalías y evaluación del riesgo de un evento como el intento de inicio de sesión antes de que el evento pueda continuar.

### <a name="remediated-risk-event"></a>Corregido (evento de riesgo)
Estado del evento de riesgo establecido automáticamente por Identity Protection, que indica que el evento de riesgo se corrigió mediante la acción de corrección estándar para este tipo de eventos de riesgo. Por ejemplo, cuando se restablece la contraseña de usuario, se corrigen automáticamente muchos eventos de riesgo que indican que se había puesto en peligro la contraseña anterior.

### <a name="remediation"></a>Corrección
Acción para proteger una identidad o un dispositivo que antes fue sospechoso o que se sabe que estuvo en peligro. Una acción de corrección restaura la identidad o el dispositivo a un estado seguro y resuelve los anteriores eventos de riesgo asociados a la identidad o el dispositivo.

### <a name="resolved-risk-event"></a>Resuelto (evento de riesgo)
Estado del evento de riesgo establecido manualmente por un usuario de Identity Protection, que indica que el usuario aplicó una acción de corrección adecuada fuera de Identity Protection y que se debe considerar el evento de riesgo cerrado.

### <a name="risk-event-status"></a>Estado de evento de riesgo
Propiedad de un evento de riesgo, que indica si el evento está activo y, si está cerrado, el motivo para haberlo cerrarlo.

### <a name="risk-event-type"></a>Tipo de evento de riesgo
Categoría del evento de riesgo, que indica el tipo de anomalía que provocó el evento para que se considere de riesgo.

### <a name="risk-level-risk-event"></a>Nivel de riesgo (evento de riesgo)
Indicación (Alto, Medio o Bajo) de la gravedad del evento de riesgo para ayudar a los usuarios de Identity Protection a dar prioridad a las acciones que aplican a fin de reducir el riesgo en su organización. 

### <a name="risk-level-sign-in"></a>Nivel de riesgo (inicio de sesión)
Indicación (Alto, Medio o Bajo) de la probabilidad de que, para un inicio de sesión específico, otra persona esté intentando utilizar la identidad del usuario.

### <a name="risk-level-user-compromise"></a>Nivel de riesgo (compromiso de usuario)
Indicación (Alto, Medio o Bajo) de la probabilidad de que se haya puesto en peligro una identidad.

### <a name="risk-level-vulnerability"></a>Nivel de riesgo (vulnerabilidad)
Indicación (Alto, Medio o Bajo) de la gravedad del punto vulnerable para ayudar a los usuarios de Identity Protection a dar prioridad a las acciones que aplican a fin de reducir el riesgo en su organización.

### <a name="secure-identity"></a>Proteger (identidad)
Aplicar acciones de corrección, como un cambio de contraseña o un restablecimiento de la imagen inicial de una máquina para restaurar una identidad potencialmente en peligro a un estado seguro.

### <a name="security-policy"></a>Directiva de seguridad
Recopilación de reglas y condiciones de una directiva. Una directiva se puede aplicar a entidades como usuarios, grupos, aplicaciones, dispositivos, plataformas de dispositivos, estados de dispositivos, intervalos IP y tipos de clientes Auth2.0. Cuando una directiva está habilitada, se evalúa cada vez que una entidad incluida en la directiva emite un token para un recurso.

### <a name="sign-in-v"></a>Iniciar sesión
Autenticar una identidad en Azure Active Directory.

### <a name="sign-in-n"></a>Inicio de sesión
Proceso o acción de autenticar una identidad en Azure Active Directory y evento que captura esta operación.

### <a name="sign-in-from-anonymous-ip-address"></a>Inicio de sesión desde dirección IP anónima
Evento de riesgo que se desencadena después de un inicio de sesión correcto desde una dirección IP identificada como una dirección IP de proxy anónima.

### <a name="sign-in-from-infected-device"></a>Inicio de sesión desde dispositivo infectado
Evento de riesgo que se desencadena cuando un inicio de sesión se origina desde una dirección IP que se sabe que utilizan uno o más dispositivos en peligro que están intentando comunicarse de forma activa con un servidor bot.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Inicio de sesión desde dirección IP con actividad sospechosa
Evento de riesgo que se desencadena después de un inicio de sesión correcto desde una dirección IP con un gran número de intentos de inicios de sesión erróneos entre varias cuentas de usuario durante un corto período de tiempo.

### <a name="sign-in-from-unfamiliar-location"></a>Inicio de sesión desde ubicación desconocida
Evento de riesgo que se desencadena cuando un usuario inicia sesión correctamente desde una nueva ubicación (IP, latitud/longitud y aviso de envío por adelantado).

### <a name="sign-in-risk"></a>Riesgo de inicio de sesión
Ver Nivel de riesgo (inicio de sesión)

### <a name="sign-in-risk-policy"></a>Directiva de riesgo de inicio de sesión
Directiva de acceso condicional que evalúa el riesgo en un inicio de sesión específico y aplica mitigaciones de acuerdo con las reglas y condiciones predefinidas.

### <a name="user-compromise-risk"></a>Riesgo de compromiso de usuario
Ver Nivel de riesgo (compromiso de usuario).

### <a name="user-risk"></a>Riesgo de usuario
Ver Nivel de riesgo (compromiso de usuario).

### <a name="user-risk-policy"></a>Directiva de riesgo de usuario
Directiva de acceso condicional que tiene en cuenta el inicio de sesión y aplica mitigaciones de acuerdo con las reglas y condiciones predefinidas.

### <a name="users-flagged-for-risk"></a>Usuarios marcados con riesgo
Usuarios con eventos de riesgo activos o corregidos.

### <a name="vulnerability"></a>Punto vulnerable
Configuración o condición en Azure Active Directory, que hace que el directorio sea susceptible de recibir ataques o amenazas.

## <a name="see-also"></a>Otras referencias
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)


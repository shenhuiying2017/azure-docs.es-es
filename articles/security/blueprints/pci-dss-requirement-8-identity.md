---
title: "Plano técnico de procesamiento de pagos de Azure: requisitos de identidad"
description: Requisito 8 de PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 1a398601-8c48-4f8e-b3d4-eba94edad61c
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: f77cc3c9926b5316913c70e5f4412383e55c5193
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="identity-requirements-for-pci-dss-compliant-environments"></a>Requisitos de identidad para entornos compatibles con PCI DSS 
## <a name="pci-dss-requirement-8"></a>Requisito 8 de PCI DSS

**Identificación y autenticación del acceso a componentes del sistema**

> [!NOTE]
> El [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) (Consejo de estándares de seguridad para el sector de tarjetas de pago) define estos requisitos como parte del [Estándar de Seguridad de Datos (DDS) para la PCI, versión 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte el PCI DSS para obtener información sobre procedimientos de comprobación e instrucciones para cada requisito.

El hecho de asignar un identificador único (id.) a cada persona que tenga acceso garantiza que cada usuario sea responsable de forma exclusiva de sus acciones. Cuando se habilita este mecanismo de responsabilidad, las acciones llevadas a cabo en los sistemas y datos críticos se pueden atribuir a usuarios y procesos conocidos y autorizados.
La eficacia de una contraseña viene determinada en gran medida por el diseño y la implementación del sistema de autenticación, en particular, la frecuencia de intentos de contraseña que puede realizar un atacante y los métodos de seguridad empleados para proteger las contraseñas de usuario en el punto de entrada, tanto durante la transmisión como en el almacenamiento.

> [!NOTE]
> Estos requisitos son aplicables a todas las cuentas (incluidas las cuentas de punto de venta) con capacidades administrativas y a todas las cuentas usadas para consultar información de los titulares, así como para acceder a dicha información o a sistemas con datos de los titulares de tarjetas. Esto incluye las cuentas usadas por proveedores y terceros (por ejemplo, para mantenimiento o soporte técnico). Estos requisitos no se aplican a las cuentas usadas por los consumidores (por ejemplo, titulares de tarjetas). Sin embargo, los requisitos 8.1.1, 8.2, 8.5, 8.2.3 a 8.2.5 y 8.1.6 a 8.1.8 no están pensados para aplicarse a cuentas de usuario que se utilizan en una aplicación de pago en punto de venta, dado que solo tienen acceso a un número de tarjeta a la vez con el fin de facilitar una única transacción (por ejemplo, cuentas de cajero).
 
## <a name="pci-dss-requirement-81"></a>Requisito 8.1 de PCI DSS

**8.1** Defina e implemente directivas y procedimientos que garanticen que la administración de la identificación de usuarios administradores y no consumidores sea adecuada en todos los componentes del sistema, como se indica a continuación.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso proporciona un caso de uso y una descripción del uso correcto de los administradores para la implementación de ejemplo.|



### <a name="pci-dss-requirement-811"></a>Requisito 8.1.1 de PCI DSS

**8.1.1** Asigne un identificador único a todos los usuarios antes de permitirles el acceso a componentes del sistema o a datos de los titulares de tarjetas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso implementa Azure Active Directory y control de acceso basado en rol (RBAC) de Azure Active Directory para garantizar que todos los usuarios tengan un identificador único. Para obtener más información, consulte [Guía de PCI: Administración de identidades](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-812"></a>Requisito 8.1.2 de PCI DSS

**8.1.2** Controle la adición, eliminación y modificación de identificadores de usuario, credenciales y otros objetos de identificación.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso implementa Azure Active Directory y control de acceso basado en rol (RBAC) de Azure Active Directory para garantizar que todos los usuarios tengan un identificador único. Para obtener más información, consulte [Guía de PCI: Administración de identidades](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-813"></a>Requisito 8.1.3 de PCI DSS

**8.1.3** Revoque de inmediato el acceso a los usuarios finalizados.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso usa Azure Active Directory para administrar los usuarios. Los usuarios se pueden revocar en Active Directory.|



### <a name="pci-dss-requirement-814"></a>Requisito 8.1.4 de PCI DSS

**8.1.4** Quite o deshabilite cuentas de usuario inactivas en un plazo de 90 días.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso usa Azure Active Directory para administrar los usuarios. Se puede establecer la opción `-enableADDomainPasswordPolicy` para garantizar que las contraseñas expiren en 90 días.|



### <a name="pci-dss-requirement-815"></a>Requisito 8.1.5 de PCI DSS

**8.1.5** Administre los identificadores usados por terceros para tener acceso a los componentes del sistema a través de acceso remoto, así como para ofrecer soporte técnico o realizar el mantenimiento pertinente, de la manera siguiente:
- Solo se habilita durante el período de tiempo necesario y se deshabilita cuando no está en uso.
- Se supervisa cuando está en uso.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure ha adoptado directivas aplicables de seguridad corporativa y organizativa, incluida una directiva de seguridad de la información. Las directivas han sido aprobadas, publicadas y comunicadas a Microsoft Azure. La directiva de seguridad de la información requiere que el acceso a los recursos de Microsoft Azure se conceda basándose en una justificación de negocio, que tenga la autorización del propietario del recurso y que sea limitado en función de los principios de "necesidad de conocer" y "privilegios mínimos". Además, la directiva también aborda los requisitos de ciclo de vida de administración de acceso, incluido el aprovisionamiento de acceso, la autenticación, la autorización de acceso, la eliminación de derechos de acceso y las revisiones de acceso periódicas. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La demostración de la tienda web de Contoso ha implementado Azure Active Directory y el control de acceso basado en rol de Azure Active Directory para administrar el acceso de los usuarios a la instalación. Para obtener más información, consulte [Guía de PCI: Administración de identidades](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-816"></a>Requisito 8.1.6 de PCI DSS

**8.1.6** Limite la repetición de intentos de acceso mediante el bloqueo del identificador de usuario tras seis intentos.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso ha implementado una separación clara de obligaciones (SOD) para todos los usuarios de la demostración. Para obtener más información, consulte "Azure Active Directory Identity Protection" en [Guía de PCI: Administración de identidades](payment-processing-blueprint.md#identity-management).|



### <a name="pci-dss-requirement-817"></a>Requisito 8.1.7 de PCI DSS

**8.1.7** Establezca la duración del bloqueo en un mínimo de 30 minutos o hasta que un administrador habilite el identificador de usuario.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | Los clientes son responsables de crear, aplicar y supervisar una directiva de contraseña que cumpla con los requisitos de PCI DSS.|



### <a name="pci-dss-requirement-818"></a>Requisito 8.1.8 de PCI DSS

**8.1.8** Si una sesión ha estado inactiva durante más de 15 minutos, requiera que el usuario vuelva a autenticarse para activar de nuevo el terminal o la sesión.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | Los clientes son responsables de crear, aplicar y supervisar una directiva de contraseña que cumpla con los requisitos de PCI DSS.|



## <a name="pci-dss-requirement-82"></a>Requisito 8.2 de PCI DSS

**8.2** Además de asignar un identificador único, debe garantizarse una administración correcta de la autenticación de administradores y usuarios no consumidores en todos los componentes del sistema mediante el uso de al menos uno de los métodos siguientes para autenticar a todos los usuarios:
- Algo que conozca, como una contraseña o frase de contraseña
- Algo que tenga, como un dispositivo token o una tarjeta inteligente
- Algo que lo represente, como un dispositivo biométrico

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La implementación de la tienda web de Contoso para la autenticación multifactor se ha deshabilitado para facilitar el uso en la demostración. La autenticación multifactor puede implementarse mediante [Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/).|



### <a name="pci-dss-requirement-821"></a>Requisito 8.2.1 de PCI DSS

**8.2.1** Use criptografía segura para que la representación de las credenciales de autenticación (como contraseñas o frases de contraseña) no se pueda leer durante la transmisión y el almacenamiento en todos los componentes del sistema.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure ha establecido procedimientos de administración de claves para administrar las claves criptográficas a lo largo de su ciclo de vida (generación, distribución y revocación). Microsoft Azure usa la infraestructura de PKI de empresa de Microsoft. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso exige contraseñas seguras, documentadas en la guía de implementación. Para obtener más información, consulte [Guía de PCI: Cifrado](payment-processing-blueprint.md#encryption-and-secrets-management).<br /><br />|



### <a name="pci-dss-requirement-822"></a>Requisito 8.2.2 de PCI DSS

**8.2.2** Compruebe la identidad del usuario antes de modificar cualquier credencial de autenticación (por ejemplo, restablecer contraseñas, aprovisionar nuevos tokens o generar nuevas claves).


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure ha establecido procedimientos de administración de claves para administrar las claves criptográficas a lo largo de su ciclo de vida (generación, distribución y revocación). Microsoft Azure usa la infraestructura de PKI de empresa de Microsoft. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso exige contraseñas seguras, documentadas en la guía de implementación. Para obtener más información, consulte [Guía de PCI: Cifrado](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-823"></a>Requisito 8.2.3 de PCI DSS

**8.2.3** Las contraseñas o frases de contraseña deben cumplir los siguientes requisitos:
- Requerir una longitud mínima de siete caracteres.
- Contener caracteres alfabéticos y numéricos.
Como alternativa, las contraseñas o frases de contraseña deben ser complejas y seguras en un grado como mínimo equivalente al de los parámetros especificados anteriormente.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso exige contraseñas seguras, documentadas en la guía de implementación.|



### <a name="pci-dss-requirement-824"></a>Requisito 8.2.4 de PCI DSS

**8.2.4** Cambie las contraseñas o frases de contraseña del usuario al menos una vez cada 90 días.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso usa Azure Active Directory para administrar los usuarios. Se puede establecer la opción `-enableADDomainPasswordPolicy` para garantizar que las contraseñas expiren al menos una vez cada 90 días.|



### <a name="pci-dss-requirement-825"></a>Requisito 8.2.5 de PCI DSS

**8.2.5** No permita que un usuario individual envíe una nueva contraseña o frase de contraseña que sea igual a una de las últimas cuatro contraseñas o frases de contraseña que ha utilizado.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso exige contraseñas seguras, documentadas en la guía de implementación. Para obtener más información, consulte [Guía de PCI: Administración de identidades](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-826"></a>Requisito 8.2.6 de PCI DSS

**8.2.6** Establezca un valor único para cada contraseña o frase de contraseña que se vaya a usar por primera vez tras haberla restablecido, y cambiarla inmediatamente después del primer uso.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso exige contraseñas seguras, documentadas en la guía de implementación. Para obtener más información, consulte [Guía de PCI: Administración de identidades](payment-processing-blueprint.md#identity-management).<br /><br />|



## <a name="pci-dss-requirement-83"></a>Requisito 8.3 de PCI DSS

**8.3** Proteja mediante la autenticación multifactor todos los accesos individuales al entorno de datos del titular de la tarjeta (CDE), tanto los administrativos que no se hagan a través de la consola como los realizados desde sistemas remotos.

> [!NOTE]
> La autenticación multifactor requiere usar al menos dos de los tres métodos de autenticación (consulte el requisito 8.2 en el que se describen los métodos de autenticación). Usar dos veces uno de los factores (por ejemplo, utilizar dos contraseñas distintas) no se considera autenticación multifactor.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Los administradores de Azure deben usar la autenticación multifactor para obtener acceso al realizar tareas de mantenimiento y administración en servidores y sistemas de Azure. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso crea tres cuentas durante la implementación: admin, sqladmin y edna (el usuario predefinido con la sesión iniciada en la aplicación web durante la ejecución de la demostración). La autenticación multifactor no está implementada para la demostración.|



### <a name="pci-dss-requirement-831"></a>Requisito 8.3.1 de PCI DSS

**8.3.1** Incorpore la autenticación multifactor para todo el acceso al CDE desde fuera de la consola para el personal con acceso administrativo.

> [!NOTE]
> Este requisito es un procedimiento recomendado hasta el 31 de enero de 2018. A partir de esa fecha, será un requisito.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Los administradores de Azure deben usar la autenticación multifactor para obtener acceso al realizar tareas de mantenimiento y administración en servidores y sistemas de Azure. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso crea tres cuentas durante la implementación: admin, sqladmin y edna (el usuario predefinido con la sesión iniciada en la aplicación web durante la ejecución de la demostración). La autenticación multifactor no está implementada para la demostración.|



### <a name="pci-dss-requirement-832"></a>Requisito 8.3.2 de PCI DSS

**8.3.2** Incorpore la autenticación multifactor para todos los accesos de red remotos (usuario, administrador y terceros para soporte técnico o mantenimiento) procedentes del exterior de la red de la entidad.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Los administradores de Azure deben usar la autenticación multifactor para obtener acceso al realizar tareas de mantenimiento y administración en servidores y sistemas de Azure. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso crea tres cuentas durante la implementación: admin, sqladmin y edna (el usuario predefinido con la sesión iniciada en la aplicación web durante la ejecución de la demostración). La autenticación multifactor no está implementada para la demostración.|



## <a name="pci-dss-requirement-84"></a>Requisito 8.4 de PCI DSS

**8.4** Documente las directivas de autenticación y los procedimientos y comuníquelos a todos los usuarios, incluido lo siguiente:
- Instrucciones sobre cómo seleccionar credenciales de autenticación seguras
- Instrucciones sobre cómo proteger las credenciales de autenticación
- Instrucciones para no volver a usar contraseñas utilizadas previamente
- Instrucciones para cambiar la contraseña si hay sospechas de que la seguridad de la contraseña está en riesgo

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | Los clientes son responsables de seguir las instrucciones y de documentar y comunicar las directivas y los procedimientos de autenticación a todos los usuarios.|



## <a name="pci-dss-requirement-85"></a>Requisito 8.5 de PCI DSS

**8.5** No use identificadores, contraseñas u otros métodos de autenticación que sean grupales, compartidos o genéricos, de la manera siguiente:
- Los identificadores de usuario genéricos se deshabilitan o eliminan.
- Los identificadores de usuario compartidos no existen para la administración del sistema y otras funciones críticas.
- Los identificadores de usuario compartidos o genéricos no se usan para administrar ningún componente del sistema.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso crea tres cuentas durante la implementación: admin, sqladmin y edna (el usuario predefinido con la sesión iniciada en la aplicación web durante la ejecución de la demostración). La autenticación multifactor no está implementada para la demostración.|



### <a name="pci-dss-requirement-851"></a>Requisito 8.5.1 de PCI DSS

**8.5.1** **Requisito adicional solo para proveedores de servicios:** Los proveedores de servicios con acceso remoto a las instalaciones del cliente (por ejemplo, para proporcionar soporte técnico a servidores o sistemas de punto de venta) deben usar una credencial de autenticación única (por ejemplo, una contraseña o frase de contraseña) para cada cliente. 

> [!NOTE]
> Este requisito no va dirigido a los proveedores de hospedaje compartido que tienen acceso a su propio entorno de hospedaje en el que se hospedan varios entornos de cliente.

**Responsabilidades:&nbsp;&nbsp;`Not Applicable`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No se aplica a los clientes de Microsoft Azure. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | No se aplica a los clientes de Microsoft Azure.|



## <a name="pci-dss-requirement-86"></a>Requisito 8.6 de PCI DSS

**8.6** Cuando se usan otros mecanismos de autenticación (por ejemplo, tokens de seguridad físicos o lógicos, tarjetas inteligentes, certificados, etc.), el uso de estos mecanismos debe asignarse de la manera siguiente:
- Los mecanismos de autenticación deben asignarse a una cuenta individual y no deben compartirse entre varias cuentas.
- Deben implementarse controles físicos o lógicos para asegurarse de que solo la cuenta correcta puede utilizar el mecanismo para obtener acceso.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso crea tres cuentas durante la implementación: admin, sqladmin y edna (el usuario predefinido con la sesión iniciada en la aplicación web durante la ejecución de la demostración). La autenticación multifactor no está implementada para la demostración. Todo el acceso se administra mediante [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), lo que ayuda a proteger las claves criptográficas y los secretos usados por los servicios y las aplicaciones en la nube. |



## <a name="pci-dss-requirement-87"></a>Requisito 8.7 de PCI DSS

**8.7** Todo acceso a cualquier base de datos que contenga información de los titulares de tarjetas (incluido el acceso por parte de aplicaciones, administradores y demás usuarios) se restringe de la manera siguiente:
- Todos los accesos, consultas y acciones de los usuarios en las bases de datos se realizan a través de métodos programáticos.
- Solo los administradores de base de datos tienen la capacidad de obtener acceso directo a las bases de datos o consultarlas.
- Los identificadores de las aplicaciones de base de datos únicamente pueden ser utilizados por las aplicaciones (y no por usuarios individuales ni por procesos que no sean de las aplicaciones).

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso protege los datos de los titulares de tarjetas con Azure Key Vault, y el cifrado de registros se describe en la documentación de implementación. Para obtener más información, consulte [Guía de PCI: Cifrado](payment-processing-blueprint.md#encryption-and-secrets-management).<br /><br />|



## <a name="pci-dss-requirement-88"></a>Requisito 8.8 de PCI DSS

**8.8** Asegúrese de que las directivas de seguridad y los procedimientos operativos para la identificación y autenticación queden documentados y que todas las partes afectadas los conozcan y los usen.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | Los clientes son responsables de garantizar que las directivas de seguridad y los procedimientos operativos para la identificación y autenticación queden documentados y que todas las partes afectadas los conozcan y los usen.|





---
title: 'Proyecto de procesamiento de pagos de Azure: requisitos de CHD'
description: Requisito 3 de PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 9736f7c8-c632-4b86-8b8a-6e4f45c1a7aa
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 356599cbe1e4e1948a5ec16d0d504835fa7dcd43
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="chd-requirements-for-pci-dss-compliant-environments"></a>Requisitos de CHD para entornos que cumplen el estándar PCI DSS
## <a name="pci-dss-requirement-3"></a>Requisito 3 de PCI DSS

**Protección de los datos de titulares de tarjeta almacenados**

> [!NOTE]
> El [Consejo sobre normas de seguridad de la industria de tarjetas de pago (PCI)](https://www.pcisecuritystandards.org/pci_security/) establece estos requisitos en el [Estándar de seguridad de datos (DSS) de PCI, versión 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte el estándar PCI DSS para más información sobre los procedimientos de comprobación e instrucciones para cada requisito.

Los métodos de protección, como el cifrado, el truncamiento, el enmascaramiento y el uso de algoritmos hash, son componentes esenciales para la protección de los datos de los titulares de tarjeta. Si un intruso consigue burlar otros controles de seguridad y obtiene acceso a los datos cifrados, no podrá leernos ni utilizarlos sin las claves criptográficas apropiadas. También existen otros métodos eficaces para proteger los datos almacenados que deberían tenerse en cuenta como posibles opciones de mitigación de riesgos. Por ejemplo, entre los métodos para minimizar riesgos está la opción de no guardar los datos de los titulares de las tarjetas a menos que sea absolutamente necesario, truncar los datos de los titulares si no es necesario ver todo el PAN (número de tarjeta) y no enviar los PAN sin proteger mediante tecnologías de envío de mensajes para usuarios finales, como el correo electrónico y la mensajería instantánea.
Consulte en el glosario de términos, abreviaturas y acrónimos de PCI DSS y PA-DSS la definición de "criptografía sólida" y otros términos de PCI DSS.

## <a name="pci-dss-requirement-31"></a>Requisito 3.1 de PCI DSS

**3.1** Almacene la mínima cantidad de datos de los titulares e implemente procedimientos, procesos y directivas de retención y eliminación de datos que contemplen, como mínimo, las siguientes especificaciones sobre el almacenamiento de todos los datos de titulares de tarjeta (CHD):
- La cantidad de datos almacenados y el período de retención deben limitarse a lo estipulado por la legislación, la normativa aplicable o los requisitos del negocio.
- Deben aplicarse requisitos de retención específicos para los datos de los titulares de tarjeta.
- Deben aplicarse procesos para eliminar los datos de forma segura cuando ya no resulten necesarios.
- Debe aplicarse un proceso trimestral para identificar y eliminar con seguridad los datos de titulares de tarjeta almacenados que sobrepasen los plazos de retención estipulados.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Azure es responsable de garantizar que los datos del cliente designados para su eliminación se retiran de forma segura aplicando los protocolos compatibles con NIST 800-88 que se especifican en las directivas de eliminación segura. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | La tienda web de Contoso no elimina ni destruye los datos CHD almacenados. No obstante, todos los datos están cifrados y no se guardan los números de tarjeta (PAN).<br /><br />|



## <a name="pci-dss-requirement-32"></a>Requisito 3.2 de PCI DSS

**3.2** No almacene datos de autenticación confidenciales después de la autorización (aunque estén cifrados). Si recibe datos de autenticación confidenciales, procéselos de forma que, una que se complete el proceso de autorización, resulten irrecuperables. 
- Existe una justificación comercial, y 
- Los datos se almacenan de forma segura.
Entre los datos de autenticación confidenciales, se encuentran los que se describen en los requisitos 3.2.1 a 3.2.3:


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | La tienda web de Contoso Webstore no elimina ni destruye los datos CHD almacenados; los datos de ejemplo se almacenan únicamente con fines ilustrativos. No obstante, todos los datos están cifrados y no se guardan los números de tarjeta (PAN).<br /><br />|



### <a name="pci-dss-requirement-321"></a>Requisito 3.2.1 de PCI DSS

**3.2.1** No almacene el contenido completo de ninguna pista (procedente de la banda magnética ubicada en la parte posterior de la tarjeta, los datos incluidos en el chip o en otro lugar) después de la autorización. Estos datos también se denominan pista completa, pista, pista 1, pista 2 y datos de banda magnética. 

> [!NOTE]
> En el transcurso normal de la actividad, es posible que tengan que guardarse los siguientes elementos de datos de la banda magnética: 
> - Nombre del titular de la tarjeta 
> - Número de la tarjeta (PAN) 
> - Fecha de expiración 
> - Código de servicio 
>
> Para minimizar el riesgo, estos datos solo deben almacenarse cuando resulte necesario para el negocio.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | La tienda web de Contoso no guarda el contenido completo de CHD.|



### <a name="pci-dss-requirement-322"></a>Requisito 3.2.2 de PCI DSS

**3.2.2** No almacene el número o código de verificación de tarjeta (número de tres o cuatro dígitos impreso en la parte anterior o posterior de la tarjeta de pago que se utiliza para verificar las transacciones en las que la tarjeta no está presente) tras la autorización.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | La tienda web de Contoso cifra todos los datos, incluidos los CVV de muestra.|



### <a name="pci-dss-requirement-323"></a>Requisito 3.2.3 de PCI DSS

**3.2.3** No guarde el número de identificación personal (PIN) ni el bloque de PIN cifrado después de la autorización.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | La tienda web de Contoso no guarda información del PIN.|



## <a name="pci-dss-requirement-33"></a>Requisito 3.3 de PCI DSS

**3.3** Enmascare el número PAN cuando se muestre en la pantalla (lo máximo que debe mostrarse son los seis primeros dígitos o los cuatro últimos), de forma que solo el personal que lo necesite por un motivo comercial legítimo pueda ver el número PAN completo. 

> [!NOTE]
> Este requisito no sustituye a otros requisitos más estrictos en relación con la visualización de los datos de los titulares; por ejemplo, requisitos legales o de la marca de la tarjeta de pago sobre los recibos del punto de venta (POS).

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | La tienda web de Contoso enmascara el número de tarjeta (PAN) mediante Cifrado de datos transparente, columnas de Always Encrypted y Enmascaramiento dinámico de datos. Para más información, consulte la [PCI Guidance - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database) (Guía de PCI: Azure SQL Database).|



## <a name="pci-dss-requirement-34"></a>Requisito 3.4 de PCI DSS

**3.4** Procese los números PAN para que resulten ilegibles en cualquier lugar donde estén almacenados (incluidos los medios digitales portátiles, los soportes físicos de copia de seguridad y los registros) mediante cualquiera de los métodos siguientes:
- Algoritmos hash unidireccionales basados en una criptografía segura (el hash debe abarcar todo el número PAN)
- Truncamiento (no se puede utilizar un algoritmo hash para reemplazar el segmento truncado del número PAN)
- Tokens y almohadillas de índice (las almohadillas deben guardarse de forma segura)
- Criptografía segura con procesos y procedimientos asociados para la administración de claves 

> [!NOTE]
> Para los individuos malintencionados, no resulta demasiado complicado reconstruir los números PAN originales si tienen acceso tanto a la versión truncada como a la versión de hash de un número PAN. Cuando tanto la versión truncada como la versión de hash de un mismo número PAN están presentes en el entorno de una entidad, deben adoptarse medidas adicionales que garanticen que no se puede establecer una correlación entre estas versiones para reconstruir el número PAN original.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | La tienda web de Contoso cifra todos los datos de las tarjetas de crédito y utiliza Azure Key Vault para administrar las claves, lo que impide que se puedan recuperar los datos CHD.<br /><br />Para más información, consulte [PCI Guidance - Encryption](payment-processing-blueprint.md#encryption-and-secrets-management) (Guía de PCI: Cifrado).|



### <a name="pci-dss-requirement-341"></a>Requisito 3.4.1 de PCI DSS

**3.4.1** Si se utiliza el cifrado de disco (en lugar del cifrado de base de datos en el nivel de archivo o columna), el acceso lógico debe administrarse con independencia de los mecanismos nativos de autenticación y control de acceso del sistema operativo (por ejemplo, sin utilizar credenciales de inicio de sesión en la red general o bases de datos de cuentas de usuario locales). Las claves de descifrado no deben estar vinculadas con las cuentas de usuario. 

> [!NOTE]
> Este requisito se suma a todos los demás requisitos de cifrado y administración de claves de PCI DSS.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | La tienda web de Contoso cifra todos los datos almacenados y aísla el tráfico para evitar la elevación de privilegios en las funciones de DevOps.<br /><br />Como App Service Environment está protegido y bloqueado, se necesita un mecanismo que tenga en cuenta cualquier versión o cambio de DevOps que sea necesario; por ejemplo, la capacidad de supervisar una aplicación web mediante Kudu.<br /><br />Una máquina virtual se establece como JumpBox (host de tipo bastión) con las siguientes configuraciones:<br /><br /><ul><li>[Extensión de Antimalware](/azure/security/azure-security-antimalware)</li><li>[Extensión de supervisión de OMS](/azure/virtual-machines/virtual-machines-windows-extensions-oms)</li><li>[Extensión de diagnóstico de máquina virtual](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)</li><li>[Disco cifrado con BitLocker](/azure/security/azure-security-disk-encryption)</li></ul>Al utilizar Azure Key Vault, se cumplen los requisitos de Azure Government, PCI DSS e HIPAA.|



## <a name="pci-dss-requirement-35"></a>Requisito 3.5 de PCI DSS

**3.5** Documente e implemente procedimientos para proteger las claves que se utilizan para guardar los datos de los titulares de forma segura y evitar su divulgación y uso indebido. 

> [!NOTE]
> Este requisito se aplica a las claves que se utilizan para cifrar los datos almacenados de los titulares de tarjeta, así como a las claves de cifrado de claves que se utilizan para proteger las claves de cifrado de datos (estas claves de cifrado de claves deben ser por lo menos tan seguras como la clave de cifrado de datos).

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | **Para los clientes que usan Key Vault:**<br /><br />Microsoft Azure garantiza que los almacenes de claves del cliente están aislados de forma lógica entre sí y del sistema de administración del servicio Key Vault. Key Vault está diseñado para que Microsoft no tenga acceso permanente al almacén de claves del cliente. <br /><br />Las claves están protegidas por Microsoft Azure mediante longitudes de clave, módulos de seguridad de hardware (HSM) y algoritmos estándar del sector.<br /><br />Las claves almacenadas en Microsoft Azure Key Vault pueden utilizarse para proteger otras claves. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | La tienda web de Contoso cuenta con documentación en la que se ilustra y facilita la implementación de una solución de claves protegidas que permite proteger los datos CHD de muestra.|



### <a name="pci-dss-requirement-351"></a>Requisito 3.5.1 de PCI DSS

**3.5.1** *Requisito adicional exclusivo para proveedores de servicios:* mantenga una descripción documentada de la arquitectura de cifrado que incluya:
- Detalles de todos los algoritmos, claves y protocolos utilizados para la protección de los datos de los titulares de tarjeta, incluida la fecha de caducidad y el nivel de seguridad de la clave
- Descripción del uso de cada clave
- Inventario de los HSM y otras DVL que se utilizan para la administración de claves 

> [!NOTE]
> Este requisito se va a considerar un procedimiento recomendado hasta el 31 de enero de 2018. A partir de esa fecha, pasará a convertirse en un requisito.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | **Para los clientes que usan Key Vault:**<br /><br />Microsoft Azure garantiza que los almacenes de claves del cliente están aislados de forma lógica entre sí y del sistema de administración del servicio Key Vault. Key Vault está diseñado para que Microsoft no tenga acceso permanente al almacén de claves del cliente. <br /><br />Las claves están protegidas por Microsoft Azure mediante longitudes de clave, módulos de seguridad de hardware (HSM) y algoritmos estándar del sector.<br /><br />Las claves almacenadas en Microsoft Azure Key Vault pueden utilizarse para proteger otras claves. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | La tienda web de Contoso cuenta con documentación en la que se ilustra y facilita la implementación de una solución de claves protegidas que permite proteger los datos CHD de muestra.|



### <a name="pci-dss-requirement-352"></a>Requisito 3.5.2 de PCI DSS

**3.5.2** Restrinja el acceso a las claves criptográficas al menor número de administradores necesarios.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | **Para los clientes que usan Key Vault:**<br /><br />Key Vault admite directivas de acceso detalladas, que permiten que el propietario de un almacén de claves pueda conceder acceso a una funcionalidad específica a determinadas entidades para realizar operaciones concretas. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | En la tienda web de Contoso, la administración de claves está aislada en cada cuenta de usuario (admin##@contosowebstore.com).|



### <a name="pci-dss-requirement-353"></a>Requisito 3.5.3 de PCI DSS

**3.5.3** Guarde en todo momento las claves secretas y privadas que se utilizan para cifrar o descifrar los datos de los titulares de tarjeta utilizando uno (o varios) de los siguientes mecanismos:
- Los datos deben cifrarse con una clave de cifrado de claves que sea al menos tan segura como la clave de cifrado de datos y que esté guardada en un lugar diferente al de la clave de cifrado de datos.
- Los datos deben guardarse en un dispositivo de cifrado seguro; por ejemplo, un módulo de seguridad (host) de hardware (HSM) o un dispositivo de punto de interacción aprobado por PTS.
- Los datos deben guardarse en al menos dos componentes o recursos compartidos de claves que se ajusten a un método aceptado por el sector. 

> [!NOTE]
> Estos requisitos no se aplican a las claves públicas.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | **Para los clientes que usan Key Vault:**<br /><br />Las claves se guardan en los almacenes de claves especificados por el cliente.<br /><br />Dado que varias aplicaciones pueden acceder a Key Vault de forma simultánea y desde distintas partes del mundo, no es necesario copiar las claves y guardarlas en diferentes ubicaciones. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | La tienda web de Contoso utiliza Azure Key Vault para toda la administración de claves. Para más información, consulte [PCI Guidance - Encryption](payment-processing-blueprint.md#encryption-and-secrets-management) (Guía de PCI: Cifrado).|



### <a name="pci-dss-requirement-354"></a>Requisito 3.5.4 de PCI DSS

**3.5.4** Guarde las claves criptográficas en el menor número de ubicaciones posibles.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | **Para los clientes que usan Key Vault:**<br /><br />Las claves se guardan en los almacenes de claves especificados por el cliente. <br /><br />Dado que varias aplicaciones pueden acceder a Key Vault de forma simultánea y desde distintas partes del mundo, no es necesario copiar las claves y guardarlas en diferentes ubicaciones. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | La tienda web de Contoso utiliza Azure Key Vault para administrar todas las claves. Para más información, consulte [PCI Guidance - Encryption](payment-processing-blueprint.md#encryption-and-secrets-management) (Guía de PCI: Cifrado).|



## <a name="pci-dss-requirement-36"></a>Requisito 3.6 de PCI DSS

**3.6** Documente e implemente todos los procedimientos y procesos de administración de las claves criptográficas que se utilizan para cifrar los datos de los titulares de tarjeta, incluidos los siguientes: 

> [!NOTE]
> Los numerosos estándares sectoriales para la administración de claves que están disponibles en diferentes recursos, como NIST, que se encuentra en http://csrc.nist.gov.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | La tienda web de Contoso utiliza Azure Key Vault para toda la administración de claves. Para más información, consulte [PCI Guidance - Encryption](payment-processing-blueprint.md#encryption-and-secrets-management) (Guía de PCI: Cifrado).|



### <a name="pci-dss-requirement-361"></a>Requisito 3.6.1 de PCI DSS

**3.6.1** Generación de claves criptográficas seguras

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | **Para los clientes que usan Key Vault:** <br /><br />Cuando se generan claves en Key Vault, Azure es responsable de crearlas de acuerdo con las especificaciones del cliente. Las claves se generan utilizando un HSM. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | La tienda web de Contoso utiliza Azure Key Vault para toda la administración de claves. Para más información, consulte [PCI Guidance - Encryption](payment-processing-blueprint.md#encryption-and-secrets-management) (Guía de PCI: Cifrado).|



### <a name="pci-dss-requirement-362"></a>Requisito 3.6.2 de PCI DSS

**3.6.2** Distribución segura de claves criptográficas

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | **Para los clientes que usan Key Vault:**<br /><br />La herramienta BYOK (traiga su propia clave) encapsula la clave del cliente y le asigna un almacén de seguridad específico que está asociado a una suscripción de Azure concreta. La clave solo se puede importar en el almacén de claves de la suscripción especificada, en la región indicada. Este proceso utiliza los procedimientos de cifrado proporcionados por el fabricante del hardware. Los clientes reciben una notificación donde se indica que la transferencia se ha realizado correctamente. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | La tienda web de Contoso utiliza Azure Key Vault para toda la administración de claves. Para más información, consulte [PCI Guidance - Encryption](payment-processing-blueprint.md#encryption-and-secrets-management) (Guía de PCI: Cifrado).|



### <a name="pci-dss-requirement-363"></a>Requisito 3.6.3 de PCI DSS

**3.6.3** Almacenamiento seguro de claves criptográficas

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | **Para los clientes que usan Key Vault:**<br /><br />Las claves se almacenan en los HSM y se protegen mediante las medidas de seguridad criptográficas del fabricante de hardware. Los metadatos de las claves se guardan en Azure Storage con un estado cifrado que es único en cada almacén de claves. <br /><br /> |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | La tienda web de Contoso utiliza Azure Key Vault para toda la administración de claves. Para más información, consulte [PCI Guidance - Encryption](payment-processing-blueprint.md#encryption-and-secrets-management) (Guía de PCI: Cifrado).|



### <a name="pci-dss-requirement-364"></a>Requisito 3.6.4 de PCI DSS

**3.6.4** La clave criptográfica de las claves que llegan al final de su período criptográfico (por ejemplo, una vez transcurrido un período de tiempo definido o una vez que una clave ha generado cierta cantidad de texto cifrado) cambia según las especificaciones del proveedor de la aplicación asociada o del propietario de la clave y de acuerdo con las directrices y procedimientos recomendados por el sector (por ejemplo, la publicación especial de NIST 800-57).

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | **Para los clientes que usan Key Vault:**<br /><br />Key Vault admite funcionalidades para actualizar y sustituir claves, lo que viene establecido por el cliente. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | La tienda web de Contoso utiliza Azure Key Vault para toda la administración de claves. Para más información, consulte [PCI Guidance - Encryption](payment-processing-blueprint.md#encryption-and-secrets-management) (Guía de PCI: Cifrado).|



### <a name="pci-dss-requirement-365"></a>Requisito 3.6.5 de PCI DSS

**3.6.5** Retirada o sustitución (por ejemplo, archivo, destrucción y/o revocación) de las claves según sea necesario cuando la integridad de la clave se ha debilitado (por ejemplo, con la salida de un empleado que conoce un componente de claves no cifradas) o existe la sospecha de que las claves se han visto comprometidas. 

> [!NOTE]
> Si se retiran o se reemplazan claves criptográficas que es necesario conservar, estas claves deben archivarse de forma segura (por ejemplo, utilizando una clave de cifrado de claves). Las claves criptográficas archivadas solo deben utilizarse para tareas de descifrado y comprobación.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | **Para los clientes que usan Key Vault:**<br /><br />Key Vault admite funcionalidades para retirar o reemplazar claves, lo que viene establecido por el cliente. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | La tienda web de Contoso utiliza Azure Key Vault para toda la administración de claves. Para más información, consulte [PCI Guidance - Encryption](payment-processing-blueprint.md#encryption-and-secrets-management) (Guía de PCI: Cifrado).|



### <a name="pci-dss-requirement-366"></a>Requisito 3.6.6 de PCI DSS

**3.6.6** Si se utilizan operaciones manuales para administrar las claves criptográficas o cifradas, estas operaciones deben administrarse mediante medidas de control dual y conocimiento parcial. 

> [!NOTE]
> Algunos ejemplos de operaciones manuales de administración de claves son, entre otros, la generación, transmisión, carga, almacenamiento y destrucción de claves.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | La tienda web de Contoso utiliza Azure Key Vault para toda la administración de claves. Para más información, consulte [PCI Guidance - Encryption](payment-processing-blueprint.md#encryption-and-secrets-management) (Guía de PCI: Cifrado).|



### <a name="pci-dss-requirement-367"></a>Requisito 3.6.7 de PCI DSS

**3.6.7** Prevención de la sustitución no autorizada de claves criptográficas.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | **Para los clientes que usan Key Vault:**<br /><br />Los almacenes de Key Vault están separados lógicamente y no permiten la autorización entre directorios. De este modo, se impide que se realicen sustituciones no autorizadas. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | La tienda web de Contoso utiliza Azure Key Vault para toda la administración de claves. Para más información, consulte [PCI Guidance - Encryption](payment-processing-blueprint.md#encryption-and-secrets-management) (Guía de PCI: Cifrado).|



### <a name="pci-dss-requirement-368"></a>Requisito 3.6.8 de PCI DSS

**3.6.8** Es obligatorio que los administradores de las claves criptográficas confirmen formalmente que conocen y aceptan las responsabilidades de los administradores de claves.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | En la tienda web de Contoso, la administración de claves está aislada en cada cuenta de usuario (admin##@contosowebstore.com).|



## <a name="pci-dss-requirement-37"></a>Requisito 3.7 de PCI DSS

**3.7** Asegúrese de que las directivas de seguridad y los procedimientos operativos para proteger los datos de los titulares de tarjeta queden documentados y que todas las partes afectadas los conozcan y los usen.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(proyecto de&nbsp;PCI&#8209;DSS)** | La tienda web de Contoso utiliza Azure Key Vault para toda la administración de claves. Para más información, consulte [PCI Guidance - Encryption](payment-processing-blueprint.md#encryption-and-secrets-management) (Guía de PCI: Cifrado).|





---
title: "Plano técnico del procesamiento de pagos de Azure: requisitos de acceso físico"
description: Requisito 9 de PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 91595a69-e9ce-4f9c-8388-10224165d9c0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 89f7b20a130e988bfe4964d50ae97de788ca4623
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="physical-access-requirements-for-pci-dss-compliant-environments"></a>Requisitos de acceso físico para entornos compatibles con PCI DSS 
## <a name="pci-dss-requirement-9"></a>Requisito 9 de PCI DSS

**Restricción del acceso físico a datos de los titulares de tarjetas**

> [!NOTE]
> El [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) (Consejo de estándares de seguridad para el sector de tarjetas de pago) define estos requisitos como parte del [Estándar de Seguridad de Datos (DDS) para la PCI, versión 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte el PCI DSS para obtener información sobre procedimientos de comprobación e instrucciones para cada requisito.

Cualquier acceso físico a los datos o sistemas que almacenan datos de titulares de tarjetas ofrece la oportunidad a usuarios individuales de tener acceso a los dispositivos o datos, y de quitar sistemas o copias impresas, y deben restringirse de forma adecuada. Para los fines del Requisito 9, "personal interno" hace referencia a los empleados a jornada completa y a tiempo parcial, empleados temporales, contratistas y consultores que se encuentran físicamente presentes en las instalaciones de la entidad. Un "visitante" hace referencia a un proveedor, invitado de cualquier personal interno, personal de mantenimiento o cualquiera que necesite entrar en las instalaciones durante un período corto de tiempo, normalmente no más de un día. "Medios" hace referencia a todos los soportes físicos en papel y electrónicos que contienen datos de los titulares de tarjetas.

## <a name="pci-dss-requirement-91"></a>Requisito 9.1 de PCI DSS

**9.1** Use los controles adecuados de acceso a las instalaciones para limitar y supervisar el acceso físico a los sistemas del entorno de datos de los titulares de tarjetas.

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure es responsable de implementar, aplicar y supervisar la seguridad de acceso físico para los centros de datos. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | No aplicable.|



### <a name="pci-dss-requirement-911"></a>Requisito 9.1.1 de PCI DSS

**9.1.1** Use cámaras de vídeo o mecanismos de control de acceso (o ambos) para supervisar el acceso físico individual a zonas sensibles. Revise los datos recopilados y correlacionar con las demás entradas. Almacene los datos durante al menos tres meses, a menos que en caso contrario esté limitado por ley.

> [!NOTE]
> "Zonas sensibles" hace referencia a cualquier centro de datos, sala de servidores o cualquier área en la que se alojen sistemas que almacenan, procesan o transmiten datos de los titulares de tarjetas. Esto excluye las áreas de acceso público donde solo hay terminales de punto de venta, como las áreas de cajas de una tienda.

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure es responsable de implementar, aplicar y supervisar mecanismos de control de acceso de CCTV y biométricos para los centros de datos. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | No aplicable.|



### <a name="pci-dss-requirement-912"></a>Requisito 9.1.2 de PCI DSS

**9.1.2** Implemente controles físicos o lógicos para restringir el acceso a los conectores de red de acceso público. 

Por ejemplo, los conectores de red ubicados en áreas públicas y zonas accesibles para los visitantes se pueden deshabilitar y habilitar únicamente cuando el acceso a la red se autorice de forma explícita. Como alternativa, se pueden implementar procesos para asegurarse de que los visitantes estén acompañados en todo momento en las áreas con conectores de red activos.

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | En la plataforma de Microsoft Azure no hay ningún conector de red de acceso público. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | No aplicable.|



### <a name="pci-dss-requirement-913"></a>Requisito 9.1.3 de PCI DSS

**9.1.3** Restrinja el acceso físico a los puntos de acceso inalámbrico, las puertas de enlace, los dispositivos de mano, el hardware de red y comunicación, y las líneas de telecomunicaciones.

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | El acceso físico al hardware de red de Microsoft Azure se controla de forma estricta mediante listas de acceso, varias formas de autenticación o barreras de entrada físicas, y es necesario aprobar el requisito para la empresa para tener acceso al equipo. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | No aplicable.|



## <a name="pci-dss-requirement-92"></a>Requisito 9.2 de PCI DSS

**9.2** Desarrolle procedimientos para distinguir fácilmente entre el personal interno y los visitantes, e incluya lo siguiente:
- Identificación del personal interno y los visitantes (por ejemplo, asignar tarjetas).
- Cambios en los requisitos de acceso.
- Revocación o terminación de las identificaciones expiradas del personal interno y los visitantes (por ejemplo, las tarjetas de identificación).

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure es responsable de implementar, aplicar y supervisar la seguridad del acceso físico y la identificación de empleados y contratistas cuando visiten los centros de datos. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | No aplicable.|



## <a name="pci-dss-requirement-93"></a>Requisito 9.3 de PCI DSS

**9.3** Controle el acceso físico del personal interno a las zonas sensibles como sigue:
- El acceso se debe autorizar y basar en la función de trabajo del individuo.
- El acceso se revoca inmediatamente tras el despido, y todos los mecanismos de acceso físicos (como llaves, tarjetas de acceso, etc.) deben devolverse o deshabilitarse.

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Las autorizaciones de acceso a los centros de datos de Microsoft se controlan mediante una lista de acceso autorizado aprobada por el equipo del centro de datos según el principio de privilegios mínimos. La lista de control de acceso se revisa, comprueba y actualiza de forma trimestral.<br /><br />En los centros de datos de Microsoft Azure se usan dispositivos de acceso físico, como puertas perimetrales, lectores de tarjetas de acceso electrónico, lectores biométricos, sistemas de puertas tipo esclusa y dispositivos antiefracción. Los dispositivos con tarjeta de acceso se supervisan de forma continua. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | No aplicable.|



## <a name="pci-dss-requirement-94"></a>Requisito 9.4 de PCI DSS

**9.4** Implemente procedimientos para identificar y autorizar a los visitantes. Los procedimientos deben incluir lo siguiente.

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure es responsable de garantizar que las entregas previamente aprobadas se reciben en una plataforma de carga segura aislada físicamente de las instalaciones de procesamiento de información y se supervisan por personal autorizado. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | No aplicable.|



### <a name="pci-dss-requirement-941"></a>Requisito 9.4.1 de PCI DSS

**9.4.1** Debe autorizarse a los visitantes antes de entrar, y estos deben estar acompañados en todo momento dentro de las áreas donde se procesan o mantienen los datos de los titulares de tarjetas.


**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure es responsable de garantizar que las entregas previamente aprobadas se reciben en una plataforma de carga segura aislada físicamente de las instalaciones de procesamiento de información y se supervisan por personal autorizado. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | No aplicable.|



### <a name="pci-dss-requirement-942"></a>Requisito 9.4.2 de PCI DSS

**9.4.2** Los visitantes se identifican y se les proporciona una tarjeta u otra identificación que expira y que distingue de forma visible a los visitantes del personal interno.

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | El acceso a los centros de datos de Microsoft debe estar previamente aprobado, y los visitantes autorizados tienen que registrarse con la seguridad física en el punto de llegada y proporcionar una identificación válida antes de entrar. Las tarjetas deben identificar claramente a los empleados. Los contratistas y visitantes recibirán tarjetas temporales que se deben devolver al salir de la instalación. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | No aplicable.|



### <a name="pci-dss-requirement-943"></a>Requisito 9.4.3 de PCI DSS

**9.4.3** Es necesario solicitar a los visitantes que entreguen la tarjeta o identificación antes de salir de la instalación o en la fecha de expiración.

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Los visitantes tienen que entregar las tarjetas al salir de cualquier instalación de Microsoft. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | No aplicable.|



### <a name="pci-dss-requirement-944"></a>Requisito 9.4.4 de PCI DSS

**9.4.4** Se usa un registro de visitantes para mantener un código de seguimiento físico de la actividad del visitante en las instalaciones, así como en las salas informáticas y centros de datos donde se almacenan o transmiten los datos de titulares de tarjetas.
En el registro se documenta el nombre del visitante, la empresa que representa y el personal interno que autoriza el acceso físico.
Es necesario conservar este registro durante un período mínimo de tres meses, a menos que esto esté limitado por ley.

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure es responsable de mantener un registro de visitantes como un código de seguimiento físico de la actividad del visitante en las instalaciones, así como en las salas informáticas y centros de datos donde se almacenan o transmiten los datos de titulares de tarjetas. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | No aplicable.|



## <a name="pci-dss-requirement-95"></a>Requisito 9.5 de PCI DSS

**9.5** Es necesario proteger todos los medios de forma física.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso almacena todos los datos en Azure SQL Database. Las instancias de SQL Database de PaaS se usan para mostrar las medidas de seguridad de base de datos. Para más información, vea [Guía de PCI: Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-951"></a>Requisito 9.5.1 de PCI DSS

**9.5.1** Almacene las copias de seguridad de los medios en una ubicación segura, preferiblemente en una instalación externa, como un sitio alternativo o de copia de seguridad, o una instalación de almacenamiento comercial. Revise la seguridad de la ubicación al menos de forma anual.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso almacena todos los datos en Azure SQL Database. Las instancias de SQL Database de PaaS se usan para mostrar las medidas de seguridad de base de datos. Para más información, vea [Guía de PCI: Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



## <a name="pci-dss-requirement-96"></a>Requisito 9.6 de PCI DSS

**9.6** Mantenga un control estricto sobre la distribución interna o externa de cualquier tipo de medios, incluidos los siguientes.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso almacena todos los datos en Azure SQL Database. Las instancias de SQL Database de PaaS se usan para mostrar las medidas de seguridad de base de datos. Para más información, vea [Guía de PCI: Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-961"></a>Requisito 9.6.1 de PCI DSS

**9.6.1** Clasifique los medios para poder determinar la confidencialidad de los datos.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso almacena todos los datos en Azure SQL Database. Las instancias de SQL Database de PaaS se usan para mostrar las medidas de seguridad de base de datos. Para más información, vea [Guía de PCI: Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-962"></a>Requisito 9.6.2 de PCI DSS

**9.6.2** Envíe los medios mediante transporte seguro u otro método de entrega del que se pueda realizar el seguimiento con precisión.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso almacena todos los datos en Azure SQL Database. Las instancias de SQL Database de PaaS se usan para mostrar las medidas de seguridad de base de datos. Para más información, vea [Guía de PCI: Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-963"></a>Requisito 9.6.3 de PCI DSS

**9.6.3** Asegúrese de que la administración apruebe todos los medios que se trasladen de un área protegida (incluso cuando se distribuyan a los usuarios).

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso almacena todos los datos en Azure SQL Database. Las instancias de SQL Database de PaaS se usan para mostrar las medidas de seguridad de base de datos. Para más información, vea [Guía de PCI: Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



## <a name="pci-dss-requirement-97"></a>Requisito 9.7 de PCI DSS

**9.7** Mantenga un control estricto sobre el almacenamiento y la accesibilidad de los medios.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso almacena todos los datos en Azure SQL Database. Las instancias de SQL Database de PaaS se usan para mostrar las medidas de seguridad de base de datos. Para más información, vea [Guía de PCI: Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-971"></a>Requisito 9.7.1 de PCI DSS

**9.7.1** Mantenga correctamente registros de inventario de todos los medios y realice inventarios de los medios al menos de forma anual.


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso almacena todos los datos en Azure SQL Database. Las instancias de SQL Database de PaaS se usan para mostrar las medidas de seguridad de base de datos. Para más información, vea [Guía de PCI: Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



## <a name="pci-dss-requirement-98"></a>Requisito 9.8 de PCI DSS

**9.8** Destruya los medios cuando ya no sean necesarios por razones empresariales o legales como se indica a continuación.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso almacena todos los datos en Azure SQL Database. Las instancias de SQL Database de PaaS se usan para mostrar las medidas de seguridad de base de datos. Para más información, vea [Guía de PCI: Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-981"></a>Requisito 9.8.1 de PCI DSS

**9.8.1** Destruya, incinere o recicle los materiales impresos para que los datos de los titulares de tarjetas no se puedan reconstruir. Proteja los contenedores de almacenamiento que se usan para los materiales que se vayan a destruir.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso almacena todos los datos en Azure SQL Database. Las instancias de SQL Database de PaaS se usan para mostrar las medidas de seguridad de base de datos. Para más información, vea [Guía de PCI: Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-982"></a>Requisito 9.8.2 de PCI DSS

**9.8.2** Inutilice los datos de los titulares de tarjetas en medios electrónicos para hacerlos irrecuperables y que no se puedan reconstruir.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | Las técnicas de destrucción de datos varían según el tipo de objeto de datos que se destruye, ya sean suscripciones, almacenamiento, máquinas virtuales o bases de datos. En el entorno multiempresa de Microsoft Azure, se presta especial atención para garantizar que los datos de un cliente no se "filtran" a los datos de otro cliente, o bien que cuando un cliente elimina datos, ningún otro (incluido, en la mayoría de los casos, el cliente al que pertenecían antes los datos) pueda obtener acceso a esos datos eliminados.<br /><br />Microsoft Azure sigue las directrices NIST 800-88 de saneamiento de medios, que solucionan el problema principal de garantizar que los datos no se liberen accidentalmente. Estas directrices incluyen tanto el saneamiento electrónico como físico. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso se puede eliminar por completo si se elimina el grupo de recursos que se usó durante la implementación.|



## <a name="pci-dss-requirement-99"></a>Requisito 9.9 de PCI DSS

**9.9** Proteja contra la manipulación y sustitución los dispositivos que capturan datos de tarjeta de pago a través de una interacción física directa con la tarjetas.

> [!NOTE]
> Estos requisitos se aplican a dispositivos de lectura de tarjetas que se usan en transacciones en las que la tarjeta está presente (es decir, cuando se pasa o se introduce) en el punto de venta. Este requisito no se aplica a los componentes de entrada de clave manuales, como teclados y teclados de punto de venta. 

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | En la tienda web de Contoso se usa OMS para registrar todos los cambios en el sistema.<br /><br />[Operations Management Suite (OMS)](/azure/operations-management-suite/) proporciona un registro completo de los cambios. Se puede revisar y comprobar la exactitud de los cambios. Para obtener instrucciones más específicas, vea [Guía de PCI: Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing).|



### <a name="pci-dss-requirement-991"></a>Requisito 9.9.1 de PCI DSS

**9.9.1** Mantenga una lista actualizada de los dispositivos. En la lista se debe incluir lo siguiente:
- Marca y modelo del dispositivo
- Ubicación del dispositivo (por ejemplo, la dirección del sitio o instalación donde se encuentre)
- Número de serie del dispositivo u otro método de identificación única

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | La tienda web de Contoso proporciona una arquitectura de referencia y una lista de todos los servicios que se usan en su documentación de implementación.|



### <a name="pci-dss-requirement-992"></a>Requisito 9.9.2 de PCI DSS

**9.9.2** Inspeccione periódicamente las superficies de los dispositivos para detectar alteraciones (por ejemplo, la adición de lectores de tarjeta a los dispositivos) o sustituciones (por ejemplo, comprobando el número de serie u otras características del dispositivo para comprobar que no se ha intercambiado por un dispositivo fraudulento).

> [!NOTE]
> Ejemplos de señales de que un dispositivo puede haber sido manipulado o sustituido son accesorios o cables inesperados conectados al dispositivo, etiquetas de seguridad que faltan o se han modificado, carcasas rotas o de diferente color, o cambios en el número de serie u otras marcas externas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | No aplicable.|



### <a name="pci-dss-requirement-993"></a>Requisito 9.9.3 de PCI DSS

**9.9.3** Proporcione la formación necesaria para que el personal sea capaz de reconocer los intentos de manipulación o reemplazo de dispositivos. La formación debe incluir lo siguiente:
- Comprobar la identidad de terceros que afirmen ser personal de mantenimiento o reparación, antes de concederles acceso para modificar o solucionar problemas de los dispositivos.
- No instalar, reemplazar o devolver los dispositivos sin comprobarlos.
- Conocer los comportamientos sospechosos alrededor de los dispositivos (por ejemplo, los intentos de personas desconocidas de desconectarlos o abrirlos).
- Notificar los comportamientos sospechosos y los indicios de manipulación o sustitución del dispositivo al personal adecuado (por ejemplo, a un administrador o responsable de seguridad).

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | No aplicable.|



## <a name="pci-dss-requirement-910"></a>Requisito 9.10 de PCI DSS

**9.10** Asegúrese de que las directivas de seguridad y los procedimientos operativos para restringir el acceso físico a los datos de titulares de tarjetas estén documentados, en uso y sean conocidos por todas las partes afectadas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de &nbsp;PCI&#8209;DSS)** | No aplicable.|





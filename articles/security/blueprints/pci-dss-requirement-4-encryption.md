---
title: "Plano técnico del procesamiento de pagos de Azure: requisitos de cifrado"
description: Requisito 4 de PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 43f75ba9-cb4e-49ab-b3f4-09e48310bc18
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 3eb5b663558c2a68c13368b179ff942dd3c53716
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="encryption-requirements-for-pci-dss-compliant-environments"></a>Requisitos de cifrado para entornos compatibles con PCI DSS 
## <a name="pci-dss-requirement-4"></a>Requisito 4 de PCI DSS

**Cifrado de la transmisión de datos de titulares de tarjetas en redes públicas y abiertas**

> [!NOTE]
> Estos requisitos los define el [Consejo de Estándares de Seguridad para la Industria de Tarjeta de Pago (PCI)](https://www.pcisecuritystandards.org/pci_security/) como parte del [Estándar de Seguridad de Datos PCI (DSS) versión 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte el PCI DSS para obtener información sobre procedimientos de pruebas e instrucciones para cada requisito.

La información confidencial se debe cifrar durante la transmisión a través de redes a las que pueden acceder fácilmente individuos malintencionados. Las redes inalámbricas configuradas erróneamente y las vulnerabilidades del cifrado heredado y de los protocolos de autenticación siguen siendo el objetivo de individuos malintencionados que aprovechan estas vulnerabilidades para obtener acceso privilegiado a los entornos de datos de titulares de tarjetas.

## <a name="pci-dss-requirement-41"></a>Requisito 4.1 de PCI DSS

**4.1** Use protocolos seguros de criptografía y seguridad (por ejemplo, TLS, IPSEC, SSH, etc.) para proteger la información confidencial de titulares de tarjetas durante la transmisión a través de redes públicas y abiertas, incluidos los siguientes casos:
- Se aceptan solo certificados y claves de confianza.
- El protocolo en uso solo admite configuraciones o versiones seguras.
- La seguridad del cifrado es adecuada para la metodología de cifrado en uso. 

> [!NOTE]
> Cuando se utiliza SSL o TLS anticipado, se deben cumplir los requisitos del apéndice A2.
>
> Ejemplos de redes públicas y abiertas, incluidas (entre otras) las siguientes:
> - Internet
> - Tecnologías inalámbricas, incluidas 802.11 y Bluetooth
> - Tecnologías de telefonía móviles, por ejemplo, el sistema global para comunicaciones móviles (GSM) o el acceso múltiple por división de código (CDMA)
> - Servicio general de paquetes vía radio (GPRS)
> - Comunicaciones por satélite


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore es una solución de PaaS que implementa la criptografía segura para la implementación como se muestra a continuación:<br /><br />Para cumplir los requisitos de datos en reposo cifrados, [Azure Storage](https://azure.microsoft.com/services/storage/) usa lo siguiente:<br /><br /><ul><li>[Cifrado del servicio de Azure Storage (SSE) para datos en reposo](/azure/storage/storage-service-encryption)</li><li>SQL Database: una instancia de SQL Database de PaaS se usa para mostrar las medidas de seguridad de la base de datos. Para obtener más información, consulte la [Guía de PCI: Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).</li><li>[Azure Disk Encryption (BitLocker)](/azure/security/azure-security-disk-encryption)</li></ul>El uso de Azure Key Vault satisface los requisitos de HIPAA, PCI DSS y Azure Government.|



### <a name="pci-dss-requirement-411"></a>Requisito 4.1.1 de PCI DSS

**4.1.1** Asegúrese de que las redes inalámbricas que transmitan los datos de titulares de tarjetas o que estén conectadas con el entorno de datos de titulares de tarjetas usen los procedimientos recomendados del sector (por ejemplo, IEEE 802.11i) para implementar el cifrado seguro para la autenticación y la transmisión.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Las redes inalámbricas y SNMP no se implementan en la solución.|



## <a name="pci-dss-requirement-42"></a>Requisito 4.2 de PCI DSS

**4.2** No enviar nunca PAN sin protección mediante tecnologías de mensajería del usuario final (por ejemplo, correo electrónico, mensajería instantánea, SMS, chat, etc.).

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Contoso Webstore no tiene ninguna solución de mensajería implementada que pueda enviar datos del número de cuenta principal (PAN) sin protección.|



## <a name="pci-dss-requirement-43"></a>Requisito 4.3 de PCI DSS

**4.3** Asegúrese de que las directivas de seguridad y los procedimientos operativos para cifrar las transmisiones de los datos de titulares de tarjetas estén documentados, en uso y que todas las partes afectadas los conozcan.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Proveedor<br />(Microsoft&nbsp;Azure)** | No aplicable. |
| **Cliente<br />(plano técnico de&nbsp;PCI&#8209;DSS)** | Los clientes son responsables de documentar y cifrar las transmisiones que contienen datos de titulares de tarjetas.|





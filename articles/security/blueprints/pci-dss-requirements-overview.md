---
title: "Plano de procesamiento de pagos de Azure: información general de alto nivel"
description: "Plano de procesamiento de pagos de Azure: matriz de responsabilidades del cliente (información general)"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 23cf68d8-bebd-4ac4-a194-39e052281c0e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 49acce706f09fe08b257ce8a8554de5da20060a1
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="pci-dss-requirements---high-level-overview"></a>Requisitos de PCI DDS: información general de alto nivel

El Estándar de Seguridad de Datos para la Industria de Tarjeta de Pago (PCI DDS) se desarrolló para fomentar y mejorar la seguridad de los datos de los titulares de tarjetas y para facilitar la adopción en general de medidas de seguridad de datos consistentes a nivel mundial. PCI DDS proporciona una base de referencia de requisitos técnicos y operativos diseñados para proteger datos de cuentas. Este estándar se aplica a todas las entidades relacionadas con el procesamiento de tarjetas de pagos, incluidos los comerciantes, procesadores, compradores, emisores y proveedores de servicios. PCI DDS también se aplica al resto de entidades que almacenan, procesan o transmiten datos del titular de tarjeta (CHD) o datos de autenticación confidenciales (SAD). A continuación se muestra información general de alto nivel de los 12 requisitos de PCI DDS.

> [!NOTE]
> El [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) (Consejo de estándares de seguridad para el sector de tarjetas de pago) define estos requisitos como parte del [Estándar de Seguridad de Datos (DDS) para la PCI, versión 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte el PCI DSS para obtener información sobre procedimientos de comprobación e instrucciones para cada requisito.

|   |   |
|---|---|
| **Creación y mantenimiento de<br/>sistemas y redes seguros** | 1. [Instalación y mantenimiento de una configuración de firewall para proteger los datos de los titulares de tarjetas](pci-dss-requirement-1-firewall.md)<br/><br/> 2. [No usar valores predeterminados proporcionados por el proveedor para contraseñas de sistemas u otros parámetros de seguridad](pci-dss-requirement-2-password.md) |  
| **Protección de los datos de los titulares de tarjetas** | 3. [Protección de los datos de los titulares de tarjetas almacenados](pci-dss-requirement-3-chd.md)<br/><br/> 4. [Cifrado de la transmisión de datos de titulares de tarjetas en las redes abiertas y públicas](pci-dss-requirement-4-encryption.md) |
| **Mantenimiento de un programa de<br/>administración de vulnerabilidades** | 5. [Protección de todos los sistemas ante malware y actualización frecuentemente de los programas o software antivirus](pci-dss-requirement-5-malware.md)<br/><br/> 6. [Desarrollo y mantenimiento de aplicaciones y sistemas seguros](pci-dss-requirement-6-secure-system.md) |
| **Implementación de medidas de control<br/>de acceso sólidas** | 7. [Restricción del acceso a los datos de los titulares de tarjetas según la necesidad de conocimiento de la información de las empresas](pci-dss-requirement-7-access.md)<br/><br/> 8. [Identificación y autenticación del acceso a componentes del sistema](pci-dss-requirement-8-identity.md) <br/><br/> 9. [Restricción del acceso físico a datos de los titulares de tarjetas](pci-dss-requirement-9-physical-access.md) |
| **Supervisión y pruebas de<br/>las redes con frecuencia** | 10. [Seguimiento y supervisión de todos los accesos a recursos de red y datos de los titulares de tarjetas](pci-dss-requirement-10-monitoring.md) <br/><br/> 11. [Pruebas frecuentes de los procesos y sistemas de seguridad](pci-dss-requirement-11-testing.md) |
| **Mantenimiento de una directiva de<br/>seguridad de la información** | 12. [Mantenimiento de una directiva que trate la seguridad de la información para todos los usuarios](pci-dss-requirement-12-policy.md) |


---
title: Introducción a Azure Blockchain Workbench
description: Introducción a Azure Blockchain Workbench y sus funcionalidades.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 3/21/2018
ms.topic: overview
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 9cd8ef3977d12364759838b46632ba32e0de6e70
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075676"
---
# <a name="what-is-azure-blockchain-workbench"></a>¿Qué es Azure Blockchain Workbench?

Azure Blockchain Workbench es una colección de servicios y funcionalidades de Azure diseñadas para ayudarle a crear e implementar aplicaciones de cadena de bloques para compartir datos y procesos empresariales con otras organizaciones. Azure Blockchain Workbench proporciona el scaffolding de infraestructura para compilar aplicaciones de cadena de bloques, lo que permite a los desarrolladores centrarse en crear lógica de negocios y contratos inteligentes. También facilita la creación de aplicaciones de cadena de bloques al integrar varios servicios y funcionalidades de Azure para ayudar a automatizar tareas comunes de desarrollo.

## <a name="create-blockchain-applications"></a>Creación de aplicaciones de cadena de bloques

Con Blockchain Workbench, puede definir aplicaciones de cadena de bloques con la configuración y con la escritura de código de contrato inteligente. También puede iniciar rápidamente el desarrollo de aplicaciones de cadena de bloques y centrarse en definir el contrato y escribir lógica de negocios en lugar de compilar scaffolding y configurar servicios auxiliares.

## <a name="manage-applications-and-users"></a>Administrar aplicaciones y usuarios

Azure Blockchain Workbench proporciona una aplicación web y API de REST para administrar usuarios y aplicaciones de cadena de bloques. Los administradores de Blockchain Workbench pueden administrar el acceso a la aplicación y asignar usuarios a roles de aplicación. Los usuarios de Azure AD se asignan automáticamente a los miembros de la aplicación.

## <a name="integrate-blockchain-with-applications"></a>Integrar cadenas de bloques con aplicaciones

Puede usar las API de REST de Blockchain Workbench y API basadas en mensajes para realizar la integración con sistemas existentes. Las API proporcionan una interfaz para permitir el reemplazo o uso de varias ofertas de tecnologías de libro de contabilidad distribuida, almacenamiento y base de datos.

Blockchain Workbench puede transformar mensajes enviados a la API basada en mensajes para compilar transacciones en un formato compatible con la API nativa de esa cadena de bloques.  Workbench puede firmar y enrutar las transacciones a la cadena de bloques adecuada. 

Workbench entrega eventos automáticamente a Service Bus y Event Grid para enviar mensajes a consumidores de bajada. Los desarrolladores pueden integrar con cualquiera de estos sistemas de mensajería para dirigir transacciones y observar los resultados.

## <a name="deploy-a-blockchain-network"></a>Implementar una red de cadena de bloques

Azure Blockchain Workbench simplifica la configuración de red de cadena de bloques del consorcio como una solución preconfigurada con una plantilla de solución de Azure Resource Manager. La plantilla proporciona implementación simplificada que implementa todos los componentes necesarios para ejecutar un consorcio. En la actualidad, Blockchain Workbench admite Ethereum.

## <a name="use-active-directory-login"></a>Usar inicio de sesión de Active Directory

Con los protocolos existentes de cadena de bloques, las identidades de cadena de bloques se representan como una dirección en la red. Azure Blockchain Workbench abstrae la identidad de cadena de bloques mediante la asociación con una identidad de Active Directory, lo que simplifica la compilación de aplicaciones empresariales con identidades de Active Directory.

## <a name="synchronize-on-chain-data-with-off-chain-storage"></a>Sincronizar datos en cadena con almacenamiento fuera de la cadena

Azure Blockchain Workbench facilita el análisis de datos y eventos de cadena de bloques mediante la sincronización automática de datos de la cadena de bloques con el almacenamiento fuera de la cadena. En lugar de extraer datos directamente desde la cadena de bloques, puede consultar sistemas de bases de datos fuera de la cadena, como SQL Server. Los usuarios finales que realizan tareas de análisis de datos no necesitan conocimientos específicos sobre cadenas de bloques. 

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Arquitectura de Azure Blockchain Workbench](blockchain-workbench-architecture.md)

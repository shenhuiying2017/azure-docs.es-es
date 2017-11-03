---
title: Compatibilidad con el firewall de Azure Cosmos DB y control de acceso basado en IP | Microsoft Docs
description: Aprenda a usar directivas de control de acceso IP para la compatibilidad con el firewall en cuentas de base de datos de Azure Cosmos DB.
keywords: Control de acceso IP, compatibilidad con el firewall
services: cosmos-db
author: shahankur11
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: c1b9ede0-ed93-411a-ac9a-62c113a8e887
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: ankshah
ms.openlocfilehash: 9e4419b57edf86e03044ad1047b18397ff4d8d19
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/13/2017
---
# <a name="azure-cosmos-db-firewall-support"></a>Compatibilidad con un firewall de Azure Cosmos DB
Para proteger los datos almacenados en una cuenta de base de datos de Azure Cosmos DB, este proporciona compatibilidad con un [modelo de autorización](https://msdn.microsoft.com/library/azure/dn783368.aspx) basado en secreto que utiliza un código seguro de autenticación de mensajes basado en hash (HMAC). Ahora, además del modelo de autorización basado en secreto, Azure Cosmos DB admite controles de acceso basado en IP orientado a directivas para la compatibilidad con el firewall de entrada. Este modelo es muy parecido a las reglas de firewall de un sistema de base de datos tradicional y proporciona un nivel de seguridad adicional para la cuenta de base de datos de Azure Cosmos DB. Con él, ahora puede configurar una cuenta de base de datos de Azure Cosmos DB para que solo sea accesible desde un conjunto aprobado de máquinas o servicios en la nube. El acceso a recursos de Azure Cosmos DB desde estos conjuntos aprobados de máquinas y servicios requerirá que el autor de la llamada presente un token de autorización válido.

## <a name="ip-access-control-overview"></a>Introducción al control de acceso IP
De forma predeterminada, una cuenta de base de datos de Azure Cosmos DB es accesible desde la red Internet pública siempre y cuando la solicitud vaya acompañada de un token de autorización válido. Para configurar el control de acceso basado en directiva IP, el usuario debe proporcionar el conjunto de direcciones IP o los intervalos de direcciones IP en formato CIDR para incluir en la lista permitida de direcciones IP de cliente para una cuenta de base de datos dada. Una vez que se aplica esta configuración, el servidor bloquea todas las solicitudes que se originan en máquinas que están fuera de esta lista de permitidos.  En el diagrama siguiente, se describe el flujo de proceso de conexión para el control de acceso basado en IP.

![Diagrama que muestra el proceso de conexión para el control de acceso basado en IP](./media/firewall-support/firewall-support-flow.png)

## <a name="connections-from-cloud-services"></a>Conexiones desde servicios en la nube
En Azure, los servicios en la nube son una forma muy común de hospedar lógica de servicio de nivel intermedio mediante Azure Cosmos DB. Para permitir el acceso a una cuenta de base de datos de Azure Cosmos DB desde un servicio en la nube, la dirección IP pública del servicio en la nube se debe agregar a la lista de direcciones IP permitidas asociada a dicha cuenta mediante la [configuración de la directiva de control de acceso a la dirección IP](#configure-ip-policy).  De esta forma se garantiza que todas las instancias de rol de servicios en la nube tengan acceso a la cuenta de base de datos de Azure Cosmos DB. Puede recuperar las direcciones IP para los servicios de nube en Azure Portal, como se muestra en la siguiente captura de pantalla.

![Captura de pantalla con la dirección IP pública de un servicio en la nube que se muestra en Azure Portal](./media/firewall-support/public-ip-addresses.png)

Al escalar horizontalmente su servicio de nube mediante la adición de instancias de rol adicionales, esas nuevas instancias tendrán acceso automáticamente a la cuenta de base de datos de Azure Cosmos DB que forman parte del mismo servicio en la nube.

## <a name="connections-from-virtual-machines"></a>Conexiones desde máquinas virtuales
También se pueden usar [máquinas virtuales](https://azure.microsoft.com/services/virtual-machines/) o [conjuntos de escalado de máquinas virtuales](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) para hospedar servicios de nivel intermedio mediante Azure Cosmos DB.  Para configurar la cuenta de base de datos de Azure Cosmos DB con el fin de permitir el acceso desde máquinas virtuales, se deben configurar direcciones IP públicas de máquina virtual o un conjunto de escalado de máquinas virtuales como una de las direcciones IP permitidas para dicha cuenta mediante la [configuración de la directiva de control de acceso a la dirección IP](#configure-ip-policy). Puede recuperar las direcciones IP para máquinas virtuales en Azure Portal, como se muestra en la captura de pantalla siguiente.

![Captura de pantalla con una dirección IP pública de una máquina virtual que se muestra en Azure Portal](./media/firewall-support/public-ip-addresses-dns.png)

Al agregar instancias adicionales de máquina virtual al grupo, se les proporciona automáticamente acceso a su cuenta de base de datos de Azure Cosmos DB.

## <a name="connections-from-the-internet"></a>Conexiones desde Internet
Cuando se accede a una cuenta de base de datos de Azure Cosmos DB desde un equipo de Internet, se debe agregar la dirección IP del cliente o el intervalo de direcciones IP de la máquina a la lista de direcciones IP permitidas para dicha cuenta. 

## <a id="configure-ip-policy"></a> Configuración de la directiva de control de acceso IP
La directiva de control de acceso basado en IP se puede establecer en Azure Portal mediante programación a través la [CLI de Azure](cli-samples.md), [Azure Powershell](powershell-samples.md) o la [API de REST](/rest/api/documentdb/) actualizando la propiedad `ipRangeFilter`. Los intervalos o direcciones IP deben ir separados por una coma y no deben contener espacios. Ejemplo: "13.91.6.132,13.91.6.1/24". Al actualizar la cuenta de la base de datos mediante estos métodos, asegúrese de rellenar todas las propiedades para evitar que se restablezca la configuración predeterminada.

> [!NOTE]
> Al habilitar una directiva de control de acceso IP para su cuenta de base de datos de Azure Cosmos DB, todo el acceso a esta cuenta desde máquinas fuera de la lista configurada de intervalos de direcciones IP permitidas se bloquea. En virtud de este modelo, la exploración de la operación de plano de datos desde el portal también se bloqueará para garantizar la integridad del control de acceso.

Para simplificar el desarrollo, Azure Portal lo ayuda a identificar y agregar la dirección IP de su equipo cliente a la lista de permitidos, por lo que las aplicaciones que se ejecutan en la máquina pueden tener acceso a la cuenta de Azure Cosmos DB. Tenga en cuenta que la dirección IP de cliente se detecta tal como se muestra en el Portal. Es posible que sea la dirección IP del cliente de la máquina, pero también podría ser la de la puerta de enlace de red. No olvide quitarla antes de pasar a la fase de producción.

Para establecer la directiva de control de acceso de IP en Azure Portal, vaya a la hoja de la cuenta de Azure Cosmos DB, haga clic en **Firewall** en el menú de navegación, y en **ACTIVADO**. 

![Captura de pantalla que muestra cómo abrir la hoja Firewall en Azure Portal](./media/firewall-support/azure-portal-firewall.png)

En el panel nuevo, especifique si Azure Portal puede tener acceso a la cuenta y agregar otras direcciones e intervalos según corresponda. Después, haga clic en **Guardar**.  

> [!NOTE]
> Cuando se habilita una directiva de control de acceso de IP, debe agregar la dirección IP correspondiente a Azure Portal para mantener el acceso. Las direcciones IP del portal son las siguientes:
> |Region|Dirección IP|
> |------|----------|
> |Todas las regiones, a excepción de las especificadas a continuación|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|
> |Alemania|51.4.229.218|
> |China|139.217.8.252|
> |Gobierno de EE. UU.|52.244.48.71|
>

![Captura de pantalla que muestra cómo configurar un firewall en Azure Portal](./media/firewall-support/azure-portal-firewall-configure.png)

## <a name="troubleshooting-the-ip-access-control-policy"></a>Solución de problemas de la directiva de control de acceso IP
### <a name="portal-operations"></a>Operaciones del portal
Al habilitar una directiva de control de acceso IP para su cuenta de base de datos de Azure Cosmos DB, todo el acceso a esta cuenta desde máquinas fuera de la lista configurada de intervalos de direcciones IP permitidas se bloquea. Por lo tanto, si desea habilitar las operaciones en el plano de los datos del Portal como explorar las colecciones y consultar documentos, debe permitir expresamente el acceso a Azure Portal usando la hoja **Firewall** del Portal. 

![Captura de pantalla que muestra cómo habilitar el acceso a Azure Portal](./media/firewall-support/azure-portal-access-firewall.png)

### <a name="sdk--rest-api"></a>SDK y API de Rest
Por motivos de seguridad, el acceso a través del SDK o la API de REST desde máquinas que no en la lista de permitidos, devolverá una respuesta genérica 404 No encontrado sin más detalles. Compruebe la lista de direcciones IP permitidas configurada para su cuenta de base de datos de Azure Cosmos DB para asegurarse de que se aplica la configuración de directiva correcta a dicha cuenta.

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre las sugerencias de rendimiento relacionadas con la red, consulte [Sugerencias de rendimiento](performance-tips.md).


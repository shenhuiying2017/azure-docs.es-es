---
title: Compatibilidad con el firewall de Azure DocumentDB y control de acceso basado en IP | Microsoft Docs
description: Aprenda a usar directivas de control de acceso IP para la compatibilidad con el firewall en cuentas de base de datos de Azure DocumentDB.
keywords: Control de acceso IP, compatibilidad con el firewall
services: documentdb
author: shahankur11
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: c1b9ede0-ed93-411a-ac9a-62c113a8e887
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2016
ms.author: ankshah
translationtype: Human Translation
ms.sourcegitcommit: 08cac64a6b08266f78bca03f1139a13e9686ebc3
ms.openlocfilehash: 819602cda932ea698287724e307ebbd73f1af988


---
# <a name="documentdb-firewall-support"></a>Compatibilidad con el firewall de DocumentDB
Para proteger los datos almacenados en una cuenta de base de datos de Azure DocumentDB, DocumentDB proporciona compatibilidad con un [modelo de autorización](https://msdn.microsoft.com/library/azure/dn783368.aspx) basado en secreto que utiliza un código seguro de autenticación de mensajes basado en hash (HMAC). Ahora, además del modelo de autorización basado en secreto, DocumentDB admite controles de acceso basado en IP orientado a directivas para la compatibilidad con el firewall de entrada. Este modelo es muy parecido a las reglas de firewall de un sistema de base de datos tradicional y proporciona un nivel de seguridad adicional para la cuenta de base de datos de DocumentDB. Con él, ahora puede configurar una cuenta de base de datos de DocumentDB para que solo sea accesible desde un conjunto aprobado de máquinas o servicios en la nube. El acceso a recursos de DocumentDB desde estos conjuntos aprobados de máquinas y servicios requerirá que el autor de la llamada presente un token de autorización válido.

## <a name="ip-access-control-overview"></a>Introducción al control de acceso IP
De forma predeterminada, una cuenta de base de datos de DocumentDB es accesible desde la red Internet pública siempre y cuando la solicitud vaya acompañada de un token de autorización válido. Para configurar el control de acceso basado en directiva IP, el usuario debe proporcionar el conjunto de direcciones IP o los intervalos de direcciones IP en formato CIDR para incluir en la lista permitida de direcciones IP de cliente para una cuenta de base de datos dada. Una vez que se aplica esta configuración, el servidor bloquea todas las solicitudes que se originan en máquinas que están fuera de esta lista de permitidos.  En el diagrama siguiente, se describe el flujo de proceso de conexión para el control de acceso basado en IP.

![Diagrama que muestra el proceso de conexión para el control de acceso basado en IP](./media/documentdb-firewall-support/documentdb-firewall-support-flow.png)

## <a name="connections-from-cloud-services"></a>Conexiones desde servicios en la nube
En Azure, los servicios en la nube son una forma muy común de hospedar lógica de servicio de nivel intermedio mediante DocumentDB. Para permitir el acceso a una cuenta de base de datos de DocumentDB desde un servicio en la nube, la dirección IP pública del servicio en la nube se debe agregar a la lista de direcciones IP permitidas asociada a dicha cuenta mediante la [configuración de la directiva de control de acceso a la dirección IP](#configure-ip-policy).  De esta forma se garantiza que todas las instancias de rol de servicios en la nube tengan acceso a la cuenta de base de datos de DocumentDB. Puede recuperar las direcciones IP para los servicios de nube en Azure Portal, como se muestra en la siguiente captura de pantalla.

![Captura de pantalla con la dirección IP pública de un servicio en la nube que se muestra en Azure Portal](./media/documentdb-firewall-support/documentdb-public-ip-addresses.png)

Al escalar horizontalmente su servicio de nube mediante la adición de instancias de rol adicionales, esas nuevas instancias tendrán acceso automáticamente a la cuenta de base de datos de DocumentDB que forman parte del mismo servicio en la nube.

## <a name="connections-from-virtual-machines"></a>Conexiones desde máquinas virtuales
También se pueden usar [máquinas virtuales](https://azure.microsoft.com/services/virtual-machines/) o [conjuntos de escalado de máquinas virtuales](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) para hospedar servicios de nivel intermedio mediante DocumentDB.  Para configurar la cuenta de base de datos de DocumentDB con el fin de permitir el acceso desde máquinas virtuales, se deben configurar direcciones IP públicas de máquina virtual o un conjunto de escalado de máquinas virtuales como una de las direcciones IP permitidas para dicha cuenta mediante la [configuración de la directiva de control de acceso a la dirección IP](#configure-ip-policy). Puede recuperar las direcciones IP para máquinas virtuales en Azure Portal, como se muestra en la captura de pantalla siguiente.

![Captura de pantalla con una dirección IP pública de una máquina virtual que se muestra en Azure Portal](./media/documentdb-firewall-support/documentdb-public-ip-addresses-dns.png)

Al agregar instancias adicionales de máquina virtual al grupo, se les proporciona automáticamente acceso a su cuenta de base de datos de DocumentDB.

## <a name="connections-from-the-internet"></a>Conexiones desde Internet
Cuando se accede a una cuenta de base de datos de DocumentDB desde un equipo de Internet, se debe agregar la dirección IP del cliente o el intervalo de direcciones IP de la máquina a la lista de direcciones IP permitidas para dicha cuenta. 

## <a name="a-idconfigure-ip-policya-configuring-the-ip-access-control-policy"></a><a id="configure-ip-policy"></a> Configuración de la directiva de control de acceso IP
La directiva de control de acceso basado en IP se puede establecer mediante programación a través la [CLI de Azure](documentdb-automation-resource-manager-cli.md), [Azure Powershell](documentdb-manage-account-with-powershell.md) o la [API de REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) actualizando la propiedad `ipRangeFilter`. Los intervalos o direcciones IP deben ir separados por comas y no deben contener espacios. Ejemplo: "13.91.6.132,13.91.6.1/24". Al actualizar la cuenta de la base de datos mediante estos métodos, asegúrese de rellenar todas las propiedades para evitar que se restablezca la configuración predeterminada.

> [!NOTE]
> Al habilitar una directiva de control de acceso IP para su cuenta de base de datos de DocumentDB, todo el acceso a esta cuenta desde máquinas fuera de la lista configurada de intervalos de direcciones IP permitidas se bloquea. En virtud de este modelo, la exploración de la operación de plano de datos desde el portal también se bloqueará para garantizar la integridad del control de acceso.

## <a name="troubleshooting-the-ip-access-control-policy"></a>Solución de problemas de la directiva de control de acceso IP
### <a name="portal-operations"></a>Operaciones del portal
Al habilitar una directiva de control de acceso IP para su cuenta de base de datos de DocumentDB, todo el acceso a esta cuenta desde máquinas fuera de la lista configurada de intervalos de direcciones IP permitidas se bloquea. En virtud de este modelo, la exploración de la operación de plano de datos desde el portal también se bloqueará para garantizar la integridad del control de acceso. 

### <a name="sdk--rest-api"></a>SDK y API de Rest
Por motivos de seguridad, el acceso a través del SDK o la API de REST desde máquinas que no en la lista de permitidos, devolverá una respuesta genérica 404 No encontrado sin más detalles. Compruebe la lista de direcciones IP permitidas configurada para su cuenta de base de datos de DocumentDB para asegurarse de que se aplica la configuración de directiva correcta a dicha cuenta.

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre las sugerencias de rendimiento relacionadas con la red, consulte [Sugerencias de rendimiento](documentdb-performance-tips.md).




<!--HONumber=Feb17_HO1-->



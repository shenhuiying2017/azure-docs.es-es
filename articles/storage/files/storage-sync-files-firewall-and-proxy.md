---
title: Configuración del proxy y el firewall locales de Azure File Sync | Microsoft Docs
description: Configuración de la red local de Azure File Sync
services: storage
documentationcenter: ''
author: fauhse
manager: klaasl
editor: tamram
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: fauhse
ms.openlocfilehash: 81425c6ac4e463bd4242328206bd43ce78a1105a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Configuración del proxy y el firewall de Azure File Sync
Azure File Sync conecta los servidores locales a Azure Files, lo que permite sincronizar las características de niveles de nube y de sincronización multisitio. Por lo tanto, un servidor local debe estar conectado a Internet. Un administrador de TI tiene que decidir cuál es la mejor ruta de acceso para que el servidor acceda a los servicios en la nube de Azure.

Este artículo ofrece un resumen de las opciones y los requisitos específicos disponibles para conectar el servidor a Azure File Sync de forma correcta y segura.

## <a name="overview"></a>Información general
Azure File Sync funciona como un servicio de orquestación entre el servidor Windows, el recurso compartido de archivos de Azure y otros servicios de Azure con el objetivo de sincronizar los datos tal y como se describe en el grupo de sincronización. Para que Azure File Sync funcione correctamente, debe configurar los servidores para que se comuniquen con los siguientes servicios de Azure:

- Azure Storage
- Azure File Sync
- Azure Resource Manager
- Servicios de autenticación

> [!Note]  
> El agente de Azure File Sync en Windows Server inicia todas las solicitudes a servicios en la nube, por lo que solo hay que tener en cuenta el tráfico saliente en el firewall. <br /> Ningún servicio de Azure inicia una conexión con el agente de Azure File Sync.


## <a name="ports"></a>Puertos
Azure File Sync mueve los metadatos y los datos de archivo exclusivamente a través de HTTPS y requiere que el puerto 443 esté abierto al tráfico saliente.
Como resultado, se cifra todo el tráfico.

## <a name="networks-and-special-connections-to-azure"></a>Redes y conexiones especiales a Azure
El agente de Azure File Sync no tiene ningún requisito en lo que respecta a canales especiales como [ExpressRoute](../../expressroute/expressroute-introduction.md), etc., en Azure.

Azure File Sync funciona con cualquier medio disponible que permita conectarse con Azure, de modo que se adapta automáticamente a diversas características de red, como el ancho de banda, la latencia y la posibilidad de usar el control de administración para realizar ajustes. * No todas las características están disponibles en este momento. Si desea configurar un comportamiento específico, comuníquelo en [UserVoice de Azure Files](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Proxy
Azure File Sync admite actualmente la configuración del proxy en el nivel de máquinas. Esta configuración de proxy es transparente para el agente de Azure File Sync, ya que todo el tráfico del servidor se enruta a través de este proxy.

La configuración de proxy específico de las aplicaciones está actualmente en desarrollo y se admitirá en futuras versiones del agente de Azure File Sync. De este modo, se permite la configuración de un proxy específicamente para el tráfico de Azure File Sync.

## <a name="firewall"></a>Firewall
Como se mencionó en una sección anterior, el puerto 443 tiene que abrirse al tráfico saliente. En función de las directivas del centro de datos, la rama o la región, puede ser recomendable u obligatorio restringir aún más el tráfico a través de este puerto a dominios concretos.

En la tabla siguiente se describen los dominios necesarios para la comunicación:

| Servicio | Dominio | Uso |
|---------|----------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | Todas las llamadas de usuario (como PowerShell) van a esta URL, incluida la llamada de registro inicial del servidor. |
| **Azure Active Directory** | https://login.windows.net | Las llamadas de Azure Resource Manager deben realizarlas usuarios autenticados. Para que el proceso se complete correcta, esta URL se utiliza para la autenticación de usuarios. |
| **Azure Active Directory** | https://graph.windows.net/ | Como parte de la implementación de Azure File Sync, se crea una entidad de servicio en la instancia de Azure Active Directory de la suscripción. Esta URL se utiliza con ese fin. Esta entidad de seguridad se usa para delegar un conjunto mínimo de derechos en el servicio Azure File Sync. El usuario que realiza la configuración inicial de Azure File Sync debe ser un usuario autenticado con privilegios de propietario en la suscripción. |
| **Azure Storage** | &ast;.core.windows.net | Cuando el servidor descarga un archivo, realiza ese movimiento de datos de forma más eficaz al conectarse directamente al recurso compartido de archivos de Azure en la cuenta de almacenamiento. El servidor tiene una clave SAS que solo se permite el acceso al recurso compartido de archivos de destino. |
| **Azure File Sync** | &ast;.one.microsoft.com | Después del registro inicial del servidor, el servidor recibe una URL regional para la instancia de servicio de Azure File Sync en dicha región. El servidor puede utilizar la URL para comunicarse de forma directa y eficaz con la instancia que controla la sincronización. |

> [!Important]
> Al permitir el tráfico a &ast;.one.microsoft.com, es posible dirigir el tráfico del servidor a más recursos que simplemente el servicio de sincronización. Existen muchos otros servicios de Microsoft en subdominios.

Si &ast;.one.microsoft.com es demasiado extensa, puede limitar la comunicación del servidor permitiendo solo las instancias regionales explícitas del servicio Azure File Sync. La elección de las instancias dependerá de la región del servicio de sincronización de almacenamiento en el que haya implementado y registrado el servidor. Esa es la región que debe permitir para el servidor. Pronto habrá más direcciones URL para habilitar las nuevas características de continuidad empresarial. 

| Region | URL del punto de conexión regional de Azure File Sync |
|--------|---------------------------------------|
| Australia Oriental | https://kailani-aue.one.microsoft.com |
| Centro de Canadá | https://kailani-cac.one.microsoft.com |
| Este de EE. UU | https://kailani1.one.microsoft.com |
| Sudeste asiático | https://kailani10.one.microsoft.com |
| Sur del Reino Unido 2 | https://kailani-uks.one.microsoft.com |
| Europa occidental | https://kailani6.one.microsoft.com |
| Oeste de EE. UU | https://kailani.one.microsoft.com |

> [!Important]
> Si define estas reglas de firewall detalladas, revise este documento a menudo y actualice dichas reglas para evitar interrupciones en el servicio debido a listados de URL obsoletos o incompletos en la configuración del firewall.

## <a name="summary-and-risk-limitation"></a>Resumen y limitación de riesgos
Las listas que aparecen anteriormente en este documento contienen las URL con las que actualmente se comunica Azure File Sync. Los firewalls deben poder permitir el tráfico saliente a estos dominios, así como las respuestas de ellos. Microsoft se esfuerza por mantener esta lista actualizada.

Configurar reglas de firewall que restrinjan dominios puede ser una medida para mejorar la seguridad. Si se usan estas configuraciones de firewall, es necesario tener en cuenta que las URL se agregan y cambian con el tiempo. Por lo tanto, es prudente comprobar las tablas de este documento como parte de un proceso de administración de cambios de una versión del agente de Azure File Sync a otra en una implementación de prueba del agente más reciente. De este modo, puede garantizar que el firewall está configurado para permitir el tráfico a los dominios que requiera el agente más reciente.

## <a name="next-steps"></a>Pasos siguientes
- [Planeamiento de una implementación de Azure File Sync](storage-sync-files-planning.md)
- [Implementar Azure File Sync (versión preliminar)](storage-sync-files-deployment-guide.md)

---
title: Apertura de los puertos de firewall necesarios para una aplicación de proxy de aplicación | Microsoft Docs
description: Averigüe qué puertos se deben abrir para que el proxy de aplicación de Azure AD funcione correctamente
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 72acfbd21159e15fe237be6d509cb2c4a2b1bffd
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Apertura de los puertos de firewall necesarios para una aplicación de proxy de aplicación

Para ver una lista completa de los puertos necesarios y la función de cada uno, consulte la sección Requisitos previos de la [documentación del proxy de aplicación](manage-apps/application-proxy-enable.md). Tenga en cuenta que el proxy de aplicación solo usa puertos de salida.

También puede comprobar si tiene todos los puertos necesarios abiertos abriendo la [herramienta de prueba de puertos de conector](https://aadap-portcheck.connectorporttest.msappproxy.net/) desde la red local. Cuanto mayor sea el número de marcas de verificación verdes, mayor será la resistencia. 

## <a name="app-proxy-regions"></a>Regiones de proxy de aplicación

Se está trabajando en una forma de hacerle saber cuál de estas regiones debe estar verde para su caso. Por ahora, asegúrese de que todas ellas lo estén. También se necesita Centro de EE. UU., independientemente de en qué región se encuentre.

Para asegurarse de que la herramienta ofrezca los resultados correctos, debe:

-   Abrir la herramienta en un explorador desde el servidor donde esté instalado el conector.

-   Asegurarse de que los servidores proxy o firewalls aplicables a su conector también estén aplicados a esta página. Para hacerlo en Internet Explorer, vaya a **Configuración** -&gt; **Opciones de Internet** -&gt; **Conexiones** -&gt; **Configuración de LAN**. En esta página, verá el campo "Usar un servidor proxy para la LAN". Active esta casilla y escriba la dirección del proxy en el campo "Dirección".

## <a name="next-steps"></a>Pasos siguientes
[Descripción de los conectores del Proxy de aplicación de Azure AD](manage-apps/application-proxy-connectors.md)

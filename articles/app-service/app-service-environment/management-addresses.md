---
title: "Direcciones de administración de Azure App Service Environment"
description: "Enumera las direcciones de administración utilizadas en App Service Environment"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 949b445793768f593af205321bf3ab5a7aa150c0
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="app-service-environment-management-addresses"></a>Direcciones de administración de App Service Environment

Una instancia de App Service Environment (ASE) es una implementación de Azure App Service en una subred de su red virtual de Azure (VNet).  La instancia de ASE debe ser accesible desde Azure App Service para que pueda administrarse.  Este tráfico de administración de ASE atraviesa la red controlada por el usuario.  Procede de servidores de administración de Azure App Service hacia la dirección VIP pública asociada con la instancia de ASE.  Para más información sobre las dependencias de redes de ASE, consulte [Consideraciones de redes y App Service Environment][networking].  Para obtener información general sobre ASE puede consultar [Introducción a App Service Environment][intro].

Este documento enumera la direcciones IP de origen para el tráfico de administración de la instancia de ASE. Puede usar estas direcciones para crear grupos de seguridad de red para bloquear el tráfico entrante o utilizarlas en tablas de rutas según sea necesario.  Para usar esta información debe usar:

* las direcciones IP que se enumeran para todas las regiones
* las direcciones IP que coinciden con la región en la que está implementada la instancia de ASE.

El tráfico de administración entrante procede de estas direcciones IP hacia los puertos 454 y 455.

| Region | Direcciones |
|--------|-----------|
| Todas las regiones | 70.37.57.58, 157.55.208.185, 52.174.22.21,13.94.149.179,13.94.143.126,13.94.141.115, 52.178.195.197, 52.178.190.65, 52.178.184.149, 52.178.177.147, 13.75.127.117, 40.83.125.161, 40.83.121.56, 40.83.120.64, 52.187.56.50, 52.187.63.37, 52.187.59.251, 52.187.63.19, 52.165.158.140, 52.165.152.214, 52.165.154.193, 52.165.153.122, 104.44.129.255, 104.44.134.255, 104.44.129.243, 104.44.129.141 |
| Centro y Sur de EE. UU. y Centro y norte de EE. UU. | 23.102.188.65, 191.236.154.88 |
| Sudeste de Australia y Este de Australia | 23.101.234.41, 104.210.90.65 |
| Oeste de EE. UU. y Este de EE. UU. | 104.45.227.37, 191.236.60.72 |
| Europa Occidental y Europa del Norte | 191.233.94.45, 191.237.222.191 |
| Centro occidental de EE.UU y Oeste de EE.UU. 2 | 13.78.148.75, 13.66.225.188 |
| Centro de EE. UU. y Este de EE. UU. 2 | 104.43.165.73, 104.46.108.135 |
| Este de Asia y Sudeste de Asia | 23.99.115.5, 104.215.158.33 |
| Este de Japón y Oeste de Japón | 104.41.185.116, 191.239.104.48 |
| Centro de Canadá & Este de Canadá | 40.85.230.101, 40.86.229.100 |
| Oeste del Reino Unido & Sur del Reino Unido | 51.141.8.34, 51.140.185.75 |
| Corea del Sur y Corea Central | 52.231.200.177, 52.231.32.117 |
| Sur de Brasil y Centro y Sur de EE. UU.| 104.41.46.178, 23.102.188.65 |
| India central e India meridional | 104.211.98.24, 104.211.225.66 |
| India occidental e India meridional | 104.211.160.229, 104.211.225.66 |


<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md


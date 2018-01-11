---
title: "Configuración del servicio Cloud App Discovery en Azure Active Directory | Microsoft Docs"
description: "Busque y administre aplicaciones con Cloud App Discovery para proporcionar información que requiere acción sobre el uso de la nube y de shadow IT."
services: active-directory
keywords: cloud app discovery, administrar aplicaciones
documentationcenter: 
author: curtand
manager: mtillman
tags: ignite
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: curtand
ms.reviewer: nigu
ms.openlocfilehash: 4a0cb1b7793c846f98ae4e89b99b4bda984cd5e4
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/13/2017
---
# <a name="set-up-cloud-app-discovery-in-azure-ad"></a>Configuración de Cloud App Discovery en Azure Active Directory

Cloud App Discovery en Azure AD se basa ahora en la integración con los datos disponibles de Microsoft Cloud App Security. Cloud App Discovery compara los registros de tráfico con el catálogo de Cloud App Security de más de 15 000 aplicaciones de nube para proporcionar información continua sobre el uso de la nube y shadow IT. En este artículo se describe el proceso de instalación y contiene vínculos a información detallada para cada paso. También se detalla la información de proxy y de firewall, y la compatibilidad con el archivo de registro.

## <a name="prerequisites"></a>Requisitos previos

* Su organización debe tener una licencia P1 de Azure AD Premium para usar el producto. Para más información, consulte los [precios de Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
* Para configurar Cloud App Discovery, debe ser un administrador global o un Lector de seguridad de Azure Active Directory.

## <a name="setup-steps"></a>Pasos de configuración

1. [Configure los informes de instantánea](cloudappdiscovery-set-up-snapshots.md) para comprobar el formato del registro, a fin de asegurarse de que los registros proporcionan información útil a Cloud App Discovery. También pueden proporcionar visibilidad ad hoc en los registros de tráfico que ha cargado manualmente desde los firewalls y servidores proxy.

2. [Configure informes continuos](https://docs.microsoft.com/cloud-app-security/discovery-docker) para analizar todos los registros que se desvían desde su red mediante el recopilador de registros de Cloud App Security. Puede utilizarlos para identificar nuevas aplicaciones y tendencias de uso.

3. Si los registros no se admiten actualmente, [configure un analizador de registros personalizado](https://docs.microsoft.com/cloud-app-security/custom-log-parser) para que Cloud App Discovery pueda analizarlos.
  
## <a name="log-processing-flow"></a>Flujo de proceso de registros

La generación de informes puede tardar de unos minutos a varias horas, según la cantidad de datos. Esto es lo que se analiza:

* **Cargar** Los registros de tráfico web de la red se cargan en el portal.
* **Redistribuir** Cloud App Security redistribuye y extrae datos de tráfico de los registros de tráfico con un analizador dedicado para cada origen de datos.
* **Analizar** Los datos de tráfico se analizan respecto al catálogo de Cloud App Security para identificar más de 15 000 aplicaciones en la nube. Los usuarios activos y las direcciones IP también se identifican como parte del análisis.
* **Generar informe** Cloud App Security genera un informe de los datos extraídos de los archivos de registro y lo pone a disposición de Cloud App Discovery.

> [!NOTE]
> * Los datos de informes continuos se analizan dos veces al día.
> * El recopilador de registros comprime los datos antes de que se carguen. El tráfico saliente en el recopilador de registros es aproximadamente el 10 % del tamaño de los registros de tráfico recibidos.

## <a name="using-traffic-logs-for-cloud-app-discovery"></a>Uso de registros de tráfico para Cloud App Discovery

Cloud App Discovery usa los datos de los registros de tráfico. Cuantos más detalles agregue en el registro, mejor visibilidad obtendrá. Cloud App Discovery requiere datos de tráfico web con los siguientes atributos:

* Fecha de la transacción
* Dirección IP de origen
* Usuario de origen **recomendado**
* Dirección IP de destino
* Dirección URL de destino **recomendada** (las direcciones URL proporcionan más precisión para la detección de la aplicación en la nube que las direcciones IP)
* Cantidad total de datos
* Cantidad de datos cargados o descargados, para obtener información sobre los patrones de uso de aplicaciones de nube
* Acción realizada (permitido o bloqueado)

Cloud App Discovery no puede mostrar ni analizar atributos que no están incluidos en los registros. Por ejemplo, el formato de registro estándar del **firewall de Cisco ASA** no contiene la **cantidad de bytes cargados por transacción**, el **nombre de usuario** o la **dirección URL de destino**  pero solo la dirección IP de destino. Por lo tanto, es posible que tenga menos visibilidad en las aplicaciones de nube desde este origen de datos. Para los firewall de Cisco ASA, establezca el nivel de información en 6.1.

Para generar correctamente un informe de Cloud App Discovery, los registros de tráfico deben cumplir estas condiciones:

1.  El origen de datos es [un servidor proxy o firewall compatible](#supported-firewalls-and-proxies).
2.  El formato del registro coincide con el formato estándar esperado. Esto se comprueba en tiempo de carga. Para optimizar el formato de registro, consulte [Create snapshot Cloud App Discovery reports](cloudappdiscovery-set-up-snapshots.md) (Creación de informes de instantáneas de Cloud App Discovery).
3.  Los eventos no tienen más de 90 días de antigüedad.
4.  El archivo de registro es válido e incluye información sobre el tráfico de salida.

## <a name="supported-firewalls-and-proxy-servers"></a>Servidores proxy y firewalls admitidos

* Barracuda - Firewall de aplicación web (W3C)
* Blue Coat Proxy SG - Registros de acceso (W3C)
* Punto de comprobación
* Cisco ASA FirePOWER
* Firewall Cisco ASA (para los firewalls de Cisco ASA, establezca el nivel de información en 6)
* Cisco IronPort WSA
* ScanSafe de Cisco
* Cisco Meraki – Registro de direcciones URL
* Clavister NGFW (Syslog)
* Dell Sonicwall
* Fortinet Fortigate
* Juniper SRX
* Juniper SSG
* Puerta de enlace web de McAfee Secure
* Microsoft Forefront Threat Management Gateway (W3C)
* Firewall de serie Palo Alto
* Sophos SG
* Sophos Cyberoam
* Squid (Common)
* Squid (Native)
* Websense - Soluciones de seguridad web - Informe de detalle de investigación (CSV)
* Websense - Soluciones de seguridad web - Registro de actividad de Internet (CEF)
* Zscaler

> [!NOTE]
> Cloud App Discovery admite direcciones IPv4 e IPv6.

Si no se admite el registro, seleccione **Otros** como el **origen de datos** y especifique el dispositivo y el registro que está intentando cargar. El registro lo revisa el equipo de analistas de nube de Cloud App Security. Recibirá una notificación cuando se agregue compatibilidad con el tipo de registro pero, en su lugar, puede definir un analizador personalizado que coincide con el formato del registro. Para más información, consulte [Uso de un analizador de registros personalizado](https://docs.microsoft.com/cloud-app-security/custom-log-parser).

## <a name="data-attributes-according-to-vendor-documentation"></a>Atributos de datos (según la documentación del proveedor)

| Origen de datos         | URL de la aplicación de destino | Dirección de IP de la aplicación de destino | Nombre de usuario | Dirección IP de origen | Tráfico total | Bytes cargados |
|-----------------------------------------|----------------|---------------|----------|-----------|---------------|----------------|
| Barracuda                               | **Sí**        | **Sí**       | **Sí**  | **Sí**   | No            | No             |
| Blue Coat                               | **Sí**        | No            | **Sí**  | **Sí**   | **Sí**       | **Sí**        |
| Punto de control                              | No             | **Sí**       | No       | **Sí**   | No            | No             |
| Cisco ASA                               | No             | **Sí**       | No       | **Sí**   | **Sí**       | No             |
| Cisco FWSM                              | No             | **Sí**       | No       | **Sí**   | **Sí**       | No             |
| Cisco Ironport WSA                      | **Sí**        | **Sí**       | **Sí**  | **Sí**   | **Sí**       | **Sí**        |
| Cisco Meraki                            | **Sí**        | **Sí**       | No       | **Sí**   | No            | No             |
| Clavister NGFW (Syslog)                 | **Sí**        | **Sí**       | **Sí**  | **Sí**   | **Sí**       | **Sí**        |
| Dell SonicWall                          | **Sí**        | **Sí**       | No       | **Sí**   | **Sí**       | **Sí**        |
| Fortigate                               | No             | **Sí**       | No       | **Sí**   | **Sí**       | **Sí**        |
| Juniper SRX                             | No             | **Sí**       | No       | **Sí**   | **Sí**       | **Sí**        |
| Juniper SSG                             | No             | **Sí**       | No       | **Sí**   | **Sí**       | **Sí**        |
| McAfee SWG                              | **Sí**        | No            | No       | **Sí**   | **Sí**       | **Sí**        |
| MS TMG                                  | **Sí**        | No            | **Sí**  | **Sí**   | **Sí**       | **Sí**        |
| Palo Alto Networks                      | **Sí**        | **Sí**       | **Sí**  | **Sí**   | **Sí**       | **Sí**        |
| Sophos                                  | **Sí**        | **Sí**       | **Sí**  | **Sí**   | **Sí**       | No             |
| Squid (Common)                          | **Sí**        | No            | **Sí**  | **Sí**   | No            | **Sí**        |
| Squid (Native)                          | **Sí**        | No            | **Sí**  | **Sí**   | No            | **Sí**        |
| Websense - Informe de investigación (CSV)   | **Sí**        | **Sí**       | **Sí**  | **Sí**   | **Sí**       | **Sí**        |
| Websense - Registro de actividad de Internet (CEF)  | **Sí**        | **Sí**       | **Sí**  | **Sí**   | **Sí**       | **Sí**        |
| Zscaler                                 | **Sí**        | **Sí**       | **Sí**  | **Sí**   | **Sí**       | **Sí**        |


## <a name="next-steps"></a>Pasos siguientes
Use los vínculos siguientes para continuar con la configuración de Cloud App Discovery en Azure AD.

* [Creación de informes de instantánea](cloudappdiscovery-set-up-snapshots.md)
* [Configuración de informes continuos](https://docs.microsoft.com/cloud-app-security/discovery-docker)
* [Uso de un analizador de registros personalizado](https://docs.microsoft.comcommit/cloud-app-security/custom-log-parser)

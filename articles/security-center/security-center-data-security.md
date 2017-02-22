---
title: Seguridad de datos de Azure Security Center | Microsoft Docs
description: "En este documento se explica cómo se administran y protegen los datos en Azure Security Center."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/06/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 9852981e530cd147c2d34ac2ede251b58a167a0a
ms.openlocfilehash: 5c030f463b21284c15752cf95aa1f9a75f17ffb0


---
# <a name="azure-security-center-data-security"></a>Seguridad de datos de Azure Security Center
Para ayudar a los clientes a evitar, detectar y responder a las amenazas, Azure Security Center recopila y procesa datos relacionados con la seguridad, entre los que se incluyen la información de configuración, los metadatos, los registros de eventos y los archivos de volcado de memoria, entre otros. Microsoft se adhiere a instrucciones estrictas de seguridad y cumplimiento de normas, desde la codificación hasta la operación de un servicio.

En este artículo se explica cómo se administran y protegen los datos en Azure Security Center.


## <a name="data-sources"></a>Orígenes de datos
Azure Security Center analiza los datos de los orígenes siguientes para proporcionar visibilidad de su estado de seguridad, identificar vulnerabilidades y recomendar mitigaciones y detectar amenazas activas:

- Servicios de Azure: utiliza la información acerca de la configuración de los servicios de Azure que se hayan implementado mediante la comunicación con el proveedor de recursos de cada uno de dichos servicios.
- Tráfico de red: usa metadatos muestreados del tráfico de red de la infraestructura de Microsoft, como el IP o el puerto de origen o destino, el tamaño de paquete y el protocolo de red.
- Soluciones de asociados: usa alertas de seguridad de las soluciones de asociados integradas, como firewalls y soluciones antimalware.
- Sus Virtual Machines: usa información de configuración e información sobre eventos de seguridad, como los eventos y registros de auditoría de Windows, registros IIS, mensajes de Syslog y archivos de volcado de memoria de sus máquinas virtuales.


## <a name="data-protection"></a>Protección de datos
**Segregación de datos**: los datos se mantienen separados de forma lógica en cada componente a lo largo de todo el servicio. Todos los datos se etiquetan por organización. Este etiquetado persiste a lo largo del ciclo de vida de los datos y se aplica en cada nivel del servicio.

**Acceso a datos**: con el fin de proporcionar recomendaciones de seguridad e investigar las posibles amenazas de seguridad, el personal de Microsoft puede tener acceso a la información recopilada o analizada por los servicios de Azure, incluidos archivos de volcado de memoria, eventos de creación de proceso, instantáneas y artefactos de disco de máquina virtual, que de forma involuntaria pueden incluir datos de cliente o datos personales de sus máquinas virtuales. Cumplimos la [Declaración de privacidad y los Términos de Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), en donde se estipula que Microsoft no utilizará los datos de los clientes ni información derivada de ellos con fines comerciales, publicitarios o similares. Solo usamos los datos del cliente necesarios para proporcionar los servicios de Azure. El usuario conserva todos los derechos sobre los datos del cliente.

**Uso de datos**: Microsoft utiliza los patrones y la información sobre amenazas vistos en varios inquilinos para mejorar las funcionalidades de detección y prevención; esto se realiza según lo dispuesto en los compromisos de privacidad que se describen en nuestra [declaración de privacidad](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

## <a name="data-location"></a>Ubicación de los datos
**Su cuenta o cuentas de almacenamiento**: se especifica una cuenta de almacenamiento para cada región en la que se ejecutan máquinas virtuales. Esto permite almacenar los datos en la misma región en que se encuentra la máquina virtual de la que se recopilan. Dichos datos, entre los que se incluyen los archivos de volcado de memoria, se almacenarán de forma persistente en su cuenta de almacenamiento. Las instantáneas del disco de máquina virtual se almacenan en la misma cuenta de almacenamiento que el disco de máquina virtual.

**Almacenamiento de Azure Security Center**: Además, se guarda información acerca de las alertas de seguridad, incluidas las alertas de asociado, recomendaciones y estado de mantenimiento de seguridad en el almacenamiento centralizado, que actualmente se encuentra en Estados Unidos. Esta información puede incluir información de configuración relacionada y eventos de seguridad recopilados de las máquinas virtuales que son necesarios para proporcionarle la alerta de seguridad, la recomendación o el estado de mantenimiento de seguridad.

Azure Security Center recopila copias efímeras de los archivos de volcado de memoria y las analiza para buscar pruebas de intentos de vulnerabilidad y de riesgos ciertos. Azure Security Center realiza este análisis en la misma geoárea en la que se encuentra el área de trabajo y elimina las copias efímeras una vez que el análisis se completa.

Los artefactos de la máquina se almacenan de forma centralizada en la misma región que la máquina virtual.


## <a name="managing-data-collection-from-virtual-machines"></a>Administración de recolección de datos de máquinas virtuales
Si elige habilitar Azure Security Center, la recolección de datos estará activada para todas las suscripciones. También se puede activar la recolección de datos en la sección "Directiva de seguridad" de Azure Security Center. Cuando se activa la recolección de datos, Azure Security Center aprovisiona el Agente de supervisión de Azure en todas las máquinas virtuales compatibles existentes y en las nuevas que se crean. La extensión Supervisión de seguridad de Azure busca diversas configuraciones relacionadas con la seguridad y las envía a [Event Tracing for Windows (ETW)](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (Seguimientos de eventos de Windows). Además, el sistema operativo generará eventos del registro de eventos mientras la máquina se ejecute. Estos son algunos ejemplos de dichos datos: tipo y versión del sistema operativo, registros del sistema operativo (registros de eventos de Windows), procesos en ejecución, nombre de la máquina, direcciones IP, usuario conectado e identificador de inquilino. El Agente de supervisión de Azure lee las entradas de los registros de eventos y los seguimientos de ETW y los copia en la cuenta de almacenamiento para su análisis.

La recolección de datos de máquinas virtuales se puede deshabilitar en cualquier momento, lo que eliminará todos los agentes de supervisión que Azure Security Center haya instalado previamente. Las instantáneas de disco de máquina virtual y la recolección de artefactos continuará habilitada aunque la recopilación de datos se deshabilite.


## <a name="see-also"></a>Consulte también
En este documento, se ha explicado cómo se administran y protegen los datos en Azure Security Center. Para más información sobre Azure Security Center, consulte:

* [Guía de planeamiento y operaciones de Azure Security Center](security-center-planning-and-operations-guide.md) : aprenda a planear y conozca las consideraciones de diseño necesarias para usar Azure Security Center.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md) : obtenga información sobre cómo supervisar el estado de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md) : obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md) : aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md) : encuentre las preguntas más frecuentes sobre el uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/) : encuentre entradas de blog sobre el cumplimiento y la seguridad de Azure.



<!--HONumber=Feb17_HO1-->



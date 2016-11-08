---
title: Seguridad de datos de Azure Security Center | Microsoft Docs
description: En este documento se explica cómo se administran y protegen los datos en Azure Security Center.
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: ''

ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2016
ms.author: yurid

---
# Seguridad de datos de Azure Security Center
Para ayudar a los clientes a evitar, detectar y responder a las amenazas, Azure Security Center recopila y procesa datos de los recursos de Azure, entre los que se incluyen la información de configuración, los metadatos, los registros de eventos y los archivos de volcado de memoria, entre otros. Estamos totalmente comprometidos a proteger la privacidad y la seguridad de estos datos. Microsoft se adhiere a instrucciones estrictas de seguridad y cumplimiento de normas, desde la codificación hasta la operación de un servicio.

En este artículo se explica cómo se administran y protegen los datos en Azure Security Center.

## Orígenes de datos
Azure Security Center analiza los datos de los siguientes orígenes:

* Servicios de Azure: lee la información acerca de la configuración de los servicios de Azure que se hayan implementado mediante la comunicación con el proveedor de recursos de cada uno de dichos servicios.
* Tráfico de red: lee metadatos muestreados del tráfico de red de la infraestructura de Microsoft, como el IP o el puerto de origen o destino, el tamaño de paquete y el protocolo de red.
* Soluciones de asociados: recopila alertas de seguridad de las soluciones de asociados integradas, como firewalls y soluciones antimalware. Estos datos se guardan en el almacenamiento de Azure Security Center, que actualmente se encuentra en Estados Unidos.
* Sus máquinas virtuales: Azure Security Center puede recopilar tanto información de configuración como información sobre eventos de seguridad, como los eventos y registros de auditoría de Windows, registros IIS, mensajes de Syslog y archivos de volcado de memoria de las máquinas virtuales mediante los agentes de recolección de datos. Para más información, consulte la sección "Administración de recolección de datos".

Además, se guarda información acerca de las alertas de seguridad, recomendaciones y estado de mantenimiento de seguridad en el almacenamiento de Azure Security Center, que actualmente se encuentra en Estados Unidos. Esta información puede incluir información de configuración relacionada y eventos de seguridad recopilados de las máquinas virtuales que son necesarios para proporcionarle la alerta de seguridad, la recomendación o el estado de mantenimiento de seguridad.

## Protección de datos
**Segregación de datos**: los datos se mantienen separados de forma lógica en cada componente a lo largo de todo el servicio. Todos los datos se etiquetan por organización. Este etiquetado persiste a lo largo del ciclo de vida de los datos y se aplica en cada nivel del servicio. Además, los datos recopilados de las máquinas virtuales se almacenan en las cuentas de almacenamiento.

**Acceso a datos**: para proporcionar recomendaciones de seguridad e investigar las posibles amenazas de seguridad, el personal de Microsoft puede acceder a la información recopilada o analizada por parte de los servicios de Azure, lo que incluye los archivos de volcado de memoria. Tanto los archivos de volcado de memoria como los eventos de creación de procesos pueden incluir accidentalmente los datos del cliente o datos personales de sus máquinas virtuales. Cumplimos los [términos ](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) y [la declaración de privacidad de los Servicios en línea de Microsoft](https://www.microsoft.com/privacystatement/es-ES/OnlineServices/Default.aspx), donde se estipula que Microsoft no utilizará los datos de los clientes ni información derivada de ellos con fines comerciales, publicitarios o similares. Solo usamos los datos del cliente necesarios para proporcionar los servicios de Azure. El usuario conserva todos los derechos sobre los datos del cliente.

**Uso de datos**: Microsoft utiliza los patrones y la inteligencia de amenazas vistos en varios inquilinos para mejorar las funcionalidades de detección y prevención; esto se realiza según lo dispuesto en los compromisos de privacidad que se describen en nuestra [declaración de privacidad](https://www.microsoft.com/privacystatement/es-ES/OnlineServices/Default.aspx).

**Ubicación de datos**: se especifica una cuenta de almacenamiento para cada región en que se ejecutan máquinas virtuales. Esto permite almacenar los datos en la misma región en que se encuentra la máquina virtual de la que se recopilan. Dichos datos, entre los que se incluyen los archivos de volcado de memoria, se almacenarán de forma persistente en su cuenta de almacenamiento. El servicio también almacena información acerca de las alertas de seguridad (lo que incluye las alertas de las soluciones de asociados integradas), recomendaciones y estado de mantenimiento de seguridad en el almacenamiento de Azure Security Center, que actualmente se encuentra en Estados Unidos.

## Administración de recolección de datos de máquinas virtuales
Si elige habilitar Azure Security Center, la recolección de datos estará activada para todas las suscripciones. La recolección de datos se puede desactivar en la sección "Directiva de seguridad" del panel de Azure Security Center. Cuando se activa la recolección de datos, Azure Security Center aprovisiona el Agente de supervisión de Azure en todas las máquinas virtuales compatibles existentes y en las nuevas que se crean. La extensión Supervisión de seguridad de Azure busca diversas configuraciones relacionadas con la seguridad y las envía a los [Seguimientos de eventos de Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW). Además, el sistema operativo generará eventos del registro de eventos mientras la máquina se ejecute. Estos son algunos ejemplos de dichos datos: tipo y versión del sistema operativo, registros del sistema operativo (registros de eventos de Windows), procesos en ejecución, nombre de la máquina, direcciones IP, usuario conectado e identificador de inquilino. El Agente de supervisión de Azure lee las entradas de los registros de eventos y los seguimientos de ETW y los copia en la cuenta de almacenamiento para su análisis.

Se especifica una cuenta de almacenamiento para cada región en la que haya máquinas virtuales en ejecución, donde se almacenan los datos que se recopilan de las máquinas virtuales de la misma región. Esto facilita el mantenimiento de los datos en la misma zona geográfica para fines de privacidad y soberanía de datos. Las cuentas de almacenamiento de cada región se pueden configurar en la sección "Directiva de seguridad" del panel de Azure Security Center.

El Agente de supervisión de Azure también copia los archivos de volcado de memoria en la cuenta de almacenamiento. Azure Security Center recopila copias efímeras de los archivos de volcado de memoria y las analiza para buscar pruebas de intentos de vulnerabilidad y de riesgos ciertos. Azure Security Center realiza este análisis en la misma región geográfica en que se encuentra la cuenta de almacenamiento y elimina las copias efímeras cuando análisis se completa.

La recolección de datos de máquinas virtuales se puede deshabilitar en cualquier momento, lo que eliminará todos los agentes de supervisión que Azure Security Center haya instalado previamente.

## Pasos siguientes
En este documento, se ha explicado cómo se administran y protegen los datos en Azure Security Center. Para más información sobre Azure Security Center, consulte:

* [Guía de planeamiento y operaciones de Azure Security Center](security-center-planning-and-operations-guide.md): aprenda a planear y conozca las consideraciones de diseño necesarias para usar Azure Security Center.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md): obtenga información sobre cómo supervisar el estado de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md): aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md): encuentre las preguntas más frecuentes sobre el uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): encuentre entradas de blog sobre el cumplimiento y la seguridad de Azure.

<!---HONumber=AcomDC_0817_2016-->
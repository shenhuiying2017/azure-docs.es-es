---
title: "Comparación de productos de supervisión de Microsoft | Microsoft Docs"
description: "Microsoft Operations Management Suite (OMS) es la solución de administración de TI basada en la nube de Microsoft que le ayuda a administrar y proteger su infraestructura en la nube y local.  Este artículo identifica los distintos servicios que se incluyen en OMS y proporciona vínculos a contenido detallado."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: a63ca0ad-61f8-425d-a48c-d87ba518c104
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2016
ms.author: bwren
ms.openlocfilehash: b4201f105a87b0a41059c061eb37fb35d4514e02
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="microsoft-monitoring-product-comparison"></a>Comparación de productos de supervisión de Microsoft
Este artículo proporciona una comparación entre System Center Operations Manager (SCOM) y Log Analytics en Operations Management Suite (OMS) en cuanto a su arquitectura, la lógica de cómo supervisan los recursos y la forma en la que realizan un análisis de los datos recopilados.  En él se proporciona información fundamental sobre sus diferencias y sus fuerzas relativas.  

## <a name="basic-architecture"></a>Arquitectura básica
### <a name="system-center-operations-manager"></a>System Center Operations Manager
Todos los componentes de SCOM están instalados en su centro de datos.  [Los agentes están instalados](http://technet.microsoft.com/library/hh551142.aspx) en equipos Windows y Linux administrados por SCOM.  Los agentes se conectan a [servidores de administración de](https://technet.microsoft.com/library/hh301922.aspx) , que se comunican con el almacén de datos y la base de datos de SCOM.  Los agentes se basan en la autenticación de dominio para conectarse a servidores de administración.  Los que están fuera de un dominio de confianza pueden realizar la autenticación de certificado o conectarse a un [servidor de puerta de enlace](https://technet.microsoft.com/library/hh212823.aspx).

SCOM requiere dos bases de datos SQL, una para datos operativos y otro almacén de datos para admitir informes y análisis de datos.  Un [servidor de informes](https://technet.microsoft.com/library/hh298611.aspx) ejecuta SQL Reporting Services para informar sobre los datos del almacén de datos. 

SCOM puede supervisar los recursos en la nube con los módulos de administración para productos como [Azure](https://www.microsoft.com/download/details.aspx?id=38414), [Office 365](https://www.microsoft.com/download/details.aspx?id=43708) y [AWS](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/AWSManagementPack.html).  Estos módulos de administración utilizan uno o más agentes locales como servidores proxy para detectar recursos en la nube y ejecutar flujos de trabajo para medir su rendimiento y disponibilidad.  También se utilizan agentes proxy para [supervisar dispositivos de red](https://technet.microsoft.com/library/hh212935.aspx) y otros recursos externos.

La consola del operador es una aplicación de Windows que se conecta a uno de los servidores de administración y permite al administrador ver y analizar los datos recopilados y configurar el entorno de SCOM.  Una consola basada en web se puede hospedar en cualquier servidor IIS y proporciona análisis de datos a través de un explorador.

![Arquitectura de SCOM](media/operations-management-suite-monitoring-product-comparison/scom-architecture.png)

### <a name="log-analytics"></a>Log Analytics
La mayoría de los componentes de OMS se encuentran en la nube de Azure, por lo que puede implementarla y administrarla con el mínimo costo y esfuerzo administrativo.  Todos los datos que recopila Log Analytics se almacenan en el repositorio de OMS.

Log Analytics puede recopilar datos desde uno de estos tres orígenes:

* Máquinas físicas y virtuales con Windows y [Microsoft Monitoring Agent (MMA)](https://technet.microsoft.com/library/mt484108.aspx) o Linux y [Operations Management Suite Agent para Linux](https://technet.microsoft.com/library/mt622052.aspx).  Estos equipos pueden ser locales o máquinas virtuales en Azure u otra nube.
* Una cuenta de Almacenamiento de Azure con datos de [Diagnósticos de Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) que recopila datos de un rol de trabajo de Azure, rol web o máquina virtual.
* [Conexión a un grupo de administración de SCOM](https://technet.microsoft.com/library/mt484104.aspx).  En esta configuración, los agentes se comunican con los servidores de administración de SCOM que entregan los datos a la base de datos de SCOM, y desde allí pasan al almacén de datos de OMS.
  Los administradores analizan los datos recopilados y configuran Log Analytics con el portal de OMS que se hospeda en Azure y al que puede obtenerse acceso desde cualquier explorador.  Las aplicaciones móviles para obtener acceso a estos datos están disponibles para las plataformas estándares.

![Arquitectura de Log Analytics](media/operations-management-suite-monitoring-product-comparison/log-analytics-architecture.png)

### <a name="integrating-scom-and-log-analytics"></a>Integración de SCOM y Log Analytics
Cuando SCOM se utiliza como origen de datos para Log Analytics, puede aprovechar las características de ambos productos en un entorno de supervisión de híbrido.  Puede configurar los agentes de SCOM existentes a través de la consola del operaciones para que la administre OMS, además de continuar ejecutando módulos de administración de SCOM.  
Los datos de un grupo de administración de SCOM conectado se entregan a Log Analytics mediante uno de estos cuatro métodos:

* El agente recopila los datos de rendimiento y eventos y los entrega a SCOM.  Servidores de administración de SCOM, después entrega de los datos a Log Analytics.
* Algunos eventos, como los eventos de seguridad y los registros de IIS seguirán enviándose directamente a Log Analytics desde el agente.
* Algunas soluciones proporcionarán software adicional al agente o requerirán que el software se instale para recopilar datos adicionales.  Normalmente, estos datos se enviarán directamente a Log Analytics.
* Algunas soluciones recopilarán datos directamente desde los servidores de administración de SCOM que no se originan desde el agente.  Por ejemplo, la [solución de administración de alertas](https://technet.microsoft.com/library/mt484092.aspx) recopila alertas de SCOM después de que se hayan creado.

## <a name="monitoring-logic"></a>Lógica de supervisión
SCOM y Log Analytics funcionan con datos similares recopilados a partir de los agentes, pero tienen diferencias fundamentales en cómo definen e implementan su lógica de recopilación de datos y cómo analizan los datos que recopilan.

### <a name="operations-manager"></a>Operations Manager
La lógica de supervisión para SCOM se implementa en [módulos de administración](https://technet.microsoft.com/library/hh457558.aspx) , que contienen lógica para la detección de componentes que supervisar, la medición del estado de los componentes y la recopilación de datos que analizar.  La supervisión de datos puede ser tan simple como la recopilación de un evento o contador de rendimiento, o podría usar la lógica compleja que se implementa en un script.  Los módulos de administración que incluyen una supervisión completa están disponibles para una variedad de [Aplicaciones de Microsoft y de terceros](http://go.microsoft.com/fwlink/?LinkId=82105) , además de dispositivos de red y hardware.  También puede [crear sus propios módulos de administración](http://aka.ms/mpauthor) para aplicaciones personalizadas.

Los módulos de administración contienen varios flujos de trabajo que realizan algunas funciones de supervisión distintas, como el muestreo un contador de rendimiento, la comprobación el estado de un servicio o la ejecución de un script.  Cada flujo de trabajo se ejecuta de forma independiente y define sus propios resultados como base de datos en la que se escribirá y generará una alerta. 

Puede invalidar los detalles del flujo de trabajo como la frecuencia con la que se ejecutan, el umbral a partir desde el que se considera un error y la gravedad de la alerta que generan.  También puede proporcionar una funcionalidad adicional mediante la adición de sus propios flujos de trabajo.

![Invalidaciones](media/operations-management-suite-monitoring-product-comparison/scom-overrides.png)

Los módulos de administración se instalan en la base de datos de Operations Manager y se distribuyen automáticamente a los agentes mediante servidores de administración.  Cada agente descargará los módulos de administración automáticamente y cargará los flujos de trabajo relevantes para las aplicaciones que hayan instalado.  Los datos recopilados por el agente se vuelven a entregar al servidor de administración para la inserción en el almacén de datos y la base de datos de SCOM.  La consola del operador permite ver y analizar estos datos a través de vistas personalizadas, paneles e informes incluidos en el módulo de administración.

La distribución de módulos de administración se muestra en el siguiente diagrama.

![Flujo del módulo de administración](media/operations-management-suite-monitoring-product-comparison/scom-mpflow.png)

### <a name="log-analytics"></a>Log Analytics
#### <a name="event-and-performance-collection"></a>Recopilación de rendimiento y eventos
Log Analytics recopila eventos y contadores de rendimiento de los sistemas de agente con orígenes como el registro de eventos de Windows, los registros de IIS y Syslog.  Puede definir los criterios para los se recopilan los datos a través del portal de Log Analytics y, a continuación, crear consultas de registro para analizar los datos recopilados.  Un conjunto de criterios estándar se define cuando se crea el área de trabajo de OMS, y puede definir datos adicionales para aplicaciones concretas. 

![Definición de registros de eventos en Log Analytics](media/operations-management-suite-monitoring-product-comparison/log-analytics-definedata.png)

Aunque SCOM tiene muchos flujos de trabajo detallados que normalmente definen criterios específicos para los datos y la acción que debe realizarse como respuesta, Log Analytics tiene criterios más generales para la recopilación de datos.  Las soluciones y consultas de registro ofrecen más criterios de destino para analizar y actuar en datos específicos en la nube después de la recopilación.

#### <a name="solutions"></a>Soluciones
Las soluciones proporcionan una lógica adicional para el análisis y la recopilación de datos.  Puede seleccionar soluciones que agregar a su suscripción de OMS desde la Galería de soluciones.

![Galería de soluciones](media/operations-management-suite-monitoring-product-comparison/log-analytics-solutiongallery.png)

Principalmente, las soluciones se ejecutan en la nube y proporcionan un análisis de eventos y contadores de rendimiento recopilados en el repositorio de OMS.  También pueden definir datos adicionales que recopilar que se pueden analizar con las consultas de registros o mediante una interfaz de usuario adicional que brinda la solución en el panel de OMS. 

Por ejemplo, la [solución de seguimiento de cambios](https://technet.microsoft.com/library/mt484099.aspx) detecta que la configuración cambia en los sistemas de agente y escribe eventos en el repositorio de OMS que se puede analizar con varias vistas gráficas que resuman los cambios detectados.  Puede profundizar desde la vista resumida en las consultas de registro que muestran los datos detallados recopilados por la solución.

Aunque puede seleccionar las soluciones que agregar a la suscripción, actualmente no dispone de la capacidad para crear sus propias soluciones.  Puede seleccionar los eventos y los contadores de rendimiento para recopilar y crear vistas personalizadas basadas en sus propias consultas de registro.

La lógica de supervisión de Log Analytics se resume en el siguiente diagrama.

![Flujo de soluciones de Log Analytics](media/operations-management-suite-monitoring-product-comparison/log-analytics-solution-flow.png)

## <a name="health-monitoring"></a>Supervisión del estado
### <a name="operations-manager"></a>Operations Manager
SCOM puede modelar los distintos componentes de una aplicación y proporcionar un estado en tiempo real para cada uno.  Esto le permite no solo ver los errores detectados y el rendimiento con el paso del tiempo, sino también para validar el estado real de una aplicación o sistema y cada uno de sus componentes en un momento dado.  Debido a que comprende los períodos que una aplicación está disponible, el motor de estado en SCOM también admite Acuerdos de Nivel de Servicio (SLA) que analizan e informan de la disponibilidad de una aplicación con el paso del tiempo.

Por ejemplo, la vista siguiente muestra el estado en tiempo real de los motores de Base de datos SQL supervisado por SCOM.  El estado de cada una de las bases de datos para uno de los motores de base de datos se muestra en la mitad inferior de la vista.

![Vista de estado](media/operations-management-suite-monitoring-product-comparison/scom-state-view.png)

El Explorador de estado de uno de los motores de base de datos se muestra a continuación con los monitores que se utilizan para determinar el estado global.  Estos monitores se definen en el módulo de administración de SQL y se ejecutan en todos los motores de Base de datos SQL detectados por SCOM.

![Explorador de estado](media/operations-management-suite-monitoring-product-comparison/scom-health-explorer.png)

Los componentes en varios sistemas pueden combinarse para medir el estado de una aplicación distribuida.  Esto puede ser especialmente útil para aplicaciones de línea de negocio que incluyan varios componentes distribuidos.  Puede crear un modelo que mida el estado de cada componente que se distribuya en la disponibilidad de la aplicación.

Active Directory es un ejemplo de un módulo de administración que proporciona un modelo para analizar sus componentes distribuidos.  El diagrama de ejemplo siguiente muestra el estado del entorno general y la relación entre bosques, dominios y controladores de dominio.  Cada uno de estos componentes incluye subcomponentes y varios monitores similares al ejemplo de SQL anterior.

![Vista de diagrama de SCOM](media/operations-management-suite-monitoring-product-comparison/scom-diagram-view.png)

### <a name="log-analytics"></a>Log Analytics
OMS no incluye un motor común para las aplicaciones de modelo ni mide su estado en tiempo real.  Las soluciones individuales pueden evaluar el estado general de los servicios determinados basándose en los datos recopilados y pueden instalar una lógica personalizada en el agente para realizar el análisis en tiempo real.  Dado que las soluciones se ejecutan en la nube con acceso al repositorio de OMS, a menudo pueden proporcionar un análisis más profundo que normalmente se realiza por módulos de administración. 

Por ejemplo, las [soluciones de evaluación de SQL y evaluación de AD](https://technet.microsoft.com/library/mt484102.aspx) analizan los datos recopilados y proporcionan una clasificación de distintos aspectos del entorno.  Incluye recomendaciones para mejoras que se pueden realizar para que la disponibilidad y el rendimiento del entorno sean superiores.

![Solución de evaluación de AD](media/operations-management-suite-monitoring-product-comparison/log-analytics-ad-assessment.png)

## <a name="data-analysis"></a>Análisis de datos
SCOM y Log Analytics ofrecen diferentes características para analizar los datos recopilados.  SCOM tiene vistas y paneles en la consola del operador para analizar datos recientes en una variedad de formatos y los informes para presentar los datos del almacén de datos en formato tabular.  Log Analytics proporciona una interfaz y un lenguaje de consulta de registro completo para analizar los datos en el repositorio de OMS.  Cuando SCOM se utiliza como origen de datos para Log Analytics, el repositorio incluye los datos recopilados por SCOM, por lo que las herramientas de Log Analytics pueden utilizarse para analizar los datos de ambos sistemas.

### <a name="operations-manager"></a>Operations Manager
#### <a name="views"></a>Vistas
Las vistas de la consola del operador permiten ver diferentes tipos de datos recopilados por SCOM en diferentes formatos, normalmente tabulares para eventos, alertas y datos de estado, y gráficos de línea para datos de rendimiento.  Las vistas realizan una consolidación o análisis mínimos de los datos, pero le permiten filtrar según determinados criterios. 

![Vistas](media/operations-management-suite-monitoring-product-comparison/scom-views.png)

Los módulos de administración proporcionarán normalmente varias vistas que son compatibles con la aplicación o el sistema que supervisa.  Esto puede incluir vistas de estado para los distintos objetos que detecta el módulo de administración, vistas de alertas para los problemas detectados y vistas de rendimiento para los contadores.

Las vistas son especialmente útiles para analizar el estado actual del entorno, incluidas las alertas abiertas y el estado de mantenimiento de los objetos y sistemas supervisados.  Puede desglosar los datos de rendimiento o evento detallados que ofrecen una alerta determinada para diagnosticar la causa raíz. De forma similar, puede ver el rendimiento y el estado de diferentes componentes de una aplicación para evaluar su estado actual.

#### <a name="dashboards"></a>Paneles
Los paneles de la consola del operador trabajan principalmente con los mismos datos que las vistas pero son más personalizables y pueden incluir visualizaciones más enriquecidas.  Hay disponible un conjunto de paneles estándar que puede personalizar con facilidad para sus propios fines.  También puede utilizar un widget de PowerShell que puede mostrar los datos devueltos de una consulta de PowerShell.

![Panel](media/operations-management-suite-monitoring-product-comparison/scom-dashboard.png)

Los desarrolladores tienen la capacidad de agregar componentes personalizados a los paneles que se incluyen en sus módulos de administración.  Estos pueden disponer de una alta especialización en una aplicación determinada, como el panel en el módulo de administración de SQL que se muestra a continuación.  Este panel también puede utilizarse como plantilla para versiones personalizadas.

![Panel SQL](media/operations-management-suite-monitoring-product-comparison/scom-sql-dashboard.png)

#### <a name="reports"></a>Informes
Los informes de SCOM analizan los datos desde el almacén de datos en formato tabular.  Pueden imprimirse y programarse para la entrega automatizada en diferentes formatos de archivo como PDF, CSV y Word.  Los informes trabajan con datos del almacén de datos, por lo que son especialmente adecuados para el análisis de tendencias a largo plazo.

Normalmente, los módulos de administración proporcionarán informes personalizados para una aplicación determinada.  También puede realizar la selección a partir de una biblioteca de informes genéricos que puede personalizar para sus propias aplicaciones o para llevar a cabo un análisis ad hoc.

A continuación se muestra un informe de rendimiento de ejemplo que muestra los datos recopilados por el módulo de administración de Active Directory.

![Informe](media/operations-management-suite-monitoring-product-comparison/scom-report.png)

### <a name="log-analytics"></a>Log Analytics
Log Analytics tiene un [lenguaje de consulta](https://technet.microsoft.com/library/mt484120.aspx) que puede utilizar para realizar un análisis de datos desde varias aplicaciones sin necesidad de crear un informe o vista personalizados.  Como OMS se implementa en la nube, el rendimiento de las consultas y el análisis de datos no están sujetos a las limitaciones de hardware, y puede analizar rápidamente las consultas, incluidos millones de registros. 

Las consultas de Log Analytics también son la base de otras funcionalidades.  Puede guardar una consulta, exportar los resultados a Excel o que se ejecute en intervalos regulares automáticamente y genere una alerta si los resultados coinciden con determinados criterios.  

![Flujo de consulta de registro](media/operations-management-suite-monitoring-product-comparison/log-analytics-query-flow.png)

A continuación se muestra un ejemplo de una consulta de Log Analytics.  En este ejemplo se devuelven todos los eventos con "iniciado" en el nombre y agrupados por el Id. de evento.  El usuario simplemente proporciona la consulta y Log Analytics genera dinámicamente la interfaz de usuario para realizar el análisis.  La selección de un elemento en la lista devolverá los datos de eventos detallados.

![Consulta de registro](media/operations-management-suite-monitoring-product-comparison/log-analytics-query.png)

Además de proporcionar un análisis ad hoc, las consultas de Log Analytics pueden guardarse para un uso futuro y también agregarse al [panel de OMS](http://technet.microsoft.com/library/mt484090.aspx) tal como se muestra en el ejemplo siguiente.

![panel de OMS](media/operations-management-suite-monitoring-product-comparison/log-analytics-dashboard.png)

## <a name="next-steps"></a>Pasos siguientes
* Implemente [System Center Operations Manager (SCOM)](https://technet.microsoft.com/library/hh205987.aspx).
* Suscríbase a [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics).  


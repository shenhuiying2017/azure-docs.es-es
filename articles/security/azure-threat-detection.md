---
title: "Detección de amenazas avanzada de Azure | Microsoft Docs"
description: "Información acerca de la protección de identidad y sus funcionalidades."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2017
ms.author: TomSh
ms.openlocfilehash: a7a1179f815395028c4d0324e2bfb6fbed77229c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-advanced-threat-detection"></a>Detección de amenazas avanzada de Azure
## <a name="introduction"></a>Introducción

### <a name="overview"></a>Información general

Microsoft ha desarrollado una serie de notas del producto, información general de seguridad, procedimientos recomendados y las listas de comprobación para ayudar a los clientes de Azure con las diferentes funcionalidades de seguridad disponibles en plataforma Azure y relacionadas con ella. Los temas varían en extensión y detalle, y se actualizan periódicamente. Este documento forma parte de esa serie, como se resume en la breve descripción de la siguiente sección.

### <a name="azure-platform"></a>Plataforma Azure

Azure es una plataforma de servicios en la nube pública que admite la selección más amplia de sistemas operativos, lenguajes de programación, plataformas, herramientas, bases de datos y dispositivos.
Admite los lenguajes de programación siguientes:
-   Ejecute contenedores de Linux con integración de Docker.
-   Compile aplicaciones con JavaScript, Python,. NET, PHP, Java y Node.js.
-   Cree back-end para dispositivos iOS, Android y Windows.

Los servicios en la nube pública de Azure admiten las mismas tecnologías en las que ya confían millones de desarrolladores y profesionales de TI.

Si va a migrar a una nube pública con una organización, esta es responsable de proteger los datos y de proporcionar seguridad y gobernanza en todo el sistema.

La infraestructura de Azure está diseñada desde la instalación hasta las aplicaciones para hospedar millones de clientes simultáneamente, y proporciona una base de confianza en la que las empresas pueden satisfacer sus necesidades de seguridad. Azure proporciona una amplia gama de opciones para configurar y personalizar la seguridad para que cumpla los requisitos de las implementaciones de la aplicación. Este documento le ayuda a cumplir estos requisitos.

### <a name="abstract"></a>Descripción breve

Microsoft Azure ofrece funcionalidades de detección de amenazas avanzada integradas en servicios como Azure Active Directory, Azure Operations Management Suite (OMS) y Azure Security Center. Esta colección de servicios de seguridad y funcionalidades ofrece una manera sencilla y rápida de comprender lo que ocurre en las implementaciones de Azure.

Estas notas del producto le guiarán con los "enfoques de Microsoft Azure" hacia el diagnóstico de la vulnerabilidad de amenaza y el análisis del riesgo asociado a las actividades malintencionadas destinadas a los servidores y otros recursos de Azure. Esto le ayudará a conocer los métodos de identificación y la administración de la vulnerabilidad con soluciones optimizadas por los servicios de seguridad y las tecnologías de la plataforma Azure orientados al cliente.

Estas notas del producto se centran en la tecnología de la plataforma Azure y los controles orientados al cliente y no intenta abordar los SLA, los modelos de precios ni las consideraciones sobre los procedimientos de DevOps.

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

![Azure Active Directory Identity Protection](./media/azure-threat-detection/azure-threat-detection-fig1.png)


[Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) es una característica de la edición [Premium P2 de Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-editions) que proporciona información general de los eventos de riesgo y las posibles vulnerabilidades que afectan a las identidades de la organización. Microsoft ha estado protegiendo identidades basadas en la nube durante más de una década, y con Azure AD Identity Protection, Microsoft proporciona estos mismos sistemas de protección a los clientes de empresa. Identity Protection usa las funcionalidades de detección de anomalías existentes de Azure AD (disponibles mediante [informes de actividad anómala de Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-view-access-usage-reports#anomalous-activity-reports)) e introduce nuevos tipos de eventos de riesgo que pueden detectar anomalías en tiempo real.

Identity Protection utiliza algoritmos de aprendizaje automático adaptables y heurísticos para detectar anomalías y eventos de riesgo que pueden indicar que se ha puesto en peligro una identidad. Con estos datos, Identity Protection genera informes y alertas que permiten investigar estos eventos de riesgo y tomar las acciones de corrección o mitigación adecuadas.

Pero Azure Active Directory Identity Protection es más de una herramienta de supervisión e informes. En función de los eventos de riesgo, Identity Protection calcula un nivel de riesgo del usuario para cada usuario, lo que le permite configurar las directivas basadas en riesgos para proteger automáticamente las identidades de su organización.

Estas directivas basadas en riesgos, además de otros [controles de acceso condicional](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) proporcionados por Azure Active Directory y [EMS](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access), pueden bloquear automáticamente u ofrecer acciones de corrección adaptables que incluyen el restablecimiento de contraseñas y el cumplimiento de la autenticación multifactor.

### <a name="identity-protections-capabilities"></a>Funcionalidades de Identity Protection

Azure Active Directory Identity Protection es más que una herramienta de supervisión e informes. Para proteger las identidades de la organización, puede configurar directivas basadas en riesgos que respondan automáticamente a problemas detectados si se alcanza un nivel de riesgo específico. Estas directivas, además de otros controles de acceso condicional proporcionados por Azure Active Directory y EMS, pueden bloquear automáticamente o iniciar acciones de corrección adaptables que incluyen el restablecimiento de contraseñas y el cumplimiento de la autenticación multifactor.

Ejemplos de algunas de las maneras en que Azure Identity Protection puede ayudar a proteger las cuentas e identidades:

[Detección de eventos de riesgo y cuentas peligrosas:](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#detection)
-   Detectar seis tipos de eventos de riesgo mediante el aprendizaje automático y las reglas heurísticas
-   Calcular los niveles de riesgo del usuario
-   Proporcionar recomendaciones personalizadas para mejorar la posición de seguridad general al resaltar los puntos vulnerables

[Investigación de los eventos de riesgo:](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#investigation)
-   Enviar notificaciones de los eventos de riesgo
-   Investigar los eventos de riesgo con información pertinente y contextual
-   Proporcionar los flujos de trabajo básicos para realizar un seguimiento de las investigaciones
-   Proporcionar un fácil acceso a las acciones de corrección, como el restablecimiento de contraseñas

[Directivas de acceso condicional basadas en riesgos:](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#risky-sign-ins)
-   Directiva para mitigar inicios de sesión peligrosos al bloquear inicios de sesión o solicitar desafíos de la autenticación multifactor.
-   Directiva para proteger o bloquear cuentas de usuario peligrosas
-   Directiva para exigir que los usuarios se registren en la autenticación multifactor

### <a name="azure-ad-privileged-identity-management-pim"></a>Azure AD Privileged Identity Management (PIM)

Con [Azure Active Directory (AD) Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure),

![Administración de identidades con privilegios de Azure AD](./media/azure-threat-detection/azure-threat-detection-fig2.png)

puede administrar, controlar y supervisar el acceso dentro de la organización. Esto incluye el acceso a los recursos de Azure AD y de otros servicios en línea de Microsoft, como Office 365 o Microsoft Intune.

Privileged Identity Management de Azure AD le ayuda a:

-   Obtener alertas e informes de los administradores de Azure AD y el acceso administrativo "justo a tiempo" en servicios de Microsoft Online Services, como Office 365 e Intune

-   Obtener informes sobre el historial de acceso de administrador y sobre los cambios en las asignaciones de administrador

-   Obtener alertas sobre el acceso a un rol con privilegios

## <a name="microsoft-operations-management-suite-oms"></a>Microsoft Operations Management Suite (OMS)

[Microsoft Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) es la solución de administración de TI basada en la nube de Microsoft que le ayuda a administrar y proteger su infraestructura local y en la nube. Puesto que OMS se implementa como un servicio basado en la nube, puede hacer que funcione rápidamente con una inversión mínima en servicios de infraestructura. Las características nuevas de seguridad se entregan automáticamente, lo que supone un ahorro en costos permanentes de mantenimiento y actualización.

Además de proporcionar servicios valiosos en sí, OMS puede integrarse con componentes de System Center, como [System Center Operations Manager](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/) para ampliar sus inversiones existentes en la administración de la seguridad en la nube. System Center y OMS pueden trabajar juntos para proporcionar una experiencia de administración totalmente híbrida.

### <a name="holistic-security-and-compliance-posture"></a>Seguridad integral y postura de cumplimiento

El [panel Seguridad y auditoría de OMS](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) proporciona una vista completa de la postura de seguridad de TI de la organización, con consultas de búsqueda integradas para problemas importantes que requieren su atención. El panel Seguridad y auditoría es la pantalla principal para todo lo relacionado con la seguridad en OMS. Proporciona información detallada sobre el estado de seguridad de los equipos. También incluye la capacidad de ver todos los eventos de las últimas 24 horas, 7 días o cualquier otro intervalo personalizado.

Los paneles de OMS le ayudarán a comprender rápida y fácilmente la postura de seguridad global de cualquier entorno, todo ello en el contexto de las operaciones de TI, por ejemplo: la evaluación de actualizaciones de software, la evaluación de antimalware y líneas básicas de configuración. Además, los datos del registro de seguridad están accesibles en todo momento para simplificar los procesos de auditoría de seguridad y cumplimiento.

El panel de Seguridad y auditoría de OMS se organiza en cuatro categorías principales:

![Panel de Seguridad y auditoría de OMS](./media/azure-threat-detection/azure-threat-detection-fig3.jpg)

-   **Dominios de seguridad**: en esta área podrá explorar más los registros de seguridad a lo largo del tiempo; acceder a la evaluación del malware; actualizar la evaluación; acceder a la seguridad de la red, la información de identidad y acceso, las máquinas con eventos de seguridad y, rápidamente, al panel Azure Security Center.

-   **Problemas importantes**: esta opción le permite identificar rápidamente la cantidad de problemas activos y su gravedad.

-   **Detecciones (versión preliminar)**: permite identificar patrones de ataque mediante la visualización de las alertas de seguridad en los recursos a medida que se producen.

-   **Información sobre amenazas**: le permite identificar patrones de ataque mediante la visualización del número total de servidores con tráfico de IP malintencionado saliente, el tipo de amenaza malintencionada y un mapa que muestra de dónde proceden estas direcciones IP.

-   **Consultas comunes de seguridad**: esta opción le proporciona una lista de las consultas de seguridad más comunes que puede utilizar para supervisar el entorno. Al hacer clic en una de las consultas, se abrirá la hoja Buscar con los resultados de la consulta.

### <a name="insight-and-analytics"></a>Insight and Analytics
En el centro de [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) se encuentra el repositorio de OMS, hospedado en la nube de Azure.

![Insight and Analytics](./media/azure-threat-detection/azure-threat-detection-fig4.png)

Los datos se recopilan en el repositorio desde los orígenes conectados mediante la configuración de orígenes de datos y la incorporación de soluciones a la suscripción.

![suscripción](./media/azure-threat-detection/azure-threat-detection-fig5.png)

Cada uno de los orígenes de datos y soluciones creará tipos de registros distintos que tendrán su propio conjunto de propiedades pero, de todos modos, podrían seguirse analizando en conjunto en consultas al repositorio. Esto le permite usar las mismas herramientas y los mismos métodos para trabajar con distintas variantes de datos recopilados por distintos orígenes.


La mayor parte de la interacción con Log Analytics se realiza a través del portal de OMS que se ejecuta en cualquier explorador y proporciona acceso a las opciones de configuración y a varias herramientas para analizar y realizar acciones sobre los datos recopilados. En el portal, puede usar las [búsquedas de registros](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-searches), en las que se construyen consultas para analizar los datos recopilados, los [paneles](https://docs.microsoft.com/azure/log-analytics/log-analytics-dashboards), que se pueden personalizar con vistas gráficas de las búsquedas más valiosas, y las [soluciones](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions), que proporcionan herramientas de análisis y funcionalidades adicionales.

![herramientas de análisis](./media/azure-threat-detection/azure-threat-detection-fig6.png)

Las soluciones agregan funcionalidad a Log Analytics. Se ejecutan principalmente en la nube y brindan un análisis de los datos recopilados en el repositorio de OMS. También pueden definir tipos de registros nuevos para recopilar y que se pueden analizar con las búsquedas de registros o mediante una interfaz de usuario adicional que brinda la solución en el panel de OMS.
Seguridad y auditoría es un ejemplo de solución de este tipo.



### <a name="automation--control-alert-on-security-configuration-drifts"></a>Automation & Control: desviación de la alerta de configuración de seguridad

Azure Automation automatiza los procesos administrativos con runbooks basados en PowerShell que se ejecutan en la nube de Azure. Los runbooks pueden ejecutarse en un servidor en el centro de datos local para administrar recursos locales. Azure Automation proporciona administración de configuración con DSC de PowerShell (Configuración de estado deseada).

![Automatización de Azure](./media/azure-threat-detection/azure-threat-detection-fig7.png)

Puede crear y administrar recursos de DSC hospedados en Azure y aplicarlos a los sistemas de nube y locales para definir y aplicar automáticamente su configuración u obtener informes de la desviación para ayudar a garantizar que las configuraciones de seguridad cumplen siempre las directivas.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center ayuda a proteger los recursos de Azure. Proporciona una supervisión de la seguridad y una administración de directivas integradas en suscripciones de Azure. Dentro del servicio, es posible definir directivas no solo para las suscripciones de Azure, sino también para [grupos de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), por lo que puede ser más específico.

![Azure Security Center](./media/azure-threat-detection/azure-threat-detection-fig8.png)

Los investigadores de seguridad de Microsoft trabajan sin descanso para localizar amenazas. Tienen acceso al amplio conjunto de recursos de telemetría que les proporciona la presencia global de Microsoft en la nube y en sistemas locales. La amplitud y diversidad de estos conjuntos de datos permite a Microsoft detectar nuevos patrones y tendencias de ataque tanto en sus productos locales, destinados a particulares y empresas, como en sus servicios en línea.

Por lo tanto, Security Center es capaz de actualizar rápidamente los algoritmos de detección a medida que los atacantes idean nuevos y más sofisticados ataques de seguridad. Este enfoque le ayuda a mantenerse al día en entornos con amenazas que cambian continuamente.

![Security Center](./media/azure-threat-detection/azure-threat-detection-fig9.jpg)

La detección de amenazas de Security Center recopila automáticamente información de seguridad de sus recursos de Azure, de la red y de soluciones de asociados relacionadas.  Analiza estos datos (a menudo, al relacionar la información de diferentes orígenes) para identificar las amenazas.
En Security Center, las alertas de seguridad están clasificadas por prioridad y se incluyen recomendaciones para solucionar la amenaza.

Security Center utiliza análisis avanzados que superan con creces los enfoques basados en firmas. Los grandes avances registrados en las tecnologías de macrodatos y [aprendizaje automático](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) se usan para evaluar eventos en todo el tejido de la nube, lo que permite identificar amenazas que no se podrían identificar con métodos manuales, así como predecir la evolución de los ataques. Estas técnicas de análisis de la seguridad incluyen lo siguiente.

### <a name="threat-intelligence"></a>Información sobre amenazas

Microsoft dispone de una ingente cantidad de información sobre amenazas globales.
Los recursos telemétricos proceden de diferentes fuentes, como Azure, Office 365, Microsoft CRM Online, Microsoft Dynamics AX, outlook.com, MSN.com, la Unidad de crímenes digitales de Microsoft (DCU) y Microsoft Security Response Center (MSRC).

![Información sobre amenazas](./media/azure-threat-detection/azure-threat-detection-fig10.jpg)

Los investigadores también cuentan con la información sobre amenazas que comparten los principales proveedores de servicios en la nube y que procede de fuentes de terceros. Azure Security Center puede usar todos estos datos para avisarle de las amenazas procedentes de actores malintencionados conocidos. Estos son algunos ejemplos:

-   **Aprovechamiento de la energía de Machine Learning**: Azure Security Center tiene acceso a una gran cantidad de datos sobre la actividad de red en la nube, que puede utilizarse para detectar amenazas dirigidas a sus implementaciones de Azure. Por ejemplo:

-   **Detecciones de fuerza bruta:** Machine Learning se usa para crear un patrón histórico de intentos de acceso remoto, lo que le permite detectar los ataques de fuerza bruta contra los puertos SQL, RDP y SSH.

-   **Salida DDoS y detección de Botnet:** Un objetivo común de los ataques dirigidos a los recursos de nube consiste en utilizar la capacidad de proceso de estos recursos para ejecutar otros ataques.

-   **Nuevos servidores de análisis de comportamiento y máquinas virtuales:** una vez que un servidor o una máquina virtual están en peligro, los atacantes emplean una gran variedad de técnicas para ejecutar código malintencionado en el sistema sin que les detecten, lo que garantiza persistencia y consigue evitar los controles de seguridad.

-   **Detección de amenazas de Azure SQL Database:** identifica actividades anómalas de la base de datos e indica los intentos inusuales o posiblemente dañinos de acceso o ataque a las bases de datos.

### <a name="behavioral-analytics"></a>Análisis del comportamiento

El análisis del comportamiento es una técnica que analiza datos y los compara con una serie de patrones conocidos. No obstante, estos patrones no son simples firmas, sino que están determinados por unos complejos algoritmos de aprendizaje automático que se aplican a conjuntos de datos masivos.

![Análisis del comportamiento](./media/azure-threat-detection/azure-threat-detection-fig11.jpg)


También se determinan por medio de un análisis cuidadoso, llevado a cabo por analistas expertos, de los comportamientos malintencionados. Azure Security Center puede utilizar el análisis del comportamiento para identificar recursos en peligro a partir del análisis de registros de las máquinas virtuales, registros de los dispositivos de redes virtuales, registros de los tejidos, volcados de memoria y otros orígenes.

Además, existe una correlación con otras señales que se comprueban para fundamentar las pruebas de que se trata una campaña de gran difusión. Dicha correlación permite identificar eventos que se ajustan a unos indicadores de peligro establecidos.

Estos son algunos ejemplos:
-   **Ejecución de procesos sospechosos:** los atacantes utilizan varias técnicas para ejecutar software malintencionado sin que se detecte. Por ejemplo, un atacante podría asignar a un malware los mismos nombres que se utilizan en los archivos legítimos del sistema, pero situar estos archivos en otras ubicaciones, utilizar un nombre muy parecido al de un archivo legítimo o enmascarar la verdadera extensión del archivo. Security Center adapta el comportamiento de los procesos y supervisa su ejecución para detectar valores atípicos como estos.

-   **Malware oculto e intentos de aprovechamiento:** el malware sofisticado es capaz de eludir los productos antimalware tradicionales, porque evita escribir en los discos o cifra los componentes del software almacenados en disco. Sin embargo, este malware se puede detectar mediante un análisis de memoria, ya que, para funcionar, deja rastros en la memoria. En el momento en que el software se bloquea, un volcado de memoria captura una porción de la memoria. Con un análisis de la memoria durante el volcado, Azure Security Center puede detectar las técnicas utilizadas para aprovechar las vulnerabilidades del software, acceder a información confidencial y permanecer en secreto en una máquina afectada sin que esto afecte al rendimiento de la máquina.

-   **Desplazamiento lateral y reconocimiento interno:** para poder permanecer en una red afectada y localizar o recopilar información valiosa, los atacantes suelen tratar de desplazar lateralmente el contenido de la máquina afectada a otras de la misma red. Security Center supervisa las actividades de los procesos e inicios de sesión para detectar cualquier intento de expandir el punto de apoyo del atacante en la red, como sondeos de redes de ejecución remota de comandos y enumeración de cuentas.

-   **Scripts de PowerShell malintencionados:** los atacantes utilizan PowerShell para ejecutar código malintencionado en las máquinas virtuales objetivo con diferentes propósitos. Security Center analiza la actividad de PowerShell en busca de evidencias de actividad sospechosa.

-   **Ataques de salida:** a menudo, el objetivo de los atacantes son recursos en la nube, que utilizan con el propósito de perpetrar otros ataques. Por ejemplo, las máquinas virtuales afectadas pueden usarse para iniciar ataques por fuerza bruta contra otras máquinas virtuales, enviar correo no deseado o buscar puertos abiertos y otros dispositivos en Internet. Gracias a la aplicación del aprendizaje automático en el tráfico de red, Security Center puede detectar cuándo las comunicaciones de red salientes superan la norma establecida. En el caso del correo no deseado, Security Center relaciona el tráfico inusual de correo con la información de Office 365 para determinar si es probable que el mensaje sea fraudulento o si es el resultado de una campaña de correo electrónico legítima.

### <a name="anomaly-detection"></a>Detección de anomalías

Azure Security Center también utiliza la detección de anomalías para identificar amenazas. A diferencia del análisis del comportamiento, que depende de patrones conocidos obtenidos a partir de grandes conjuntos de datos, la detección de anomalías es una técnica más “personalizada” y se basa en referencias que son específicas de las implementaciones. El aprendizaje automático se aplica para determinar la actividad normal de las implementaciones. A partir de ahí, se generan reglas para definir condiciones de valores atípicos que podrían constituir un evento de seguridad. Veamos un ejemplo:

-   **Ataques por fuerza bruta de RDP/SSH entrantes:** es posible que las implementaciones integren máquinas virtuales con mucha actividad que tengan multitud de inicios de sesión al día y otras máquinas virtuales que tengan pocos o ningún inicio de sesión. Azure Security Center puede determinar la actividad de referencia de los inicios de sesión de estas máquinas virtuales y utilizar el aprendizaje automático para definir las actividades relacionadas con el inicio de sesión normal. Si no hay discrepancias con la línea básica definida para las características relacionadas con el inicio de sesión, puede generarse una alerta. Una vez más, es el aprendizaje automático el que se encarga de determinar qué es significativo.

### <a name="continuous-threat-intelligence-monitoring"></a>Supervisión continuada de la información sobre amenazas

Azure Security Center cuenta con equipos de científicos de datos e investigadores de seguridad de todo el mundo que supervisan sin descanso los cambios que se registran en el terreno de las amenazas. Estos son algunas de las iniciativas que llevan a cabo:

-   **Supervisión de la información sobre amenazas:** la información sobre amenazas incluye mecanismos, indicadores, implicaciones y notificaciones que tienen relación con amenazas nuevas o existentes. Esta información se comparte con la comunidad de seguridad, y Microsoft supervisa sin descanso las fuentes de orígenes internos y externos.

-   **Uso compartido de señales:** se comparte y se analiza la información que recopilan los equipos de Microsoft a partir de la amplia cartera de servicios en la nube y locales, de servidores y de dispositivos cliente de punto de conexión de Microsoft.

-   **Especialistas en seguridad de Microsoft:** colaboración continua con equipos de Microsoft que trabajan en ámbitos de seguridad especializados, como análisis forense y detección de ataques web.

-   **Ajuste preciso de la detección:** los algoritmos se ejecutan en conjuntos de datos de clientes reales y los investigadores de seguridad trabajan con los clientes para validar los resultados. Los falsos positivos y los positivos verdaderos se utilizan para perfeccionar los algoritmos de aprendizaje automático.

Toda esta combinación de esfuerzos culmina en nuevas y mejoradas técnicas de detección, de las que puede beneficiarse al instante sin ninguna acción por su parte.

## <a name="advanced-threat-detection-features---other-azure-services"></a>Características de la detección de amenazas avanzada: otros servicios de Azure

### <a name="virtual-machine-microsoft-antimalware"></a>Microsoft Antimalware de máquina virtual

[Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) para Azure es una solución de un único agente dirigida a entornos de aplicaciones e inquilinos, concebida para la ejecución en segundo plano sin intervención humana. Puede implementar la protección en función de las necesidades de sus cargas de trabajo de aplicaciones, con configuración de protección básica o personalizada avanzada que incluye supervisión antimalware. Azure Antimalware es una opción de seguridad para Azure Virtual Machines que se instala automáticamente en todas las máquinas virtuales de PaaS de Azure.

**Características de Azure para implementar y habilitar Microsoft Antimalware para las aplicaciones**

#### <a name="microsoft-antimalware-core-features"></a>Características principales de Microsoft Antimalware

-   **Protección en tiempo real:** supervisa la actividad en Cloud Services y Virtual Machines para detectar y bloquear la ejecución de malware.

-   **Análisis programado:** realiza periódicamente exámenes dirigidos para detectar malware, por ejemplo, programas que se ejecutan mediante programación.

-   **Corrección de malware:** actúa automáticamente sobre el malware detectado, y elimina o pone en cuarentena los archivos malintencionados y limpia las entradas del registro malintencionadas.

-   **Actualizaciones de firmas:** instala automáticamente las últimas firmas de protección (definiciones de virus) para garantizar que la protección está actualizada con una frecuencia determinada previamente.

-   **Actualizaciones del motor antimalware:** actualiza automáticamente el motor de Microsoft Antimalware.

-   **Actualizaciones de la plataforma antimalware:** actualiza automáticamente la plataforma Microsoft Antimalware.

-   **Protección activa:** envía a Microsoft Azure informes de metadatos de telemetría sobre las amenazas detectadas y los recursos sospechosos para garantizar una respuesta rápida a las amenazas en constante evolución, y para permitir la entrega sincrónica de firmas en tiempo real a través de Microsoft Active Protection System (MAPS).

-   **Informes de ejemplos:** proporciona informes de ejemplos al servicio Microsoft Antimalware que ayudan a mejorar el servicio y permiten la solución de problemas.

-   **Exclusiones:** permite a los administradores de las aplicaciones y los servicios configurar determinados archivos, procesos y unidades para que se excluyan de la protección y el examen por motivos de rendimiento o de otro tipo.

-   **Recopilación de eventos antimalware:** registra el estado del servicio antimalware, las actividades sospechosas y las acciones de corrección adoptadas en el registro de eventos del sistema operativo y los recopila en la cuenta de Azure Storage del cliente.

### <a name="azure-sql-database-threat-detection"></a>Detección de amenazas de Azure SQL Database

[Detección de amenazas de Azure SQL Database](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) es una nueva característica de inteligencia de seguridad integrada en el servicio de Azure SQL Database. Estamos trabajando permanentemente para conocer y detectar actividades anómalas de la base de datos y generar perfiles; Detección de amenazas de Azure SQL Database identifica las posibles amenazas a la base de datos.

Los responsables de seguridad u otros administradores designados pueden recibir una notificación inmediata sobre las actividades sospechosas en las bases de datos cuando se producen. Cada notificación proporciona detalles de la actividad sospechosa y recomienda cómo investigar más y mitigar la amenaza.

Actualmente, Detección de amenazas de Azure SQL Database detecta posibles vulnerabilidades y ataques de inyección de SQL, así como patrones de acceso anómalos a las bases de datos.

Al recibir la notificación de detección de amenazas por correo electrónico, los usuarios pueden ir a ver los registros de auditoría pertinentes a través del vínculo profundo del mensaje, que abre un visor de auditoría o una plantilla de Excel de auditoría preconfigurada que muestra los registros de auditoría correspondiente de la hora aproximada del evento sospechoso según lo siguiente:
-   El almacenamiento de información de auditoría para el servidor o la base de datos con las actividades anómalas de la base de datos

-   La tabla de almacenamiento de información de auditoría correspondiente que se usó en el momento del evento para escribir el registro de auditoría

-   Los registros de auditoría de la hora siguiente al evento.

-   Los registros de auditoría con identificadores de evento similares en el momento del evento (opcional para algunos detectores)

Los detectores de amenazas de SQL Database usan uno de los métodos de detección siguientes:

-   **Detección determinista:** detecta patrones sospechosos (en función de reglas) en las consultas de cliente SQL que coinciden con ataques conocidos. Este método tiene un grado alto de detección y bajo de falsos positivos; sin embargo, la cobertura es limitada, ya que se encuentra dentro de la categoría de "detecciones atómicas".

-   **Detección de comportamiento:** detecta la actividad anómala, comportamiento anómalo de la base de datos que no se haya observado durante los últimos 30 días.  Un ejemplo de actividad anómala del cliente SQL puede ser un pico de consultas o inicios de sesión fallidos, un gran volumen de datos extraídos, consultas canónicas inusuales y direcciones IP no familiares que se hayan usado para acceder a la base de datos

### <a name="application-gateway-web-application-firewall"></a>Firewall de aplicaciones web de Application Gateway

El [firewall de aplicaciones web](../app-service/environment/app-service-app-service-environment-web-application-firewall.md) (WAF) es una característica de [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-webapplicationfirewall-overview) que protege las aplicaciones web que utilizan una puerta de enlace de aplicaciones para las funciones estándar de [Application Delivery Control](https://kemptechnologies.com/in/application-delivery-controllers). El firewall de aplicaciones web ofrece protección contra las [10 vulnerabilidades web identificadas por OWASP](https://www.owasp.org/index.php/Top_10_2010-Main) más comunes

![Firewall de aplicaciones web de Application Gateway](./media/azure-threat-detection/azure-threat-detection-fig13.png)

-   Protección contra la inyección de código SQL

-   Protección contra scripts entre sitios

-   Protección contra ataques web comunes, como inyección de comandos, contrabando de solicitudes HTTP, división de respuestas HTTP y ataque remoto de inclusión de archivos

-   Protección contra infracciones del protocolo HTTP

-   Protección contra anomalías del protocolo HTTP, como la falta de agentes de usuario de host y encabezados de aceptación

-   Prevención contra bots, rastreadores y escáneres

-   Detección de errores de configuración comunes en aplicaciones (es decir, Apache, IIS, etc.)

La configuración de WAF en Application Gateway proporciona las siguientes ventajas:

-   Protección de la aplicación web contra las vulnerabilidades y los ataques web sin modificación del código de back-end.

-   Protección de varias aplicaciones web al mismo tiempo detrás de una puerta de enlace de aplicaciones. Application Gateway admite el hospedaje de hasta 20 sitios web detrás de una única puerta de enlace, todos los cuales se podrían proteger contra los ataques web.

-   Supervisión de las aplicaciones web de cara a los ataques mediante un informe en tiempo real generado por los registros WAF de Application Gateway.

-   Algunos controles de cumplimiento requieren que todos los puntos de conexión accesibles desde Internet estén protegidos por una solución WAF. Mediante el uso de Application Gateway con WAF habilitado, puede satisfacer estos requisitos de cumplimiento.

### <a name="anomaly-detection--an-api-built-with-azure-machine-learning"></a>Detección de anomalías: API creada con Azure Machine Learning

La Detección de anomalías es una API creada con Azure Machine Learning que resulta útil para detectar distintos tipos de patrones anómalos en los datos de series temporales. La API asigna una puntuación de anomalía a cada punto de datos de la serie temporal, que se puede usar para generar alertas, supervisar a través de paneles o conectar con los sistemas de vales.

La [API de detección de anomalías](https://docs.microsoft.com/azure/machine-learning/machine-learning-apps-anomaly-detection-api) detecta los siguientes tipos de anomalías en datos de series temporales:

-   **Cambios abruptos e interrupciones**: por ejemplo, al supervisar el número de errores de inicio de sesión en un servicio o el número de finalizaciones de compras en un sitio de comercio electrónico, los cambios abruptos y las interrupciones inusuales podrían indicar ataques a la seguridad o interrupciones en el servicio.

-   **Tendencias positivas y negativas:** al supervisar el uso de la memoria en computación, por ejemplo, reducir el tamaño de memoria libre, es indicativo de una posible pérdida de memoria; al supervisar la longitud de la cola del servicio, una tendencia al alza persistente puede indicar un problema de software subyacente.

-   **Cambios de nivel y cambios en el intervalo dinámico de valores:** por ejemplo, puede ser interesante supervisar los cambios de nivel en las latencias de un servicio después de una actualización o los niveles menores de excepciones después de una actualización.

La API basada en aprendizaje automático permite lo siguiente:

-   **Detección fiable y flexible:** los modelos de detección de anomalías permiten a los usuarios configurar las opciones de sensibilidad y detectar anomalías en conjuntos de datos de temporada y no estacionales. Los usuarios pueden ajustar el modelo de detección de anomalías para que la API de detección tenga más o menos sensibilidad en función de sus necesidades. Esto significaría detectar las anomalías más o menos visibles en los datos con y sin patrones estacionales.

-   **Detección escalable y a tiempo:** la forma tradicional de supervisión con umbrales presentes definidos por el conocimiento de dominios de los expertos es costosa y no escalable a millones de conjuntos de datos que cambian dinámicamente. Los modelos de detección de anomalías de esta API se aprenden y los modelos se optimizan automáticamente a partir de datos tanto históricos como en tiempo real.

-   **Detección proactiva y factible:** la detección de cambios lentos en las tendencias y los niveles se puede aplicar a la detección de anomalías tempranas. Las señales anómalas tempranas detectadas sirven para dirigir a las personas para que investiguen y tomen medidas en las zonas problemáticas.  Por otra parte, además de este servicio de API de detección de anomalías se pueden desarrollar modelos de análisis de causa raíz y herramientas de alerta.

La API de detección de anomalías es una solución eficaz y eficiente para una amplia gama de escenarios, como el estado del servicio y la supervisión de KPI, IoT, la supervisión del rendimiento y la supervisión del tráfico de red. Estos son algunos escenarios populares donde esta API puede resultar útil:
- Los departamentos de TI necesitan herramientas de seguimiento de eventos, códigos de error, registros de uso y rendimiento (CPU, memoria, etc.) de manera puntual.

-   Los sitios de comercio en línea desean realizar el seguimiento de las actividades de los clientes, las vistas a la página, los clics y mucho más.

-   Las empresas de servicios públicos desean realizar el seguimiento del consumo de agua, gas, electricidad y otros recursos.

-   Los servicios de administración de instalaciones/edificios desean supervisar la temperatura, la humedad, el tráfico y mucho más.

-   Los fabricantes e IoT desean utilizar datos de detección de series temporales para supervisar el flujo de trabajo, la calidad y mucho más.

-   Los proveedores de servicios, como los centros de llamadas, necesitan supervisar las tendencias de demanda de los servicios, el volumen de incidentes, la longitud de la cola de espera y mucho más.

-   Los grupos de análisis de negocios desean supervisar los movimientos anómalos en los KPI (como el volumen de las ventas, las opiniones de los clientes o los precios) en tiempo real.

### <a name="cloud-app-security"></a>Cloud App Security

[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) es un componente esencial de la pila de seguridad de Microsoft Cloud. Es una solución integral que ayuda a la organización a medida que avanza para aprovechar al máximo la promesa de las aplicaciones en la nube, pero permite mantener el control gracias a la mejor visibilidad de la actividad. También ayuda a aumentar la protección de los datos críticos en todas las aplicaciones de la nube.

Con las herramientas que ayudan a descubrir zonas oscuras de TI, evaluar los riesgos, aplicar directivas, investigar actividades y detener amenazas, la organización puede mover datos a la nube con mayor seguridad y sin perder el control sobre los datos críticos.

<table style="width:100%">
 <tr>
   <td>Descubra</td>
   <td>Descubra las zonas oscuras de TI con Cloud App Security. Gane visibilidad al descubrir aplicaciones, actividades, usuarios, datos y archivos del entorno de la nube. Descubra las aplicaciones de terceros conectadas a su nube.</td>
 </tr>
 <tr>
   <td>Investigación</td>
   <td>Investigue las aplicaciones de nube mediante herramientas forenses de nube en profundidad en las aplicaciones de riesgo o archivos y usuarios específicos de la red. Identifique patrones en los datos recopilados de la nube. Genere informes para supervisar su nube.</td>

 </tr>
 <tr>
   <td>Control</td>
   <td>Mitigue el riesgo al establecer directivas y alertas para lograr el máximo control sobre el tráfico de red en la nube. Use Cloud App Security para migrar los usuarios a alternativas de aplicaciones de nube seguras y autorizadas.</td>

 </tr>
 <tr>
   <td>Protección</td>
   <td>Use Cloud App Security para autorizar o prohibir aplicaciones, prevenir la pérdida de datos, otorgar permisos de control, compartir y generar alertas e informes personalizados.</td>

 </tr>
 <tr>
   <td>Control</td>
   <td>Mitigue el riesgo al establecer directivas y alertas para lograr el máximo control sobre el tráfico de red en la nube. Use Cloud App Security para migrar los usuarios a alternativas de aplicaciones de nube seguras y autorizadas.</td>

 </tr>
</table>


![Cloud App Security](./media/azure-threat-detection/azure-threat-detection-fig14.png)

Cloud App Security integra visibilidad con la nube gracias a lo siguiente:

-   El uso de Cloud Discovery para asignar e identificar el entorno de la nube y las aplicaciones de nube que usa la organización.


-   La prohibición y autorización de aplicaciones en la nube.



-   El uso de conectores de aplicaciones fáciles de implementar que aprovechan las ventajas de las API de proveedor, para ganar visibilidad y gobierno de las aplicaciones a las que se conecte.

-   La ayuda para mantener el control constante mediante la configuración y posterior ajuste permanente de las directivas.

En la recopilación de datos de estos orígenes, Cloud App Security ejecuta un sofisticado análisis de los datos. Le avisa inmediatamente en caso de actividades anómalas y proporciona visibilidad profunda en el entorno de nube. Puede configurar una directiva en Cloud App Security y usarla para proteger todo el contenido del entorno de nube.

## <a name="third-party-atd-capabilities-through-azure-marketplace"></a>Funcionalidades ATD de terceros a través de Azure Marketplace

### <a name="web-application-firewall"></a>Firewall de aplicaciones web

El firewall de aplicaciones web inspecciona el tráfico web entrante y bloquea las inyecciones de SQL, el scripting entre sitios, las cargas de malware y DDoS de aplicación, así como otros ataques dirigidos a las aplicaciones web. También examina las respuestas de los servidores web back-end para prevención de pérdida de datos (DLP). El motor de control de acceso integrado permite a los administradores crear directivas de control de acceso pormenorizadas para la autenticación, la autorización y la contabilidad (AAA), lo cual proporciona autenticación fiable a las organizaciones así como control sobre los usuarios.

**Aspectos destacados:**
-   Detecta y bloquea las inyecciones de SQL, el scripting entre sitios, las cargas de malware, el DDoS de aplicación y cualquier otro ataque contra la aplicación.

-   Autenticación y control de acceso.

-   Analiza el tráfico saliente para detectar los datos confidenciales y oculta o bloquea la información para que no se filtre.

-   Acelera la entrega de contenido de aplicación web, con funcionalidades como el almacenamiento en caché, la compresión y otras optimizaciones del tráfico.

A continuación se presentan unos ejemplos de firewall de aplicaciones web disponibles en Azure Marketplace:

[Firewall de aplicaciones web Barracuda, firewall de aplicaciones web virtuales Brocade (Brocade vWAF), Imperva SecureSphere y firewall ThreatSTOP IP.](https://azure.microsoft.com/marketplace/partners/brocade_communications/brocade-virtual-web-application-firewall-templatevtmcluster/)

## <a name="next-steps"></a>Pasos siguientes

- [Funcionalidades de detección de Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities)

Las funcionalidades de detección avanzada de Azure Security Center ayudan a identificar las amenazas activas dirigidas a los recursos de Microsoft Azure y proporcionan la información necesaria para que pueda responder a estas amenazas con rapidez.

- [Detección de amenazas de Azure SQL Database](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/)

La Detección de amenazas de Azure SQL Database ayudó a eliminar sus preocupaciones sobre las amenazas para la base de datos.

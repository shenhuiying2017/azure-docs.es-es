---
title: Azure Government Operations Management Suite | Microsoft Docs
description: "En este artículo describe cómo Log Analytics de Operations Management Suite se aplica a los proveedores de soluciones y organismos gubernamentales estadounidenses."
services: azure-government
cloud: gov
documentationcenter: 
author: sacha
manager: jobruno
ms.assetid: 
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/05/2016
ms.author: saweld
translationtype: Human Translation
ms.sourcegitcommit: 35cefe7f394e0f332b7b6fd4ec1609ae04f5fc6f
ms.openlocfilehash: 84eabe4bb79262c47f530c59e36119bacc4115d7

---

# <a name="azure-government-cybersecurity-monitoring-and-securing-your-assets-with-operations-management-suite"></a>Ciberseguridad de Azure Government: supervisión y protección de los recursos con Operations Management Suite 

## <a name="cybersecurity-in-the-cloud"></a>Ciberseguridad en la nube
Una preocupación fundamental para aquellos de nuestros clientes que se están trasladando a la nube es conservar la administración de los recursos y la seguridad de sus servicios de Azure Government que hayan implementado en la nube. Se debe configurar adecuadamente los firewalls de las máquinas virtuales. Las redes virtuales deben tener aplicadas los grupos de seguridad de red adecuados. Por otra parte, el acceso a los recursos debe estar bloqueado en el momento indicado. Todos estos flujos de trabajo necesarios deben planearse, diseñarse y aprovisionarse con miras a implantar una infraestructura segura que pueda usar su organismo.

Configurar este tipo de entorno puede resultar un verdadero desafío. La incorporación de su flota de servidores a cualquier servicio de supervisión constituye una operación difícil de escalar y también puede resultar complicado actualizar el servicio de supervisión. La supervisión de la infraestructura en diferentes proveedores de nube, así como a través de la nube y los sistemas locales, resulta complicada. Por último, para mantener el sistema de supervisión actualizado y posibilitar que Azure Application Insights supervise, detecte, alerte y contrarreste las amenazas de ciberseguridad, se necesitan recursos, un sistema informático eficaz y tiempo.

## <a name="microsoft-operations-management-suite"></a>Microsoft Operations Management Suite
Microsoft Operations Management Suite, ahora disponible en Azure Government, es un conjunto de servicios de Azure que le permite realizar todas estas operaciones de forma rápida y sencilla. Este artículo se centra en usar Log Analytics, que emplea búsquedas de registros de hiperescala para analizar rápidamente sus datos y revelar las amenazas en su entorno.

Azure Log Analytics puede:

* Implementar agentes en máquinas virtuales individuales (Windows y Linux) en Azure, otros proveedores de nube y de forma local.
* Conectar los registros existentes a través de un punto de conexión de System Center Operations Manager o cuenta de almacenamiento de Azure Government con los datos de registro existentes.

Veamos cómo podemos integrar Log Analytics en su flota y examinemos algunas de las soluciones integradas que atienden las cuestiones descritas en este artículo.

## <a name="onboarding-servers-to-log-analytics"></a>Incorporación de servidores en Log Analytics
El primer paso para integrar los recursos en la nube con Log Analytics consiste en instalar el agente de Log Analytics en los distintos orígenes de los registros. En el caso de máquinas virtuales, resulta muy sencillo porque puede descargar manualmente el agente del portal de Log Analytics.

![Figura 1. Servidores de Windows conectados a Operations Management Suite](./media/documentation-government-oms-figure1.png)
<p align="center">Figura 1. Servidores de Windows conectados a Log Analytics</p>

Puede conectar máquinas virtuales de Azure a Log Analytics directamente a través de Azure Portal. Para obtener instrucciones, consulte el artículo sobre las [nuevas formas de habilitar Log Analytics en las máquinas virtuales de Azure](https://blogs.technet.microsoft.com/momteam/2016/02/10/new-ways-to-enable-log-analytics-oms-on-your-azure-vms/).

También puede conectarlas mediante programación o configurar la extensión de máquina virtual de Log Analytics directamente dentro de sus plantillas de Azure Resource Manager. Consulte las instrucciones para las máquinas basadas en Windows en [Conexión de equipos Windows a Log Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-windows-agents) y para las máquinas basadas en Linux en [Conexión de equipos Linux a Log Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-linux-agents).

## <a name="onboarding-storage-accounts-and-operations-manager-to-log-analytics"></a>Incorporación de cuentas de almacenamiento y Operations Manager en Log Analytics
Log Analytics también puede conectarse a su cuenta de almacenamiento o las implementaciones existentes de System Center Operations Manager para ofrecerle administración de operaciones en escenarios híbridos (a través de proveedores de nube o en infraestructuras de nube/entorno local).

![Figura 2. Conexión de Azure Storage y Operations Manager con Log Analytics](./media/documentation-government-oms-figure2.png)
<p align="center">Figura 2. Conexión de Azure Storage y Operations Manager con Log Analytics</p>

Log Analytics también permite recopilar información de registro de otros servicios de supervisión como Chef o Puppet. Además, para las implementaciones de Azure, tenemos las máquinas virtuales con plantillas de Azure Resource Manager habilitadas para Log Analytics de manera que pueda implementar Compute e incorporar a su área de trabajo de Log Analytics al mismo tiempo.

![Figura 3. Plantillas de Azure Resource Manager para máquinas virtuales de Azure con la extensión de máquina virtual de Log Analytics](./media/documentation-government-oms-figure3a.png)
![Figura 3. Plantillas de Azure Resource Manager para máquinas virtuales de Azure con la extensión de máquina virtual de Log Analytics](./media/documentation-government-oms-figure3b.png)
<p align="center">Figura 3. Plantillas de Azure Resource Manager para máquinas virtuales de Azure con la extensión de máquina virtual de Log Analytics</p>

Podrá encontrar información sobre cómo configurar Log Analytics con su implementación local existente de Operations Manager en [Conexión de Operations Manager con Log Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-om-agents).

## <a name="applying-intelligence-through-operations-management-suite-solution-packs"></a>Aplicación de inteligencia mediante los paquetes de solución de Operations Management Suite
Ahora que tiene varios orígenes de los que registrar datos, debe encontrarles el sentido a todos estos datos.

Log Analytics es, por definición, un servicio de búsqueda de registros que le permite escribir consultas eficaces para buscar rápidamente en miles o incluso millones de registros. Sin embargo, puede resultar difícil detectar las cuestiones para las que necesita escribir consultas.

Aquí es donde entran las soluciones de Operations Management Suite. Se trata de paquetes de consultas integradas de forma nativa en Log Analytics para proporcionarle de forma proactiva información relevante de la flota que administra con Log Analytics.

Siguiendo con el tema de la ciberseguridad, explicaremos brevemente tres escenarios que Log Analytics puede resolver directamente por usted.

### <a name="antimalware-assessment"></a>Evaluación de antimalware
La evaluación antimalware le proporciona un conjunto preconfigurado de consultas, notificaciones y paneles de supervisión de manera que pueda comprobar de un solo vistazo si los servidores están protegidos frente al malware.

Este panel proporciona una lista de cuatro elementos:
* Todos los servidores con amenazas activas o corregidas.
* Las amenazas detectadas actualmente.
* Los equipos que no están suficientemente protegidos. Log Analytics encuentra esta información rastreando los registros de sus equipos para detectar cualquier sitio de FW que se esté abriendo o reglas mal configuradas en los exploradores web más comunes.
* Análisis de cómo se protegen sus servidores protegidos, por ejemplo, mediante la protección contra virus nativa del sistema operativo Windows o una solución como System Center Endpoint Protection.

Por ejemplo, puede ver que se identificó la siguiente amenaza y que System Center la clasificó de forma automática:

![Figura 4. Solución de evaluación antimalware de Operations Management Suite](./media/documentation-government-oms-figure4.png)
<p align="center">Figura 4. Solución de evaluación antimalware de Operations Management Suite</p>

Para obtener más información sobre la evaluación antimalware, consulte el artículo [Solución de evaluación de malware en Log Analytics](https://azure.microsoft.com/en-us/documentation/articles/log-analytics-malware/).

### <a name="identity-and-access"></a>Identidad y acceso
Otra cuestión frecuente en relación con la ciberseguridad gira en torno al riesgo que corren las credenciales. Su suscripción a la nube no es la única que tiene credenciales, sino que cada máquina virtual tiene un usuario o un secreto (normalmente un certificado o una contraseña) asociados.

Log Analytics organiza todos los intentos de inicio de sesión de su flota y los agrupa en función del tipo (remotos, locales, nombre de usuario, etc.). En el ejemplo siguiente, podemos ver una gran cantidad de intentos de inicio de sesión erróneos de, esencialmente, cadenas aleatorias como nombres de usuario. Esto indica que es muy probable que mis equipos se hayan visto expuestos y que los firewalls y las listas de control de acceso no los hayan protegido adecuadamente.

![Figura 5. El 97,3 % de los inicios de sesión no se produjeron correctamente en las últimas 24 horas.](./media/documentation-government-oms-figure5.png)
<p align="center">Figura 5. El 97,3 % de los inicios de sesión no se produjeron correctamente en las últimas 24 horas.</p>

### <a name="threat-intelligence"></a>Información sobre amenazas
Log Analytics también proporciona protección frente a los escenarios de infiltración malintencionada; esto es, cuando hay un riesgo para la seguridad dentro de la organización y un usuario malintencionado está tratando de sacar esos datos.

La información frente a amenazas de Log Analytics examina todos los registros de red del equipo y busca automáticamente las conexiones de red entrante/saliente a direcciones IP malintencionadas conocidas (por ejemplo: direcciones IP en la red oscura sin indexar); en caso de encontrar alguna, se lo notifica.

Por ejemplo, en la siguiente captura de pantalla se aprecia que existen conexiones de red entrantes y salientes relacionadas con la República Popular China.

Al hacer doble clic en la etiqueta entrante, descubrimos que una máquina virtual de Linux que administra Log Analytics está estableciendo conexiones salientes a una dirección IP que se sabe que pertenece a la red oscura de China.

Además, puede configurar alertas para soluciones de Operations Management Suite, como la inteligencia de amenazas. En la siguiente captura de pantalla, he configurado una alerta para que si Log Analytics detecta más de 10 conexiones salientes a una dirección IP malintencionada conocida, me envíe una alerta por correo electrónico. Después, se configura esa alerta para desencadenar un trabajo de Azure Automation configurado para apagar automáticamente esa máquina virtual.

![Figura 6. Alertas y automatización de Operations Management Suite](./media/documentation-government-oms-figure6.png)
<p align="center">Figura 6. Alertas y automatización de Operations Management Suite</p>

Esto es simplemente un ejemplo de una solución integrada de Operations Management Suite que puede aplicar a su flota de servidores, con independencia de que se ejecuten en Azure, otro proveedor de servicios en la nube o en su entorno local.

Operations Management Suite sigue actualizando sus funciones de aprendizaje automático para luchar contra las últimas amenazas automáticamente en su lugar y también seguimos publicando nuevas soluciones en Azure Marketplace.

Para obtener más información sobre Operations Management Suite, consulte [nuestra página de documentación](https://azure.microsoft.com/en-us/documentation/articles/documentation-government-overview/).



<!--HONumber=Jan17_HO1-->



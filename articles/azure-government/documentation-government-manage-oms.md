---
title: OMS de Azure Government | Microsoft Docs
description: "Este artículo describe el escenario de OMS aplicable a los organismos gubernamentales y los proveedores de soluciones de EE. UU."
services: Azure-Government
cloud: gov
documentationcenter: 
author: sacha
manager: jobruno
editor: 
translationtype: Human Translation
ms.sourcegitcommit: 1f44321bc0ec33362c64da9a19d3309c45783ed2
ms.openlocfilehash: fb38649d6f40e24849ba5a8de371b1c911089fc7

---

# <a name="azure-government-cybersecurity-monitoring-and-securing-your-assets-with-operations-management-suite-oms"></a>Ciberseguridad de Azure Government: supervisión y protección de los recursos con Operations Management Suite (OMS)

## <a name="cybersecurity-in-the-cloud"></a>Ciberseguridad en la nube
Un aspecto fundamental para nuestros clientes que se trasladan a la nube es conservar la administración de los recursos y la seguridad de sus servicios de Azure Government que haya implementado en la nube. Es necesario configurar adecuadamente los firewalls de las máquinas virtuales. Además, es necesario haber aplicado el derecho Grupos de seguridad de red a las redes virtuales. Por otra parte, el acceso a los recursos debe estar bloqueado para las personas indicadas en el momento indicado. Todo esto son flujos de trabajo necesarios que deben planearse, diseñarse y aprovisionarse con miras a disponer una infraestructura segura que pueda usar su organismo.

La configuración de un entorno de este tipo puede plantear dificultades. La incorporación de su flota de servidores a cualquier servicio de supervisión es difícil de escalar y de actualizar. La supervisión de la infraestructura en diferentes proveedores de nube, así como a través de la nube y los sistemas locales, es complicada. Por último, para mantener el sistema de supervisión actualizado con mecanismos para controlar, detectar, alertar y tomar medidas contra las amenazas de seguridad se necesita tiempo, recursos y un eficaz sistema informático.

## <a name="microsoft-operations-management-suite-oms"></a>Microsoft Operations Management Suite (OMS)
Microsoft Operations Management Suite (ahora disponible en Azure Government) es un servicio con el que puede hacer todo esto gracias a que aprovecha la eficacia de MapReduce y el aprendizaje automático como servicio. OMS puede:
* Implementar agentes en máquinas virtuales individuales (Windows y Linux) en Azure, otros proveedores de nube o de forma local.
* Conectar los registros existentes a través de un punto de conexión SCOM o cuenta de almacenamiento de Azure Government con los datos de registro existentes.
* Ejecutar servicios permanentes de aprendizaje automático y MapReduce con la tecnología de búsqueda de registros de hiperescala para exponer las amenazas en su entorno sin necesidad de configuraciones adicionales.

Veamos cómo podemos integrar OMS en su flota de servidores y algunas de las soluciones directas que pueden abordar los puntos anteriores.

## <a name="onboarding-servers-to-oms"></a>Incorporación de servidores a OMS
Lo primero que hay que hacer para integrar los recursos de la nube con Operations Management Suite es instalar el agente de OMS en los orígenes de los registros.
En el caso de las máquinas virtuales es muy sencillo: puede descargar manualmente el agente desde el área de trabajo de OMS.

![Ilustración 1: servidores de Windows Server conectados a OMS](./media/documentation-government-oms-figure1.png)
<p align="center">Ilustración 1: servidores de Windows Server conectados a OMS</p>

Puede conectar máquinas virtuales de Azure a OMS directamente a través del Portal. Consulte las instrucciones [aquí](https://blogs.technet.microsoft.com/momteam/2016/02/10/new-ways-to-enable-log-analytics-oms-on-your-azure-vms/).

También puede conectarlas mediante programación o configurar la extensión OMS directamente en las plantillas de Azure Resource Manager. Las instrucciones para los equipos basados en Windows están [aquí](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-windows-agents) y para Linux [aquí](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-linux-agents).

## <a name="onboarding-storage-accounts-and-scom-to-oms"></a>Incorporación de cuentas de almacenamiento y SCOM a OMS
OMS también puede conectarse a su cuenta de almacenamiento o las implementaciones existentes de System Center Operations Manager para ofrecerle administración de operaciones en escenarios híbridos (a través de proveedores de nube o en infraestructuras de nube/entorno local).

![Ilustración 2: conexión de Azure Storage y SCOM a OMS](./media/documentation-government-oms-figure2.png)
<p align="center">Ilustración 2: conexión de Azure Storage y SCOM a OMS</p>

OMS también admite la información de registro de otros servicios de supervisión como Chef o Puppet. Además, para las implementaciones de Azure, también tenemos las máquinas virtuales con plantillas de Azure Resource Manager habilitadas para OMS de manera que pueda implementar Compute e incorporar a su espacio de OMS al mismo tiempo. 

![Ilustración 3: plantillas de Azure Resource Manager para máquinas virtuales de Azure con la extensión OMS](./media/documentation-government-oms-figure3a.png)
![Ilustración 3: plantillas de Azure Resource Manager para máquinas virtuales de Azure con la extensión OMS](./media/documentation-government-oms-figure3b.png)
<p align="center">Ilustración 3: plantillas de Azure Resource Manager para máquinas virtuales de Azure con la extensión OMS</p>

Puede encontrar información sobre cómo configurar OMS con su implementación existente de SCOM de manera local [aquí](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-om-agents).

## <a name="leveraging-intelligence-through-oms-solution-packs"></a>Aprovechamiento de inteligencia mediante los paquetes de solución de OMS.
Ahora que tiene varios orígenes de datos de registro, surge otro problema: conseguir unificarlos para que juntos tengan sentido.
OMS es, por definición, un servicio de búsqueda de registros que le permite escribir consultas eficaces para buscar rápidamente en miles o incluso millones de registros. Sin embargo, es muy difícil detectar las cuestiones para las que necesita escribir consultas.

Escriba soluciones de OMS: paquetes de consultas integradas de manera nativa con la tecnología de aprendizaje automático y MapReduce de OMS para ofrecer de manera proactiva información sobre su flota administrada de OMS.

En cuanto al tema de la ciberseguridad, explicaremos brevemente tres escenarios que OMS puede resolver directamente por usted.

###<a name="antimalware-assessment"></a>Evaluación antimalware
La evaluación antimalware le proporciona un conjunto preconfigurado de consultas, notificaciones y paneles de supervisión de manera que pueda comprobar de un solo vistazo si los servidores están protegidos frente al malware.

Este panel le ofrece 4 elementos:
* Todos los servidores con amenazas activas o corregidas.
* Las amenazas detectadas actualmente
* Los equipos que no están suficientemente protegidos. Para ello, OMS revisa los registros de los equipos y la apariencia de cualquier sitio de FW que se esté abriendo o las reglas mal configuradas en los exploradores web más comunes.
* Análisis del modo de protección de los servidores que estén efectivamente protegidos. Por ejemplo: mediante la protección antivirus nativa del sistema operativo Windows o algo como System Center Endpoint Protection.

Por ejemplo, a continuación puede ver un caso de amenaza detectada y evaluada automáticamente por Systems Center:

![Ilustración 4: solución de evaluación antimalware de OMS](./media/documentation-government-oms-figure4.png)
<p align="center">Ilustración 4: solución de evaluación antimalware de OMS</p>

Puede obtener más información sobre la evaluación antimalware aquí: [https://azure.microsoft.com/es-es/documentation/articles/log-analytics-malware/](https://azure.microsoft.com/en-us/documentation/articles/log-analytics-malware/)

### <a name="identity-and-access"></a>Identidad y acceso
Otra cuestión frecuente en relación con la ciberseguridad es el riesgo que corren las credenciales. Su suscripción a la nube no es la única que tiene credenciales, sino que cada máquina virtual tiene un usuario o un secreto (normalmente un certificado o una contraseña) asociados.

OMS sumará y organizará todos los intentos de inicio de sesión en servidores y los clasificará según el tipo (remoto, local, nombre de usuario utilizado, etc.). Por ejemplo: en el siguiente caso se aprecia una gran cantidad de intentos de inicios de sesión que no tuvieron éxito a partir de cadenas aleatorias en gran medida como nombres de usuario. Esto es probablemente un indicio de que los equipos están expuestos y no están suficientemente protegidos por firewalls y listas de control de acceso.

![Ilustración 5: 97,3 % de intentos de inicio de sesión no conseguidos en las últimas 24 horas](./media/documentation-government-oms-figure5.png)
<p align="center">Ilustración 5: 97,3 % de intentos de inicio de sesión no conseguidos en las últimas 24 horas</p>

### <a name="threat-intelligence"></a>Información sobre amenazas
OMS también proporciona protección frente a los escenarios de infiltración malintencionada; esto es, cuando hay un riesgo para la seguridad dentro de la organización y un usuario malintencionado está tratando de sacar esos datos.

La información frente a amenazas de OMS examina todos los registros de red del equipo y busca automáticamente las conexiones de red entrante/saliente a direcciones IP malintencionadas conocidas (por ejemplo: direcciones IP en la red oscura sin indexar); en caso de encontrar alguna, se lo notifica.
Por ejemplo, a continuación se aprecian conexiones de red entrantes y salientes relacionadas con la República Popular China. 

Al hacer doble clic en la etiqueta de entrada se puede constatar que una máquina virtual Linux administrada por OMS está realizando conexiones salientes a una dirección de IP que se sabe que pertenece a la red oscura en China.

También puede configurar alertas para soluciones de OMS, como la de información sobre amenazas. A continuación se ha configurado una alerta por la que si OMS detecta más de 10 conexiones de salida a una dirección IP que se sabe que es malintencionada; debería avisar mediante el envío de un correo electrónico. Luego se configura esa alerta para desencadenar un trabajo de Azure Automation que está establecido para apagar automáticamente esa máquina virtual.

![Figura 6: alertas de OMS y Automation](./media/documentation-government-oms-figure6.png)
<p align="center">Figura 6: alertas de OMS y Automation</p>

Esto es simplemente un ejemplo de una solución de OMS que puede aplicar directamente a su flota de servidores, con independencia de que se ejecuten en Azure, otro proveedor de servicios en la nube o en sus instalaciones.
OMS seguirá actualizando automáticamente su aprendizaje automático para detectar las amenazas más recientes; además, seguiremos desplegando nuevas soluciones en la Galería de soluciones de OMS.

Para obtener más información sobre OMS, consulte nuestra página de documentación aquí: [https://azure.microsoft.com/es-es/documentation/articles/documentation-government-overview/](https://azure.microsoft.com/en-us/documentation/articles/documentation-government-overview/). 



<!--HONumber=Dec16_HO1-->



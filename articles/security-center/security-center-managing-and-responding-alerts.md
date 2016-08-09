<properties
   pageTitle="Administración y respuesta a las alertas de seguridad en el Centro de seguridad de Azure | Microsoft Azure"
   description="Este documento le ayuda a usar las capacidades del Centro de seguridad de Azure para administrar y responder a las alertas de seguridad."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/21/2016"
   ms.author="yurid"/>

# Administración y respuesta a las alertas de seguridad en el Centro de seguridad de Azure
Este documento le ayuda a usar Azure Security Center para administrar las alertas de seguridad y responder a ellas.

## ¿Qué son las alertas de seguridad?
Security Center recopila, analiza e integra automáticamente los datos de registro de los recursos de Azure, la red y las soluciones de asociados conectados, como firewalls y soluciones de protección de puntos de conexión, para detectar amenazas reales y reducir los falsos positivos. En Security Center, se muestra una lista de alertas de seguridad prioritarias, junto con la información que necesita para investigar rápidamente y recomendaciones para corregir un ataque.

> [AZURE.NOTE] Para más información acerca de cómo actúan las funcionalidades de detección de Security Center, consulte [Funcionalidades de detección de Azure Security Center](security-center-detection-capabilities.md).

Los investigadores en seguridad de Microsoft están analizando constantemente las amenazas que surgen en todo el mundo, incluidos los nuevos patrones y tendencias de ataque vistos en sus productos para consumidores y empresas, y servicios en línea. Como consecuencia, el Centro de seguridad puede actualizar sus algoritmos de detección a medida que se descubren nuevas vulnerabilidades para ayudar a los clientes a mantenerse al ritmo de las amenazas cambiantes. Algunos ejemplos de los tipos de amenazas que puede detectar el Centro de seguridad son:

- **Detección por fuerza bruta en los datos de red**: usa los modelos de aprendizaje automático que comprenden los patrones típicos del tráfico de red para las aplicaciones y permite una detección más eficaz de los intentos de acceso llevados a cabo por actores malintencionados en lugar de por usuarios legítimos.
- **Detección por fuerza bruta en datos de puntos de conexión**: se basa en el análisis de registros de la máquina; esto permite diferenciar entre intentos incorrectos y correctos.
- **Máquinas virtuales comunicándose con direcciones IP malintencionadas**: compara el tráfico de red con la inteligencia sobre amenazas globales de Microsoft y detecta las máquinas que están en peligro y comunicándose con servidores de comando y control (C&C) y viceversa.
- **Máquinas virtuales en peligro**: en función del análisis del comportamiento de registros de máquina y la correlación con otras señales, identifica los eventos anómalos que probablemente sean resultado de la existencia de máquinas vulnerables y en peligro.

## Administración de alertas de seguridad

Puede revisar las alertas actuales en el icono **Alertas de seguridad**. Abra el Portal de Azure y siga los pasos siguientes para ver más detalles sobre cada alerta:

1. En el panel del Centro de seguridad, verá el icono **Alertas de seguridad**.

    ![Icono Alertas de seguridad en Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-ga.png)

2.  Haga clic en el icono para abrir la hoja **Alertas de seguridad** que contiene información más detallada acerca de las alertas, como se muestra a continuación.

    ![La hoja Alertas de seguridad en Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-ga.png)

En la parte inferior de esta hoja aparecen los detalles de cada alerta. Para ordenar, haga clic en la columna que desea ordenar. A continuación se muestra la definición de cada columna:

- **Alerta**: una breve explicación de la alerta.
- **Recuento**: una lista de todas las alertas de este tipo específico que se han detectado en un día concreto.
- **Detectado por**: el servicio responsable de desencadenar la alerta.
- **Fecha**: la fecha en que se ha producido el evento.
- **Estado**: el estado actual de esa alerta. Existen dos tipos de servicios:
    - **Activa**: se ha detectado la alerta de seguridad.
    - **Descartada**: el usuario ha descartado la alerta de seguridad. Este estado suele utilizarse para alertas que se han investigado pero que no se han mitigado, o se ha observado que no plantean un ataque real.

- **Gravedad**: el nivel de gravedad, que puede ser alto, medio o bajo.

### Filtrado de alertas

Puede filtrar alertas en función de la fecha, el estado y la gravedad. Puede resultar útil filtrar las alertas en aquellos escenarios en que necesite restringir el ámbito de las alertas de seguridad que se muestran. Por ejemplo, podría comprobar las alertas de seguridad que se produjeron en las 24 horas anteriores, ya que se está investigando una posible brecha en el sistema.

1. Haga clic en **Filtro** en la hoja **Alertas de seguridad**. Se abre la hoja **Filtro**, donde podrá seleccionar los valores de fecha, estado y gravedad que desee ver.

	![Filtrado de alertas en Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3-ga.png)

2. 	Después de investigar una alerta de seguridad, es posible que descubra que es un falso positivo para su entorno o que indica un comportamiento normal de un recurso determinado. En cualquier caso, si determina que una alerta de seguridad no es aplicable, puede descartarla y después filtrarla de la vista. Hay dos formas de descartar una alerta de seguridad. Haga clic con el botón derecho en una alerta y seleccione **Descartar**, o bien mantenga el mouse sobre un elemento, haga clic en los tres puntos que aparecen a la derecha y seleccione **Descartar**. Para ver las alertas de seguridad descartadas, haga clic en **Filtro** y seleccione **Descartado**.

	![Descarte de alertas en Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig4-ga.png)

### Responder a alertas de seguridad

Seleccione una alerta de seguridad para ver más información sobre el evento o los eventos que la desencadenaron y, si existen, los pasos que debe seguir para corregir un ataque. Las alertas de seguridad se agrupan según el tipo y la fecha. Haga clic en una alerta de seguridad y se abrirá una hoja que contiene una lista de las alertas agrupadas.

![Responder a alertas de seguridad en el Centro de seguridad de Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-ga.png)

En este caso, las alertas desencadenadas hacen referencia a las actividades sospechosas del Protocolo de escritorio remoto (RDP). En la primera columna se muestran los recursos atacados, en la segunda, las veces que el recurso fue atacado, en la tercera, la hora en la que se produjo el ataque, en la cuarta, el estado de la alerta y, en la quinta, la gravedad del ataque. Después de revisar esta información, haga clic en el recurso atacado y se abrirá una nueva hoja.

![Sugerencias sobre qué hacer con las alertas de seguridad en el Centro de seguridad de Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-ga.png)

En el campo **Descripción** de esta hoja encontrará más detalles sobre este evento. Estos detalles adicionales ofrecen información detallada sobre lo que activó la alerta de seguridad, el recurso de destino, la dirección IP de origen si corresponde, y recomendaciones sobre cómo corregirla. En algunos casos, la dirección IP de origen estará vacía (no disponible) porque no todos los registros de eventos de seguridad de Windows incluyen la dirección IP.

> [AZURE.NOTE] La corrección sugerida por el Centro de seguridad variará según la alerta de seguridad. En algunos casos, tendrá que utilizar otras capacidades de Azure para implementar la corrección recomendada. Por ejemplo, la solución para este ataque consiste en colocar la dirección IP que lo está generando en la lista negra mediante una [ACL de red](../virtual-network/virtual-networks-acl.md) o una regla de [grupo de seguridad de red](../virtual-network/virtual-networks-nsg.md).

## Alertas de seguridad por tipo
Los mismos pasos que se usaron para acceder a la alerta de actividad sospechosa de RDP pueden utilizarse para acceder a otro tipo de alertas. Estos son otros ejemplos de alertas que se pueden ver en las alertas de Security Center:

### Posible inyección de código SQL
La inyección de código SQL es un ataque en el que se inserta código malintencionado en cadenas que posteriormente se pasan a una instancia de SQL Server para su análisis y ejecución. Deben revisarse todos los procedimientos que crean instrucciones SQL en busca de vulnerabilidades por inyección de código, porque SQL Server ejecutará todas las consultas sintácticamente válidas que reciba.

![Alerta de inyección de código SQL](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig9.png)

Esta alerta proporciona información que le permite identificar el recurso atacado, la hora de detección, el estado del ataque y también proporciona un vínculo a pasos de investigación adicionales.

### Suspicious outgoing traffic detected (Tráfico saliente sospechoso detectado)

Se pueden detectar dispositivos de red y perfiles de la misma manera que otros tipos de sistemas. Los atacantes suelen comenzar con la exploración de puertos o con el barrido de puertos. En el ejemplo siguiente se muestra un tráfico SSH sospechoso desde una máquina virtual que puede estar realizando un ataque por fuerza bruta de SSH o un barrido de puerto contra un recurso externo.

![Alerta de tráfico saliente sospechoso](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig-10.png)

Esta alerta proporciona información que le permite identificar el recurso que se usó para iniciar este ataque, el equipo en riesgo, la hora de detección, el protocolo y puerto que se utilizó. Esta hoja también proporciona una lista de pasos de corrección que pueden utilizarse para mitigar este problema.

### Network communication with a malicious machine (Comunicación de red con una máquina malintencionada)
 
Mediante el aprovechamiento de las fuentes de inteligencia de amenazas de Microsoft, Azure Security Center puede detectar equipos en peligro que se comunican con direcciones IP malintencionadas, en muchos casos, mediante un comando y un centro de control. En este caso Azure Security Center ha detectado que la comunicación se realizó mediante el malware Pony Loader (también conocido como [Fareit](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=PWS:Win32/Fareit.AF)).

![Comunicación de red con una alerta de máquina malintencionada](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig9-ga.png)

Esta alerta proporciona información que le permite identificar el recurso que se usó para iniciar este ataque, el recurso atacado, la dirección IP víctima, la dirección IP del atacante y la hora de detección.

> [AZURE.NOTE] Las direcciones IP activas se quitaron de esta captura de pantalla por privacidad.


## Consulte también

En este documento ha aprendido a configurar directivas de seguridad en el Centro de seguridad. Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

- [Guía de planeamiento y operaciones de Azure Security Center](security-center-planning-and-operations-guide.md)
- [Administración y respuesta a las alertas de seguridad en el Centro de seguridad de Azure](security-center-managing-and-responding-alerts.md)
- [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md): obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md): aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
- [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md): encuentre las preguntas más frecuentes sobre el uso del servicio.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): encuentre entradas de blog sobre el cumplimiento y la seguridad de Azure.

<!---HONumber=AcomDC_0803_2016-->
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
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/16/2015"
   ms.author="yurid"/>

# Administración y respuesta a las alertas de seguridad en el Centro de seguridad de Azure
Este documento le ayuda a usar las capacidades del Centro de seguridad de Azure para administrar y responder a las alertas de seguridad.

> [AZURE.NOTE]La información de este documento se aplica a la versión preliminar del Centro de seguridad de Azure.

## ¿Qué es el Centro de seguridad de Azure?
El Centro de seguridad de Azure ayuda a evitar, detectar y responder a amenazas con más visibilidad y control de la seguridad en sus recursos de Azure. Proporciona administración de directivas y supervisión de la seguridad integrada en las suscripciones, ayuda a detectar las amenazas que podrían pasar desapercibidas y funciona con un amplio ecosistema de soluciones de seguridad.

## ¿Qué son las alertas de seguridad?
El Centro de seguridad de Azure recopila, analiza e integra automáticamente los datos de registro de los recursos de Azure, la red y soluciones de asociados como firewalls y antimalware para detectar amenazas reales y reducir los falsos positivos. A continuación, aparece una lista de alertas con prioridad como Alertas de seguridad.

Puede revisar las alertas actuales en el icono de Alertas de seguridad. Siga los pasos siguientes para ver más detalles sobre cada alerta:

1. En el panel **Centro de seguridad de Azure**, verá el icono **Alertas de seguridad**. 

    ![Habilitación de la recopilación de datos](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1.png)

2.  Haga clic en la repetición de la alerta de seguridad en el gráfico y se abrirá la hoja **Alertas de seguridad** con más detalles acerca de esta alerta, tal como se muestra a continuación:
    
    ![Habilitación de la recopilación de datos](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2.png)

En la parte inferior de esta hoja tiene los detalles de cada alerta. Puede ordenarla por cada columna haciendo clic en la columna que desee. A continuación se muestra la definición para cada columna:

- **Alerta**: una breve explicación sobre la alerta.
- **Recuento**: representa el número de todas las alertas de este tipo específico que se han detectado en un día concreto.
- **Detectado por**: el servicio responsable de desencadenar la alerta.
- **Fecha**: cuándo se produjo el evento.
- **Estado**: muestra el estado actual de esa alerta. Existen tres tipos de estado:
    - **Activa**: se detectó la alerta de seguridad.
    - **Descartada**: el usuario ha descartado la alerta de seguridad. Este estado se usaría normalmente para las alertas investigadas pero que se ha detectado que no son un ataque real o que se investigaron y mitigaron

- **Gravedad**: muestra el nivel de gravedad, que puede ser alta, media o baja.
  

### Responder a alertas de seguridad
Muchas actividades podrían indicar un posible ataque a su organización. Por ejemplo, un administrador de red que realiza una captura de red legítima podría parecerse a alguien que está iniciando alguna forma de ataque. En otros casos, un sistema mal configurado puede conducir a varios falsos positivos en un sistema de detección de intrusiones, lo cual dificultaría detectar incidentes reales. Después de revisar las alertas de seguridad mediante el Centro de seguridad de Azure, puede empezar a actuar en función de la gravedad de la alerta.

Para realizar una acción, seleccione la alerta a la que desea responder y se abrirá una nueva hoja de la derecha con más detalles, como se muestra a continuación:

![Habilitación de la recopilación de datos](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3.png)

En este caso, las alertas que se desencadenaron hacen referencia a las actividades sospechosas del Protocolo de escritorio remoto (RDP). La primera columna muestra los recursos que han sido atacados, seguido por la hora a la que se ha detectado este ataque, el estado de la alerta y la gravedad. Después de revisar esta información, debe hacer clic en el recurso que se ha atacado y se abrirá una nueva hoja con más sugerencias acerca de lo que debe hacer a continuación como se muestra en el ejemplo siguiente:

![Habilitación de la recopilación de datos](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig4.png)
  
> [AZURE.NOTE]La corrección sugerida por el Centro de seguridad de Azure varía según la alerta de seguridad y, en algunos casos, es posible que deba usar otras capacidades de Azure para implementar la corrección recomendada. Por ejemplo, la corrección para este tipo de ataque es colocar la dirección IP que está generando este ataque en la lista negra mediante el uso de [ACL de red](virtual-networks-acl.md) o una regla de [Grupo de seguridad de red](virtual-networks-nsg.md).

### Administración de alertas de seguridad
Puede filtrar alertas en función de la fecha, el estado y la gravedad. En la hoja de Alertas de seguridad, haga clic en Filtro y habilite las opciones que desee, como se muestra a continuación:

![Habilitación de la recopilación de datos](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5.png)

Filtrar las alertas puede ser útil para escenarios en los que necesita restringir el ámbito de lo que se ve en el panel. Por ejemplo, desea comprobar las alertas de seguridad que se produjeron en las últimas 24 horas, ya que se está investigando una posible infracción en el sistema. Mientras que la mayoría de las alertas de seguridad tendrán recomendaciones que se deben aplicar, en algunas circunstancias es posible que necesite ignorar una alerta porque para su entorno es un falso positivo o un comportamiento esperado de un recurso determinado. Independientemente del caso, puede ocultar las recomendaciones de un recurso determinado mediante la opción **Descartar**.

Para descartar una tarea, haga clic en el recurso para aplicar la información adicional de visualización o aplicar la configuración recomendada. Haga clic con el botón secundario en la tarea para descartarla y verá la opción **Descartar** de modo similar a la siguiente imagen:

![Habilitación de la recopilación de datos](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6.png)

En un conjunto de alertas, cada una tendrá una descripción y corrección muy específica. Las alertas que se ven en el Centro de seguridad de Azure se basan en el escenario de ataque. Los escenarios de ataque siguientes los desencadena el motor de Microsoft:

- **Detección de fuerza bruta en datos de la red**: estas detecciones se basan en modelos de aprendizaje automático que aprenden de los datos de tráfico de red. 
- **Detección de fuerza bruta en datos de punto de conexión**: estas detecciones se basan en las consultas del Centro de seguridad de Azure de registros de la máquina; esto permite la diferenciación entre intentos incorrectos y correctos. 
- **Máquinas virtuales que se comunican con direcciones IP malintencionadas**: estas detecciones se basan en el Centro de seguridad de Azure que descubre máquinas atacadas por robots y que se comunican con sus servidores de comando y control (C&C) (y viceversa). 

## Pasos siguientes
En este documento ha aprendido a configurar directivas de seguridad en el Centro de seguridad de Azure. Para obtener más información sobre el Centro de seguridad de Azure, consulte los siguientes recursos:

- [Supervisión del estado de seguridad en el Centro de seguridad de Azure](security-center-monitoring.md): obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Preguntas más frecuentes sobre el Centro de seguridad de Azure](security-center-faq.md): busque preguntas frecuentes sobre el uso del servicio.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): encuentre entradas de blog sobre el cumplimiento y la seguridad de Azure.

<!---HONumber=AcomDC_1217_2015-->
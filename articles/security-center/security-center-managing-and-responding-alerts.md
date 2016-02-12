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
   ms.date="02/02/2016"
   ms.author="yurid"/>

# Administración y respuesta a las alertas de seguridad en el Centro de seguridad de Azure
Este documento le ayuda a usar las capacidades del Centro de seguridad de Azure para administrar las alertas de seguridad y responder a ellas.

> [AZURE.NOTE] La información de este documento se aplica a la versión preliminar del Centro de seguridad de Azure.

## ¿Qué es el Centro de seguridad de Azure?
 El Centro de seguridad ayuda a evitar, detectar y responder a amenazas con más visibilidad y control de la seguridad en los recursos de Azure. Proporciona administración de directivas y supervisión de la seguridad integrada en las suscripciones, ayuda a detectar las amenazas que podrían pasar desapercibidas y funciona con un amplio ecosistema de soluciones de seguridad.

## ¿Qué son las alertas de seguridad?
 El Centro de seguridad recopila, analiza e integra automáticamente los datos de registro de los recursos de Azure, la red y las soluciones de asociados como firewalls y programas antimalware para detectar amenazas reales y reducir los falsos positivos. A continuación, aparece una lista de alertas con prioridad como alertas de seguridad.

Puede revisar las alertas actuales en el icono de **Alertas de seguridad**. Siga los pasos siguientes para ver más detalles sobre cada alerta:

1. En el panel Centro de seguridad, verá el icono **Alertas de seguridad**.

    ![El icono Alertas de seguridad en el Centro de seguridad de Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1.png)

2.  Haga clic en el icono de repetición de las alertas de seguridad. La hoja **Alertas de seguridad** se abrirá con más detalles sobre las alertas, tal como se muestra a continuación.

    ![La hoja Alertas de seguridad en el Centro de seguridad de Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2.png)

En la parte inferior de esta hoja aparecen los detalles de cada alerta. Para ordenar, haga clic en la columna que desea ordenar. A continuación se muestra la definición de cada columna:

- **Alerta**: una breve explicación de la alerta.
- **Recuento**: una lista de todas las alertas de este tipo específico que se han detectado en un día concreto.
- **Detectado por**: el servicio responsable de desencadenar la alerta.
- **Fecha**: la fecha en que se ha producido el evento.
- **Estado**: el estado actual de esa alerta. Existen tres tipos de estado:
    - **Activa**: se ha detectado la alerta de seguridad.
    - **Descartada**: el usuario ha descartado la alerta de seguridad. Este estado suele utilizarse para alertas que se han investigado pero que no se han mitigado, o se ha observado que no plantean un ataque real.

- **Gravedad**: el nivel de gravedad, que puede ser alta, media o baja.

### Responder a alertas de seguridad
Muchas actividades pueden indicar un posible ataque a su organización. Por ejemplo, un administrador de red que realiza una captura de red legítima podría parecerse a alguien que está iniciando alguna forma de ataque. En otros casos, un sistema mal configurado puede conducir a varios falsos positivos en un sistema de detección de intrusiones, lo cual puede dificultar la detección de incidentes reales. Después de revisar las alertas de seguridad en el Centro de seguridad, puede empezar a actuar en función de la gravedad de una alerta.

Para realizar una acción, seleccione la alerta a la que desea responder, y se abrirá una nueva hoja a la derecha con más detalles, como se muestra a continuación.

![Responder a alertas de seguridad en el Centro de seguridad de Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3.png)

En este caso, las alertas desencadenadas hacen referencia a las actividades sospechosas del Protocolo de escritorio remoto (RDP). En la primera columna se muestran los recursos atacados; en la segunda, la hora a la que se detectó el ataque; en la tercera, el estado de la alerta; y en la cuarta, la gravedad del ataque. Después de revisar esta información, haga clic en el recurso atacado. Se abrirá una nueva hoja con más sugerencias sobre qué hacer a continuación, tal como se muestra en el ejemplo siguiente.

![Sugerencias sobre qué hacer con las alertas de seguridad en el Centro de seguridad de Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig4.png)

> [AZURE.NOTE] La corrección sugerida por el Centro de seguridad variará según la alerta de seguridad. En algunos casos, tendrá que utilizar otras capacidades de Azure para implementar la corrección recomendada. Por ejemplo, la corrección para este ataque es colocar la dirección IP que está generando este ataque en la lista negra mediante el uso de [ACL de red](virtual-networks-acl.md) o de una regla de [Grupo de seguridad de red](virtual-networks-nsg.md).

### Administración de alertas de seguridad
Puede filtrar alertas en función de la fecha, el estado y la gravedad. En la hoja **Alertas de seguridad**, haga clic en **Filtro** y luego habilite las opciones que desee, como se muestra a continuación.

![Filtrar alertas en el Centro de seguridad de Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5.png)

Filtrar las alertas puede ser útil para escenarios en los que necesita restringir el ámbito de lo que se ve en el panel. Por ejemplo, es posible que desee comprobar las alertas de seguridad que se produjeron en las últimas 24 horas, ya que se está investigando una posible infracción en el sistema.

En la mayoría de los casos, debe aplicar las recomendaciones de las alertas de seguridad. En algunos casos, sin embargo, debe omitir una alerta porque es un falso positivo para su entorno o indica un comportamiento normal de un recurso determinado. Independientemente del caso, puede ocultar las recomendaciones de un recurso determinado mediante la opción **Descartar**.

Para descartar una tarea, haga clic en ella y verá la opción **Descartar**, de modo similar a la siguiente imagen.

![La opción Descartar en el Centro de seguridad de Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6.png)

En un conjunto de alertas, cada una tendrá una descripción y corrección muy específicas. Las alertas que se ven en el Centro de seguridad se basan en el escenario de ataque. Las aletas sobre los escenarios de ataque siguientes las desencadena el motor de Microsoft:

- **Detección de fuerza bruta en datos de la red**: se basa en modelos de aprendizaje automático que aprenden de los datos de tráfico de red.
- **Detección de fuerza bruta en datos de punto de conexión**: se basa en las consultas del Centro de seguridad de registros de la máquina; esto permite la diferenciación entre intentos incorrectos y correctos.
- **Máquinas virtuales que se comunican con direcciones IP malintencionadas**: se basan en el Centro de seguridad que descubre máquinas atacadas por robots y que se comunican con sus servidores de comando y control (C&C) (y viceversa).

## Pasos siguientes
En este documento ha aprendido a configurar directivas de seguridad en el Centro de seguridad. Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

- [Supervisión del estado de seguridad en el Centro de seguridad de Azure](security-center-monitoring.md): obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Preguntas más frecuentes sobre el Centro de seguridad de Azure](security-center-faq.md): encuentre las preguntas más frecuentes sobre el uso del servicio.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): encuentre entradas de blog sobre el cumplimiento y la seguridad de Azure.

<!---HONumber=AcomDC_0204_2016-->
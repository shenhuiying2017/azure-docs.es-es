<properties
   pageTitle="Introducción al Centro de seguridad de Azure | Microsoft Azure"
   description="Este documento le ha ayudado a configurar directivas de seguridad en el Centro de seguridad de Azure."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/03/2016"
   ms.author="yurid"/>

# Establecimiento de directivas de seguridad en el Centro de seguridad de Azure
Este documento le ayuda a configurar las directivas de seguridad en el Centro de seguridad de Azure guiándole por los pasos necesarios para realizar esta tarea.

> [AZURE.NOTE] La información de este documento se aplica a la versión preliminar del Centro de seguridad de Azure.

## ¿Qué es el Centro de seguridad de Azure?
El Centro de seguridad de Azure ayuda a evitar, detectar y responder a amenazas con más visibilidad y control de la seguridad en sus recursos de Azure. Proporciona administración de directivas y supervisión de la seguridad integrada en las suscripciones, ayuda a detectar las amenazas que podrían pasar desapercibidas y funciona con un amplio ecosistema de soluciones de seguridad.

## ¿Qué son las directivas de seguridad?
Una directiva de seguridad define el conjunto de controles recomendados para los recursos en la suscripción o grupo de recursos especificados. En Azure Security Center, el usuario define directivas para sus suscripciones o grupos de recursos de Azure de acuerdo con las necesidades de seguridad de su compañía y el tipo de aplicaciones o la confidencialidad de los datos de cada suscripción.

Por ejemplo, es posible que los recursos usados para el desarrollo o las pruebas tengan distintos requisitos de seguridad que los de aquellos que se emplean para aplicaciones de producción. Del mismo modo, es posible que las aplicaciones con datos regulados como información de identificación personal (PII) requieran un mayor nivel de seguridad. Las directivas de seguridad habilitadas en el Centro de seguridad de Azure tratarán las recomendaciones de seguridad y la supervisión para ayudarle a identificar posibles vulnerabilidades y mitigar las amenazas.

## Configuración de directivas de seguridad para las suscripciones

Las directivas de seguridad se pueden configurar para cada suscripción o grupo de recursos. Para modificar una directiva de seguridad, debe ser propietario o colaborador de la suscripción. Siga estos pasos para configurar directivas de seguridad en el Centro de seguridad de Azure:

1. Haga clic en el icono **Directiva de seguridad** en el panel del Centro de seguridad de Azure.

2. En la hoja **Directiva de seguridad - Definir directiva por suscripción o grupo de recursos** que se abre en el lado derecho, seleccione la suscripción que desee para habilitar la directiva de seguridad. Si prefiere habilitar la directiva de seguridad para un grupo de recursos en lugar de para toda la suscripción, desplácese hacia abajo hasta la siguiente sección en la que hablamos sobre la configuración de directivas de seguridad para grupos de recursos.

    ![Definición de directiva](./media/security-center-policies/security-center-policies-fig01.png)

3. La hoja **Directiva de seguridad** de esa suscripción se abrirá con un conjunto de opciones similares a las que se muestran a continuación:

    ![Habilitación de la recopilación de datos](./media/security-center-policies/security-center-policies-fig1-new.png)

4. Asegúrese de que la opción **Recopilar datos de máquinas virtuales** está **Activada**. Esta opción habilita la recopilación de inicio de sesión automático para recursos nuevos y existentes.

    >[AZURE.NOTE] Se recomienda activar la recopilación de datos para cada una de las suscripciones, ya que esto garantizará la disponibilidad de la supervisión de seguridad para todas las máquinas virtuales nuevas y existentes. Al habilitar la recopilación de datos se instala el agente de supervisión. Si no desea activar la recopilación de datos ahora desde esta ubicación, puede hacerlo más tarde desde las vistas de estado y recomendaciones. También puede habilitar la recopilación de datos solo para la suscripción o para determinadas máquinas virtuales. Consulte las [preguntas más frecuentes sobre el Centro de seguridad de Azure](security-center-faq.md) para más información sobre VM compatibles.

5. Si la cuenta de almacenamiento todavía no está configurada, puede ver una advertencia similar a la mostrada en la figura siguiente al abrir la **Directiva de seguridad**:

    ![Selección de almacenamiento](./media/security-center-policies/security-center-policies-fig2.png)

6. Si aparece esta advertencia, haga clic en esta opción y seleccione la región tal y como se muestra en la siguiente figura:

    ![Selección de almacenamiento](./media/security-center-policies/security-center-policies-fig3.png)

7. Para cada región en la que disponga de máquinas virtuales en funcionamiento, elija la cuenta de almacenamiento en la que se almacenan los datos recopilados de esas máquinas virtuales. Esto facilita el mantenimiento de los datos en la misma zona geográfica para fines de privacidad y soberanía de datos. Cuando decida qué región va a utilizar, selecciónela y después elija la cuenta de almacenamiento.

8. En la hoja **Elegir cuentas de almacenamiento** haga clic en **Aceptar**.

    > [AZURE.NOTE] Si lo prefiere, puede agregar datos desde máquinas virtuales de varias regiones en una cuenta de almacenamiento central. Consulte las [preguntas más frecuentes sobre el Centro de seguridad de Azure](security-center-faq.md) para obtener más información.

9. En la hoja **Directiva de seguridad**, haga clic en **Activado** para habilitar las recomendaciones de seguridad que desea usar en esta suscripción. Haga clic en la opción **Prevention policy** (Directiva de prevención). Se abrirá la hoja **Directiva de seguridad** tal como se muestra a continuación.

	![Selección de las directivas de seguridad](./media/security-center-policies/security-center-policies-fig1-1-new2.png)

Utilice la tabla siguiente como referencia para comprender lo que hará cada opción:

| Directiva | Cuando el estado es Activado |
|----- |-----|
| Actualizaciones del sistema | Recupera a diario una lista de actualizaciones críticas y de seguridad disponibles desde Windows Update o WSUS, dependiendo de qué servicio está configurado para esa máquina virtual, y recomienda que se apliquen las actualizaciones que faltan. |
| Reglas de línea de base | Analiza a diario las configuraciones de SO que podrían hacer que la máquina virtual fuera más vulnerable a ataques y recomienda cambios de configuración para hacer frente a estas vulnerabilidades. Vea la [lista de líneas de base recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) para más información sobre las configuraciones específicas que se están supervisando. |
| Endpoint Protection | Recomienda que se aprovisione el servicio Endpoint Protection para todas las máquinas virtuales de Windows para ayudar a identificar y a quitar virus, spyware y otro software malintencionado. 
| Grupos de seguridad de red | Recomienda que se configuren [grupos de seguridad de red](../virtual-network/virtual-networks-nsg.md) (NSG) para controlar el tráfico entrante y saliente a subredes e interfaces de red. Todas las interfaces de red de máquina virtual heredarán los grupos de seguridad de red configurados para una subred a menos que se especifique lo contrario. Además de comprobar que se ha configurado un grupo de seguridad de red, esta opción evalúa las reglas de seguridad de entrada para identificar si hay alguna que permita el tráfico entrante. |
| Firewall de aplicaciones web | Recomienda aprovisionar un Firewall de aplicaciones web en máquinas virtuales cuando: se usa una [Dirección IP pública a nivel de instancia](../virtual-network/virtual-networks-instance-level-public-ip.md) (ILPIP) y se configuran las reglas de seguridad de entrada del grupo de seguridad de red asociadas para permitir el acceso al puerto 80 o 443. Se utiliza la dirección IP de carga equilibrada (VIP) y se configuran las reglas NAT de entrada y de equilibrio de carga asociadas para permitir el acceso al puerto 80 o 443 (para obtener más información, consulte [Compatibilidad del Administrador de recursos de Azure con el Equilibrador de carga](../load-balancer/load-balancer-arm.md)). |
| Firewall de próxima generación | Esto amplía las medidas de protección de la red más allá de los grupos de seguridad de red, que están integrados en Azure. Security Center detectará las implementaciones para las que se recomiende un firewall de próxima generación y permitirá que se aprovisione una aplicación virtual. |
| Auditoría SQL | Se recomienda que la auditoría del acceso a bases de datos y servidores SQL de Azure esté habilitada para fines de cumplimiento, detección avanzada e investigación. |
| Cifrado de datos transparente de SQL | Recomienda que el cifrado en reposo se habilite para las bases de datos SQL de Azure, las copias de seguridad asociadas y los archivos de registro de transacciones, de forma que aunque haya infracción de datos, no se puedan leer. |

11\. Cuando termine de configurar todas las opciones, haga clic en **Aceptar** en la hoja **Directiva de seguridad** con las recomendaciones y después haga clic en **Guardar** en la hoja **Directiva de seguridad** con la configuración inicial.

## Configuración de directivas de seguridad para grupos de recursos

Si prefiere configurar sus directivas de seguridad por grupo de recursos, los pasos son similares a aquellos que se utilizan para configurar directivas de seguridad para las suscripciones. La principal diferencia es que necesitará expandir el nombre de la suscripción y seleccionar el grupo de recursos para el que desea configurar la directiva de seguridad exclusiva:

![Selección de grupo de recursos](./media/security-center-policies/security-center-policies-fig4.png)

Después de seleccionar el grupo de recursos, se abrirá la hoja **Directiva de seguridad**. De forma predeterminada, la opción **Herencia** está habilitada, lo que significa que todas las directivas de seguridad para este grupo de recursos se heredan del nivel de suscripción. Puede cambiar esta configuración en caso de que desee una directiva de seguridad personalizada por grupo de recursos. Si ese es el caso, debe seleccionar **Exclusiva** y realizar los cambios en la opción **Prevention policy** (Directiva de prevención).

![Directiva de seguridad por grupo de recursos](./media/security-center-policies/security-center-policies-fig5-new.png)

> [AZURE.NOTE] En caso de un conflicto entre la directiva del nivel de suscripción y la directiva del nivel de grupo de recursos, la del nivel de recursos tiene prioridad.


## Pasos siguientes

En este documento ha aprendido a configurar directivas de seguridad en el Centro de seguridad de Azure. Para obtener más información sobre el Centro de seguridad de Azure, consulte los siguientes recursos:

- [Guía de planeamiento y operaciones de Azure Security Center](security-center-planning-and-operations-guide.md): aprenda a planear y comprender las consideraciones de diseño para adoptar Azure Security Center.
- [Supervisión del estado de seguridad en Centro de seguridad de Azure](security-center-monitoring.md): obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Administración y respuesta a las alertas de seguridad en el Centro de seguridad de Azure](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
- [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md): aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
- [Preguntas más frecuentes acerca del Centro de seguridad de Azure](security-center-faq.md): busque las preguntas más frecuentes sobre cómo usar el servicio.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): encuentre entradas de blog sobre el cumplimiento y la seguridad de Azure.

<!---HONumber=AcomDC_0608_2016-->
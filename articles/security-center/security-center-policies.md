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
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/03/2015"
   ms.author="yurid"/>

#Establecimiento de directivas de seguridad en el Centro de seguridad de Azure
Aprenda a configurar directivas de seguridad en el Centro de seguridad de Azure mediante este tutorial.

> [AZURE.NOTE]La información de este documento se aplica a la versión preliminar del Centro de seguridad de Azure.

##¿Cuándo debo utilizar directivas de seguridad?
Una directiva de seguridad define el conjunto de controles que se recomiendan para los recursos en la suscripción especificada. En el Centro de seguridad de Azure, el usuario define directivas para sus suscripciones de Azure de acuerdo con las necesidades de seguridad de su compañía y el tipo de aplicaciones o a la confidencialidad de los datos de cada suscripción.
 
Por ejemplo, es posible que los recursos usados para el desarrollo o las pruebas tengan distintos requisitos de seguridad que los de aquellos que se emplean para aplicaciones de producción. Del mismo modo, es posible que las aplicaciones con datos regulados como información de identificación personal (PII) requieran un mayor nivel de seguridad. Las directivas de seguridad habilitadas en el Centro de seguridad de Azure tratarán las recomendaciones de seguridad y la supervisión para ayudarle a identificar posibles vulnerabilidades y mitigar las amenazas.

####Configuración de directivas de seguridad

Las directivas de seguridad se configuran para cada suscripción. Para modificar una directiva de seguridad, debe ser propietario o colaborador de la suscripción. Siga estos pasos para configurar directivas de seguridad en el Centro de seguridad de Azure:

1. Haga clic en el icono **Directiva de seguridad** en el panel del Centro de seguridad de Azure.
 
2. En la hoja **Directiva de seguridad - Definir directiva por suscripción** que se abre en el lado derecho, seleccione la suscripción que desea para habilitar la directiva de seguridad.

    ![Habilitación de la recopilación de datos](./media/security-center-policies/security-center-policies-fig0.png)

3. La hoja **Directiva de seguridad** de esa suscripción se abrirá con un conjunto de opciones similares a las que se muestran a continuación:

    ![Habilitación de la recopilación de datos](./media/security-center-policies/security-center-policies-fig1.png)

    
4. Asegúrese de que la opción **Recopilar datos de máquinas virtuales** está **Activada**. Esta opción habilita la recopilación de inicio de sesión automático para recursos nuevos y existentes.

5. Si la cuenta de almacenamiento todavía no está configurada, puede ver una advertencia similar a la mostrada en la figura siguiente al abrir la **Directiva de seguridad**:

    ![Selección de almacenamiento](./media/security-center-policies/security-center-policies-fig2.png)

6. Si aparece esta advertencia, haga clic en esta opción y seleccione la región tal y como se muestra en la siguiente figura:

    ![Selección de almacenamiento](./media/security-center-policies/security-center-policies-fig3.png)

7. Para cada región en la que disponga de máquinas virtuales en funcionamiento, elija la cuenta de almacenamiento en la que se almacenan los datos recopilados de esas máquinas virtuales. Esto facilita el mantenimiento de los datos en la misma zona geográfica para fines de privacidad y soberanía de datos. Cuando decida qué región va a utilizar, selecciónela y después elija la cuenta de almacenamiento.

8. En la hoja **Elegir cuentas de almacenamiento** haga clic en **Aceptar**.

    > [AZURE.NOTE]Si lo prefiere, puede agregar datos desde máquinas virtuales de varias regiones en una cuenta de almacenamiento central. Consulte las [preguntas más frecuentes sobre el Centro de seguridad de Azure](security-center-faq.md) para obtener más información.

9. En la hoja **Directiva de seguridad**, haga clic en **Activado** para habilitar las recomendaciones de seguridad que desea utilizar en esta suscripción. Utilice la tabla siguiente como referencia para comprender lo que hará cada opción:

    | Directiva | Estado de activada |
|----- |-----|
| Actualizaciones del sistema | Recupera una lista de actualizaciones disponibles desde Windows Update o WSUS, dependiendo de qué servicio esté configurado para esa máquina virtual, cada 12 horas y recomienda que se instalen actualizaciones que faltan en las máquinas virtuales de Windows. |
| Reglas de línea de base | Analiza todas las máquinas virtuales cada 12 horas para identificar las configuraciones de SO que podrían hacer que la máquina virtual fuera más vulnerable a ataques y recomienda cambios de configuración para hacer frente a estas vulnerabilidades. Consulte la [lista de líneas de base recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) para obtener más información sobre las configuraciones específicas que se están supervisando. |
| Antimalware | Recomienda que se aprovisione antimalware para todas las máquinas virtuales de Windows para ayudar a identificar y a quitar virus, spyware y otro software malintencionado. |
| Lista de control de acceso en puntos de conexión | Recomienda que se configure una [lista de control de acceso](virtual-machines-set-up-endpoints.md) (ACL) para limitar el acceso a puntos de conexión de máquinas virtuales clásicas. Esto normalmente se usaría para asegurarse de que solo los usuarios que están conectados a la red corporativa pueden acceder a las máquinas virtuales. |
| Grupos de seguridad de red en subredes e interfaz de red | Recomienda que los [grupos de seguridad de red](virtual-networks-nsg.md) (NSG) se configuren para controlar el tráfico entrante y saliente a subredes e interfaces de red para máquinas virtuales del Administrador de recursos. Todas las interfaces de red de máquina virtual heredarán los grupos de seguridad de red configurados para una subred a menos que se especifique lo contrario. Además de comprobar que un grupo de seguridad de red se ha configurado, se evalúan las reglas de seguridad de entrada para identificar reglas que permiten el tráfico entrante. |
| Firewall de aplicaciones web | Recomienda aprovisionar un Firewall de aplicaciones web en máquinas virtuales del Administrador de recursos cuando: se usa una [Dirección IP pública a nivel de instancia](virtual-networks-instance-level-public-ip.md) (ILPIP) y se configuran las reglas de seguridad de entrada del grupo de seguridad de red asociadas para permitir el acceso al puerto 80 o 443. Se utiliza la dirección IP de carga equilibrada (VIP) y se configuran las reglas NAT de entrada y de equilibrio de carga asociadas para permitir el acceso al puerto 80 o 443 (para obtener más información, consulte [Compatibilidad del Administrador de recursos de Azure con el Equilibrador de carga](load-balancer-arm.md)). |
| Auditoría SQL | Se recomienda que la auditoría del acceso a bases de datos y servidores SQL de Azure esté habilitada para fines de cumplimiento, detección avanzada e investigación. |
| Cifrado de datos transparente de SQL | Recomienda que el cifrado en reposo se habilite para las bases de datos SQL de Azure, las copias de seguridad asociadas y los archivos de registro de transacciones, de forma que aunque haya infracción de datos, no se puedan leer. |

10. Cuando termine de configurar todas las opciones, haga clic en **Guardar** para confirmar los cambios.

> [AZURE.NOTE]Al habilitar la recopilación de datos se instala el agente de supervisión. Si no desea activar la recopilación de datos ahora desde esta ubicación, puede hacerlo más tarde desde las vistas de estado y recomendaciones. También puede habilitarla para la suscripción o solo para una VM seleccionada. Consulte las [preguntas más frecuentes sobre el Centro de seguridad de Azure](security-center-faq.md) para obtener más información sobre VM compatibles.

## Pasos siguientes
En este documento ha aprendido a configurar directivas de seguridad en el Centro de seguridad de Azure. Para obtener más información sobre el Centro de seguridad de Azure, consulte los siguientes recursos:

- [Supervisión del estado de seguridad en el Centro de seguridad de Azure](security-center-monitoring.md): Obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Administración de alertas de seguridad y respuesta a estas en el Centro de seguridad de Azure](security-center-managing-and-responding-alerts.md): Obtenga información sobre cómo administrar alertas de seguridad y responder a estas.
- [Preguntas más frecuentes sobre el Centro de seguridad de Azure](security-center-faq.md): Encuentre las preguntas más frecuentes sobre el uso del servicio.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): Encuentre entradas de blog sobre el cumplimiento y la seguridad de Azure.

<!---HONumber=AcomDC_1203_2015-->
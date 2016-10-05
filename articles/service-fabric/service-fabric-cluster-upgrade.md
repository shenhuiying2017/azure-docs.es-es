<properties
   pageTitle="Actualización de un clúster de Service Fabric | Microsoft Azure"
   description="Actualice el código de Service Fabric o la configuración que ejecuta un clúster de Service Fabric, incluida la actualización de los certificados, la incorporación de puertos de aplicación, las revisiones del sistema operativo, etc. ¿Qué se puede esperar cuando se realizan actualizaciones?"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="chackdan"/>


# Actualización de un clúster de Service Fabric

Un clúster de Azure Service Fabric es un recurso de su propiedad que está parcialmente administrado por Microsoft. En este artículo se describe lo que se administra automáticamente y lo que puede configurar usted mismo.

## Configuración de clústeres que se administran automáticamente

Microsoft mantiene el código de tejido y la configuración que se ejecuta en un clúster. Realizamos actualizaciones supervisadas automáticas del software según se necesitan. Estas actualizaciones podrían ser de código, de configuración o ambas. Para asegurarse de que la aplicación no sufra ningún impacto (o solo un impacto mínimo) durante el proceso, las actualizaciones se realizan en las tres fases siguientes.

### Fase 1: La actualización se realiza con todas las directivas de mantenimiento de clústeres

Durante esta fase, las actualizaciones se realizan en un dominio de actualización cada vez, y las aplicaciones que se ejecutaban en el clúster continúan ejecutándose sin tiempo de inactividad. Las directivas de mantenimiento del clúster (una combinación del estado del nodo y el estado de todas las aplicaciones que se ejecutan en el clúster) se cumplen mientras dura la actualización.

Si no se cumplen las directivas de mantenimiento del clúster, la actualización se revierte. Después, se envía un correo electrónico al propietario de la suscripción. El correo electrónico contiene la siguiente información:

- Notificación de que tuvimos que revertir una actualización de clúster.
- Acciones correctoras sugeridas, si hay alguna.
- El número de días (n) hasta que ejecutemos la fase 2.

Vamos a intentar ejecutar la misma actualización unas cuantas veces más por si se produjeron errores en las actualizaciones por razones de infraestructura. Después de n días desde la fecha en que se envió el correo electrónico, continuamos con la fase 2.

Si se cumplen las directivas de mantenimiento del clúster, la actualización se considera correcta y se marca como completada. Esto puede ocurrir durante la ejecución inicial de la actualización o cualquiera de las ejecuciones posteriores de la actualización en esta fase. No hay ningún correo electrónico de confirmación de una ejecución correcta. Esto es para evitar el envío de demasiados mensajes de correo electrónico: recibir un correo electrónico debe considerarse una excepción a la normalidad. Esperamos que la mayoría de las actualizaciones de clúster funcionen sin afectar a la disponibilidad de las aplicaciones.

### Fase 2: La actualización se realiza solo con las directivas de mantenimiento predeterminadas

En esta fase, las directivas de mantenimiento se establecen de forma que el número de aplicaciones que tenían un estado correcto al principio de la actualización siga siendo el mismo durante el proceso de actualización. Al igual que en la fase 1, en la fase 2 las actualizaciones se realizan en un dominio de actualización cada vez, y las aplicaciones que se ejecutaban en el clúster continúan ejecutándose sin tiempo de inactividad. Las directivas de mantenimiento del clúster (una combinación del estado del nodo y el estado de todas las aplicaciones que se ejecutan en el clúster) se cumplen mientras dura la actualización.

Si no se cumplen las directivas de mantenimiento del clúster, la actualización se revierte. Después, se envía un correo electrónico al propietario de la suscripción. El correo electrónico contiene la siguiente información:

- Notificación de que tuvimos que revertir una actualización de clúster.
- Acciones correctoras sugeridas, si hay alguna.
- El número de días (n) hasta que ejecutemos la fase 3.

Vamos a intentar ejecutar la misma actualización unas cuantas veces más por si se produjeron errores en las actualizaciones por razones de infraestructura. Se envía un recordatorio por correo electrónico un par de días antes de llegar a n días. Después de los n días desde la fecha en que se envió el correo electrónico, continuamos con la fase 3. Es necesario tomarse en serio los correos electrónicos que le enviamos en la fase 2 y realizar acciones de corrección.

Si se cumplen las directivas de mantenimiento del clúster, la actualización se considera correcta y se marca como completada. Esto puede ocurrir durante la ejecución inicial de la actualización o cualquiera de las ejecuciones posteriores de la actualización en esta fase. No hay ningún correo electrónico de confirmación de una ejecución correcta.

### Fase 3: La actualización se realiza con directivas de mantenimiento agresivas

En esta fase, estas directivas de mantenimiento están orientadas a la finalización de la actualización en lugar de al mantenimiento de las aplicaciones. Muy pocas actualizaciones del clúster terminarán en esta fase. Si el clúster llega a esta fase, es probable que la aplicación no tenga el mantenimiento adecuado o pierda disponibilidad.

Igual que las otras dos fases, las actualizaciones de la fase 3 se realizan en un dominio de actualización cada vez.

Si no se cumplen las directivas de mantenimiento del clúster, la actualización se revierte. Vamos a intentar ejecutar la misma actualización unas cuantas veces más por si se produjeron errores en las actualizaciones por razones de infraestructura. Después, el clúster se ancla y ya no recibirán soporte técnico ni actualizaciones.

Se enviará un correo electrónico con esta información al propietario de la suscripción, junto con las acciones correctoras. No esperamos que los clústeres entren en un estado en el que la fase 3 produzca errores.

Si se cumplen las directivas de mantenimiento del clúster, la actualización se considera correcta y se marca como completada. Esto puede ocurrir durante la ejecución inicial de la actualización o cualquiera de las ejecuciones posteriores de la actualización en esta fase. No hay ningún correo electrónico de confirmación de una ejecución correcta.

## Opciones de configuración de clúster controladas por el usuario

A continuación se muestran las configuraciones que puede cambiar en un clúster activo.

### Certificados

Puede agregar nuevos o eliminar fácilmente los certificados del clúster y el cliente a través del portal. Consulte [este documento para obtener instrucciones detalladas](service-fabric-cluster-security-update-certs-azure.md).

![Captura de pantalla que muestra las huellas digitales del certificado en el portal de Azure.][CertificateUpgrade]


### Puertos de aplicación

Puede cambiar los puertos de aplicación cambiando las propiedades del recurso del equilibrador de carga asociado al tipo de nodo. Puede usar el portal o Resource Manager de PowerShell directamente.

Para abrir un nuevo puerto en todas las máquinas virtuales en un tipo de nodo, haga lo siguiente:

1. Agregue un sondeo nuevo al equilibrador de carga adecuado.

    Si implementó el clúster mediante el portal, los equilibradores de carga se llamarán "EC-nombre del grupo de recursos-nombreDelTipoDeNodo" y así sucesivamente, uno para cada tipo de nodo. Como los nombres de los equilibradores de carga son únicos solo dentro de un grupo de recursos, es mejor buscarlos en un determinado grupo.

    ![Captura de pantalla que muestra cómo agregar un sondeo a un equilibrador de carga en el portal.][AddingProbes]

2. Agregue una nueva regla al equilibrador de carga.

    Agregue una nueva regla al mismo equilibrador de carga mediante el sondeo que creó en el paso anterior.

    ![Captura de pantalla que muestra cómo agregar una nueva regla a un equilibrador de carga en el portal.][AddingLBRules]


### Propiedades de colocación

Para cada uno de los tipos de nodo, puede agregar las propiedades de colocación personalizadas que desee usar en sus aplicaciones. NodeType es una propiedad predeterminada que se puede usar sin agregarla explícitamente.

>[AZURE.NOTE] Para obtener más información sobre el uso de las restricciones de selección de ubicación y cómo definirlas, consulte la sección "Placement Constraints and Node Properties" (Restricciones de selección de ubicación y propiedades de nodo) en el documento del Administrador de recursos de clúster de Service Fabric que se muestra en [Describing a service fabric cluster](service-fabric-cluster-resource-manager-cluster-description.md) (Descripción de un clúster de Service Fabric).

### Métricas de capacidad

Para cada uno de los tipos de nodo, puede agregar las métricas de capacidad personalizadas que desee usar en las aplicaciones para la carga de informes. Para obtener más información sobre el uso de las métricas de capacidad para notificar la carga, consulte los documentos del Administrador de recursos de clúster de Service Fabric que se describen en [Describing a service fabric cluster](service-fabric-cluster-resource-manager-cluster-description.md) (Descripción de un clúster de Service Fabric) y [Managing resource consumption and load in Service Fabric with metrics](service-fabric-cluster-resource-manager-metrics.md) (Administración del consumo de recursos y de la carga en Service Fabric con métricas).

### Revisiones de sistema operativo en las máquinas virtuales que componen el clúster

Esta funcionalidad está prevista para el futuro como una característica automatizada. Actualmente, la aplicación de las revisiones a las máquinas virtuales es responsabilidad suya. Debe hacer esto en una máquina virtual cada vez para no inactivar más de una al mismo tiempo.

### Actualizaciones del sistema operativo en las máquinas virtuales que componen el clúster

Si debe actualizar la imagen de sistema operativo en las máquinas virtuales del clúster, debe hacerlo en una máquina virtual cada vez. Usted es responsable de esta actualización: todavía no se ha automatizado esta tarea.

## Pasos siguientes

- Obtenga información sobre cómo [escalar o reducir verticalmente el clúster](service-fabric-cluster-scale-up-down.md).
- Obtenga información sobre [actualizaciones de aplicaciones](service-fabric-application-upgrade.md).

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png

<!---HONumber=AcomDC_0921_2016-->
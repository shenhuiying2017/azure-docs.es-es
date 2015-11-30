<properties
   pageTitle="Actualización de un clúster de Service Fabric | Microsoft Azure"
   description="Actualice él código de Fabric y/o la configuración que ejecuta un clúster de Service Fabric, incluidos la actualización de certificados, la incorporación de puertos de aplicación, las revisiones del sistema operativo, etc. ¿Qué se puede esperar cuando se realizan actualizaciones?"
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
   ms.date="11/03/2015"
   ms.author="chackdan"/>

# Descripción de un clúster de Service Fabric

Un clúster de Service Fabric es un recurso del que es propietario, pero que Microsoft administra parcialmente. En este artículo se describe lo que se administra automáticamente y lo que puede configurar usted mismo.

## Configuración de clústeres que se administran automáticamente

Microsoft mantiene el código de Fabric y la configuración que se ejecuta en un clúster; realizamos actualizaciones automáticas supervisadas en el software según sea necesario. Estas actualizaciones podrían ser de código, configuración o ambos. Para asegurarse de que la aplicación no sufre ningún impacto o solo un impacto mínimo debido a estas actualizaciones, las actualizaciones se realizan en tres fases.

### Fase 1: La actualización se realiza con todas las directivas de mantenimiento de clústeres

Durante esta fase, las actualizaciones se realizan en un dominio de actualización cada vez y las aplicaciones que se ejecutaban en el clúster continúan ejecutándose sin tiempo de inactividad. Las directivas de mantenimiento del clúster (una combinación del estado del nodo y el estado de todas las aplicaciones que se ejecutan en el clúster) se cumplen mientras dura la actualización.

Si no se cumplen las directivas de mantenimiento del clúster, la actualización se revierte, se envía un correo electrónico al propietario de la suscripción que indica que tuvimos que revertir una actualización de clúster, con acciones correctoras si existen y que ejecutaremos la fase 2 en n días. n es una variable. Intentamos ejecutar la misma actualización varias veces más para descartar las actualizaciones que dieron error debido a motivos mínimos y después de los n días desde la fecha en la que se envió el correo electrónico, procedemos a la fase 2.

Si se cumplen las directivas de mantenimiento del clúster, la actualización se considera correcta y se marca como completada. Esto puede ocurrir durante la ejecución inicial de las actualizaciones o cualquiera de las posteriores en esta fase. No hay ningún correo electrónico de confirmación de una ejecución correcta. (Esto es para evitar enviar demasiados mensajes de correo electrónico, recibir un correo electrónico debe considerarse una excepción a la normalidad. Sin embargo, esperamos que la mayoría de las actualizaciones del clúster funcionen sin afectar a la disponibilidad de las aplicaciones).

Para obtener más información sobre cómo establecer las directivas de mantenimiento personalizadas para el clúster, consulte [Actualización del clúster y los parámetros de mantenimiento](service-fabric-cluster-health-parameters.md).

### Fase 2: La actualización se realiza solo con las directivas de mantenimiento predeterminada

Las directivas de mantenimiento se establecen de forma que el número de aplicaciones que tenían un correcto al principio de la actualización siguen siendo el mismo durante el proceso de actualización. Al igual que en la fase 1, durante esta fase, las actualizaciones se realizan en un dominio de actualización cada vez y las aplicaciones que se ejecutaban en el clúster continúan ejecutándose sin tiempo de inactividad. Las directivas de mantenimiento del clúster (una combinación del estado del nodo y el estado de todas las aplicaciones que se ejecutan en el clúster) se cumplen mientras dura la actualización.

Si no se cumplen las directivas de mantenimiento del clúster en vigor, la actualización se revierte, se envía un correo electrónico al propietario de la suscripción que indica que tuvimos que revertir una actualización de clúster, con acciones correctoras si existen y que ejecutaremos la fase 3 en n días. n es una variable.

Intentamos ejecutar la misma actualización unas cuantas veces más para descartar las actualizaciones que dieron error debido a motivos mínimos. Se envía un recordatorio por correo electrónico un par de días antes de llegar a n días. Después de los n días desde la fecha en que se envió el correo electrónico, continuamos con la fase 3. Es necesario tomarse en serio los correos electrónicos que le enviamos en la fase 2 y realizar acciones de corrección.

Si se cumplen las directivas de mantenimiento del clúster, la actualización se considera correcta y se marca como completada. Esto puede ocurrir durante la ejecución inicial de las actualizaciones o cualquiera de las posteriores en esta fase. No hay ningún correo electrónico de confirmación de una ejecución correcta.

### Fase 3: La actualización se realiza con directivas de mantenimiento agresivas

Estas directivas de mantenimiento están orientadas a la finalización de la actualización en lugar de al mantenimiento de las aplicaciones. Muy pocas actualizaciones del clúster terminarán en esta fase. Si el clúster termina en esta fase, es probable que la aplicación no tenga el mantenimiento adecuado o pierda disponibilidad.

Igual que las otras dos fases, las actualizaciones de la fase 3 se realizan en un dominio de actualización cada vez.

Si las directivas de mantenimiento del clúster en vigor no se cumplen, la actualización se revierte, intentamos ejecutar la misma actualización varias veces más para descartar las actualizaciones que dieron error debido a motivos mínimos y después de anclar el clúster, de forma que ya no recibirá soporte técnico ni actualizaciones.

Se enviará un correo electrónico al propietario de la suscripción con esta información y las acciones correctoras. No esperamos que ningún clúster llegue a un estado en el que falle la fase 3.

Si se cumplen las directivas de mantenimiento del clúster, la actualización se considera correcta y se marca como completada. Esto puede ocurrir durante la ejecución inicial de las actualizaciones o cualquiera de las posteriores en esta fase. No hay ningún correo electrónico de confirmación de una ejecución correcta.

## Configuración del clúster controlada por el usuario

A continuación se muestran las configuraciones que puede cambiar en un clúster activo.

### Certificados

Puede actualizar los certificados principales o secundarios fácilmente desde el portal o mediante un comando PUT en el recurso servicefabric.cluster.

![CertificateUpgrade][CertificateUpgrade]

### Puertos de la aplicación

Para hacerlo, cambie las propiedades del recurso de equilibrador de carga asociadas al tipo de nodo. Puede usar el portal o ARM con PowerShell directamente.

Para abrir un nuevo puerto en todas las máquinas virtuales en un tipo de nodo, debe hacer lo siguiente:

1. **Agregue un sondeo nuevo al equilibrador de carga adecuado**

 Si ha implementado el clúster con el portal, el equilibrador de carga se denomina "loadBalancer-0", "loadBalancer-1" y así sucesivamente, uno para cada tipo de nodo. Dado que los nombres de equilibrador de carga solo son únicos en un grupo de recursos, es mejor buscarlos en un determinado grupo.

 ![AddingProbes][addingProbes]

2. **Agregue una nueva regla al equilibrador de carga**

  Agregue una nueva regla al mismo equilibrador de carga mediante el sondeo que creó en el paso anterior.

  ![AddingLBRules][AddingLBRules]

### Propiedades de colocación

  Para cada uno de los tipos de nodos, puede agregar las propiedades de colocación personalizadas que desee usar en sus aplicaciones. NodeType es una propiedad predeterminada que se puede usar sin agregarla explícitamente.

  >[AZURE.NOTE]Para obtener más información sobre el uso de la propiedad de colocación, consulte [la documentación de restricciones de colocación](service-fabric-placement-constraint.md).

### Métricas de capacidad

Para cada uno de los tipos de nodo puede agregar las métricas de capacidad personalizadas que desee usar en las aplicaciones para la carga de informes. Para obtener más información sobre el uso de las métricas de capacidad para cargar informes, consulte [la información general de informes de carga dinámicos](service-fabric-resource-balancer-dynamic-load-reporting.md).

### Aplicación de revisiones del sistema operativo a las máquinas virtuales que componen el clúster
Se trata de una característica que estará disponible pronto. En la actualidad, es responsabilidad suya revisar sus máquinas virtuales; debe hacerlo una máquina virtual cada vez, para que no haya más de una máquina virtual inactiva a la vez.

### Actualización del sistema operativo a uno nuevo en las máquinas virtuales que componen el clúster
Si debe actualizar la imagen de sistema operativo que usa, debe hacerlo una máquina virtual cada vez y esta actualización es responsabilidad suya. Actualmente no está automatizado.


## Pasos siguientes

- Obtenga información acerca de cómo [escalar o reducir verticalmente el clúster](service-fabric-cluster-scale-up-down.md)
- Obtenga información acerca de las [actualizaciones de las aplicaciones](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes.png
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png

<!---HONumber=Nov15_HO4-->
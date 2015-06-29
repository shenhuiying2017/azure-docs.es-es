<properties
   pageTitle="Ejecución de un runbook en Automatización de Azure"
   description="Describe los detalles de cómo se procesa un runbook en Automatización de Azure."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/28/2015"
   ms.author="bwren" />

# Ejecución de un runbook en Automatización de Azure


Cuando se inicia un runbook en Automatización de Azure, se crea un trabajo. Un trabajo es una instancia única de ejecución de un runbook. Un trabajador de Automatización de Azure está asignado para ejecutar cada trabajo. Aunque los trabajadores se comparten por varias cuentas de Azure, los trabajos de diferentes cuentas de Automatización están aislados entre sí. No tiene el control sobre qué trabajador prestará servicio a la solicitud para el trabajo. Un único runbook puede tener varios trabajos que se ejecutan al mismo tiempo. Al ver la lista de runbooks en el portal de Azure, se mostrará el estado del última trabajo iniciado por cada runbook. Puede ver la lista de trabajos para cada runbook para hacer un seguimiento del estado de cada uno. Para obtener una descripción de los distintos estados de trabajo, consulte [ Estados del trabajo](automation-viewing-the-status-of-a-runbook-job.md#job-statuses).

![Estados del trabajo](./media/automation-runbook-execution/job-statuses.png)


Los trabajos tendrán acceso a los recursos de Azure mediante una conexión a la suscripción de Azure. Solo tendrán acceso a los recursos de su centro de datos si estos recursos están accesibles desde la nube pública.

## Permisos

Los runbooks de Automatización de Azure normalmente requieren acceso a los recursos en su suscripción de Azure. [Autenticación en Azure mediante Azure Active Directory](http://aka.ms/runbookauthor/authentication) describe cómo configurar Azure Active Directory y una [credencial]() de Automatización de Azure para autenticarse con recursos de Azure. En este tema también incluye información sobre el uso del [cmdlet Add-AzureAccount](http://aka.ms/runbookauthor/azurecmdlets/addazureaccount) para acceder a los recursos de Azure en los runbooks creados. Consulte en la documentación de los runbooks individuales que ha importado sus requisitos de seguridad.

## Equitativamente

Para compartir recursos entre todos los runbooks en la nube, Automatización de Azure descargará temporalmente cualquier trabajo cuando haya estado ejecutándose durante 3 horas y lo volverá a iniciar desde su último [punto de control](http://aka.ms/runbookauthor/checkpoints). Durante este tiempo, el trabajo mostrará un estado En ejecución, Esperando recursos. Si el runbook no tiene puntos de control o el trabajo no había alcanzado el primer punto de control antes de la descarga, se reiniciará desde el principio.

Si el runbook se reinicia desde el mismo punto de control o desde el principio del runbook tres veces consecutivas, terminará con un estado Error, esperando recursos. De esta forma se impide que los runbooks se ejecuten de manera indefinida sin completarse, ya que no pueden llegar al siguiente punto de control sin que se descarguen de nuevo. En este caso, recibirá la siguiente excepción con el error.

El trabajo no puede continuar ejecutándose porque se expulsó repetidamente del mismo punto de control. Asegúrese de que el runbook no realiza operaciones largas sin conservar su estado.

Cuando se crea un runbook, debe asegurarse de que el tiempo para ejecutar las actividades entre dos puntos de control no supere las 3 horas. Puede que necesite agregar puntos de control a un runbook para asegurarse de que no alcanza este límite de 3 horas. También tendrá que dividir las operaciones de ejecución prolongada. Por ejemplo, su runbook podría realizar una reindexación en una gran base de datos SQL. Si esta operación no se completa dentro del límite de distribución equilibrada, el trabajo se descargará y se reiniciará desde el principio. En este caso, debe dividir la operación de reindexación en varios pasos, como volver a indexar una tabla a la vez y, a continuación, inserte un punto de control después de cada operación, de modo que el trabajo se pueda reanudar después de la última operación para completar.

## Artículos relacionados

- [Inicio de un runbook en Automatización de Azure](automation-starting-a-runbook)
- [Visualización del estado de un trabajo de runbook en Automatización de Azure](automation-viewing-the-status-of-a-runbook-job)
 

<!---HONumber=58_postMigration-->
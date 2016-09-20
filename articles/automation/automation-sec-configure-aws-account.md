<properties
   pageTitle="Configuración de la autenticación con Amazon Web Services | Microsoft Azure"
   description="En este artículo se describe cómo crear y validar una credencial de AWS para los Runbooks en Automatización de Azure y administrar los recursos de AWS."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="autenticación AWS, configurar aws"/>
<tags
   ms.service="automation"
   ms.workload="tbd"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.date="09/12/2016"
   ms.author="magoedte"/>

# Autenticación de Runbooks con Amazon Web Services
La automatización de las tareas comunes con recursos de Amazon Web Services (AWS) se puede realizar con los Runbooks de Automatización de Azure. Puede automatizar muchas tareas en AWS mediante Runbooks de Automatización exactamente igual que con recursos de Azure. Todo lo que se necesita son dos cosas:

* Una suscripción a AWS y un conjunto de credenciales. En concreto, la clave de acceso y la clave secreta de AWS. Para más información, consulte el artículo [Using AWS Credentials](http://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html) (Uso de credenciales de AWS).
* Una suscripción a Azure y una cuenta de Automatización. Para más información sobre cómo configurar una cuenta de Automatización de Azure, consulte el artículo [Autenticación de Runbooks con una cuenta de ejecución de Azure](../automation/automation-sec-configure-azure-runas-account.md).

Para realizar la autenticación con AWS, debe especificar un conjunto de credenciales de AWS para autenticar los Runbooks que se ejecutan desde Automatización de Azure. Si ya tiene creada una cuenta de Automatización y quiere usarla para autenticarse con AWS, puede seguir los pasos que se describen en la sección siguiente. Si desea dedicar una cuenta a runbooks dirigidos a recursos de AWS, primero debe crear una nueva [cuenta de ejecución de Automatización](../automation/automation-sec-configure-azure-runas-account.md) (omita la opción para crear una entidad de servicio) y, luego, seguir los pasos que se indican a continuación.

## Configuración de la cuenta de Automatización
Para que Automatización de Azure se comunique con AWS, primero deberá recuperar las credenciales de AWS y almacenarlas como recursos en Automatización de Azure. Realice los siguientes pasos que se describen en el documento de AWS [Managing Access Keys for your AWS Account](http://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) (Administración de las claves de acceso para la cuenta de AWS) y copie el **id. de clave de acceso** y la **clave de acceso secreta** (también puede descargar el archivo de clave para almacenarlo en un lugar seguro).

Después de crear y copiar las claves de seguridad de AWS, deberá crear un recurso de credencial con una cuenta de Automatización de Azure para almacenarlas de forma segura y hacer referencia a ellas con sus Runbooks. Siga los pasos que se describen en la sección **Creación de credenciales** en el artículo [Recursos de credenciales en Automatización de Azure] (../automation/automation-certificates.md/###Para crear credenciales con el Portal de Azure) y escriba la siguiente información:

1. En el cuadro **Nombre**, escriba **AWScred** o un valor adecuado que siga las normas de nomenclatura.
2. Escriba su **id. de acceso** en el cuadro **Nombre de usuario**, y su **clave de acceso secreta** en los cuadros **Contraseña** y **Confirmar contraseña**.

## Pasos siguientes

- Revise el artículo [Solución de Automatización de Azure: aprovisionamiento de una máquina virtual de AWS](../automation/automation-scenario-aws-deployment.md) para aprender a crear runbooks para automatizar las tareas en AWS.

<!---HONumber=AcomDC_0914_2016-->
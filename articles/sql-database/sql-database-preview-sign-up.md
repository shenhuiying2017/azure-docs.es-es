<properties 
	pageTitle="Tutorial: Activación de la versión más reciente de SQL Database Update V12 (vista previa)" 
	description="Describe los pasos para probar la versión de vista previa de Base de datos SQL de Azure V12 mediante el nuevo portal de interfaz de usuario de Azure." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jhubbard, jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/29/2015" 
	ms.author="genemi"/>


# Tutorial: Activación de la versión más reciente de SQL Database Update V12 (vista previa)

En este tema, se describen los pasos que puede seguir para activar la opción de Azure SQL Database Update V12 (vista previa), tal y como la publicó Microsoft en diciembre de 2014.

Para probar la versión de vista previa más reciente de V12, primero tiene que suscribirse a Microsoft Azure o, al menos, a una suscripción de [prueba gratuita](http://azure.microsoft.com/pricing/free-trial/).

Puede activar la versión de vista previa de V12 utilizando el nuevo portal de administración vista previa de Microsoft Azure, en [http://portal.azure.com/](http://portal.azure.com/). Después de activar la versión de vista previa de V12 para la suscripción, las opciones de creación y actualización de la versión de vista previa de V12 se desbloquearán en el portal de Azure. A continuación, los usuarios pueden iniciar el flujo de trabajo de [creación](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/) o [actualización](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/) desde la hoja del servidor o la de la base de datos.

> [AZURE.NOTE]
> Las bases de datos de prueba, copias de la base de datos o bases de datos nuevas son buenas candidatas para la actualización a la vista previa. Las bases de datos de producción de las que depende su negocio deben esperar hasta después del período de la versión de vista previa.

Para obtener más información sobre la versión de vista previa, consulte [Planeación y preparación para actualizar a Base de datos SQL de Azure V12 (vista previa)](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/).


## A. Autorización de seguridad

El primer paso es asegurarse de que tiene autorización suficiente para activar la versión de vista previa de V12 para su suscripción. Al intentar activar la opción de versión de vista previa de V12, se realiza una comprobación de autorización para comprobar que tiene autoridad suficiente dentro de la suscripción.

 Para probar la versión de vista previa, debe tener una de las autorizaciones siguientes:

- El propietario de la suscripción
- Un coadministrador de la suscripción

Para obtener más información acerca de las cuentas de Azure, consulte [Administrar cuentas, suscripciones y roles administrativos](http://msdn.microsoft.com/library/hh531793.aspx).

## B. Pasos en la interfaz de usuario del portal

Esta sección describe una secuencia de clics que puede seguir una vez que esté en la interfaz de usuario del portal de Azure para activar la opción de versión de vista previa de V12. Después de activar la opción, permanecerá disponible a partir de entonces.

Todos los escenarios de activación siguen la misma idea básica. Cuando trate de crear [por primera vez un nuevo servidor de base de datos SQL](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/), se mostrará una hoja denominada **Última actualización (vista previa)** que incluye una casilla que se puede seleccionar para activar el privilegio de usar la versión de vista previa de V12. Después de activar el privilegio, no se volverá a mostrar la casilla de nuevo. En su lugar, verá un control **Sí|No** que puede utilizar para especificar si desea que el nuevo servidor use la versión de vista previa de V12. Si elige **No**, se crea un servidor de V11 (tal y como indica SELECT @@VERSION;).

### B.1 Control Sí|No para la versión de vista previa de V12

Después de activar el privilegio de la versión de vista previa de V12, verá el control **Sí|No**, que se muestra rodeado con un círculo en la siguiente captura de pantalla del portal.

![YesNoOptionForTheV12Preview][Image1]


## C. Pasos siguientes

A continuación, en los siguientes temas se explican las distintas formas de utilizar la versión de vista previa de V12.

- [Creación de una base de datos en la versión más reciente de SQL Database Update V12 (vista previa)](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/)
- [Actualización a la versión más reciente de SQL Database Update V12 (vista previa)](http://azure.microsoft.com/documentation/articles/sql-database-preview-upgrade/)

> [AZURE.NOTE]
> Las bases de datos de prueba, copias de la base de datos o bases de datos nuevas son buenas candidatas para la actualización a la vista previa. Las bases de datos de producción de las que depende su negocio deben esperar hasta después del período de la versión de vista previa.


<!-- References, Images. -->
[Image1]: ./media/sql-database-preview-sign-up/V12Preview-YesNo-Option-New-SQLDatabase-Server-Newserver-Screenshot-e23.png


<!-- EOF -->

<!--HONumber=47-->
 
<properties
   pageTitle="Tutorial 1 de DotNet Core para máquinas virtuales de Azure | Microsoft Azure"
   description="Tutorial de DotNet Core para máquinas virtuales de Azure"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/21/2016"
   ms.author="nepeters"/>

# Automatización de implementaciones de aplicaciones en Azure Virtual Machines

Esta serie de cuatro partes detalla la implementación y configuración de recursos y aplicaciones de Azure mediante plantillas de Azure Resource Manager. En esta serie, se implementa una plantilla de ejemplo y se examina la plantilla de implementación. El objetivo de esta serie es describir la relación entre los recursos de Azure y proporcionar experiencia práctica de implementación de plantillas de Azure Resource Manager completamente integradas. En este documento se da por hecho que el usuario posee un nivel básico de conocimientos de Azure Resource Manager, por lo que, antes de iniciar este tutorial, debe familiarizarse con los conceptos de Azure Resource Manager.

## Aplicación Music Store

El ejemplo utilizado en esta serie es una aplicación .Net Core que simula una experiencia de compra en Music Store. Esta aplicación se puede implementar en un sistema virtual Linux o Windows; para ambos sistemas se han creado implementaciones de ejemplo. La aplicación incluye una aplicación web y una base de datos SQL. Antes de leer los artículos de esta serie, implemente la aplicación con el botón de implementación que se encuentra en esta página. Una vez completada la implementación, la arquitectura de la aplicación/Azure se asemejará al siguiente diagrama.

La plantilla Music Store de Resource Manager puede encontrarse aquí: [Music Store Linux Template](https://github.com/neilpeterson/nepeters-azure-templates/tree/master/dotnet-core-music-linux-vm-sql-db) (plantilla de Linux de Music Store).

![Aplicación Music Store](./media/virtual-machines-linux-dotnet-core/music-store.png)

En los siguientes cuatro artículos se analiza cada uno de estos componentes, incluida la plantilla JSON asociada.

- [**Arquitectura de la aplicación**](./virtual-machines-linux-dotnet-core-2-architecture.md): los componentes de la aplicación, como los sitios web y las bases de datos, deben hospedarse en recursos informáticos de Azure, como máquinas virtuales y bases de datos SQL de Azure. Este documento le guía a través de la asignación de requisitos de procesos a recursos de Azure y de la implementación de estos recursos con una plantilla de Azure Resource Manager.

- [**Acceso y seguridad**](./virtual-machines-linux-dotnet-core-3-access-security.md): cuando se hospedan aplicaciones en Azure, es necesario tener en cuenta cómo se accede a la aplicación y cómo acceden entre sí los distintos componentes de la aplicación. En este documento se describe cómo se proporciona y se protege el acceso a Internet de una aplicación y el acceso entre componentes de la aplicación.

- [**Disponibilidad y escala**](./virtual-machines-linux-dotnet-core-4-avalibility-scale.md): los términos disponibilidad y escala hacen referencia a la capacidad de las aplicaciones de mantenerse en ejecución durante períodos de inactividad de la infraestructura, así como a la capacidad de escalar recursos de proceso para satisfacer la demanda de la aplicación. Este documento detalla los componentes necesarios para implementar una carga equilibrada y una aplicación de alta disponibilidad.

- [**Implementación de aplicaciones**](./virtual-machines-linux-dotnet-core-5-app-deployment.md): al implementar aplicaciones en Azure Virtual Machines, el método por el que se instalan los archivos binarios de la aplicación en la máquina virtual debe tenerse en cuenta. En este documento se explica cómo automatizar la instalación de aplicaciones mediante extensiones de script personalizadas para máquinas virtuales de Azure.

El objetivo al desarrollar plantillas de Azure Resource Manager es automatizar la implementación de la infraestructura de Azure, así como la instalación y configuración de las aplicaciones hospedadas en esta infraestructura. En estos artículos se proporciona un ejemplo de esta experiencia.

## Implementación de la aplicación Music Store

La aplicación Music Store puede implementarse con este botón.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fdotnet-core-sample-templates%2Fmaster%2Fdotnet-core-music-linux%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

La plantilla de Azure Resource Manager requiere los siguientes valores de parámetro.

|Nombre de parámetro |Description |
|---|---|
|SSHKEYDATA | Datos de clave SSH utilizados para proteger el acceso a la máquina virtual. Para más información acerca de cómo crear un par de claves SSH, consulte [Creación de claves SSH en Linux y Mac para máquinas virtuales Linux en Azure](./ virtual-machines-linux-mac-create-ssh-keys.md). |
|ADMINUSERNAME | Nombre de usuario de administrador que se utiliza en la máquina virtual y Azure SQL Database. |
|SQLADMINPASSWORD | Contraseña que se utiliza en Azure SQL Database. |
|NUMBEROFINSTANCES | Número de máquinas virtuales que se van a crear. Cada una de estas máquinas virtuales hospeda la aplicación web Music Store y se equilibra la carga de todo el tráfico entre ellas. |
|PUBLICIPADDRESSDNSNAME | Nombre DNS único global asociado a la dirección IP pública. |

Cuando haya finalizado la implementación de la plantilla, vaya a la dirección IP pública con cualquier explorador de Internet. Se mostrará el sitio .Net Core de Music.

## Pasos siguientes

<hr>

[Paso 1: arquitectura de aplicaciones con plantillas de Azure Resource Manager](./virtual-machines-linux-dotnet-core-2-architecture.md)

[Paso 2: acceso y seguridad en plantillas de Azure Resource Manager](./virtual-machines-linux-dotnet-core-3-access-security.md)

[Paso 3: disponibilidad y escala en plantillas de Azure Resource Manager](./virtual-machines-linux-dotnet-core-4-avalibility-scale.md)

[Paso 4: implementación de aplicaciones con plantillas de Azure Resource Manager](./virtual-machines-linux-dotnet-core-5-app-deployment.md)

<!---HONumber=AcomDC_0928_2016-->
<properties
   pageTitle="Red Hat Update Infrastructure (RHUI) | Microsoft Azure"
   description="Obtenga información acerca de Red Hat Update Infrastructure (RHUI) para instancias de Red Hat Enterprise Linux a petición de Microsoft Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="BorisB2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="07/11/2016"
   ms.author="borisb"/>

# Red Hat Update Infrastructure (RHUI) para máquinas virtuales Red Hat Enterprise Linux a petición en Azure

Las máquinas virtuales creadas a partir de las imágenes a petición de Red Hat Enterprise Linux (RHEL) en Azure Marketplace se registran para acceder a la instancia de Red Hat Update Infrastructure (RHUI) implementada en Azure. Las instancias RHEL a petición tendrán acceso a un repositorio yum regional y podrán recibir las actualizaciones incrementales.

La lista del repositorio yum, que se administra mediante RHUI, se configura en la instancia de RHEL durante el aprovisionamiento. Por ello, no es necesario realizar ninguna configuración adicional: solo tiene que ejecutar `yum update` una vez que la instancia de RHEL se esté ejecutando para obtener las últimas actualizaciones.

## Introducción sobre RHUI
[Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) ofrece una solución altamente escalable para administrar el contenido del repositorio yum para las instancias en la nube de Red Hat Enterprise Linux hospedadas por proveedores de nube con certificación de Red Hat. Basado en el proyecto Pulp ascendente, RHUI permite a los proveedores de nube reflejar localmente el contenido del repositorio hospedado en Red Hat, crear repositorios personalizados con su propio contenido y poner los repositorios a disposición de un grupo grande de usuarios finales a través de un sistema de entrega de contenido de carga equilibrada.

## Regiones donde se implementa RHUI
RHUI está disponible en todas las regiones donde estén disponibles las imágenes a petición RHEL. Actualmente incluye todas las regiones públicas enumeradas en la página [Panel de estado Azure](https://azure.microsoft.com/status/). En el precio se incluye el acceso de RHUI a las máquinas virtuales aprovisionadas desde imágenes RHEL a petición. La disponibilidad de la nube regional o nacional se actualizará cuando se expanda la disponibilidad RHEL a petición en el futuro.

> [AZURE.NOTE] El acceso a RHUI hospedada en Azure está limitado a las máquinas virtuales dentro de [intervalos IP de centro de datos de Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

## Obtención de actualizaciones de otro repositorio de actualización

Si necesita obtener actualizaciones desde un repositorio de actualización diferente (en lugar de una RHUI hospedada en Azure) debe anular el registro de las instancias de RHUI y volver a registrarlas con la infraestructura de actualización deseada (por ejemplo, Red Hat Satellite o Red Hat Customer Portal CDN). Necesitará suscripciones apropiadas a Red Hat para estos servicios y un registro para [acceso a la nube de Red Hat en Azure](https://access.redhat.com/ecosystem/partners/ccsp/microsoft-azure).

Para anular el registro de RHUI y volver a registrarse en su infraestructura de actualización, siga estos pasos.

1.	Edite /etc/yum.repos.d/rh-cloud.repo y cambie todas las apariciones de `enabled=1` por `enabled=0`. Por ejemplo:

        # sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo

2.	Edite /etc/yum/pluginconf.d/rhnplugin.conf y cambie `enabled=0` por `enabled=1`.
3.	A continuación, registre la infraestructura deseada, como el portal del cliente de Red Hat. Siga la guía de soluciones en Red Hat en [How to register and subscribe a system to the Red Hat Customer Portal](https://access.redhat.com/solutions/253273) (Cómo registrar y suscribirse a un sistema en el Portal de cliente de Red Hat).

> [AZURE.NOTE] El acceso a RHUI hospedado en Azure se incluye en el precio de la imagen de pago por uso RHEL. Anular el registro de una máquina virtual RHEL de pago por uso desde la RHUI hospedada de Azure no convierte a la máquina virtual en una máquina virtual de tipo traiga su propia-licencia (BYOL) y, por tanto, se pueden incurrir en cargos dobles si se registra la misma máquina virtual con otro origen de actualizaciones.
> 
> Si necesita utilizar coherentemente una infraestructura de actualizaciones que no sea una RHUI hospedada en Azure, considere la posibilidad de crear e implementar sus propias imágenes (de tipo BYO), tal como se describe en el artículo sobre [creación y carga de una máquina virtual basada en Red Hat para Azure](virtual-machines-linux-redhat-create-upload-vhd.md).

## Pasos siguientes
Para crear una máquina virtual Linux Red Hat Enterprise desde la imagen de pago por uso de Azure Marketplace y aprovechar RHUI hospedado de Azure, vaya a [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/). Podrá utilizar `yum update` en su instancia RHEL sin configuración adicional.

<!---HONumber=AcomDC_0713_2016-->
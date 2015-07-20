<properties 
	pageTitle="Implementación entre sitios de VMWare locales" 
	description="InMage Scout en Azure Site Recovery controla la replicación, conmutación por error y recuperación entre sitios de VMWare locales." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/29/2015" 
	ms.author="raynew"/>


# Implementación entre sitios de VMWare locales


##Información general

InMage Scout en Azure Site Recovery proporciona características de replicación en tiempo real entre los sitios locales de VMWare. InMage Scout se incluye en las suscripciones al servicio Azure Site Recovery.


## Requisitos previos

- **Cuenta de Azure**: necesitará una cuenta de [Microsoft Azure](http://azure.microsoft.com/). Puede comenzar con una [evaluación gratuita](pricing/free-trial/).


##Paso 1; Creación de un almacén y descarga de InMage

1. Inicie sesión en el [Portal de administración](https://portal.azure.com).
2. Haga clic en **Servicios de datos** > **Servicios de recuperación** y **Almacén de Site Recovery**.
3. Haga clic en **Crear nuevo** > **Creación rápida**.
4. En **Nombre**, escriba un nombre descriptivo para identificar el almacén.
5. En **Región**, seleccione la región geográfica del almacén. Para comprobar las regiones admitidas, consulte Disponibilidad geográfica en [Detalles de precios de Azure Site Recovery](pricing/details/site-recovery/).

<P>Compruebe la barra de estado para confirmar que el almacén se ha creado correctamente. El almacén aparecerá como <b>Activo</b> en la página principal de Servicios de recuperación.</P>

##Paso 2: Configuración del almacén

1. Haga clic en **Crear almacén**.
2. En la página **Servicios de recuperación**, haga clic en el almacén para abrir la página Inicio rápido.
3. En la lista desplegable, seleccione **Entre dos sitios locales de VMWare**.
4. Descargue a InMage Scout.
5. Configure la replicación entre dos sitios de VMWare mediante la documentación de InMage Scout que se descarga con el producto.


##Pasos siguientes

Publique cualquier pregunta en el [Foro de servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).<

<!---HONumber=July15_HO2-->
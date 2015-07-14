<properties 
	pageTitle="Tutorial: Configuración de la protección entre sitios de VMWare locales" 
	description="InMage en Azure Site Recovery controla características de replicación, conmutación por error y recuperación entre sitios de VMWare locales." 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="raynew"/>

# Tutorial: Configuración de la protección entre sitios de VMWare locales


<h2><a id="overview" name="overview" href="#overview"></a>Información general</h2>

<p>InMage en Azure Site Recovery proporciona características de replicación en tiempo real entre los sitios locales de VMWare. InMage se incluye en las suscripciones al servicio Azure Site Recovery.</p>




<h2><a id="before" name="before" href="#before"></a>Requisitos previos</h2>
<div class="dev-callout"> 

<UL>
<LI><b>Cuenta de Azure</b>: necesitará una cuenta de Azure. Si no dispone de una, consulte <a href="http://aka.ms/try-azure">Evaluación gratuita de Azure</a>. Puede obtener información sobre precios de suscripción en <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Detalles de precios de Azure Site Recovery Manager</a>.</LI>
</UL>


<h2><a id="tutorial" name="tutorial" href="#tutorial"></a>Pasos del tutorial</h2>
<a name="vault"></a> <h3>Paso 1: Crear un almacén y descargar InMage</h3>

1. Inicie sesión en el [Portal de administración](https://manage.windowsazure.com).


2. Expanda <b>Servicios de datos</b>, luego <b>Servicios de recuperación</b> y haga clic en <b>Almacén de Site Recovery</b>.


3. Haga clic en <b>Crear nuevo</b> y luego en <b>Creación rápida</b>.
	
4. En <b>Name</b>, escriba un nombre descriptivo para identificar el almacén.

5. En <b>Región</b>, seleccione la región geográfica del almacén. Para comprobar las regiones admitidas, consulte Disponibilidad geográfica en <a href="http://go.microsoft.com/fwlink/?LinkId=389880">Detalles de precios de Azure Site Recovery</a>

6. Haga clic en <b>Crear almacén</b>. 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SRSAN_HvVault.png)

<P>Compruebe la barra de estado para confirmar que el almacén se ha creado correctamente. El almacén aparecerá como <b>Activo</b> en la página principal de Servicios de recuperación.</P>

<a name="upload"></a> <h3>Paso 2: Configuración del almacén</h3>


1. En la página <b>Servicios de recuperación</b>, haga clic en el almacén para abrir la página de inicio rápido. El inicio rápido también se puede abrir en cualquier momento mediante el icono.

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SRSAN_QuickStartIcon.png)

2. En la lista desplegable, seleccione **Entre dos sitios locales de VMWare**.
3. Descargue a InMage Scout.
	
	![VMWare](./media/hyper-v-recovery-manager-configure-vault/SRVMWare_SelectScenario.png)

4. Configure la replicación entre dos sitios de VMWare mediante la documentación de InMage que se descarga con el producto.


<h2><a id="next" name="next" href="#next"></a>Pasos siguientes</h2>
<UL>

<LI>Si tiene preguntas, visite el <a href="http://go.microsoft.com/fwlink/?LinkId=313628">foro de Servicios de recuperación de Azure</a>.</LI> 
</UL>

<!--HONumber=49-->
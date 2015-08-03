<properties 
	pageTitle="Guía para desarrolladores de Azure Government" 
	description="Esto proporciona una comparación de funciones e instrucciones sobre cómo desarrollar aplicaciones para la administración de Azure" 
	services="" 
	documentationCenter="" 
	authors="Joharve2" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="azure-government" 
	ms.date="01/21/2014" 
	ms.author="jharve"/>


#  Guía para desarrolladores de Microsoft Azure Government 

<p> Microsoft Azure Government es una instancia aislada físicamente y en la red de Microsoft Azure. Esta guía para desarrolladores ofrece detalles sobre las diferencias con las que los desarrolladores de aplicaciones y administradores se encontrarían al interactuar y trabajar con estas regiones independientes de Azure.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## En este tema


+ [Información general](#Overview)
+ [Instrucciones para desarrolladores](#Guidance)
+ [Características disponibles actualmente en Microsoft Azure Government](#Features)
+ [Asignación de extremos](#Endpoint)
+ [Pasos siguientes](#next)


## <a name="Overview"></a>Información general

Microsoft Azure Government es una instancia independiente del servicio de Microsoft Azure que aborda las necesidades de seguridad y cumplimiento de las agencias federales, los gobiernos locales y estatales y sus proveedores de soluciones de los Estados Unidos. Azure Government ofrece un aislamiento físico y de red de las implementaciones externas al gobierno de los Estados Unidos y protege al personal de los Estados Unidos.

Microsoft proporciona varias herramientas para crear e implementar aplicaciones en la nube en el servicio de Microsoft Azure global de Microsoft (servicio "Global") y los servicios de Microsoft Azure Government.

Al crear e implementar aplicaciones en los servicios de Azure Government, en lugar del servicio Global, los desarrolladores necesitan conocer las principales diferencias existentes entre los dos servicios. Específicamente en torno a instalar y configurar su entorno de programación, configurar extremos, la escritura de aplicaciones y su implementación como servicios en Azure Government.

La información de este documento resume esas diferencias y complementa la información disponible en el sitio de [Azure Government](http://www.azure.com/gov "Azure Government") y la [Biblioteca de documentación técnica de Microsoft Azure](http://msdn.microsoft.com/cloud-app-development-msdn "MSDN") en MSDN. La información oficial también puede estar disponible en muchas otras ubicaciones, como el [Centro de confianza de Microsoft Azure](http://azure.microsoft.com/support/trust-center/ "Centro de confianza de Microsoft Azure"), el [Centro de documentación de Azure](http://azure.microsoft.com/documentation/) y los [blogs de Azure](http://azure.microsoft.com/blog/ "Blogs de Azure").

Este contenido está destinado a socios y desarrolladores que implementan Microsoft Azure Government.



## <a name="Guidance"></a>Instrucciones para desarrolladores
Dado que la mayoría del contenido técnico que está disponible actualmente da por supuesto que las aplicaciones se están desarrollando para el servicio Global en lugar de Microsoft Azure Government, es importante asegurarse de que los desarrolladores sean conscientes de las diferencias principales para las aplicaciones desarrolladas para ser hospedadas en Azure Government.

- En primer lugar, hay servicios y características diferentes. Esto significa que ciertas características que se encuentran en regiones específicas del servicio Global pueden no estar disponibles en Azure Government.

- En segundo lugar, para las características que se ofrecen en Azure Government, hay diferencias de configuración desde el servicio Global. Por lo tanto, debe revisar el código de ejemplo, las configuraciones y los pasos asegurarse de que genera y ejecuta dentro del entorno de servicios en la nube de Azure Government.


## <a name="Features"></a> Características disponibles actualmente en Microsoft Azure Government
Azure Government actualmente tiene los siguientes servicios disponibles en las regiones US GOV IOWA y US GOV VIRGINIA:

- Máquinas virtuales
- Servicios en la nube
- Almacenamiento
- Active Directory
- Programador
- Redes virtuales
- Base de datos SQL

Hay otros servicios disponibles y se agregarán más servicios de forma continua. Para obtener la lista más reciente de servicios, consulte la [página de regiones](http://azure.microsoft.com/regions/#services), en la que se resaltan todas las regiones disponibles y sus servicios.

Actualmente, US GOV Iowa y US GOV Virginia son los centros de datos compatibles con Azure Government. Consulte la página de regiones mencionada más arriba para ver los centros de datos y los servicios disponibles actualmente.

## <a name="Endpoint"></a>Asignación de extremos

Utilice la siguiente tabla como guía al asignar extremos públicos de Microsoft Azure y Base de datos SQL a extremos específicos de Azure Government.

<table>
<tr style='font-weight:bold'><td>
Tipo de servicio</td><td>	Azure Public</td><td>	Azure Government
</td></tr><tr><td>
Página principal de Azure Government</td><td>	windowsazure.com	</td><td>microsoftazure.us
</td></tr><tr><td>
Portal de administración</td><td>	manage.windowsazure.com</td><td>	manage.windowsazure.us
</td></tr><tr><td>
General</td><td>	*.windows.net	</td><td>*.usgovcloudapi.net
</td></tr><tr><td>
Núcleo	</td><td>*.core.windows.net	</td><td>*.core.usgovcloudapi.net
</td></tr><tr><td>
Proceso	</td><td>*.cloudapp.net	</td><td>*.usgovcloudapp.net
</td></tr><tr><td>
Almacenamiento de blobs</td><td>	*.blob.core.windows.net</td><td>	*.blob.core.usgovcloudapi.net
</td></tr><tr><td>
Almacenamiento de colas	</td><td>*.queue.core.windows.net</td><td>	*.queue.core.usgovcloudapi.net
</td></tr><tr><td>
Almacenamiento de tablas</td><td>	*.table.core.windows.net	</td><td>*.table.core.usgovcloudapi.net
</td></tr><tr><td>
Administración de servicios</td><td>	management.core.windows.net</td><td>	management.core.usgovcloudapi.net

</td></tr>
<tr><td>Base de datos SQL</td><td>	*.database.windows.net	</td><td>*.database.usgovcloudapi.net</td></tr>
</table>

## <a name="next"></a>Pasos siguientes
Si desea obtener más información sobre Azure Government y sobre cómo su organización puede calificar para tener acceso, vaya a <A href="http://azure.com/gov">http://www.azure.com/gov</a>

<!--Anchors-->



<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md

<!---HONumber=July15_HO4-->
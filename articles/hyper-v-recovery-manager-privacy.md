<properties 
	pageTitle="Información de privacidad de Azure Site Recovery" 
	description="Describe información adicional para Azure Site Recovery" 
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
	ms.date="03/19/2015" 
	ms.author="raynew"/>

# Información de privacidad de Azure Site Recovery

Esta tabla proporciona información adicional de privacidad para el servicio de Microsoft Azure Site Recovery ("el Servicio"). Para consultar la declaración de privacidad de los servicios de Microsoft Azure, consulte
[Declaración de privacidad de Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=324899)

<table>
<thead>
<tr>
	<th>Característica</th>
	<th>Qué hace</th>
	<th>Información recopilada</th>
	<th>Uso</th>
	<th>Elección</th>
</tr>
</thead>
<tbody>
<tr>
	<td><p><b>Registro</b></p></td>
	<td><p>Registrar el servidor con el servicio para proteger las máquinas virtuales</p></td>
	<td><p>Después de registrar un servicio, el Servicio recopila el proceso y transmite esta información:</p>
		<ul>
			<li>Información de certificado de administración del servidor VMM designado para proporcionar recuperación ante desastres mediante el nombre del Servicio del servidor VMM</li>
			<li>Nombre de nubes de máquinas virtuales en el servidor VMM</li>
		</ul>
</td>
	<td><p>El Servicio utiliza la información anterior del modo siguiente:</p>
		<ul>
			<li>Certificado de administración: se utiliza para ayudar a identificar y autenticar el servidor VMM registrado para el acceso al Servicio. El servicio utiliza la parte de clave pública del certificado para proteger un token al que solo puede obtener acceso el servidor VMM registrado. El servidor debe utilizar este token para obtener acceso a las características del Servicio.</li>
			<li>Nombre del servidor VMM: el nombre del servidor VMM es necesario para identificar y comunicarse con el servidor VMM adecuado en donde se ubican las nubes. 
			</li>
			<li>Nombres de nubes del servidor VMM: el nombre de nube es obligatorio cuando se utiliza la característica de emparejar o desemparejar las nubes del servicio descrita a continuación. Si decide emparejar la nube de un centro de datos principal con otra nube en el centro de datos de recuperación, se muestran los nombres de todas las nubes del centro de datos de recuperación.</li>
		</ul>
</td>
	<td><p>Esta información es una parte esencial del proceso de registro del servicio, ya que ayuda al usuario y al servicio a identificar el servidor VMM para el que desea proporcionar protección de Azure Site Recovery, así como para identificar el servidor VMM registrado correcto. Si no desea enviar esta información al servicio, no lo utilice. Si registra el servidor y más tarde desea anular su registro, puede hacerlo eliminando la información del servidor VMM en el portal del Servicio (que es el Portal de Azure).</p></td>
</tr>

<tr>
	<td><p><b>Habilitación de la protección de Azure Site Recovery</b></p></td>
	<td><p>El proveedor de Azure Site Recovery instalado en el servidor VMM es el conducto para comunicarse con el Servicio. El proveedor es una biblioteca de vínculos dinámicos (DLL) que se hospeda en el proceso VMM. Después de instalar el proveedor, se habilita la característica "Datacenter Recovery" en la consola de administrador de VMM. Las máquinas virtuales nuevas o existentes en una nube pueden habilitar una propiedad denominada "Datacenter Recovery" para ayudar a proteger la máquina virtual. Una vez que se establece esta propiedad, el proveedor envía el nombre y el identificador de la máquina virtual al Servicio. La protección virtual está habilitada mediante tecnología de replicación de Windows Server 2012 o de Windows Server 2012 R2 Hyper-V. Los datos de la máquina virtual se replican de un host de Hyper-V a otro (normalmente se encuentran en un centro de datos de "recuperación" diferente).</p></td>
	<td><p>El Servicio recopila, procesa y transmite los metadatos de la máquina virtual, que incluyen el nombre, el identificador, la red virtual y el nombre de la nube a la que pertenece.</p>
	</td>
	<td><p>El Servicio utiliza la información anterior para rellenar la información de la máquina virtual en el portal del Servicio.</p>
	</td>
	<td><p>Es una parte esencial del servicio y no se puede desactivar. Si no desea que esta información se envíe al Servicio, no habilite la protección de Azure Site Recovery para ninguna máquina virtual. Tenga en cuenta que todos los datos enviados por el proveedor al Servicio se envían a través de HTTPS.</p></td>
</tr>
<tr>
	<td><p><b>Plan de recuperación</b></p></td>
	<td><p>Esta característica le ayuda a crear un plan de coordinación para el centro de datos de "recuperación". Puede definir el orden por el que se deben iniciar las máquinas virtuales o un grupo de máquinas virtuales en el sitio de recuperación. También puede especificar los scripts automatizados que se pueden ejecutar, o cualquier acción manual que se debe emprender, en el momento de la recuperación para cada máquina virtual. Normalmente se activa la conmutación por error (que se describe en la sección siguiente) en el nivel del plan de recuperación para la recuperación coordinada.</p></td>
	<td><p>El Servicio recopila el proceso y transmite esta información como parte de la característica del plan de recuperación:</p>
		<ul>
			<li>Plan de recuperación, incluidos los metadatos de la máquina virtual</li>
			<li>Metadatos del script de automatización o la nota de acción manual.</li>
		</ul>
	</td>
	<td><p>Los metadatos descritos anteriormente se utilizan para crear el plan de recuperación en el portal del Servicio.</p>
	</td>
	<td><p>Es una parte esencial del servicio y no se puede desactivar. Si no desea que esta información se envíe al Servicio, no cree planes de recuperación en este Servicio.</p></td>
</tr>
<tr>
	<td><p><b>Asignación de red</b></p></td>
	<td><p>Esta característica permite asignar la información de red del centro de datos principal al centro de datos de recuperación. Cuando se recuperan las máquinas virtuales en el sitio de recuperación, esta asignación permite establecer la conectividad de red para ellas.</p></td>
	<td><p>Como parte de la característica de asignación de red, el servicio recopila, procesa y transmite los metadatos de las redes lógicas para cada sitio (principal y centro de datos).</p>
	</td>
	<td><p>El Servicio utiliza los metadatos para rellenar el portal del Servicio donde puede asignar la información de red.</p>
	</td>
	<td><p>Es una parte esencial del servicio y no se puede desactivar. Si no desea que esta información se envíe al Servicio, no utilice la característica de asignación de red.</p></td>
</tr>
<tr>
	<td><p><b>Error: planeado, no planeado, texto</b></p></td>
	<td><p>Esta característica permite la conmutación por error de una máquina virtual de un centro de datos administrados de VMM a otro centro de datos administrados de VMM. La acción de conmutación por error la desencadena el usuario en el portal del Servicio. Entre las posibles razones para una conmutación por error se incluyen un evento no planeado (por ejemplo, en el caso de un desastre natural; un evento planeado (por ejemplo, equilibrio de carga de centro de datos); una conmutación por error de prueba (por ejemplo, un ensayo del plan de recuperación).</p>
	<p>El proveedor en el servidor VMM recibe notificación del evento desde el Servicio y ejecuta una acción de conmutación por error en el host de Hyper-V a través de las interfaces de VMM. La conmutación por error real de la máquina virtual desde un host de Hyper-V a otro (normalmente se ejecuta en un centro de datos de "recuperación" diferente) se controla mediante la tecnología de replicación de Windows Server 2012 o de Windows Server 2012 R2 Hyper-V. Una vez finalizada la conmutación por error, el proveedor instalado en el servidor VMM del centro de datos de "recuperación" envía la información de que está todo correcto al Servicio.</p>
	</td>
	<td><p>El Servicio utiliza la información anterior para rellenar el estado de la información de la acción de conmutación por error en el portal del Servicio.</p>
	</td>
	<td><p>El Servicio utiliza la información anterior del modo siguiente:</p>
		<ul>
			<li>Certificado de administración: se utiliza para ayudar a identificar y autenticar el servidor VMM registrado para el acceso al Servicio. El servicio utiliza la parte de clave pública del certificado para proteger un token al que solo puede obtener acceso el servidor VMM registrado. El servidor debe utilizar este token para obtener acceso a las características del Servicio.</li>
			<li>Nombre del servidor VMM: el nombre del servidor VMM es necesario para identificar y comunicarse con el servidor VMM adecuado en donde se ubican las nubes. 
			</li>
			<li>Nombres de nubes del servidor VMM: el nombre de nube es obligatorio cuando se utiliza la característica de emparejar o desemparejar las nubes del servicio descrita a continuación. Si decide emparejar la nube de un centro de datos principal con otra nube en el centro de datos de recuperación, se muestran los nombres de todas las nubes del centro de datos de recuperación.</li>
		</ul>
	</td>
	<td><p>Es una parte esencial del servicio y no se puede desactivar. Si no desea que esta información se envíe al Servicio, no utilice este Servicio.</p></td>
</tr>
</table>



<!--HONumber=49-->
<properties
	pageTitle="Planificación de la capacidad de la replicación de Hyper-V"
	description="Este artículo contiene instrucciones sobre cómo utilizar la herramienta de programación de capacidad de Azure Site Recovery e incluye recursos para planear la capacidad de Replicación de Hyper-V"
	services="site-recovery"
	documentationCenter="na"
	authors="csilauraa"
	manager="jwhit"
	editor="tysonn" />
<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="06/01/2015"
	ms.author="lauraa" />

# Planificación de la capacidad de la replicación de Hyper-V

Azure Site Recovery usa Réplica de Hyper-V para la replicación entre dos sitios VMM locales o entre un servidor VMM local y el almacenamiento de Azure. Este artículo contiene instrucciones paso a paso sobre cómo utilizar el programador de capacidad para Azure Site Recovery (ASR) - herramienta de Réplica de Hyper-V. El programador de capacidad para ASR - herramienta Réplica de Hyper-V guía a un administrador de TI a la hora de diseñar el servidor, el almacenamiento y la infraestructura de red necesarios para implementar correctamente Réplica de Hyper-V y para validar la conectividad de red entre dos sitios.

## Requisitos del sistema
- Sistema operativo: Windows Server ® 2012 o Windows Server ® 2012 R2
- Memoria: 20 MB (mínimo)
- CPU: sobrecarga del 5 por ciento (mínimo)
- Espacio en disco: 5 MB (mínimo)

## Pasos del tutorial
- Paso 1: Preparar el sitio primario
- Paso 2: Preparar el sitio de recuperación si el sitio de recuperación es local
- Paso 3: Ejecutar la herramienta de programación de capacidad
- Paso 4: Interpretar los resultados

## Paso 1: Preparar el sitio primario
1. Haga una lista de todas las máquinas virtuales de Hyper-V que tendrán que habilitarse para la replicación y los host/clústeres Hyper-V principales correspondientes.
2. Agrupar hosts y clústeres de Hyper-V principales en uno de los siguientes:

  - Servidores independientes de Windows Server® 2012
  - Clústeres de Windows Server® 2012
  - Servidores independientes de Windows Server® 2012 R2
  - Clústeres de Windows Server® 2012 R2

3. Deberá ejecutar la herramienta de planificación de capacidad una vez por cada grupo de servidores autónomos y otra vez para cada clúster.
4. Habilite el acceso remoto a WMI en todos los hosts y clústeres principales. Asegúrese de que se haya establecido el conjunto correcto de reglas de firewall y los permisos de usuario.

        netsh firewall set service RemoteAdmin enable

5. Habilite la supervisión de rendimiento en los hosts principales.

  - Abra el **Firewall de Windows** con el complemento **Seguridad avanzada** y, a continuación, habilite las siguientes reglas de entrada:
    - Acceso de red COM+ (DCOM-IN)
    - Todas las reglas del grupo de administración remota del registro de eventos

## Paso 2: Preparar el sitio de recuperación
Si va a utilizar Azure como el sitio de recuperación o el sitio de recuperación local no está listo, puede omitir esta sección. Pero si se omite, no podrá medir el ancho de banda disponible entre los dos sitios.

1. Identificar el método de autenticación

	a. Kerberos: debe utilizarse cuando los hosts de Hyper-V principal y de recuperación están en el mismo dominio o están en dominios de confianza mutua.

	b. Certificado: debe utilizarse cuando los hosts de Hyper-V principal y de recuperación están en dominios diferentes. Los certificados pueden crearse con makecert. Para obtener información acerca de los pasos necesarios para implementar certificados mediante esta técnica, consulte la entrada de blog [Autenticación basada en certificado de Réplica de Hyper- makecert](http://blogs.technet.com/b/virtualization/archive/2013/04/13/hyper-v-replica-certificate-based-authentication-makecert.aspx).

2. Identifique un **único** host o clúster de Hyper-V de recuperación desde el sitio de recuperación.

	a. Para replicar una máquina virtual ficticia y para calcular el ancho de banda disponible entre los sitios primarios y secundarios, se usará este host o clúster de recuperación.

	b. **Recomendado**: utilice un host de Hyper-V de recuperación única para ejecutar las pruebas.

### Preparar un único host de Hyper-V como un servidor de recuperación
1. En el Administrador de Hyper-V, en el panel **Acciones**, haga clic en **Configuración de Hyper-V**.
2. En el cuadro de diálogo **Configuración de Hyper-V**, haga clic en **Configuración de replicación**.
3. En el panel de detalles, seleccione **Habilitar este equipo como servidor de réplica**.
4. En la sección **Autentication y puertos** seleccione el método de autenticación que eligió anteriormente. Para cualquier método de autenticación, especifique el puerto que se usará (los puertos predeterminados son 80 para kerberos sobre HTTP y 443 para la autenticación basada en certificados a través de HTTPS).
5. Si se usa la autenticación basada en certificados, haga clic en **Seleccionar certificado**, y, a continuación, proporcione la información del certificado.
6. En la sección **Autorización y almacenamiento**, especifique si se permite **cualquier** servidor autenticado (principal) para enviar datos de replicación para este servidor de réplicas.
7. Haga clic en **Aceptar** o **Aplicar**.

![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image1.png)

8. Validar que se está ejecutando el agente de escucha https mediante la ejecución del comando: netsh http show servicestate
9. Abrir puertos del firewall:

        Port 443 (certificae-based authentication):
          Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"


        Port 80 (Kerberos):
          Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"

### Preparar un único clúster de Hyper-V como destino de recuperación
Si ya ha preparado un host de Hyper-V independiente en un servidor de recuperación, puede omitir esta sección.

1. Configure el agente de Réplica de Hyper-V

	a. En **Administrador del servidor**, abra **Administrador de clúster de conmutación por error**.

	b. En el panel izquierdo, conéctese al clúster y el nombre del clúster se resaltará, haga clic en **Configurar rol** en el panel **Acciones**. Se abrirá la **Asistente para alta disponibilidad**.

	c. En la pantalla **Seleccionar rol**, seleccione **Agente de Réplica de Hyper-V**.

	d. Complete el asistente, proporcionando un **Nombre NetBIOS** y una **Dirección IP** para usarse como punto de conexión con el clúster (denominado punto de acceso de cliente). El **Agente de réplica de Hyper-V** está configurado, lo que permite obtener un nombre de punto de acceso de cliente. Anote el nombre del punto de acceso de cliente (se utilizará para configurar la réplica más adelante).

	e. Compruebe que el rol del agente de Réplica de Hyper-V se conecte correctamente y puede conmutar por error entre todos los nodos del clúster. Para ello, haga clic con el botón derecho del ratón en el rol, seleccione **Mover**, y, a continuación, haga clic en **Seleccionar nodo**. Seleccione un nodo > **Aceptar**.

	f. Si utiliza la autenticación basada en certificados, asegúrese de que cada nodo del clúster y el punto de acceso del cliente del agente de Réplica de Hyper-V tienen instalado el certificado del punto de acceso.

2. Configurar opciones de réplica:

	a. En **Administrador del servidor**, abra **Administrador de clúster de conmutación por error**.
	
	b. En el panel izquierdo, conéctese al clúster y con el nombre del clúster resaltado, haga clic en **Roles** en la categoría **Navegar** del panel **Detalles**.
	
	c. Haga clic con el botón derecho del ratón en el rol y, a continuación, elija **Configuración de replicación**.
	
	d. En el panel **Detalles**, seleccione **Habilitar este clúster como un servidor de réplica**.

	e. En la sección **Autenticación y puertos**, seleccione el método de autenticación que seleccionó anteriormente. Para cualquier método de autenticación, especifique el puerto que se usará (los puertos predeterminados son 80 para Kerberos sobre HTTP y 443 para la autenticación basada en certificados a través de HTTPS).

	f. Si se usa la autenticación basada en certificados, haga clic en **Seleccionar certificado** y, a continuación, proporcione la información del certificado solicitada.

	g. En la sección **Autorización y almacenamiento**, especifique si desea permitir a **cualquier** servidor autenticado (principal) enviar datos de replicación a este servidor de réplica o limitar la aceptación a los datos de servidores principales específicos. Puede usar caracteres comodín para limitar la aceptación a servidores de un dominio particular sin tener que especificar cada uno individualmente (por ejemplo, *. contoso.com).

	h. Abra puertos de firewall en todos los hosts de Hyper-V de recuperación: puerto 443 (autenticación de certificado): Get-ClusterNode | ForEach-Object {Invoke-command - computername \$_.name - scriptblock {Enable-Netfirewallrule - displayname "Escucha HTTPS de réplica de Hyper-V (TCP-In)"}}


          Port 80 (Kerberos auth):
              Get-ClusterNode | ForEach-Object {Invoke-command -computername \$_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"}}


## Paso 3: Ejecutar la herramienta de programación de capacidad
1. Descargue la herramienta de programación de capacidad.
2. Ejecute la herramienta desde uno de los servidores principales (o uno de los nodos del clúster principal). Haga clic con el botón derecho del ratón en el archivo .exe y, a continuación, elija **Ejecutar como administrador**.
3. Si lo elige, acepte los **Términos de la licencia**, y, a continuación, haga clic en **Siguiente**.
4. Seleccione una **Duración de la recopilación de mediciones**. Es altamente recomendable que la herramienta se ejecute durante las horas de producción para asegurarse de que se recopilan los datos más representativos. La duración sugerida para la recopilación de mediciones es de 30 minutos. Si solo desea validar la conectividad de red, puede elegir un minuto como la duración.

![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image2.png)

5. Especifique los **Detalles del sitio principal** como se muestra y, a continuación, haga clic en **Siguiente**.

	Para un host independiente, escriba el nombre del servidor o el FQDN.

	Si su host primario es parte de un clúster, puede especificar el FQDN de uno de los siguientes:

	a. El punto de acceso de cliente (CAP) del agente de Réplica de Hyper-V

	b. Nombre del clúster

	c. Cualquier nodo del clúster

  ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image3.png)


6. Escriba los **Detalles del sitio de réplica** (únicamente sitio local en la replicación de sitio local)

  Si desea habilitar la replicación en Azure o no preparó a un host o clúster de Hyper-V como servidor de recuperación (como se explica en el paso 2), debe omitir la prueba relacionada con el sitio de réplica.

  Especifique los detalles del **sitio de réplica** y, a continuación, haga clic en **Siguiente**.

i. Para un host independiente, escriba el nombre del servidor o el FQDN.

ii. Si su host primario es parte de un clúster, puede especificar el FQDN de uno de los siguientes:

a. El punto de acceso de cliente (CAP) del agente de Réplica de Hyper-V

b. Nombre del clúster

c. Cualquier nodo del clúster

   ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image4.png)

7. Omita las pruebas que implican el **sitio de réplica extendido**. ASR no las admite.
8. Seleccione las máquinas virtuales de las que crear un perfil. La herramienta se conecta a los servidores de clúster o independientes especificados en **Detalles del sitio principal** y enumera las máquinas virtuales que se están ejecutando. Seleccione las máquinas virtuales y discos virtuales de los que desea recopilar métricas.

Las siguientes máquinas virtuales no se enumerarán ni mostrarán:

- Máquinas virtuales que ya se han habilitado para la replicación
- Máquinas virtuales que no se están ejecutando

9. Escriba **la información de red** (esto solo es aplicable a la replicación de sitio local a sitio local y cuando se proporcionan detalles del sitio de réplica).

Especifique la información de red solicitada y, a continuación, haga clic en **Siguiente**.

- Ancho de banda WAN estimado
- El certificado que se utilizará para la autenticación (opcional): si piensa utilizar la autenticación basada en certificados, debe proporcionar los certificados necesarios en esta página.

   ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image5.png)

10. En el siguiente conjunto de pantallas, haga clic en **Siguiente** para iniciar la herramienta.

![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image6.png)

11. Cuando haya terminado de ejecutarse la herramienta, haga clic en **Ver informe** para revisar el resultado.

    Ubicación predeterminada del informe:

    %systemdrive%\Users\Public\Documents\Capacity Planner

    Ubicación de los registros:

    %systemdrive%\Users\Public\Documents\CapacityPlanner

## Paso 4: Interpretar los resultados
Puede omitir las métricas que no aparecen en uno de los dos escenarios siguientes, ya que no son relevantes para este escenario.

### Sitio local para la replicación de sitio local
  - Impacto de la replicación en el proceso del host principal, memoria
  - Impacto de la replicación en el servidor principal, espacio de almacenamiento de hosts de recuperación, IOPS
  - Ancho de banda total necesario para la replicación delta (Mbps)
  - Ancho de banda de red observado entre el host primario y el host de recuperación (Mbps)
  - Sugerencia para el número ideal de transferencias paralelas activas entre dos hosts/clústeres

### Replicación de sitio local a Azure
  - Impacto de la replicación en el proceso del host principal, memoria
  - Impacto de la replicación en espacio de almacenamiento del host principal, IOPS
  - Ancho de banda total necesario para la replicación delta (Mbps)

Pueden encontrar instrucciones más detalladas en la [página de descarga de Programador de capacidad para Réplica de Hyper-V](http://go.microsoft.com/?linkid=9876170).

## Otros recursos:
Los siguientes recursos pueden ayudarle a planear la capacidad para la replicación de Hyper-V:

- [Actualización: Planificador de capacidad para Réplica de Hyper-V](http://go.microsoft.com/fwlink/?LinkId=510891): lea esta entrada de blog para obtener información general de esta nueva herramienta.

- [Planificador de capacidad para Réplica de Hyper-V](http://go.microsoft.com/fwlink/?LinkId=510892): descargue la versión más reciente de esta herramienta.

- [Laboratorios de prácticas guiadas](http://go.microsoft.com/fwlink/?LinkId=510893): obtenga un excelente tutorial de la planificación de capacidad con la herramienta del blog de TechNet de Keith Mayer.

- [Pruebas de rendimiento y escalado - local a local](https://msdn.microsoft.com/library/azure/dn760892.aspx): lea los resultados de las pruebas de replicación para una implementación de local a local.


## Pasos siguientes

Para empezar a implementar ASR:

- [Configuración de la protección entre un sitio de VMM local y Azure](site-recovery-vmm-to-azure.md)
- [Configuración de la protección entre un sitio de Hyper-V local y Azure](site-recovery-hyper-v-site-to-azure)
- [Configuración de la protección entre dos sitios VMM locales](site-recovery-vmm-to-vmm)
- [Configuración de la protección entre dos sitios de VMM locales con SAN](site-recovery-vmm-san)
- [Configuración de la protección con un único servidor VMM](site-recovery-single-vmm)
 

<!---HONumber=62-->
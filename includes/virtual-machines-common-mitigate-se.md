


**Última actualización del documento**: 6 de marzo a las 10:00 h PST.

La reciente divulgación de una [nueva clase de vulnerabilidades de CPU](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002), conocidas como ataques de canal lateral de ejecución especulativa, han generado preguntas de los clientes que buscan mayor claridad.  

La infraestructura que ejecuta Azure y aísla las cargas de trabajo de los clientes entre sí está protegida.  Esto significa que otros clientes que utilizan Azure no pueden atacar su aplicación mediante estas vulnerabilidades.

> [!NOTE] 
> A finales de febrero de 2018, Intel Corporation publicó la [Microvision Revision Guidance](https://newsroom.intel.com/wp-content/uploads/sites/11/2018/03/microcode-update-guidance.pdf) actualizada sobre el estado de sus versiones de microcódigo, que mejoran la estabilidad y mitigan las vulnerabilidades recientes reveladas por [ Google Project Zero](https://googleprojectzero.blogspot.com/2018/01/reading-privileged-memory-with-side.html). La actualización de microcódigo de Intel no afecta a las mitigaciones que Azure aplicó el [3 de enero de 2018](https://azure.microsoft.com/en-us/blog/securing-azure-customers-from-cpu-vulnerability/). Microsoft ya aplicó mitigaciones seguras para proteger a los clientes de Azure contra otros inquilinos de Azure.  
>
> El microcódigo de Intel trata la variante 2 de Spectre ([CVE-2017-5715](https://www.cve.mitre.org/cgi-bin/cvename.cgi?name=2017-5715)) para protegerse contra ataques que solo serían aplicables donde se ejecutaron cargas de trabajo compartidas o de confianza dentro de las VM en Azure. Nuestros ingenieros están probando la estabilidad para minimizar el impacto en el rendimiento del microcódigo, antes de ponerlo a disposición de los clientes de Azure.  Dado que muy pocos clientes ejecutan cargas de trabajo que no son de confianza dentro de sus VM, la mayoría de los clientes no necesitarán habilitar esta funcionalidad una vez publicada. 
>
> Esta página se actualizará tan pronto como haya más información disponible.  






## <a name="keeping-your-operating-systems-up-to-date"></a>Mantener actualizados los sistemas operativos

Aunque no se necesita una actualización del sistema operativo para aislar las aplicaciones que se ejecutan en Azure de otros clientes que utilizan Azure, siempre es recomendable mantener actualizadas las versiones del sistema operativo. 

En las siguientes ofertas, se indican las acciones recomendadas para actualizar el sistema operativo: 

<table>
<tr>
<th>Oferta</th> <th>Acción recomendada </th>
</tr>
<tr>
<td>Azure Cloud Services </td>  <td>Habilite las actualizaciones automáticas o asegúrese de que está ejecutando el sistema operativo invitado más reciente.</td>
</tr>
<tr>
<td>Máquinas virtuales Linux en Azure</td> <td>Instale las actualizaciones de su proveedor de sistema operativo cuando estén disponibles. </td>
</tr>
<tr>
<td>Máquinas virtuales Windows en Azure </td> <td>Compruebe que está ejecutando una aplicación antivirus compatible antes de instalar las actualizaciones del sistema operativo. Póngase en contacto con su proveedor de software antivirus para obtener información acerca de la compatibilidad.<p> Instale el [paquete acumulativo de actualizaciones de seguridad de enero](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002). </p></td>
</tr>
<tr>
<td>Otros servicios PaaS de Azure</td> <td>No es necesaria ninguna acción para los clientes que usan estos servicios. Azure mantiene actualizadas las versiones de sistema operativo de forma automática. </td>
</tr>
</table>

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>Instrucciones adicionales si está ejecutando código que no es de confianza 

A menos que se esté ejecutando código que no es de confianza, no es necesario que el cliente realice ninguna acción adicional. Si permite código que no es de confianza (por ejemplo, permite a uno de sus clientes cargar un archivo binario o un fragmento de código que después se ejecuta en la nube dentro de la aplicación), debe realizar los siguientes pasos adicionales.  


### <a name="windows"></a>Windows 
Si está usando Windows y hospeda código que no es de confianza, también debe habilitar una característica de Windows llamada ocultación de la dirección virtual del kernel (KVA), que proporciona protección adicional contra las vulnerabilidades frente a ataques de canal lateral de ejecución especulativa. Esta característica está desactivada de forma predeterminada y puede afectar al rendimiento si se habilita. Siga las instrucciones del artículo [KB4072698 de Windows Server](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) para habilitar la protección en el servidor. Si ejecuta Azure Cloud Services, compruebe que está ejecutando WA-GUEST-OS-5.15_201801-01 o WA-GUEST-OS-4.50_201801-01 (disponible a partir del 10 de enero de 2018) y habilite la clave del Registro mediante una tarea de inicio.


### <a name="linux"></a>Linux
Si está usando Linux y hospeda código que no es de confianza, también debe actualizar Linux a una versión más reciente que implemente el aislamiento de la tabla de páginas del kernel (KPTI), que separa las tablas de páginas utilizadas por el kernel de las que pertenecen al espacio del usuario. Estas soluciones requieren una actualización del sistema operativo Linux y su proveedor de distribución puede proporcionárselas cuando estén disponibles. El proveedor del sistema operativo puede indicarle si las protecciones están habilitadas o deshabilitadas de forma predeterminada.



## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte [Securing Azure customers from CPU vulnerability](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/) (Proteger a los clientes de Azure de las vulnerabilidades de CPU).

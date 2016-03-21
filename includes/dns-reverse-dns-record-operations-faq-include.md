<BR>
## P+F 
### ¿Cuánto cuestan los registros de DNS inversos?
Son gratuitos. No existe ningún coste adicional para las consultas o los registros de DNS inversos.
### ¿Se resolverán mis registros de DNS inversos desde Internet?
Sí. Cuando haya configurado la propiedad de DNS inverso para su servicio en la nube, Azure administrará todas las delegaciones y zonas DNS necesarias para garantizar que el registro de DNS inverso se resuelve para todos los usuarios de Internet.
### ¿Se creará un registro de DNS inverso predeterminado para mis servicios en la nube?
No. El DNS inverso será una característica opcional. Si decide no configurarla, no se creará ningún registro de DNS inverso predeterminado. ¿Cuál es el formato del nombre de dominio completo (FQDN)? Los FQDN se especifican en orden progresivo y deben terminar con un punto (p. ej., “app1.contoso.com.”).
### ¿Qué ocurre si no se superan las comprobaciones de validación para el DNS inverso que he especificado?
Cuando no se supera la comprobación de validación de DNS inverso, se producirá un error en la operación de administración de servicios. Corrija el valor de DNS inverso según sea necesario y vuelva a intentarlo.
### ¿Puedo administrar el DNS inverso para mi sitio web de Azure?
No se admite el DNS inverso para los sitios web de Azure. El DNS inverso es compatible con las máquinas virtuales de IaaS y los roles de PaaS de Azure.
### ¿Puedo configurar varios registros de DNS inversos para mi servicio en la nube?
No. Azure admite un único registro de DNS inverso por cada servicio en la nube de Azure. Sin embargo, cada servicio en la nube de Azure puede tener su propio registro de DNS inverso.
### ¿Se pueden hospedar las zonas ARPA para mis IP asignadas por Azure en DNS de Azure dentro de mi propia suscripción o en mis propios servidores DNS autoritativos?
No. Azure no admite la delegación progresiva de zonas ARPA. Azure hospeda las zonas ARPA para todas las direcciones IP disponibles y permite a los clientes crear registros de DNS inversos dentro de estas zonas.
### ¿Puedo hospedar zonas ARPA para mis bloques IP asignados por el ISP en el DNS de Azure?
No. El DNS de Azure no admite actualmente registros de DNS inversos en zonas DNS de los clientes.

<!---HONumber=AcomDC_0309_2016-->
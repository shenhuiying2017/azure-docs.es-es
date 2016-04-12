<BR>
## P+F 
### ¿Cuánto cuestan los registros de DNS inversos?
Son gratuitos. No existe ningún coste adicional para las consultas o los registros de DNS inversos.
### ¿Se resolverán mis registros de DNS inversos desde Internet?
Sí. Cuando haya configurado la propiedad de DNS inverso para su dirección IP pública, Azure administrará todas las delegaciones y zonas DNS necesarias para garantizar que el registro de DNS inverso se resuelve para todos los usuarios de Internet.
### ¿Se creará un registro de DNS inverso predeterminado para mis direcciones IP públicas?
No. El DNS inverso es una característica opcional. Si decide no configurarla, no se crea ningún registro de DNS inverso predeterminado.
### ¿Cuál es el formato del nombre de dominio completo (FQDN)?
Los FQDN se especifican en orden progresivo y deben terminar con un punto (p. ej., “app1.contoso.com.”).
### ¿Qué ocurre si no se superan las comprobaciones de validación para el DNS inverso que he especificado?
Cuando no se supera la comprobación de validación de DNS inverso, se producirá un error en la operación de administración de servicios. Corrija el valor de DNS inverso según sea necesario y vuelva a intentarlo.
### ¿Puedo administrar el DNS inverso para mi sitio web de Azure?
No se admite el DNS inverso para los sitios web de Azure. El DNS inverso se admite para máquinas virtuales de Azure.
### ¿Puedo configurar varios registros de DNS inversos para mi dirección IP pública?
No. Azure admite un único registro de DNS inverso para cada dirección IP pública. Sin embargo, cada dirección IP pública puede tener su propio registro de DNS inverso.
### ¿Puedo configurar un registro de DNS inverso para mi dirección IP pública sin tener especificado un valor DomainNameLabel?
No. Si quiere utilizar los registros de DNS inverso para sus direcciones IP públicas, tendrá que especificar la propiedad DomainNameLabel.
### ¿Se pueden hospedar las zonas ARPA para mis IP asignadas por Azure en DNS de Azure dentro de mi propia suscripción o en mis propios servidores DNS autoritativos?
No. Azure no admite la delegación progresiva de zonas ARPA. Azure hospeda las zonas ARPA para todas las direcciones IP disponibles y permite a los clientes crear registros de DNS inversos dentro de estas zonas.
### ¿Puedo hospedar zonas ARPA para mis bloques IP asignados por el ISP en el DNS de Azure?
No. El DNS de Azure no admite actualmente registros de DNS inversos en zonas DNS de los clientes.

<!---HONumber=AcomDC_0316_2016-->
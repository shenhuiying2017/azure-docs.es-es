## Puerta de enlace de aplicaciones

La puerta de enlace de aplicaciones ofrece una solución de equilibrio de carga HTTP administrada de Azure basada en el equilibrio de carga de nivel 7. El equilibrio de carga de aplicaciones permite el uso de reglas de enrutamiento para el tráfico de red basado en HTTP.

La puerta de enlace de aplicaciones contiene los siguientes recursos secundarios:

- **Grupo de servidores back-end:** la lista de direcciones IP de los servidores back-end. Las direcciones IP mostradas deben pertenecer a la subred de la red virtual o ser una VIP/IP pública. 
- **Configuración del grupo de servidores back-end:** cada grupo tiene una configuración como el puerto, el protocolo y la afinidad basada en cookies. Estos valores están vinculados a un grupo y se aplican a todos los servidores del grupo.
- **Puerto front-end:** este puerto es el puerto público abierto en la puerta de enlace de aplicaciones. El tráfico llega a este puerto y después se redirige a uno de los servidores back-end.
- **Agente de escucha**: la escucha tiene un puerto front-end, un protocolo (http o https, que distinguen mayúsculas de minúsculas) y el nombre del certificado SSL (si se configura la descarga de SSL). 
- **Regla:** enlaza el agente de escucha y el grupo de servidores back-end y define a qué grupo de servidores back-end se redirigirá el tráfico cuando se seleccione un agente de escucha concreto. Actualmente, solo se admite la regla básica. La regla básica es la distribución de carga round robin.

<!---HONumber=Oct15_HO3-->
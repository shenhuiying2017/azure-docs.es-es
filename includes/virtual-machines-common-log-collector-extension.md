
El diagnóstico de problemas con un servicio en la nube de Microsoft Azure requiere la recopilación de archivos de registro del servicio en máquinas virtuales cuando se producen los problemas. Puede usar la extensión AzureLogCollector a petición para realizar una recopilación única de registros provenientes de una o más máquinas virtuales del Servicio en la nube (desde roles web y roles de trabajo) y transferir los archivos recopilados a una cuenta de almacenamiento de Azure, sin iniciar sesión de manera remota en ninguna de las máquinas virtuales.

> [AZURE.NOTE]Puede encontrar descripciones para la mayoría de la información registrada en http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.asp.

Existen dos modos de recopilación que dependen de los tipos de archivos que se van a recopilar.
- Solo los registros del agente invitado de Azure (disponibilidad general). Este modo de recopilación incluye todos los registros relacionados con los agentes invitados y otros componentes de Azure.
- Todos los registros (completo). Este modo de recopilación recopilará todos los archivos en modo GA además de:

  - los registros de eventos de aplicación y del sistema
  
  - registros de errores HTTP
  
  - Registros IIS
  
  - Registros de configuración
  
  - otros registros del sistema

En ambos modos de recopilación, pueden especificarse carpetas de recopilación de datos adicionales mediante una colección de la estructura siguiente:

- **Name**: el nombre de la colección, que se usará como el nombre de la subcarpeta dentro del archivo zip que se va a recopilar.

- **Location**: la ruta de acceso a la carpeta en la máquina virtual donde se recopilará el archivo.

- **SearchPattern**: el patrón de los nombres de archivos que se van a recopilar. El valor predeterminado es "*".

- **Recursive**: si los archivos se recopilarán de forma recursiva en la carpeta.

<!----HONumber=AcomDC_0323_2016-->
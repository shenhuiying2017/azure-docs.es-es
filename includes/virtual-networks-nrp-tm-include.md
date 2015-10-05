## Perfil del Administrador de tráfico
El Administrador de tráfico y su recurso de extremo secundario permiten la distribución del tráfico a los extremos de Azure y fuera de Azure. Esta distribución de tráfico se rige por las directivas. El Administrador de tráfico también permite supervisar el estado del extremo y desviar el tráfico adecuadamente según el estado de un extremo.

Entre las principales propiedades de un perfil de administrador de tráfico se incluyen las siguientes:

- **Método de enrutamiento de tráfico**: los valores posibles son *Rendimiento*, *Ponderado* y *Prioridad*.
- **Configuración de DNS**: nombre de dominio completo del perfil.
- **Protocolo**: protocolo de supervisión, los valores posibles son *HTTP* y *HTTPS*.
- **Puerto**: puerto de supervisión. 
- **Ruta**: ruta de acceso de supervisión.
- **Extremos**: contenedor de recursos de extremo.

### Extremo 
Un extremo es un recurso secundario de un perfil de Administrador de tráfico. Representa un servicio o extremo web al que se distribuye el tráfico del usuario según la directiva configurada en el recurso de perfil del Administrador de tráfico.

Entre las principales propiedades de un extremo se incluyen las siguientes:
 
- **Tipo**: el tipo de extremo, los posibles valores son *extremo de Azure*, *extremo externo* y *extremo anidado*. 
- **Identificador de recurso de destino**: dirección IP pública de servicio o extremo web. Puede tratarse de un extremo de Azure o uno externo.
- **Ponderación**: ponderación del extremo utilizada en la administración del tráfico. 
- **Prioridad**: prioridad del extremo, utilizada para definir una acción de conmutación por error. 

<!---HONumber=Sept15_HO4-->
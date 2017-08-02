Se aplican etiquetas a los recursos de Azure para organizarlos de forma lógica por categorías. Cada etiqueta consta de un nombre y un valor. Por ejemplo, puede aplicar el nombre "Environment" y el valor "Production" a todos los recursos en producción. Sin esta etiqueta, puede que tenga dificultades para identificar si un recurso está diseñado para el desarrollo, la prueba o la producción. Sin embargo, "Entorno" y "Producción" son solo ejemplos. Puede definir los nombres y valores que sean más convenientes para organizar la suscripción.

Después de aplicar las etiquetas, puede recuperar todos los recursos de la suscripción que tengan ese nombre y valor de etiqueta. Las etiquetas le permiten recuperar los recursos relacionados que se encuentran en distintos grupos de recursos. Este enfoque puede resultar útil si necesita organizar recursos para facturación o administración.

Se aplican las siguientes limitaciones a las etiquetas:

* Cada recurso o grupo de recursos puede tener un máximo de 15 pares de nombre/valor de etiqueta. Esta limitación solo se aplica a las etiquetas que se aplican directamente al recurso o grupo de recursos. Un grupo de recursos puede contener muchos recursos con 15 pares de clave/valor de etiqueta cada uno. 
* El nombre de etiqueta está limitado a 512 caracteres y el valor de la etiqueta, a 256. En las cuentas de almacenamiento, el nombre de etiqueta se limita a 128 caracteres y el valor de la etiqueta, a 256.
* Los recursos de un grupo de recursos no heredan las etiquetas aplicadas a este. 

Si necesita asociar más de 15 valores a un recurso, utilice una cadena JSON como valor de la etiqueta. La cadena JSON puede contener muchos valores que se aplican a un sol nombre de etiqueta. En este artículo se muestra un ejemplo de asignación de una cadena JSON a la etiqueta.
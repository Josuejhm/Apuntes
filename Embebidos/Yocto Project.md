Construir un sistema linux desde cero. Uno provee el target device y features.
El fabricante provee archivos de configuración de tarjeta.

Con esto se busca optimizar mejor el uso de recursos.

**Azure:** es una computadora en la nube.
Requisitos:
- Ubuntu 24.04
- Por lo menos 2 ó 4 cores
- 8 GB de RAM al menos (16 recomendado)
- 50 GB+ de disco

**Git for Yocto**
- Guardar recetas en Git
- Guardar archivos de configuración
- Guardar custom layers
- **No** guardar los build  porque son enormes

**Nota:** todas las tarjetas necesitan layers diferentes.
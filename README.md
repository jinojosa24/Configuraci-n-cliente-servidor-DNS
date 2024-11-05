# Configuración cliente + servidor DNS

Este archivo `docker-compose.yml` configura un entorno con dos (contenedores): un servidor DNS BIND9 y un cliente basado en Alpine. Aquí explico cada apartado:

### Servicios

1. **bind9**:
   - **image**: Use la imagen oficial `ubuntu/bind9`, que contiene el software de servidor DNS BIND9.
   - **container_name**: Nombra el contenedor como `bind9` para identificarlo fácilmente.
   - **ports**: Mapea el puerto 54 del host con el puerto 54 del contenedor, tanto en TCP como en UDP. Esto permite el acceso al servicio DNS en el contenedor a través del puerto 54 del host. Para maquinas vituarles.
   - **networks**: Conecta el contenedor `bind9` a la red `bind9` (definida más abajo).
   - **volumes**: 
     - `./conf:/home/jose/compose/conf`: Monta el directorio `./conf` en el host dentro del contenedor en `/home/jose/compose/conf`, donde se espera que estén los archivos de configuración del servidor BIND9.
     - `./zonas:/home/jose/compose/zonas`: Monta el directorio `./zonas` del host en `/home/jose/compose/zonas` del contenedor, donde BIND9 puede almacenar los archivos de zona (datos DNS).
   - **restart**: Configurado como `always`, lo que significa que el contenedor se reiniciará automáticamente si se detiene, salvo que el usuario lo detenga manualmente.

2. **cliente**:
   - **container_name**: El contenedor se nombra `cliente`.
   - **image**: Usa la imagen `alpine:latest`, una distribución ligera de Alpine Linux.
   - **tty** y **stdin_open**: Mantienen abierto un terminal interactivo, útil para sesiones interactivas y pruebas manuales.
   - **dns**: Configura el DNS del cliente apuntando a la IP `172.20.0.2`, presumiblemente la IP del contenedor `bind9`, lo que permite al cliente usar `bind9` como su servidor DNS.
   - **networks**: Conecta el contenedor `cliente` a la red `bind9`, lo que permite la comunicación directa entre ambos contenedores.

### Redes

- **networks.bind9**: Define la red `bind9` con el controlador `bridge`, asignándole también el nombre `bind9` en Docker.

dig: Es una herramienta de consulta de DNS que envía solicitudes al servidor DNS especificado.
@127.30.0.3: Especifica la dirección IP del servidor DNS al que queremos hacer la consulta. En este caso, 127.30.0.3 sería la IP del servidor DNS, que puede ser el servidor BIND9 en tu configuración.
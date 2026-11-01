# Registro de Prompts - WaterManagement

## Registro de Consultas y Diseño de Arquitectura

### 1. Control de versiones y base de datos (`.gitignore`)
* **Contexto:** Configuración del repositorio para evitar versionar datos volátiles y el archivo de base de datos local.
* **Prompts:**
  1. > *"como puedo configurar el .gitignore para que no se suba la base de datos sqlite al repo pero se mantenga la estructura?"*

### 2. Diseño de control centralizado y requisitos (`WM_Central.py` / `contrato.md`)
* **Contexto:** Análisis de los requisitos del enunciado respecto al control administrativo del riego desde la central frente a las peticiones de los operarios.
* **Prompts:**
  1. > *"estaba mirando la guia de evaluacion, no era un requisito que desde la central se pudiera ordenar el arranque y parada del riego ademas de los operarios? como encaja eso con lo que tenemos?"*
  2. > *"que tiene mas sentido hacer? se pueden dejar las dos formas (ordenes desde central y peticiones de operarios por kafka) sin que se pisen en la base de datos?"*

### 3. API Flask y Dashboard en tiempo real (`WM_Central.py` / `index.html`)
* **Contexto:** Depuración de rutas en el servidor web Flask y personalización de las acciones de control en la interfaz.
* **Prompts:**
  1. > *"al entrar a http://localhost:5000/ me sale 404 not found, como tenemos que enlazar flask para que sirva bien el index.html del dashboard y las rutas de la api?"*
  2. > *"en la web se me queda en 'esperando conexion con central...', que esta fallando en las llamadas entre el frontend y los endpoints?"*
  3. > *"no quiero botones globales arriba en el panel, prefiero que los botones de accion salgan fila por fila segun el estado de cada estacion, y que si esta desconectada no deje hacer nada. me explicas como plantear esa logica en el js sin tocarme el archivo?"*

### 4. Monitor como puente persistente (`WM_WS_M.py`)
* **Contexto:** Diseño y arquitectura multihilo del Monitor para actuar como cliente de Central y servidor del Engine.
* **Prompts:**
  1. > *"el monitor que tenemos ahora solo manda el registro y cierra conexion. explicame como estructurarlo para que mantenga la conexion viva con central, abra un puerto para el engine y gestione los ping/pong y el health sin quedarse bloqueado"*
  2. > *"no me modifiques los archivos que lo quiero programar yo, solo dime que hilos y locks me hacen falta en WM_WS_M.py para coordinar el estado entre central y el engine"*

### 5. Metodología y plan de pruebas del Monitor (`prueba_engine.py`)
* **Contexto:** Verificación del comportamiento del Monitor y transiciones de estado antes de disponer del Engine final.
* **Prompts:**
  1. > *"como puedo comprobar que el monitor funciona bien paso a paso y que central detecta los cambios de estado (available, leak, offline) si todavia no tengo hecho el engine?"*

### 6. Simulación de electroválvula y control en el Engine (`WM_WS-E.py`)
* **Contexto:** Implementación de la lógica del Engine con cálculo de caudal, consumo de órdenes Kafka y menú interactivo.
* **Prompts:**
  1. > *"ahora quiero hacer el engine. explicame como organizarlo para que se conecte al monitor, reciba su id, responda a los pings y a la vez escuche las ordenes start y stop desde el topic ws_orders de kafka"*
  2. > *"como hacemos la simulacion de la electrovalvula para ir calculando el caudal cada segundo, y como cortamos el riego al instante si pulso la 'k' para simular una fuga?"*

### 7. Diagnóstico del entorno y broker Kafka (`docker-compose.yml`)
* **Contexto:** Resolución de fallos de conexión al inicializar los clientes Kafka.
* **Prompts:**
  1. > *"me da este error al arrancar: `[KAFKA] Esperando a broker localhost:9092... (KafkaTimeoutError: Unable to bootstrap from ['localhost:9092'])`, creo que es porque no tengo encendido el docker o hay algo mas?"*

### 8. Integración de la aplicación de operario (`WM-FO.py`)
* **Contexto:** Pruebas de integración de extremo a extremo de todo el flujo distribuido.
* **Prompts:**
  1. > *"ya tengo central, monitor y engine funcionando. como puedo probar ahora el operario para verlo todo junto desde que pide el riego hasta que termina?"*

### 9. Auditoría del proyecto y reparto de responsabilidades
* **Contexto:** Verificación del cumplimiento de la rúbrica y revisión de tareas pendientes.
* **Prompts:**
  1. > *"revisa que nos faltaria por hacer en el proyecto segun el pdf para dejarlo listo"*
  2. > *"y de la parte de despliegue y pruebas finales que nos queda exactamente por hacer?"*

### 10. Empaquetado y despliegue (`Dockerfile` / Cloud)
* **Contexto:** Creación del contenedor de Central y análisis de alternativas de despliegue en la nube a coste cero.
* **Prompts:**
  1. > *"que es exactamente lo del dockerfile y como tenemos que montarlo junto con el requirements.txt para meter nuestras apps en contenedores?"*
  2. > *"quiero mirar ahora lo de railway o aws, que me recomiendas? la idea es gastar 0 euros y que soporte tanto la web como los sockets y kafka"*

### 11. Análisis del enunciado y reparto asíncrono de la práctica (`contracts/contrato.md`)
* **Contexto:** Comprensión global de la arquitectura distribuida y división equitativa del trabajo entre dos integrantes sin conflictos de Git.
* **Prompts:**
  1. > *"tengo que hacer la practica de water_management, quiero que nos expliques que hay que hacer, como funciona lo de kafka con los sockets y como organizarlo todo sin hacer codigo aun"*
  2. > *"vale pero repartelo para que podamos hacerlo los dos por separado en casa subiendo codigo a github sin pisarnos, que quede equilibrado de tiempo y dificultad"*

### 12. Evolución de la Práctica 1 de sockets al registro de estaciones (`WM_Central.py` / `WM_WS_M.py`)
* **Contexto:** Adaptación del servidor concurrente de sockets (Anexo 1b) para implementar el registro inicial de monitores.
* **Prompts:**
  1. > *"como empezamos con el codigo? el anexo 1b dice algo de basarnos en la practica 1 de sockets, que partes reutilizamos del servidor concurrente y como planteamos el primer registro?"*

### 13. Diseño del contrato de comunicaciones y utilidades comunes (`contracts/contrato.md` / `wm_common.py`)
* **Contexto:** Definición formal de las tramas TCP, topics y esquemas JSON de Kafka, junto con el módulo compartido `wm_common.py`.
* **Prompts:**
  1. > *"vamos a definir el contrato de comunicaciones pero lo mas simple y limpio posible. como estructuramos wm_common.py para leer por lineas en los sockets sin que se corten los mensajes y tener las funciones de kafka en un solo sitio?"*
  2. > *"que topics de kafka y que campos json minimos ponemos en el contrato para las peticiones del operario, las ordenes a las estaciones y la telemetria?"*

### 14. Latidos de salud, números de secuencia y traducción de errores (`WM_WS_M.py` / `WM_WS_E.py`)
* **Contexto:** Implementación del heartbeat `PING/PONG` entre Monitor y Engine y notificación por transición de estado a Central.
* **Prompts:**
  1. > *"por que hace falta meter un numero de secuencia en los mensajes ping y pong entre el monitor y el engine? explicame como evita eso falsos ok"*
  2. > *"en vez de llenar la consola todo el rato con health ok o que salgan errores raros tipo `[Errno 111] Connection refused`, como lo hacemos para que solo avise cuando cambie de ok a ko y traduzca esos errores a español claro?"*

### 15. Simulador de Central y pruebas aisladas de estación (`WM_WS_M.py` / `WM_WS_E.py`)
* **Contexto:** Verificación de la reconexión automática y transiciones de salud de la estación antes de integrar con la Central real.
* **Prompts:**
  1. > *"como puedo probar por mi cuenta que el monitor y el engine reconectan solos si se cae central o si mato el engine a mitad, sin tener todavia el resto conectado?"*

### 16. Configuración y aprovisionamiento de instancia en AWS EC2 (Cloud)
* **Contexto:** Creación paso a paso de la máquina virtual Ubuntu 24.04 LTS en AWS, configuración de claves SSH y Docker.
* **Prompts:**
  1. > *"vamos a crear ya la instancia ec2 en aws con ubuntu 24.04. que tipo de instancia elegimos del free tier y como instalamos docker y docker compose nada mas entrar?"*
  2. > *"al intentar entrar por ssh desde powershell con la clave .pem me sale `UNPROTECTED PRIVATE KEY FILE` y me rechaza la conexion, como arreglo los permisos del archivo en windows?"*

### 17. Reglas de firewall y Security Group en AWS (`5000`, `6000`, `9092`)
* **Contexto:** Apertura de puertos de entrada en AWS para el panel HTTP, el servidor de sockets de Central y el broker Kafka.
* **Prompts:**
  1. > *"que puertos tenemos que abrir en las inbound rules del security group de aws para que desde los pcs de clase podamos entrar al panel web, conectar los monitores por socket y llegar a kafka?"*

### 18. Configuración de Kafka `advertised.listeners` e IP dinámica (`.env` / `docker-compose.yml`)
* **Contexto:** Resolución del problema de direccionamiento de Kafka en la nube cuando cambia la IP pública de EC2 al apagar y encender la instancia.
* **Prompts:**
  1. > *"en aws ya estan corriendo central y kafka, pero cuando conecto el engine o el fo desde mi pc se quedan pillados. que es eso de `KAFKA_ADVERTISED_LISTENERS` y por que kafka les devuelve una direccion a la que no pueden entrar?"*
  2. > *"cada vez que apagamos y encendemos la maquina de aws cambia la ip publica y deja de ir kafka. como ponemos la ip en un .env con `KAFKA_PUBLIC_HOST` para cambiarla en un momento sin tocar el docker-compose?"*

### 19. Desacoplamiento del latido local Monitor-Engine ante caída de Central (`WM_WS_M.py`)
* **Contexto:** Garantía de continuidad de un riego activo aunque se pierda temporalmente la conexión TCP entre el Monitor y Central.
* **Prompts:**
  1. > *"probando caidas hemos visto un fallo: si tiramos central a mitad de un riego, el monitor se pone a reconectar con central y deja de mandarle pings al engine, asi que el engine corta el riego por timeout. como separamos los dos bucles para que el riego siga hasta terminar aunque central este caida?"*

### 20. Bitácora persistente del Engine ante caídas (`wm_state.py` / `EngineJournal`)
* **Contexto:** Prevención de la pérdida de progreso de riego o del mensaje final `END` si el proceso Engine o el broker caen.
* **Prompts:**
  1. > *"si se cae el engine en mitad de un riego o justo al acabar cuando kafka esta caido, al volver a abrirlo tiene amnesia y pierde todo lo que llevaba regado. como montamos un journal en sqlite para ir guardando por donde va y que retome el riego al arrancar?"*
  2. > *"y si el riego termina mientras kafka no responde, como guardamos el mensaje final pendiente en el journal para que el engine lo reenvie en cuanto vuelva la conexion?"*

### 21. Persistencia transaccional e idempotencia en Central (`WM_Central.py` / `central_outbox`)
* **Contexto:** Almacenamiento de peticiones (`requests`), cola de salida (`central_outbox`) y offsets de Kafka en SQLite para tolerar caídas de Central.
* **Prompts:**
  1. > *"si central se cae mientras una estacion esta regando y la volvemos a encender cuando ya ha terminado, como hacemos con sqlite para que central lea el end de kafka al arrancar y actualice el historial sin perder nada?"*
  2. > *"como evitamos con el `request_id` que si se reintenta un mensaje tras un corte de red se ejecute dos veces el mismo riego?"*

### 22. Recuperación de sesión del Operario y buzón de respuestas (`WM_FO.py` / `ResponseInbox`)
* **Contexto:** Persistencia del estado del operario tras reinicio y drenado por lotes de mensajes en Kafka sin pérdida de eventos `END`.
* **Prompts:**
  1. > *"cuando cerramos el operario en pleno riego y lo volvemos a abrir se olvida de que tenia un riego activo. como guardamos su estado local para que al arrancar recupere el seguimiento automaticamente?"*
  2. > *"al pedir la lista de estaciones o recuperar el estado, a veces el mensaje `END` llega mezclado en kafka y se pierde. como hacemos un buzon (`ResponseInbox`) que lea todos los mensajes pendientes sin tragarse el final del riego?"*

### 23. Eliminación de dependencia de relojes entre máquinas (Clock Skew)
* **Contexto:** Corrección de rechazos falsos por diferencias de hora del sistema entre los ordenadores del laboratorio y la instancia en la nube.
* **Prompts:**
  1. > *"nos pasa una cosa rarisima entre dos ordenadores: si el reloj de windows va unos segundos desfasado respecto al de aws, las ordenes caducan solas nada mas llegar por el campo `expires`. como quitamos esa dependencia de la hora del reloj para que no falle en el laboratorio?"*

### 24. Interfaz interactiva del Operario y procesamiento por lotes (`WM_FO.py` / `solicitudes.txt`)
* **Contexto:** Diseño del menú continuo en consola para el operario con soporte de riego manual y lectura secuencial de archivo con pausa de 4 segundos.
* **Prompts:**
  1. > *"queremos que el operario arranque pasandole solo el broker y su id, y se quede en un menu interactivo para ver estaciones, pedir riegos manuales o lanzar el txt de solicitudes esperando los 4 segundos entre una y otra sin pararse si alguna da error. como organizamos ese bucle?"*

### 25. Consola de eventos en vivo en el Dashboard web (`WM_Central/logs.py` / `index.html`)
* **Contexto:** Incorporación de un visor tipo terminal en el panel web de Central para monitorizar todos los eventos distribuidos durante la defensa.
* **Prompts:**
  1. > *"para enseñar mejor lo que pasa por detras en la entrega, como podemos meter abajo del todo en la web de central una caja tipo terminal que vaya sacando los logs en tiempo real con un boton de pausar y otro de limpiar?"*

### 26. Escalabilidad dinámica y alta en caliente de estaciones y operarios (`/api/catalog`)
* **Contexto:** Creación de un endpoint idempotente en Central para registrar nuevas estaciones (`WS-XX`) u operarios (`FO-XX`) en caliente sin reiniciar el servidor.
* **Prompts:**
  1. > *"si en la defensa el profesor nos pide levantar una estacion nueva como WS-05 o un operario FO-03 al vuelo, central los rechaza porque no estan en la base de datos. como hacemos un endpoint `/api/catalog` en central para darlos de alta en caliente sin reiniciar nada?"*

### 27. Orquestador interactivo y simulación de caídas (`WaterManagement.bat` / `tools/launcher.ps1`)
* **Contexto:** Automatización del despliegue local en Docker con menús para escalar nodos, recuperar consolas tras caídas y limpiar contenedores conservando volúmenes.
* **Prompts:**
  1. > *"queremos hacer un `WaterManagement.bat` interactivo para no tener que escribir comandos largos de docker en el examen, que nos deje elegir la ip de aws, levantar estaciones u operarios concretos (`fo 1`, `ws 2`, `3-5`) y nos abra sus consolas listas. como lo montamos con powershell?"*
  2. > *"como le añadimos tambien al .bat una opcion para matar contenedores en seco (`kill`) y probar las caidas delante del profesor, y otra opcion `clean` que borre los contenedores pero sin borrar los volumenes donde se guarda el estado?"*

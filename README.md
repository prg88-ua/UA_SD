## Guía de Despliegue Paso a Paso

Este apartado describe la secuencia exacta de despliegue para poner en marcha el escenario completo distribuido (**Cloud en AWS** + **PC Laboratorio 1** + **PC Laboratorio 2**).

---

### 1. Despliegue de la Parte Cloud en AWS (WM_Central + Kafka + SQLite)

En la instancia EC2 de AWS se deben abrir en el **Security Group** los siguientes puertos TCP entrantes:
* `5000`: Cuadro de mandos web (HTTP REST / Dashboard).
* `6000`: Canal de sockets TCP para registro y latidos de los Monitores (`WM_WS_M`).
* `9092`: Broker de Apache Kafka para eventos y telemetría externa.

Dentro de la máquina virtual en AWS, se clona el repositorio, se exporta la IP pública de la instancia y se levantan los contenedores en segundo plano.

Para actualizar el código o desplegar cambios:

```bash
# 1. Descargar la versión más reciente del repositorio
git pull --ff-only

# 2. Reconstruir la imagen y aplicar los cambios
sudo docker compose up -d --build

# 3. Comprobar el estado de los contenedores y los logs de la Central
sudo docker compose ps
sudo docker compose logs --tail=50 central
```

> **Nota:** Si la instancia EC2 se apaga y se vuelve a iniciar, su IP pública cambiará. En ese caso, basta con editar el archivo `.env` (`nano .env`), actualizar la variable con la nueva IP pública y reiniciar los contenedores con `sudo docker compose up -d`.

Con esto quedan en ejecución permanente el bróker de Kafka y `WM_Central`. El cuadro de mandos es accesible desde cualquier navegador en:  
`http://<IP_PUBLICA_AWS>:5000`

---

### 2. Despliegue en los PCs del Laboratorio con WaterManagement.bat

En ambos ordenadores del laboratorio únicamente se requiere tener descargada la carpeta del proyecto y tener **Docker Desktop iniciado** (no requiere instalar Python ni ningún IDE en Windows).

#### Paso 1 — Puesto de Estaciones de Riego (PC Laboratorio 1)

1. **Obtener el código**:
   * Si es la primera vez:
     ```bash
     git clone https://github.com/prg88-ua/UA_SD.git
     cd UA_SD
     ```
   * Si ya está descargado y solo se requiere actualizar:
     ```bash
     git pull https://github.com/prg88-ua/UA_SD.git
     ```
2. Ejecutar `WaterManagement.bat` haciendo doble clic.
3. Seleccionar el perfil `ws` e introducir la **IP pública de AWS** (los puertos por defecto son `5000`, `6000`, `9092`).
4. Elegir la opción `1 (Abrir varias)` e indicar el número de estaciones a lanzar (por ejemplo, `2` para crear `WS-01` y `WS-02`). Se construirá automáticamente la imagen Docker si no existe.
5. Confirmar o personalizar la ubicación de cada estación (ej. *Parque Central* y *Jardines del Rio*). El lanzador dará de alta las estaciones en Central y abrirá automáticamente dos ventanas de consola por estación: una para el Monitor (`WM_WS_M`) y otra para el Engine (`WM_WS_E`).

#### Paso 2 — Puesto de Operarios de Campo (PC Laboratorio 2)

1. **Obtener el código**:
   * Si es la primera vez:
     ```bash
     git clone https://github.com/prg88-ua/UA_SD.git
     cd UA_SD
     ```
   * Si ya está descargado y solo se requiere actualizar:
     ```bash
     git pull https://github.com/prg88-ua/UA_SD.git
     ```
2. Ejecutar `WaterManagement.bat` haciendo doble clic.
3. Seleccionar el perfil `fo` e introducir la misma **IP pública de AWS**.
4. Elegir la opción `1 (Abrir varias)` e indicar la cantidad de operarios a instanciar (por ejemplo, `2` para lanzar `FO-01` y `FO-02`).
5. En la solicitud «*Archivo de riegos (Enter para menu manual)*»:
   * Escribir `peticiones.txt` para que los operarios ejecuten automáticamente el lote de pruebas programado.
   * O pulsar `Enter` para controlar las solicitudes interactivamente desde el menú manual.

#### Paso 3 — Operaciones durante la corrección desde el menú del BAT

* **Escalabilidad en vivo**: La opción `1 (Abrir varias)` o la opción `2 (Añadir una)` permiten añadir nuevas estaciones u operarios en cualquier momento sin detener las instancias en marcha.
* **Simulación de caídas (Crash) y recuperación**: Puede cerrarse cualquier módulo con `Ctrl+C` en su ventana o mediante la opción `7 (Simular caída)` del lanzador (permitiendo tirar solo el Monitor, solo el Engine o la estación completa). Para reanudar la instancia caída preservando su volumen de datos y litros acumulados, se usa la opción `3 (Crear/recuperar ID)`.
* **Reinicio de numeración**: En la opción `C (Configurar)`, el lanzador permite limpiar los contenedores locales y reiniciar el contador de IDs para comenzar de nuevo desde `01`.

---

### 3. Comandos equivalentes por consola (Docker CLI manual)

Si se desea prescindir del lanzador interactivo `.bat`, es posible ejecutar cualquier componente directamente desde la línea de comandos tras construir la imagen base común con:

```bash
docker build -t water-management:local .
```

#### Lanzar Monitor de WS-01:
```bash
docker run -it --rm --name monitor-ws01 --network host water-management:local \
  python WM_WS/WM_WS_M/WM_WS_M.py --central-host <IP_AWS> --central-port 6000 \
  --monitor-port 7001 --ws-id WS-01 --location "Parque_Central"
```

#### Lanzar Engine de WS-01 (con volumen persistente para litros acumulados):
```bash
docker run -it --rm --name engine-ws01 --network host \
  --mount source=wm-engine-ws01,target=/data water-management:local \
  python WM_WS/WM_WS_E/WM_WS_E.py --monitor-host 127.0.0.1 --monitor-port 7001 \
  --kafka-broker <IP_AWS>:9092
```

#### Lanzar Operario FO-01 (con volumen persistente para recuperación de solicitudes):
```bash
docker run -it --rm --name wm-fo-01 \
  --mount source=wm-lab-fo-FO-01-data,target=/data water-management:local \
  python WM_FO/WM_FO.py --kafka-broker <IP_AWS>:9092 --id-operario FO-01
```
# AntiVirusIA: guía del logger (logger.py)

Este documento divide el logger en 16 bloques. Cada bloque muestra el código y una explicación breve, para poder presentarlos o subirlos por separado. Aqui se veran algunos bloques explicados.
Los numeros que se usan en este documento son aleatorios para que no se puedan usar como metodo de ataque y pasar el antivirus. Algunas cosas del codigo son extras para que lo puedas ejecutar 
en tu ordenador sin saturarlo, esto estara marcado en la explicacion asi (mejora de rendimiento), en el codigo que final no se usan tantos limites para detectarlo todo.

## Visión general

El logger es un programa con **cinco hilos** que trabajan a la vez y escriben en una misma base de datos SQLite:

## Bloque 2: Configuración

```python
# ---------- CONFIGURACIÓN ----------
BASE_DIR = Path(__file__).resolve().parent
DB_PATH = BASE_DIR / "logger.db"
LOG_PATH = BASE_DIR / "logger.log"

INTERVALO_RED = 20                    # segundos entre sondeos de red(mejora de rendimiento)
INTERVALO_LATIDO = 120                # segundos entre "latidos" de vida
MAX_BYTES_HASH = 500 * 1024 * 1024   # no calculamos hash de ejecutables mayores de 500 MB

log = logging.getLogger("antivirusia")
```

**Qué hace:** deja en un único sitio todo lo que se puede personalizar.

- `Path(__file__).resolve().parent` es la carpeta donde está el script. La base de datos y el log se crean siempre ahí, da igual desde dónde se lance. Esto evita el problema de las rutas relativas cuando el logger se ejecuta como tarea programada.
- Los intervalos y el límite de tamaño para el hash son constantes en mayúsculas, fáciles de ajustar.

## Bloque 3: Registro de logs
```python
def configurar_log():
    log.setLevel(logging.INFO)
    formato = logging.Formatter("%(asctime)s %(levelname)s [%(threadName)s] %(message)s")

    a_fichero = RotatingFileHandler(LOG_PATH, maxBytes=2_000_000,
                                    backupCount=3, encoding="utf-8")
    a_fichero.setFormatter(formato)
    log.addHandler(a_fichero)

    if sys.stderr is not None:          # solo hay consola si lo lanzas a mano
        a_consola = logging.StreamHandler()
        a_consola.setFormatter(formato)
        log.addHandler(a_consola)


configurar_log()
```
**Qué hace:** monta el sistema de logs del programa, en lugar de usar `print`.

- Cada mensaje lleva fecha, nivel (INFO, ERROR...) y el nombre del hilo que lo escribió.
- Se guarda en `logger.log`. Al llegar a 2 MB, el fichero se archiva y se empieza otro, y se conservan 3 copias.
- La consola solo se activa si existe (al ejecutarlo a mano). Como tarea programada no hay consola, y el fichero es lo único que queda.

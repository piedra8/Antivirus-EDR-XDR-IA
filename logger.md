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

**Qué hace:** deja en un único sitio todo lo que se puede cambiar.

- `Path(__file__).resolve().parent` es la carpeta donde está el script. La base de datos y el log se crean siempre ahí, da igual desde dónde se lance. Esto evita el problema de las rutas relativas cuando el logger se ejecuta como tarea programada.
- Los intervalos y el límite de tamaño para el hash son constantes en mayúsculas, fáciles de ajustar.

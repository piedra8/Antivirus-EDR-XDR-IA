# Antivirus-EDR-XDR-IA
Antivirus por comportamiento con enfoque **EDR/XDR** para Windows. En lugar de depender de una base de firmas que haya que actualizar, aprende cómo se comporta normalmente un equipo y señala lo que se aleja de ese comportamiento.

> Proyecto en desarrollo.
Hoy cubre la telemetría y la detección; la respuesta automática y la correlación entre fuentes (la parte XDR) están en el roadmap. No sustituye a un EDR comercial.

## Qué es (y qué no es todavía)

| Capacidad | Estado |
|---|---|
| Telemetría del endpoint (procesos, binarios, red) | Hecho |
| Detección de anomalías por comportamiento | Primer modelo hecho |
| Correlación de varias fuentes (PC + DNS + IDS de red) | Planificado |
| Explicación de alertas con un LLM local | Planificado |
| Respuesta (bloqueo de IPs/dominios, suspender procesos) | Planificado |

## Cómo funciona

1. **Telemetría**: registra en SQLite el inicio y fin de cada proceso (WMI en tiempo real), el hash SHA-256 de cada ejecutable y las conexiones de red nuevas. Fechas en UTC y un latido por minuto para detectar huecos.
2. **Características**: convierte los eventos en números que entiende un modelo (frecuencia de cada combinación programa/ruta/proceso padre, tipo de ruta y de usuario, longitud de comandos, duración...).
3. **Detección**: entrena un Isolation Forest con el comportamiento normal, prueba con datos más recientes y lista los procesos más raros con el motivo.

## Roadmap

- [x] Telemetría de procesos, binarios y red
- [x] Primer modelo por proceso

## Requisitos

- Windows 10/11, Python 3.10+
- `pip install psutil pywin32 pandas scikit-learn joblib`
- El logger se ejecuta como administrador.

## Uso

```
python logger_wmi.py              # deja que acumule datos durante varios días
python modelo_procesos_v1.py      # entrena y lista los procesos más raros
```

## Límites conocidos

- Con pocos días de datos, el modelo genera muchas falsas alarmas.
- El logger llega tarde a los procesos de vida muy corta; la solución completa sería Sysmon.
- Un equipo infectado durante el entrenamiento aprendería esa infección como normal.
- Detecta rarezas, no maldad: un programa legítimo poco frecuente también puede saltar.

## Seguridad y privacidad
- La base de datos contiene líneas de comandos, rutas e IPs: **no se sube al repositorio**.

**Parte 1**

Rpta 1.1:
- Defecto 1: publicar no depende de validar
- Archivo: pipeline.yml, lienas 42–65
- Consecuencia: Se puede publicar código que no pasó las pruebas o el análisis

- Defecto 2: SonarCloud no hace cumplir el Quality Gate
- Archivo: pipeline.yml, lineas 31–38
- Consecuencia: El pipeline puede quedar verde aunque SonarCloud considere que la calidad no es aceptable

- Defecto 3: Se ejecutan dos pipelines para un mismo cambio mediante push + pull_request
- Archivo: pipeline.yml, lineas 3-6
- Consecuencia: Se duplican validaciones y consumo de CI

- Defecto 4: El artefacto se publica sobrescribiendo el anterior
- Archivo: pipeline.yml, lineas 61–65
- Consecuencia: Se pierde la trazabilidad de qué paquete corresponde a cada ejecución


Rpta 1.2:
Las mediciones fueron
Ejecución	Validar	Publicar	Total
    1	       54 s	    14 s	58 s
    2	       67 s	    16 s	72 s
    3	       51 s	    16 s	55 s

Los tiempos observados muestran que los dos jobs se ejecutaron en paralelo, no uno después del otro.
Si publicar dependiera de validar, el tiempo total sería aproximadamente la suma de ambos jobs, y las mediciones serían
Ejec 1: 54 s + 14 s ≈ 68 s  vs  58 s 
Ejec 2: 67 s + 16 s ≈ 83 s  vs  72 s
Ejec 3: 51 s + 16 s ≈ 67 s  vs  55 s

Estas mediciones realizadas están claramente influenciadas por el tiempo que más toma, que es el validar.
El job publicar no tiene needs: validar, por lo que GitHub ejecuta ambos jobs en paralelo. 
Esto reduce el tiempo total del pipeline, pero elimina la garantía de que el artefacto solo se 
publique después de que las validaciones hayan terminado correctamente.


Rpta 1.3:
El defecto que más directamente ataca una restricción del VSM es el defecto #1: publicar no depende de validar.
Como publicar no espera a que validar termine porque no tiene needs: validar, la garantía de flujo ordenado 
se pierde: el despliegue/publicación puede avanzar mientras la validación todavía está ejecutándose.

Rpta 1.4:
- Frecuencia de despliegue:     no, porque medirla implica llevar código a producción.
- Lead time:                    (alcanzable) sí, se puede reducir el tiempo entre el commit y tener el cambio listo para producción, aunque todavía no se despliegue.
- Tasa de fallas en cambios:    (alcanzable) en pruebas con fallos en entornos previos se puede medir esta tasa
- Tiempo de restauración:       tampoco, porque requiere un fallo/degradación de un servicio desplegado.

Métrica elegida: Lead time

Rpta 1.5:
Como no se realizará un despliegue a producción durante la intervención, no es posible medir directamente el Lead Time. 
Se utilizará como proxy la sgte métrica:
- la duración total del pipeline, desde el inicio de la ejecución hasta su finalización.


**Parte 2**

Ahora la ejecucion sale 76 s, URL: https://github.com/js10prog/INF384-lab2-20203648/actions/runs/34540260624


**Justificación de versión 1.3.0**

El repositorio es un fork donde la versión original (v1.2.0) corresponde al commit b7e44ce. Desde ese commit hasta HEAD, se los cambios fueron (esto se puede ver en el historial de commits):

Cambios desde v1.2.0:
- **feat(tarifas)**: agregar desglose de la tarifa calculada (commit 562e631) - NUEVA FUNCIÓN BACKWARDS-COMPATIBLE
- **fix(validaciones)**: colapsar espacios repetidos en el nombre del cliente (commit 6ba3804) - CORRECCIÓN DE BUG
- **fix(tarifas)**: redondear el costo por peso a dos decimales (commit b481aa6) - CORRECCIÓN DE BUG
- Cambios de infraestructura: pipeline, pruebas, documentación (sin impacto en versión)

Según el historial de commits, la función `desglose()` en `src/despachos/tarifas.py` es una nueva funcionalidad pública que no rompe compatibilidad, y según la semántica de versiones, esto justifica un incremento de versión MINOR.

Por lo tanto, la versión correcta es **1.3.0** (antes 1.2.0).


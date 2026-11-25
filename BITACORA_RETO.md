# Bitácora del reto — Servidor de pruebas

**Nombre:** Sara Gómez  
**Fecha:** 24 de noviembre de 2026  
**Rama de trabajo:** integrar-taller-s7

## A. Mientras escribía el guion (Parte 1)

| # | Qué pasó (error, sorpresa o algo que tuve que hacer a mano) | Qué hice |
|---|---|---|
| 1 | El guion utilizaba `useradd` para crear el usuario `registro`, pero no verificaba si ya existía. | Identifiqué que al ejecutar el guion por segunda vez podía detenerse en esa instrucción. |
| 2 | La instrucción `mkdir` podía generar un error si la carpeta `/opt/registro` ya existía. | Revisé el comportamiento de la instrucción y entendí por qué podía fallar en otra ejecución. |
| 3 | Algunas instrucciones agregaban información en lugar de comprobar si ya estaba creada, como el registro de visitas, el archivo `registro.env` y el aviso en `.bashrc`. | Identifiqué que podían quedar registros o líneas repetidas al ejecutar nuevamente el guion. |

## B. La prueba en el Codespace nuevo (Parte 2)

**Nota:** No fue posible recuperar el resultado individual de la primera y segunda ejecución del guion. Los siguientes datos corresponden a la revisión del estado actual del Codespace.

| Condición | Resultado observado |
|---|---|
| ¿El guion terminó? | No se pudo confirmar con el historial disponible. |
| 1. sqlite3 instalado | Sí. Versión 3.53.4. |
| 2. Usuario registro | Sí. UID 999 y grupo 985. |
| 3. Carpeta con los archivos | Sí. Se encontraron `app.py`, `crear_db.py`, `registro.db`, `registro.env` y `requirements.txt`. |
| 4. ¿Cuántas visitas de verificación? | 2 registros. |
| 5. ¿Cuántas líneas tiene registro.env? | 2 líneas. |
| 6. Dueño de /opt/registro | registro. |
| 7. ¿Cuántas veces sale el aviso al abrir una terminal nueva? | Se encontró 1 instrucción del aviso en `.bashrc`. |

**¿El servidor quedó en el estado del encargo después de la segunda corrida?**

No fue posible confirmar el resultado de la segunda corrida porque no se recuperó el registro de esa ejecución. Sin embargo, al revisar el Codespace se comprobó que existen SQLite, el usuario, la carpeta y los archivos requeridos.

## C. Después de leer la Parte 3

**¿Cuál de los tres resultados tuvo mi guion (se cayó / se acumuló / se calló), y en qué línea?**

El guion puede presentar los resultados de caída y acumulación. Se cae en la línea 13, donde intenta crear el usuario `registro` si este ya existe. Esto sucede porque se utiliza `set -e`, que hace que el guion se detenga cuando una instrucción devuelve un error.

También puede acumular información en las líneas 21, 24, 25 y 31, porque agregan registros o líneas cada vez que se ejecutan.

**Mini-ejercicio (idempotente o no, y por qué):**

1. `apt-get install -y sqlite3`: Sí, porque si el programa ya está instalado, no necesita instalarse nuevamente.
2. `useradd --system registro`: No, porque genera un error si el usuario ya existe.
3. `mkdir /opt/registro`: No, porque genera un error si la carpeta ya existe.
4. `cp app.py crear_db.py requirements.txt /opt/registro/`: Sí, porque copia los archivos y reemplaza los existentes si es necesario.
5. `INSERT INTO visitas`: No, porque agrega un nuevo registro cada vez que se ejecuta.
6. `echo ... >> registro.env`: No, porque agrega líneas al final del archivo y puede repetirlas.

**Una línea de mi provisionar.sh escrita como paso, y cómo se diría como estado:**

- Paso: `sudo mkdir /opt/registro`
- Estado: La carpeta `/opt/registro` existe en el servidor.

## Conclusión

Con este reto comprendí que no basta con escribir instrucciones para instalar y crear elementos en un servidor. También es importante pensar qué sucede cuando se ejecutan nuevamente. Un proceso idempotente permite alcanzar el estado deseado sin generar errores ni duplicar información.
# Registro de provisión — Fase 3

## Verificación de idempotencia

### 27-oct · primer playbook (3 tareas)

Nota: Las pruebas se realizaron sobre un servidor que ya había sido aprovisionado previamente con la versión ampliada del playbook.

1.ª corrida:(PLAY RECAP **************************************************************** localhost : ok=4 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0)

2.ª corrida:(PLAY RECAP **************************************************************** localhost : ok=4 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0)

Resultado: En ambas ejecuciones las tres tareas encontraron los recursos existentes y no realizaron cambios. El resumen incluye también `Gathering Facts`, por eso muestra cuatro actividades.

### 27-oct · playbook con condiciones 1, 3, 5, 6
1.ª corrida: (PLAY RECAP ****************************************************************
localhost : ok=9 changed=7 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0)
2.ª corrida: (PLAY RECAP ****************************************************************
localhost : ok=9 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0)

### Romper algo a propósito
Borré `/opt/registro/registro.env` a mano y corrí el playbook: (localhost : ok=9 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0)

## La visita de verificación

Qué pasó en dos corridas: Al ejecutar dos veces el mismo comando `INSERT` en SQLite, la primera consulta mostró 1 visita y la segunda mostró 2 visitas. Esto demuestra que se insertó un registro nuevo en cada ejecución.

Por qué pasa: Ansible no sabe qué hace un comando suelto; la tarea `command` vuelve a ejecutar la instrucción sin comprobar si el registro ya existe.

Cómo creo que se podría evitar (idea, no código): Consultar primero cuántas visitas de verificación existen y agregar una nueva solamente si no hay ninguna. También se podría utilizar una estrategia que identifique cada visita de forma única para evitar duplicados.

## Verificación de idempotencia — clave desde el secreto (taller del 17-nov)

1.ª corrida: changed=7 failed=0

2.ª corrida: changed=0 failed=0

Sin la clave (env -u): se detuvo en la tarea "La clave del servidor de pruebas está en el entorno"

cat de registro.env sin sudo: Permission denied

Rotación con read -s: changed=1

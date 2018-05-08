# Anexo II: Lenguaje - Secuencias de Ejecución

En este anexo veremos casos de ejecución de scripts y los resultados que deberian tener en cada caso a fin de ser mas explicitos en cuanto a la forma que se desea que se comporte el sistema.
En todos los casos se considera que el script es el único que ejecuta en el sistema.

## Ejemplo 1 - Script que finaliza OK:

Este es el mismo ejemplo que se encuentra en el Anexo I, es por esto que vamos a tomarlo como primer ejemplo.
Dado que el orden de las sentencias siempre es el correcto, el script finaliza sin inconvenientes.

```
GET deportes:futbol:messi
SET deportes:futbol:messi Lionel Messi
GET deportes:basquet:ginobili
SET deportes:basquet:ginobili Emanuel Ginobili
# Persiste el dato de la instancia y libera esta clave, no da error ya que la misma existe.
STORE	deportes:basquet:ginobili
```

La clave `deportes:futbol:messi` que se encontraba tomada previamente se libera al finalizar el ESI.

## Ejemplo 2 - Script que Aborta por error en el Script:

En este ejemplo se intenta hacer STORE de una variable no creada previamente con lo cual aborta el ESI y devuelve el error de `Error de Clave no Identificada`

```
GET deportes:futbol:messi
SET deportes:futbol:messi Lionel Messi
# Intento hacer store de una variable que no fue creada y por lo tanto aborta el ESI.
STORE   deportes:basquet:ginobili
# Estas instrucciones no se llegan a ejecutar.
GET deportes:basquet:ginobili
SET deportes:basquet:ginobili Emanuel Ginobili
```
Al igual que en el Ejemplo 1, al finalizar el ESI la clave `deportes:futbol:messi` se libera.

## Ejemplo 3 - Script que Aborta por desconexión de la instancia:

En este caso habra una desconexion en el medio de la ejecución y al no haber reconexion antes de que se intente ejecutar la sentencia el ESI aborta.
A fin de simplificar la comprension del ejemplo tendremos una instancia que contendra inicialmente las claves de deportes:futbol:messi y otra que contendra inicialmente las claves de deportes:basquet:ginobili

```
GET deportes:futbol:messi
SET deportes:futbol:messi Lionel Messi
GET deportes:basquet:ginobili

# En este momento se desconecta la instancia que tiene la clave deportes:futbol:messi
# Esta sentencia ejecuta correctamente porque la clave deportes:basquet:ginobili se encuentra en una instancia que no se desconecta
SET deportes:basquet:ginobili Emanuel Ginobili

# Esta sentencia se ejecuta correctamente porque el algoritmo de distribución envia la nueva clave a otra instancia
GET deportes:futbol:cristiano
SET deportes:futbol:cristiano CR7

# Al momento de ejecutar la siguiente instrucción el coordinador encuentra que la clave existia en el sistema pero se encuentra en una instancia que esta desconectada, por lo tanto el ESI aborta.
STORE deportes:futbol:messi
```
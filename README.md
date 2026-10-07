# Inscripción a materias

En una Universidad de un país latinoamericano se necesita un sistema que permita realizar la inscripción a materias. En esta universidad se tiene un único curso de cada materia y sólo se maneja información de un cuatrimestre (no se consideran inscripciones anteriores). En cambio, sí se debe conocer el historial de materias aprobadas de una persona estudiante, junto con la nota que obtuvo.

## Parte 1 Inscripción a carreras

Una persona estudiante se puede inscribir a una o más carreras.

Cada materia pertenece a una única carrera.  

### Requerimientos
Construir un modelo que permita resolver los siguientes requerimientos: 

1. Inscribir a una persona estudiante a una carrera. Una persona estudiante no puede inscribirse a una carrera ya inscripta.

2. Saber las carreras en las que se inscribió una persona estudiante

3. Saber si una materia pertenece a alguna de las carreras a las cuales está inscripta una persona estudiante 

### Casos de ejemplo

En los ejemplos incluiremos tres carreras: _Programación_, _Tecnicatura en Economía Social y solidaria(TUESS)_ y _Terapia Ocupacional (TO)_.

* Programación incluye estas materias: Elementos de Programación y lógica (epl), Matemática 1, Objetos 1, Objetos 2, Objetos 3, Bases de Datos(BD), Programación Concurrente (PConc)
* TUESS incluye Matemáticas para economía y administración (MEyA), Trabajo y sociedad (TyS), Economía, Desarrollo local.
* TO incluye Lectura y escritura académica (LEA), Ciencias de la Salud, Psicología, Antropología, Sociología.

Escenario
1. Hacer que la estudiante _Alex_ se anote en las carreras de Programación, TUESS.
2. Verificar que _Alex_ está inscripta en Programación y TUESS (y no en TO)
3. Verificar que Bases de Datos es una de las materias de las carreras a la cual está inscripta _Alex_
4. Verificar que Psicología no es una de las materias de las carreras a la cual está inscripta _Alex_
5. Intentar inscribir a _Alex_ a programación, no se puede porque ya está inscripta


## Parte 2 Historia académica

Cada vez que una persona estudiante termina de cursar una materia se registra en el sistema indicando su nota. La nota es un valor numérico entre 1 y 10.
Si la nota es entre 6 y 10 entonces está aprobada. 

### Requerimientos


1. Registrar que una persona estudiante finalizó la cursada de una materia indicando la nota obtenida. Para poder cumplir el requerimiento
es importante que la nota sea entre 1 y 10 y la persona no haya aprobado previamente la materia. También se debe validar que la materia pertenezca a alguna de las carreras a la cual está inscripta (resuelto en el punto anterior)

    > **Tip**: La nota con la que la persona aprobó una materia no puede ser un atributo de la persona (porque la persona tiene muchas notas) ni de la materia (porque una materia es cursada por muchas personas). Es necesario pensar una abstracción que modele la situación: "Alex cursó matemática 1 con nota 10"  

2. Conocer para una persona: si tiene o no aprobada una materia (su nota alcanza la calificación 6). Es decir, si alguna vez alcanzó esa nota en esa materia (la pudo haber cursado más de una vez). 

3. Conocer para una persona la cantidad de materias aprobadas y el promedio de materias aprobadas **en una determinada carrera**. 

4. Conocer para una persona la cantidad de materias aprobadas y el promedio de las materias aprobadas **en todas sus carreras**. 

5. Saber para una persona todas las cursadas de una materia en el orden en que fueron registradas.

### Casos de prueba
Teniendo en cuenta las inscripciones a carreras del punto anterior:

1. Registrar que Alex cursó en este orden:
 - Matemática 1 con nota 2. 
 - Objetos 1 con nota 10.
 - Economía con nota 6
 - Trabajo y Sociedad (TyS) con nota 8
 - Matemática 1 con nota 8.
 - Bases de Datos con nota 2.
2. Intentar registrar que Alex cursó Matemática 1 con nota 7. No se debería poder porque ya está aprobada
3. Intentar registrar que Alex cursó Bases de Datos con nota 12. No se debería poder porque no es una nota válida
4. Verificar que Alex tiene aprobada Mate1
5. Verificar que Alex no tiene aprobada BD.
6. Verificar que su promedio en _Programación_ sea 9. La cuenta es (10 + 8) / 2. (No se tienen en cuenta el 2 de mate1 ni el 2 de Bases de Datos)
7. Verificar que la cantidad de materias aprobadas en programación es 2.
8. Verificar que el promedio de _TUESS_ es 7. La cuenta es (6 + 8) / 2
9. Al intentar ver el promedio de _TO_ no se puede, porque no está inscripta en esa carrera
10. Inscribir a Alex a _TO_, Al intentar ver el promedio de TO no se puede porque no tiene ninguna materia aprobada 
11. Verificar que el promedio contando todas sus carreras es 8. La cuenta es (10 + 6 + 8 + 8) / 4.
12. Verificar que la historia de cursadas de Alex para mate 1 es una cursada con 2, y luego otra con 8.

    > **Tip**: Usar un `method initialize()` en el describe para no repetir el escenario inicial con el test del punto anterior


## Parte 3 Inscripciones a las materias

El sistema permite administrar las inscripciones a las materias para el cuatrimestre actual. El objetivo de este proceso es obtener para cada
materia la colección de estudiantes que se inscribieron.

Las materias de las carreras pueden establecer _requisitos_ para aceptar una persona estudiante. Los
_requisitos_ son otras materias que deberían haber cursado previamente:

 > **Tip**  Ojo!  Armar el grafo de requisitos puede no ser tan trivial.

Si se elije configurar los requisitos en la instanciación usando un atributo constante en Materia:
``` 
class Materia {
    const requisitos = #{}
}

const obj1 = new Materia()
const mate1 = new Materia()
const obj2 = new Materia(requisitos=#{obj1, mate1})
```
hay que tener cuidado que obj1 y mate1 también se hayan instanciado antes. Esas materias también podrían necesitar requisitos que son otras materias también deben instanciarse antes!. Esta estrategia  funciona porque las dependencias, en este caso concreto, forman un árbol: siempre se pueden instanciar los requisitos antes que la materia que los necesita. Hay que tener mucho cuidado del orden en que se instancia.

Pero en muchas situaciones parecidas, las dependencias entre instancias pueden ser cíclicas: A necesita B , B necesita a C y C necesita A. 
En un caso así, no es posible configurar en la instanciación. Una estrategia más simple en la cual no es necesario pensar el orden
en que se instancian es dividir la construcción en dos fases: primero se instancian todas las materias y se usa un atributo variable sin pasarle un valor inicial en el new. Luego se configuran los requisitos con un setter.

```

class Materia {
    var requisitos = #{}
    method requisitos(_values) {requisitos = _values}
}

const obj2 = new Materia()
const obj1 = new Materia()
const mate1 = new Materia()

obj2.requisitos(#{obj1, mate1})
``` 

### Requerimientos

1. Determinar si una persona estudiante _e_ puede inscribirse a una materia _m_. Para esto se deben cumplir cuatro condiciones: 

    - _m_ debe corresponder a alguna de las carreras en la que está inscripta _e_, 
    - _e_ no puede haber aprobado _m_ previamente, 
    - _e_ no debe estar ya inscripta en _m_,
    - _e_ debe tener aprobadas todas las materias que se declaran como _requisitos_ de _m_.  
    

2. Inscribir una persona _e_ a una materia _m_, validando las condiciones de inscripción de la materia. 

3. Materias habilitadas en una carrera: dada una carrera y un estudiante, conocer todas las materias de esa carrera a las que se puede inscribir el estudiante, teniendo en cuenta todas las restricciones del punto 1.



### Casos de prueba
Se utiliza el escenario del caso de prueba anterior, incluyendo las carreras inscriptas y materias aprobadas 
que se mencionan en el punto 1

Además, se determinan los siguientes requisitos:

* Los requisitos de Obj2 son Obj1 y Mate1.
* Los requisitos de Obj3 son Obj2 y BD.
* Los requisitos de PConc son Obj1 y BD.
* Desarrollo local tiene como único requisito a Matemática para economía y administración (MEyA).

Andy es una persona estudiante que está inscripta en la carrera de Programación y tiene aprobado obj1 con 10 y mate1 con 10.

1. Verificar que Alex podría inscribirse a Objetos 2, pues tiene aprobadas Objetos 1 y Matemática 1, además de estar en la carrera de programación.
2. Verificar que en la carrera de Programación, las materias a las que puede inscribirse Alex son epl, obj2 y bd 
3. Verificar que en la carrera de TUESS, la única materia en que se puede inscribirse Alex es MEyA.
4. Verificar que Alex no podría inscribirse a psicología. (No está inscripta en la carrera TO)
5. Intentar inscribir a Alex en psicología, pero no se puede.
6. Verificar que Alex no podría inscribirse en economía. (ya la tiene aprobada)
7. Intentar inscribir a Alex en economía, pero no se puede.
8. Verificar que Alex no podría inscribirse en Desarrollo Local. (no tiene aprobado MEyA)
9. Intentar inscribir a Alex en Desarrollo Local, pero no se puede.
10. Realizar la inscripción de Alex a Obj2.
11. Intentar inscribir a Alex nuevamente en Obj2, no se puede porque ya está inscripta  
12. Realizar la inscripción de Andy a Obj2.
13. Verificar que las personas estudiantes inscriptas en Obj2 son Alex y Andy


## Parte 4: Distintos tipos de requisitos

Agregar al modelo la capacidad de expresar otros tipos de requisitos (no sólo un conjunto de materias previamente aprobadas) a la hora de verificar la inscripción a una materia. Otras opciones son:

   * Requerir una cantidad de créditos. Esto implica que cada materia conozca la cantidad de _créditos_ que otorga. Por ejemplo, para inscribirse en esta materia es necesario haber acumulado al menos 30 créditos en materias aprobadas de la carrera.

   * Requerir todas las materias del año anterior. Para esto es necesario poder indicar a qué año pertenece cada materia. Por ejemplo, para cursar Obj3, que es una materia de tercer año, es necesario haber aprobado todas las materias del segundo año de la carrera correspondiente

   * No requerir nada. Es decir no tener requerimientos. Por ejemplo epl es una de las primeras materias y por lo tanto no tiene ninguna condición especial, cualquiera puede cursarla.

Cada materia tiene sólo uno de estos tipos de requisitos: correlativas, créditos, por año o nada. 

> **Tip**: Encontrar una abstracción/tipo polimórfico que permita encapsular la responsabilidad de decidir si un estudiante cumple los requisitos. Luego hacer que cada materia conozca a un objeto (ya sea autodefinido o instancia de una clase) que lo implemente. La materia colabora con uno de estos objetos a la hora de saber si una persona estudiante puede o no inscribirse.

### Casos de prueba

#### Correlativas y Sin requisitos
    Los casos de prueba desarrollados en los puntos anteriores deberían seguir funcionando usando las opciones "sinRequisitos" y "correlativas"
    según corresponda.
    El año y créditos de las materias son indistintos para estas pruebas, pudiendo usarse cualquier valor por defecto.

#### Créditos y Año


    Ajustar las materias de la carrera TO con la siguiente información.
    - Lectura y escritura académica (LEA): año 1, créditos 8, no tiene requisitos
    - Ciencias de la Salud, año 1, créditos 8, no tiene requisitos
    - Psicología, año 2, créditos 4, debe haber aprobado el año anterior completo
    - Antropología, año 2, créditos 4, debe tener 8 créditos aprobados
    - Sociología, año 3, créditos 8, debe tener 10 créditos aprobados

    Ajustar las materias de programación con estos datos:
    - obj1: año 1 créditos 8
    - mate1: año 1 créditos 8
    - Bases de Datos: año 1 créditos 8
    
    Inscribir a Andy en la carrera TO. Además Andy debería estar inscripta, al igual que en un test anterior, en programación,
    con las materias obj1 y mate1 cursadas ambas con nota 10.

    - verificar que las únicas materias  a las que se puede inscribir Andy en TO son LEA y Ciencias de la Salud
    - registrar que Andy cursó LEA con nota 10 (Andy tiene 8 créditos en TO, no aprobó aún 1er año de TO)
    - verificar que las únicas materias a las que se puede inscribir Andy en TO son Ciencias de la Salud y Antropología
    - registrar que Andy cursó Antropología con nota 10 (Andy tiene 12 créditos en TO, no aprobó aún 1er año de TO)
    - verificar que las únicas materias a las que se puede inscribir Andy en TO son Ciencias de la Salud y Sociología
    - registrar que Andy cursó Ciencias de la Salud con nota 10, (Andy tiene 20 créditos en TO, ya aprobó primer año de TO)
    - verificar que las únicas materias a las que puede inscribir Andy en TO son Psicología y Sociología
    - inscribir a Andy a Psicología
    - inscribir a Andy a Sociología
    - verificar que la lista de estudiantes inscriptos a Psicología está compuesta solo por Andy
    - verificar que la lista de estudiantes de Sociología está compuesta solo por Andy
    
## Parte 5: Reflexiones sobre la solución
    - Realizar un diagrama dinámico que muestre la relación entre Alex, sus materias cursadas (con la nota) e inscriptas de acuerdo al caso de prueba del punto 3
    - Realizar un diagrama estático que muestre los tipos/clases involucrados que se ven en el diagrama dinámico del punto anterior
    - Mostrar un diagrama estático que refleje el polimorfismo usado en el punto 4 (requisitos de la materia). Aclarar en texto cuál es el mensaje polimórfico, quién es el emisor del mensaje y cuáles son los objetos autodefinidos o clases que lo implementan.


# BONUS

Estos requerimientos son opcionales. Asegurarse de que todo lo anterior funcione con sus test en verde antes de 
trabajar sobre esto.
Recomendación: trabajar en un feature branch separado de main/master

## Bonus 1: Listas de espera

Extender el modelo para considerar que cada materia tiene un “cupo”, es decir, una cantidad máxima de estudiantes que se pueden inscribir. Para manejar el exceso en los cupos, las materias tienen una lista de espera, de estudiantes que quisieran cursar pero no tienen lugar. Entonces, como resultado de la inscripción, la persona puede quedar confirmada, o en lista de espera (ambas situaciones, si cumple con las condiciones del punto 1 de la parte 3). No se requiere que el sistema conteste nada con respecto al resultado de la inscripción. 

### Requerimientos

1. Poder dar de baja a un estudiante de una materia. En caso de haber estudiantes en lista de espera, el primer estudiante de esa lista debe obtener su lugar en la materia.

2. Brindar resultados de inscripción, específicamente:

    * Las personas estudiantes inscriptas a una materia dada.
    * Las personas estudiantes en lista de espera para una materia dada.

3. Brindar información útil para una persona estudiante, específicamente: las materias en las que está inscripta, las materias en las que quedó en lista de espera. 

### Casos de prueba 
Suponiendo que

* Luisa, Romina, Alicia y Ana están cursando Programación.
* Alex cursa programación y tiene aprobadas las mismas materias de los tests anteriores (mate 1 con 8 y obj1 con 10)
* Andy cursa programación y tiene aprobadas las mismas materias de los tests anteriores (mate 1 con 10 y obj1 con 10)
* Luisa cursó obj1 con 8 y mate1 con 7
* Romina cursó obj1 con 6 y mate1 con 6
* Alicia cursó obj1 con 10 y mate1 con 9 y epl con 6
* Ana solo cursó obj1 con 10

* Obj2 tiene cupo para 2 estudiantes, para el resto de las materias usar un default de 30 estudiantes
* Epl otorga 8 créditos

Realizar la siguiente secuencia

* Inscribir a Alex en obj2, queda confirmada, la lista de espera está vacía
* Inscribir a Luisa en obj2. queda confirmada, los inscriptos son Alex y Luisa, la lista de espera está vacía
* Inscribir a Romina, queda en espera pues ya está el cupo de 2 lleno, los inscriptos son Alex y Luisa, en la lista de espera solo está Romina 
* Intentar inscribir a Ana, no se puede porque no cumple los requisitos, los inscriptos son Alex y Luisa, en la lista de espera solo está Romina
* Inscribir a Andy, queda en lista de espera, los inscriptos son Alex y Luisa, en la lista de espera solo está Romina y Andy
* Inscribir a Alicia, queda en lista de espera, los inscriptos son Alex y Luisa, en la lista de espera solo está Romina, Andy, Alicia

Si ahora se da de baja Luisa, la que ocupa esa posición debe ser Romina por ser la primera de la lista de espera. Los confirmados
son Alex y Romina, la de espera Andy y Alicia.


## Bonus 2: Gestión de la lista de espera

Incorpora al modelo la capacidad de configurar diferentes _estrategias para gestionar la lista de espera_ en cada materia, a saber:

- Por orden de llegada: si te querés inscribir y no hay lugar vas a la lista de espera por llegar último
- Elitista: entran los que tengan mejor promedio.
- Por grado de avance: Inscribimos al estudiante con mayor cantidad de créditos de la carrera.


### Casos de prueba 
    - si obj2 tiene configurado "orden de llegada", se comporta como en el punto anterior
    - si obj2 tiene configurado "elitista", en lugar de reemplazar a Luisa por Romina, la que ocupa ese lugar es Andy pues tiene mejor promedio
    - si obj2 tiene configurado "avance", en lugar de reemplazar a Luisa por Romina, la que ocupa ese lugar es Alicia pues tiene más créditos
    

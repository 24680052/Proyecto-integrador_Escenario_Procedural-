# Proyecto integrador U1
# Escenario procedural 

Escenario Procedural con Cámara Animada en Blender

Este proyecto consiste en la generación automática de un escenario tridimensional procedural en **Blender**, complementado con una animación de cámara que recorre el camino generado.

A partir de un script en Python, se crea un pasillo curvo tipo puente compuesto por suelos, paredes alternadas y soportes estructurales. Cada sección del camino se calcula matemáticamente para producir un trazado fluido y orgánico. Luego, se genera una trayectoria de posiciones que la cámara utiliza para animar un recorrido suave a lo largo del puente.

## Objetivo del proyecto

- El propósito principal es demostrar la capacidad de utilizar programación en Blender para:
- Construir escenarios de forma procedural, sin modelado manual.
- Integrar materiales, geometría y estructuras repetitivas mediante código.
- Automatizar la movimentación de cámara siguiendo puntos generados dinámicamente.
- Crear una escena lista para renderizado o ampliación artística.
## ¿Qué genera el script?
Materiales procedurales básicos

Se crean automáticamente materiales para:
- Suelo
- Paredes alternadas (una más oscura y otra con detalles)
- Soportes estructurales

Pasillo curvado tipo puente
El pasillo se genera usando funciones trigonométricas:
Un radio de curvatura que define el camino circular.
Un suelo por cada sección.
Paredes izquierda y derecha con variación alternada de altura y color.
Soportes cilíndricos verticales bajo cada módulo.

Iluminación automática
Se añade una luz puntual potente para iluminar toda la trayectoria.

🎥 Animación automática de cámara
La cámara se crea y se enlaza a la escena.

Luego:
Sigue punto por punto la curva generada.
Cada posición se convierte en un fotograma clave.
La cámara mantiene una orientación fija con un ángulo cenital para apreciar la geometría del puente.
La animación se extiende durante tantos fotogramas como secciones existan.
Con esto, el resultado es una recorrido cinemático completo por el escenario procedural.

## Características destacadas

100% generado por código, sin intervención manual.
Curvas suaves definidas matemáticamente para un recorrido natural.
Variación visual automática en las paredes del pasillo.
Movimiento de cámara continuo, ideal para renders o videos demostrativos.
Estructura modular: puedes ajustar materiales, tamaños, curva o animación fácilmente.O

# PASO A PASO PARA EJECUTAR TU SCRIPT EN BLENDER
## 1. Abrir Blender

Ejecuta Blender normalmente.
Cuando cargue, estarás en la interfaz principal con el cubo inicial.

## 2. Cambiar al Editor de Texto

En la parte superior izquierda de cualquier panel, haz clic en el menú donde dice "Layout", "3D Viewport", o similar.
Cambia el tipo de ventana a Text Editor (Editor de Texto).
Ahora verás un panel para escribir o cargar archivos .py.

## 3. Crear o abrir el archivo del script

Tienes dos opciones:

 ### Opción A: pegar el código directamente

Pulsa New.
Vacía el contenido si aparece algo.
Copia y pega todo el script completo que me diste.

### Opción B: abrir un archivo .py

Si lo guardaste antes:
Pulsa Open.
Selecciona tu archivo Python.
Se cargará en el editor.

### 4. Activar permisos de ejecución de scripts (si es la primera vez)

Blender a veces bloquea código externo por seguridad.
Ve a Edit → Preferences.
Ve a Interface o Save & Load (según versión).
Activa la opción:
`Auto Run Python Scripts`

Cierra la ventana de preferencias.

### 5. Asegurarte de estar en una escena limpia (opcional)

Tu script ya elimina todo, pero si quieres:
Abre una nueva escena con File → New → General.

### 6. Ejecutar el código

En el Text Editor, haz clic en:
`Run Script`
`(o presiona Alt + P)`

Blender ejecutará el código inmediatamente.

### 7. ¿Qué debe ocurrir después de ejecutarlo?

Se borran todos los objetos de la escena.
Aparecen:

El puente curvo.
Suelos, paredes y soportes.
Una luz superior.
Una cámara colocada en la escena.

### La cámara ya tiene animación a lo largo del camino generado.

Puedes comprobarlo:
Cambia a Animation Workspace o Timeline.
Presiona Spacebar para reproducir.
Verás la cámara recorriendo el puente.

### 8. Ver la animación desde la cámara

En la vista 3D, presiona Numpad 0.
O activa View → Cameras → Active Camera.
Así verás exactamente lo que la cámara está grabando.

### 9. (Opcional) Renderizar la animación

Ve a Render Properties (icono de cámara).
Configura:
resolución
fotogramas por segundo
formato de salida

Luego ve a:
Render → Render Animation

Blender generará el video final.

# Codigo completo

`import bpy
import math

def crear_material(nombre, color_rgb):
    mat = bpy.data.materials.new(name=nombre)
    mat.diffuse_color = (*color_rgb, 1.0)
    return mat

def generar_puente():

    bpy.ops.object.select_all(action='SELECT')
    bpy.ops.object.delete()

    mat_pared_a = crear_material("ParedOscura", (0.1, 0.1, 0.1))
    mat_pared_b = crear_material("ParedDetalle", (0.8, 0.2, 0.0))
    mat_suelo = crear_material("Suelo", (0.8, 0.8, 0.8))
    mat_soporte = crear_material("Soporte", (0.2, 0.2, 0.2))

    largo_pasillo = 20
    radio_curva = 20
    ancho_pasillo = 4
    altura_puente = 3

    posiciones = []

    for i in range(largo_pasillo):

        angulo = i * 0.12

        x_centro = math.cos(angulo) * radio_curva
        y_centro = math.sin(angulo) * radio_curva

        rotacion = angulo

        posiciones.append((x_centro, y_centro, altura_puente + 2))

        bpy.ops.mesh.primitive_cube_add(size=2,
            location=(x_centro, y_centro, altura_puente))
        suelo = bpy.context.active_object
        suelo.scale.x = ancho_pasillo
        suelo.scale.y = 1
        suelo.scale.z = 0.2
        suelo.rotation_euler[2] = rotacion
        suelo.data.materials.append(mat_suelo)

        bpy.ops.mesh.primitive_cube_add(size=2,
            location=(x_centro - math.cos(angulo)*ancho_pasillo,
                      y_centro - math.sin(angulo)*ancho_pasillo,
                      altura_puente + 1))
        pared_izq = bpy.context.active_object
        pared_izq.rotation_euler[2] = rotacion

        if i % 2 == 0:
            pared_izq.data.materials.append(mat_pared_a)
        else:
            pared_izq.data.materials.append(mat_pared_b)
            pared_izq.scale.z = 1.5

        bpy.ops.mesh.primitive_cube_add(size=2,
            location=(x_centro + math.cos(angulo)*ancho_pasillo,
                      y_centro + math.sin(angulo)*ancho_pasillo,
                      altura_puente + 1))
        pared_der = bpy.context.active_object
        pared_der.rotation_euler[2] = rotacion
        pared_der.data.materials.append(mat_pared_a)

        bpy.ops.mesh.primitive_cylinder_add(radius=0.3,
            depth=altura_puente,
            location=(x_centro, y_centro, altura_puente/2))
        soporte = bpy.context.active_object
        soporte.data.materials.append(mat_soporte)

    bpy.ops.object.light_add(type='POINT', location=(0, 0, 15))
    luz = bpy.context.active_object
    luz.data.energy = 1500

    crear_animacion(posiciones)

def crear_animacion(posiciones):

    scene = bpy.context.scene
    scene.frame_start = 1
    scene.frame_end = len(posiciones) * 10

    cam_data = bpy.data.cameras.new("Camara")
    cam = bpy.data.objects.new("Camara", cam_data)
    bpy.context.collection.objects.link(cam)
    scene.camera = cam

    frame = 1

    for pos in posiciones:
        cam.location = pos
        cam.rotation_euler = (math.radians(75), 0, math.radians(45))
        cam.keyframe_insert(data_path="location", frame=frame)
        cam.keyframe_insert(data_path="rotation_euler", frame=frame)
        frame += 10


generar_puente() 
`

# EXPLICACIÓN COMPLETA DEL CÓDIGO
## 1. Importación de módulos
`import bpy
import math
`
bpy → Es la API de Blender que permite crear y modificar objetos mediante Python.

math → Se utiliza para funciones matemáticas: seno, coseno, radianes, etc.

## 2. Función crear_material(nombre, color_rgb)
`def crear_material(nombre, color_rgb):
    mat = bpy.data.materials.new(name=nombre)
    mat.diffuse_color = (*color_rgb, 1.0)
    return mat`
    
¿Qué hace?

Crea un material nuevo con el nombre indicado.
Le asigna un color difuso usando el RGB que recibe.
Añade un cuarto valor 1.0, que es la opacidad (alfa).
Devuelve ese material para aplicarlo a objetos.

Se usa para evitar repetir mucho código al crear cada material.

## 3. Función generar_puente()

Esta es la función principal que crea todo el escenario.

### 3.1 Limpia la escena
`bpy.ops.object.select_all(action='SELECT')
bpy.ops.object.delete()`

Blender selecciona todo y lo elimina.
Así el script parte de una escena vacía.

### 3.2 Crea materiales reutilizables
`mat_pared_a = crear_material("ParedOscura", (0.1, 0.1, 0.1))
mat_pared_b = crear_material("ParedDetalle", (0.8, 0.2, 0.0))
mat_suelo = crear_material("Suelo", (0.8, 0.8, 0.8))
mat_soporte = crear_material("Soporte", (0.2, 0.2, 0.2))`

Cuatro materiales:

Pared oscura
Pared alternada con color más vivo
Suelo claro
Soporte gris

### 3.3 Parámetros del puente
`largo_pasillo = 20
radio_curva = 20
ancho_pasillo = 4
altura_puente = 3`

largo_pasillo → cuántos módulos genera.
radio_curva → determina lo curva que será la trayectoria.
ancho_pasillo → separación entre paredes.
altura_puente → altura del piso.

### 3.4 Lista de posiciones para animar la cámara
`posiciones = []`

Se guardará un punto por cada módulo para que la cámara recorra esa ruta.

## 4. Bucle principal que genera cada módulo del puente
`for i in range(largo_pasillo):`

El puente se construye sección por sección, en un ciclo que se repite 20 veces.

### 4.1 Calcular la curva del puente
`angulo = i * 0.12
x_centro = math.cos(angulo) * radio_curva
y_centro = math.sin(angulo) * radio_curva
rotacion = angulo`

Cada sección se ubica según una curva circular:
cos controla la posición horizontal X.
sin controla la posición vertical Y.

El “camino” gira a medida que incrementa el ángulo.
La rotación del módulo coincide con la dirección del camino.

### 4.2 Guardar posición para la cámara
`posiciones.append((x_centro, y_centro, altura_puente + 2))`

La cámara se moverá un poco sobre el puente (por eso +2).

## 5. Crear el piso
`bpy.ops.mesh.primitive_cube_add(size=2,
            location=(x_centro, y_centro, altura_puente))
suelo = bpy.context.active_object
suelo.scale.x = ancho_pasillo
suelo.scale.y = 1
suelo.scale.z = 0.2
suelo.rotation_euler[2] = rotacion
suelo.data.materials.append(mat_suelo)`

Crea un cubo.

Lo aplana para que sea un suelo.
Lo rota para que siga la curva.
Aplica el material del piso.

## 6. Crear la pared izquierda
`bpy.ops.mesh.primitive_cube_add(size=2,
            location=(x_centro - math.cos(angulo)*ancho_pasillo,
                      y_centro - math.sin(angulo)*ancho_pasillo,
                      altura_puente + 1))`

Se coloca a la izquierda del pasillo usando cos/sin para desplazarla hacia afuera.

Luego:

`pared_izq.rotation_euler[2] = rotacion`

Se rota para seguir el camino.

### 6.1 Alternancia de materiales y alturas
`if i % 2 == 0:
    pared_izq.data.materials.append(mat_pared_a)
else:
    pared_izq.data.materials.append(mat_pared_b)
    pared_izq.scale.z = 1.5`

Una pared oscura cada dos módulos.
Una pared naranja más alta en los módulos intermedios.
Este efecto le da variación visual al puente.

## 7. Crear la pared derecha

Muy similar a la izquierda, pero sin alternancia:

`bpy.ops.mesh.primitive_cube_add(size=2,
            location=(x_centro + math.cos(angulo)*ancho_pasillo,
                      y_centro + math.sin(angulo)*ancho_pasillo,
                      altura_puente + 1))`

Siempre usa el material oscuro:

`pared_der.data.materials.append(mat_pared_a)`

Y se rota igual que las demás.

## 8. Crear soporte del puente
`bpy.ops.mesh.primitive_cylinder_add(radius=0.3,
            depth=altura_puente,
            location=(x_centro, y_centro, altura_puente/2))
soporte.data.materials.append(mat_soporte)`

Crea un cilindro vertical bajo cada sección, actuando como columna estructural.

## 9. Añadir una luz a la escena
`bpy.ops.object.light_add(type='POINT', location=(0, 0, 15))
luz.data.energy = 1500`

Crea una luz de tipo punto.
La coloca arriba del puente.
Le da mucha intensidad para iluminar toda la animación.

## 10. Crear la animación de la cámara
`crear_animacion(posiciones)`

Se llama a la siguiente función usando las posiciones generadas.

## 11. Función crear_animacion(posiciones)

Esta función mueve la cámara siguiendo el camino.

### 11.1 Configuración del rango de animación
`scene.frame_start = 1
scene.frame_end = len(posiciones) * 10`

Cada posición se convierte en un keyframe cada 10 fotogramas.
La duración depende de cuántos puntos tenga el camino.

### 11.2 Creación y configuración de la cámara
`cam_data = bpy.data.cameras.new("Camara")
cam = bpy.data.objects.new("Camara", cam_data)
scene.camera = cam`

Crea el objeto cámara.
La añade a la escena.
La define como cámara activa.

### 11.3 Insertar keyframes (animación)
`for pos in posiciones:
    cam.location = pos
    cam.rotation_euler = (math.radians(75), 0, math.radians(45))
    cam.keyframe_insert(data_path="location", frame=frame)
    cam.keyframe_insert(data_path="rotation_euler", frame=frame)
    frame += 10`

Para cada punto del camino:
Mueve la cámara a la posición.
Le asigna una rotación fija (mirando hacia adelante y abajo).
Inserta un fotograma clave de posición y rotación.
Avanza 10 fotogramas.

Así se crea una animación suave desde el inicio hasta el final del puente.

## 12. Llamar a generar_puente()
`generar_puente()`

Esto ejecuta todo el proceso:

Limpiar escena
Generar puente
Generar materiales
Animar cámara

Para ejecutar se realizan los siguientes pasos:
darle click al boton de **Run**
<img width="42" height="30" alt="{2D148322-E721-4CC6-AEA5-4B0A1DA28163}" src="https://github.com/user-attachments/assets/c5fbac1f-6d94-43e2-af3c-193157ca61a2" />

Dar click a animar camara 
<img width="160" height="80" alt="{029ACA77-01E5-449B-826C-DA4FCF3D76DD}" src="https://github.com/user-attachments/assets/d9353ac5-a321-4de7-866e-c46c4969fcb3" />

Empezara a animarse la camara 
<img width="495" height="378" alt="{6E17963B-328F-4E4D-95F3-DDF5CF9B4A95}" src="https://github.com/user-attachments/assets/5b35fc14-68fa-4db6-b9ee-2c23c9ef3bb6" />



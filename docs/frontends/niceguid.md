# Nicegui frontend

Nicegui ofrece un frontend simple, funcional y rápido. Está especialmente pensado para el desarrollo de aplicaciones de IoT e industriales. Ofrece las siguientes funcionalidades:
- Gráficas en tiempo real basadas en javascript.
- Un gran catálogo de widgets predefinidos. 
- Integración con FastAPI.
- Ejemplos para retransmisión de imágenes capturas de webcams. 
- Programación en Python (más reconocible para los alumnos que javascript). 


## Instalación

Según la página oficial:

[https://nicegui.io/](https://nicegui.io/)

Se instala directamente con pip:
```bash
pip install nicegui
```

## Hola mundo

Se prueba un primer ejemplo "standalone" para comprobar el correcto funcionamiento del frontend. 

```python
from nicegui import ui

ui.label('Hello NiceGUI!')

ui.run()
```
Este ejemplo crea un servidor web en localhots en el puerto 8080 y sirve un div con un texto. 

## Prueba con los primeros widgets

El siguiente ejemplo ya integra algún elemento interactivo

```python

from nicegui import ui
from nicegui.events import ValueChangeEventArguments

def show(event: ValueChangeEventArguments):
    name = type(event.sender).__name__
    ui.notify(f'{name}: {event.value}')

ui.button('Button', on_click=lambda: ui.notify('Click'))
with ui.row():
    ui.checkbox('Checkbox', on_change=show)
    ui.switch('Switch', on_change=show)
ui.radio(['A', 'B', 'C'], value='A', on_change=show).props('inline')
with ui.row():
    ui.input('Text input', on_change=show)
    ui.select(['One', 'Two'], value='One', on_change=show)
ui.link('And many more...', '/documentation').classes('mt-8')


v = ui.checkbox('visible', value=True)
with ui.column().bind_visibility_from(v, 'value'):
    ui.slider(min=1, max=3).bind_value(demo, 'number')
    ui.toggle({1: 'A', 2: 'B', 3: 'C'}).bind_value(demo, 'number')
    ui.number().bind_value(demo, 'number')

ui.run()
```

## Animaciones SVG

El frontend renderiza en una imagen SVG creada a partir de Inkscape (software libre) cuyas propiedades (rotación) se modifican con eventos de un slider. Esto resulta en una animación viva e interactiva. 

Esto abre muchas posibilidades interesantes: animaciones tipo SCADA, representaciones reactivas de las recetas, incluir logos, etc.

Solo hay que realizar los siguientes pasos:
- Diseñar/descargar la imagen a animar. Hay repositorios de SVG con muchos símbolos/formas  (incluidos símbolos industriales):https://www.opto22.com/support/resources-tools/demos/svg-image-library.

- Identificar en el código SVG la propiedad a modificar y asignarla a una variable.
Crear una callback asociada a un evento de un widget (slider, botón, incluso una callback periódica o de un evento del BACKEND). 
- En la callback modificar el valor de la propiedad para crear la animación!


Incluso se podrían crear clases tipo tanque, cinta, cilindro como si fueran widgets propios de NiceGUI para utilizar en múltiples proyectos.

Código de ejemplo:

```python
from nicegui import ui
from nicegui.events import ValueChangeEventArguments




def giveSVG(rot=90):
    return f'''

    <svg
    width="200.839402mm"
    height="108.58428mm"
    viewBox="0 0 11.839402 108.58428"
    version="1.1"
    id="svg1"
    xmlns:inkscape="http://www.inkscape.org/namespaces/inkscape"
    xmlns:sodipodi="http://sodipodi.sourceforge.net/DTD/sodipodi-0.dtd"
    xmlns="http://www.w3.org/2000/svg"
    xmlns:svg="http://www.w3.org/2000/svg">
    
    <defs
        id="defs1" />
    <g
        inkscape:label="Capa 1"
        inkscape:groupmode="layer"
        id="layer1"
        transform="translate(-80.84625,12.515943)">
        <g
        id="g2"
        inkscape:export-filename="g2.svg"
        inkscape:export-xdpi="96"
        inkscape:export-ydpi="96"
        transform="rotate({rot},86.765945,41.776196)">
        <rect
            style="fill:#000000;stroke-width:0.264583"
            id="rect1"
            width="108.58428"
            height="3.7209566"
            x="32.473804"
            y="40.930519"
            inkscape:export-filename="rect1.svg"
            inkscape:export-xdpi="96"
            inkscape:export-ydpi="96" />
        <ellipse
            style="fill:#000000;stroke-width:0.264583"
            id="path1"
            cx="88.288155"
            cy="41.776196"
            rx="7.7801819"
            ry="5.919704" />
        </g>
    </g>
    </svg>'''

svg_plot = None
angle_num = None


def change_rot(event: ValueChangeEventArguments):
    svg_plot.content = giveSVG(event.value)
    angle_num.value = event.value

with ui.column().style("width: 300px"):
    svg_plot = ui.html(giveSVG(0))

    ui.label("Ángulo de rotación")
    ui.slider(min=0, max=180,step = 5,value=90,on_change=change_rot)#.bind_value(rot, 'number')
    angle_num = ui.number(label="angle",value=90,on_change=change_rot)



ui.run()
```

[Página principal](../../README.md)
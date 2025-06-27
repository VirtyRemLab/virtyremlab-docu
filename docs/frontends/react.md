# React 

El frontend react proporciona las siguientes funcionalidades:

- Contenido modular.
- Gran comunidad y frameworks.
- Permite integrar contenido usando markdown.
- Componentes para mostrar información en tiempo real. Charts.js, websockets.
- Integración con FastAPI.


## Configuración

Para el desarrollo de la aplicación de react, su compilación y su despligue es recomendable un entorno de desarrollo. En este caso se ha seleccionado [Vite](https://vite.dev) como entorno de desarrollo.


Para iniciar el entorno de desarrollo y crear el esqueleto de la página:

``` bashrc
npm create vite@latest my-app -- --template react
```

El framework de desarrollo te crea un directorio de trabajo. Con npm tenemos que instalar las dependencias del paquete. El gestor de paquetes npm lee el archivo packages.json e instala todas las dependencias. 


```bashrc
cd my-frontend
npm install
```
Una vez instaladas las dependencias se puede ejecutar la aplicación con el servidor de desarrollo:

```bashrc
npm run dev
```



[Página principal](../../README.md)
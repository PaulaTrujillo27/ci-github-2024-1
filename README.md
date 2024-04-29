# Getting Started with Create React App

This project was bootstrapped with [Create React App](https://github.com/facebook/create-react-app).
hi

## Available Scripts

In the project directory, you can run:

### `npm start`

Runs the app in the development mode.\
Open [http://localhost:3000](http://localhost:3000) to view it in your browser.

The page will reload when you make changes.\
You may also see any lint errors in the console.

### `npm test`

Launches the test runner in the interactive watch mode.\
See the section about [running tests](https://facebook.github.io/create-react-app/docs/running-tests) for more information.

### `npm run build`

Builds the app for production to the `build` folder.\
It correctly bundles React in production mode and optimizes the build for the best performance.

The build is minified and the filenames include the hashes.\
Your app is ready to be deployed!

See the section about [deployment](https://facebook.github.io/create-react-app/docs/deployment) for more information.

### `npm run eject`

**Note: this is a one-way operation. Once you `eject`, you can't go back!**

If you aren't satisfied with the build tool and configuration choices, you can `eject` at any time. This command will remove the single build dependency from your project.

Instead, it will copy all the configuration files and the transitive dependencies (webpack, Babel, ESLint, etc) right into your project so you have full control over them. All of the commands except `eject` will still work, but they will point to the copied scripts so you can tweak them. At this point you're on your own.

You don't have to ever use `eject`. The curated feature set is suitable for small and middle deployments, and you shouldn't feel obligated to use this feature. However we understand that this tool wouldn't be useful if you couldn't customize it when you are ready for it.

## Learn More

You can learn more in the [Create React App documentation](https://facebook.github.io/create-react-app/docs/getting-started).

To learn React, check out the [React documentation](https://reactjs.org/).

### Code Splitting

This section has moved here: [https://facebook.github.io/create-react-app/docs/code-splitting](https://facebook.github.io/create-react-app/docs/code-splitting)

### Analyzing the Bundle Size

This section has moved here: [https://facebook.github.io/create-react-app/docs/analyzing-the-bundle-size](https://facebook.github.io/create-react-app/docs/analyzing-the-bundle-size)

### Making a Progressive Web App

This section has moved here: [https://facebook.github.io/create-react-app/docs/making-a-progressive-web-app](https://facebook.github.io/create-react-app/docs/making-a-progressive-web-app)

### Advanced Configuration

This section has moved here: [https://facebook.github.io/create-react-app/docs/advanced-configuration](https://facebook.github.io/create-react-app/docs/advanced-configuration)

### Deployment

This section has moved here: [https://facebook.github.io/create-react-app/docs/deployment](https://facebook.github.io/create-react-app/docs/deployment)

### `npm run build` fails to minify

This section has moved here: [https://facebook.github.io/create-react-app/docs/troubleshooting#npm-run-build-fails-to-minify](https://facebook.github.io/create-react-app/docs/troubleshooting#npm-run-build-fails-to-minify)

# Paso a paso de despliegue continuo

Como primera medida verificamos construimos y desplegamos la aplicación la cual utiliza React, una biblioteca de JavaScript para construir interfaces de usuario, y Create React App.
Ejecutando la aplicación nos muestra lo siguiente:

![image](https://github.com/PaulaTrujillo27/ci-github-2024-1/assets/71205932/8006a177-a307-4c83-90de-56e413453c6e)

## Creación de secrets en github 

Esto lo haremos para guardar nuestro username y password de Dockerhub, ya que, como son datos sensibles, es necesario:

![image](https://github.com/PaulaTrujillo27/ci-github-2024-1/assets/71205932/2f86e504-998d-4cef-ac25-13ce60450f48)


## Creación Dockerfile

Creamos un archivo en el código llamado Dockerfile, el cual tendrá la siguiente estructura:

```
FROM node:20.5.1-alpine
RUN mkdir -p /app
WORKDIR /app
COPY . .
RUN npm cache clean --force
RUN npm install
RUN npm run build
EXPOSE 3000
ENTRYPOINT ["npm", "run", "start"]
```

## Github Actions

Para agilizar la contención y despliegue de nuestra aplicación, se usará GitHub Actions. Esta plataforma nos brinda la capacidad de crear flujos de trabajo automatizados que se desencadenan ante eventos particulares, tales como cambios de código o actualizaciones en los repositorios.

A continuación la estructura del archivo yaml de configuración para Github Actions:

```
name: Docker Image CI

on:
  push:
    branches: [ "main" ]
 
jobs:

  build:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout Repo
      uses: actions/checkout@v3
    - name: Docker Setup Buildx
      uses: docker/setup-buildx-action@v3.0.0
    - name: Docker Login
      uses: docker/login-action@v1
      with:
            username: ${{ secrets.DOCKER_USERNAME }}
            password: ${{ secrets.DOCKER_PASSWORD}}
    - name: Build and Push Docker image
      uses: docker/build-push-action@v5.0.0
      with:
          context: .  # Ruta al contexto de construcción (puede ser el directorio actual)
          file: ./Dockerfile  # Ruta al Dockerfile
          push: true  # Habilitar el empuje de la imagen
          tags: ${{ secrets.DOCKER_USERNAME }}/node-ci:latest  # Nombre y etiqueta de la imagen
      env:
          DOCKER_USERNAME: ${{ secrets.DOCKER_USERNAME }}
          DOCKER_PASSWORD: ${{ secrets.DOCKER_PASSWORD }}
```

## Ejecución del flujo

Teniendo correctamente configurado lo anterior, podemos ver ejecutado correctamente el flujo:

![image](https://github.com/PaulaTrujillo27/ci-github-2024-1/assets/71205932/19a7aedc-0407-481a-a18a-c2cfa0911938)

## Finalmente

Se puede ver la imagen de la aplicación en nuestro Dockerhub:
![image](https://github.com/PaulaTrujillo27/ci-github-2024-1/assets/71205932/d5b03abe-27d8-4885-bbda-c6247e06fee0)



# Apuntes: Micro-Frontend con Angular

## Requisitos

- Angular versión 12 como mínimo.

## Que marco de trabajo puedo usar para crear un proyecto microfrondend

- single-spa
- module-federation



## Como podemos organizar nuestros microfrontend

- Monorepo
- Multirepo

## Cuando usar module federation como solucion y cuando no

Para ser más específico, Module Federation puede ser muy útil cuando:

- Tienes un proyecto grande que tiende a terminar como un monolito de software en arquitecturas tradicionales.
- Puedes dividir tu proyecto en varios dominios más o menos autosuficientes (por ejemplo, en arquitectura de sistema autónomo).
- La aplicación será desarrollada por equipos múltiples e independientes.
- Tus equipos pueden ponerse de acuerdo en un framework de frontend (como Angular) con el fin de armonizar.
- No planeas renunciar a esta armonización en el futuro cercano.

¿Cuándo no debería usar Module Federation? Al mismo tiempo, podría haber algunos obstáculos que te impedirán usar Module Federation o al menos lo harán significativamente menos útil para ti. Eso es cuando probablemente deberías buscar otra solución para tu arquitectura de microfrontend.

Por lo tanto, podrías querer considerar una solución alternativa cuando:

- Tu aplicación no se volverá muy extensa en el futuro cercano.
- Tus equipos quieren desarrollar con diferentes frameworks y versiones mayores (perderás muchos de los beneficios de Module Federation aquí).
- No tienes control sobre el uso del framework y las versiones de los equipos individuales.
- Uno o más de tus microfrontends podrían querer cambiar a un framework diferente en el futuro cercano.
- No utilizas webpack como un agrupador de módulos (Module Federation es una característica de webpack 5).
- Requieres una versión antigua de Angular < 11.0.0 (Angular 11 primero tiene soporte opcional para Webpack 5). Te recomiendo encarecidamente que leas más acerca de este tema y trates de entender cómo funciona en detalle antes de tomar una decisión para tu proyecto. Pero si decides ir con Module Federation, te ayudaré a entender cómo puedes configurarlo para tu proyecto Angular.

  

## Ejemplo implementación MonoRepo

Versiones con las que se trabajo este ejemplo

- Node 20
- Angular CLI 17

> NOTA: Este ejercicio se desarrollo a partir del siguiente video en youtube: [MICRO - FRONTEND con Angular y Module Federation (youtube.com)](https://www.youtube.com/watch?v=12x2QpDCsfk&t=3084s&ab_channel=LogiDev)

### Creando workspace

```powershell
ng new demo-microfront-angular17 --create-application=false
```

Resultado

```powershell
CREATE demo-microfront-angular17/angular.json (146 bytes)
CREATE demo-microfront-angular17/package.json (1045 bytes)
CREATE demo-microfront-angular17/README.md (1104 bytes)
CREATE demo-microfront-angular17/tsconfig.json (889 bytes)
CREATE demo-microfront-angular17/.editorconfig (290 bytes)
CREATE demo-microfront-angular17/.gitignore (590 bytes)
CREATE demo-microfront-angular17/.vscode/extensions.json (134 bytes)
CREATE demo-microfront-angular17/.vscode/launch.json (490 bytes)
CREATE demo-microfront-angular17/.vscode/tasks.json (980 bytes)
✔ Packages installed successfully.
    Successfully initialized git.
```

Con el flag `--create-application=false` indicamos que solo queremos que se genera la estructura de carpeta de un espacio de trabajo, y no la de una aplicación web.

Luego nos movemos a nuestro espacio de trabajo con 

```powershell
cd demo-microfront-angular17
```

### Creando primer microfrontend (mf-shell) 

Se encargara de orquestar los demas microfrontend

> NOTA: En angular 17 los proyectos ser crean en modo **standalone** de manera predeterminada, provocando que no se generen los archivos `app-routing.module.ts` ni `app.module.ts`. Para evitar estos agregamos el flag **--no-standalone**

```powershell
ng generate application mf-shell --style=scss --routing=true --no-standalone
```

Resultado

```powershell
? Would you like to share pseudonymous usage data about this project with the Angular Team
at Google under Google's Privacy Policy at https://policies.google.com/privacy. For more
details and how to change this setting, see https://angular.io/analytics. No
Global setting: enabled
Local setting: disabled
Effective status: disabled
? Do you want to enable Server-Side Rendering (SSR) and Static Site Generation (SSG/Prerendering)? No
CREATE projects/mf-shell/src/app/app-routing.module.ts (255 bytes)
CREATE projects/mf-shell/src/app/app.module.ts (411 bytes)
CREATE projects/mf-shell/src/app/app.component.html (20239 bytes)
CREATE projects/mf-shell/src/app/app.component.spec.ts (1094 bytes)
CREATE projects/mf-shell/src/app/app.component.ts (220 bytes)
CREATE projects/mf-shell/src/app/app.component.scss (0 bytes)
CREATE projects/mf-shell/src/main.ts (221 bytes)
CREATE projects/mf-shell/tsconfig.app.json (285 bytes)
CREATE projects/mf-shell/tsconfig.spec.json (295 bytes)
CREATE projects/mf-shell/src/favicon.ico (15086 bytes)
CREATE projects/mf-shell/src/index.html (306 bytes)
CREATE projects/mf-shell/src/styles.scss (81 bytes)
CREATE projects/mf-shell/src/assets/.gitkeep (0 bytes)
UPDATE angular.json (3335 bytes)
UPDATE package.json (1094 bytes)
✔ Packages installed successfully.
```

### Creamos el segundo microfrontend (mf-payment)

> NOTA: en este decidimos no usar routing

```bash
ng generate application mf-payment --style=scss --routing=false --no-standalone
```

Resultado

```powershell
? Do you want to enable Server-Side Rendering (SSR) and Static Site Generation (SSG/Prerendering)? No
CREATE projects/mf-payment/src/app/app-routing.module.ts (255 bytes)
CREATE projects/mf-payment/src/app/app.module.ts (411 bytes)
CREATE projects/mf-payment/src/app/app.component.html (20239 bytes)
CREATE projects/mf-payment/src/app/app.component.spec.ts (1100 bytes)
CREATE projects/mf-payment/src/app/app.component.ts (222 bytes)
CREATE projects/mf-payment/src/app/app.component.scss (0 bytes)
CREATE projects/mf-payment/src/main.ts (221 bytes)
CREATE projects/mf-payment/tsconfig.app.json (285 bytes)
CREATE projects/mf-payment/tsconfig.spec.json (295 bytes)
CREATE projects/mf-payment/src/favicon.ico (15086 bytes)
CREATE projects/mf-payment/src/index.html (308 bytes)
CREATE projects/mf-payment/src/styles.scss (81 bytes)
CREATE projects/mf-payment/src/assets/.gitkeep (0 bytes)
UPDATE angular.json (6517 bytes)
✔ Packages installed successfully.
```

### Creando tercer microfrontend (mf-shopping)

```powershell
ng generate application mf-shopping --style=scss --routing=true --no-standalone
```

Resultado

```powershell
? Do you want to enable Server-Side Rendering (SSR) and Static Site Generation (SSG/Prerendering)? No
CREATE projects/mf-shopping/src/app/app-routing.module.ts (255 bytes)
CREATE projects/mf-shopping/src/app/app.module.ts (411 bytes)
CREATE projects/mf-shopping/src/app/app.component.html (20239 bytes)
CREATE projects/mf-shopping/src/app/app.component.spec.ts (1103 bytes)
CREATE projects/mf-shopping/src/app/app.component.ts (223 bytes)
CREATE projects/mf-shopping/src/app/app.component.scss (0 bytes)
CREATE projects/mf-shopping/src/main.ts (221 bytes)
CREATE projects/mf-shopping/tsconfig.app.json (285 bytes)
CREATE projects/mf-shopping/tsconfig.spec.json (295 bytes)
CREATE projects/mf-shopping/src/favicon.ico (15086 bytes)
CREATE projects/mf-shopping/src/index.html (309 bytes)
CREATE projects/mf-shopping/src/styles.scss (81 bytes)
CREATE projects/mf-shopping/src/assets/.gitkeep (0 bytes)
UPDATE angular.json (9716 bytes)
✔ Packages installed successfully.
```

### Creamos Una libreria

Creamos esta librería para que sirva como interfaz de comunicación entre los microfrontend  asi como también para la reutilización de algunos componentes

```powershell
ng generate library commons-lib2 --no-standalone
```

Resultado

```powershell
CREATE projects/commons-lib/ng-package.json (166 bytes)
CREATE projects/commons-lib/package.json (227 bytes)
CREATE projects/commons-lib/README.md (1042 bytes)
CREATE projects/commons-lib/tsconfig.lib.json (328 bytes)
CREATE projects/commons-lib/tsconfig.lib.prod.json (250 bytes)
CREATE projects/commons-lib/tsconfig.spec.json (287 bytes)
CREATE projects/commons-lib/src/public-api.ts (139 bytes)
CREATE projects/commons-lib/src/lib/commons-lib.component.spec.ts (648 bytes)
CREATE projects/commons-lib/src/lib/commons-lib.component.ts (254 bytes)
CREATE projects/commons-lib/src/lib/commons-lib.service.spec.ts (394 bytes)
CREATE projects/commons-lib/src/lib/commons-lib.service.ts (148 bytes)
UPDATE angular.json (10140 bytes)
UPDATE package.json (1124 bytes)
UPDATE tsconfig.json (976 bytes)
✔ Packages installed successfully.
```

### Integrando  Module Federation

Esta librería nos permite trabajar con webpack

```powershell
npm i -D @angular-architects/module-federation
```

Resultado

```powershell
added 8 packages, and audited 942 packages in 7s

122 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
```

### Agregando la federación de módulos en cada proyecto

#### Add mf-shell

Este proyecto será de tipo `host` indicando que es el principal

```powershell
ng add @angular-architects/module-federation --project mf-shell --port 4200 --type host
```

Resultado

```powershell
Skipping installation: Package already installed
Switching to webpack
CREATE projects/mf-shell/webpack.prod.config.js (46 bytes)
CREATE projects/mf-shell/webpack.config.js (413 bytes)
CREATE projects/mf-shell/src/bootstrap.ts (256 bytes)
UPDATE tsconfig.json (861 bytes)
UPDATE projects/mf-shell/tsconfig.app.json (193 bytes)
UPDATE angular.json (8445 bytes)
UPDATE package.json (1275 bytes)
UPDATE projects/mf-shell/src/main.ts (58 bytes)
✔ Packages installed successfully.
```

#### Add mf-shopping

En este proyecto será de tipo `remote` indicando que es remoto

```powershell
ng add @angular-architects/module-federation --project mf-shopping --port 4201 --type remote
```

Resultado

```powershell
Skipping installation: Package already installed
Switching to webpack
CREATE projects/mf-shopping/webpack.prod.config.js (46 bytes)
CREATE projects/mf-shopping/webpack.config.js (389 bytes)
CREATE projects/mf-shopping/src/bootstrap.ts (256 bytes)
UPDATE tsconfig.json (861 bytes)
UPDATE projects/mf-shopping/tsconfig.app.json (193 bytes)
UPDATE angular.json (8873 bytes)
UPDATE package.json (1275 bytes)
UPDATE projects/mf-shopping/src/main.ts (58 bytes)
✔ Packages installed successfully.
```

#### Add mf-payment

En este proyecto será de tipo `remote` indicando que es remoto

```powershell
ng add @angular-architects/module-federation --project mf-payment --port 4202 --type remote
```

Resultado

```powershell
Skipping installation: Package already installed
Switching to webpack
CREATE projects/mf-payment/webpack.prod.config.js (46 bytes)
CREATE projects/mf-payment/webpack.config.js (387 bytes)
CREATE projects/mf-payment/src/bootstrap.ts (256 bytes)
UPDATE tsconfig.json (861 bytes)
UPDATE projects/mf-payment/tsconfig.app.json (193 bytes)
UPDATE angular.json (9296 bytes)
UPDATE package.json (1275 bytes)
UPDATE projects/mf-payment/src/main.ts (58 bytes)
✔ Packages installed successfully.
```



### Configurando los microfrontend

Definiendo canal de comunicacion entre los Microfrontend

Agregamos **rxjs** en las dependencias en el archivo `package.json` de **commons-lib**

```json
{
  "name": "commons-lib",
  ...
  },
  "dependencies": {
    ...
    "rxjs": "~7.5.0"
  },
  ...
}

```

y para instalar esta lib nos ubicamos en la raíz del proyecto commons-lib`

```
cd .\projects\commons-lib\
```

y ejecutamos para instalar

```powershell
npm i
```

Modificamos el archivo `tsconfig.json` de workspace

Pasamos de esto:

```json
{
  ...
  "compilerOptions": {
    "paths": {
      "commons-lib": [
        "./dist/commons-lib"
      ]
    },
    ...
  },
  ...
}
```

A esto:

```json
{
  ...
  "compilerOptions": {
    "paths": {
      "@commons-lib": [
        "./projects/commons-lib/src/public-api.ts"
      ]
    },
    ...
  },
  ...
}
```

Ahora editamos el archivo `webpack.config.js` de los proyectos **mf-shell**, **mf-shopping** y **mf-payment** y agregamos el alias de la librería compartida en el key **sharedMappings**

```javascript
...

  sharedMappings: ["@commons-lib"],

});
```

### Integrando un microcrofrontend

##### Exponer Modulo

Para exponer el modulo editamos el key **expose** del archivo `projects/mf-shopping/webpack.config.js` de la siguinete manera.

```javascript
...
  name: 'mfShopping',

  exposes: {
    './ProductoModule': './projects/mf-shopping/src/app/products/products.module.ts',
  },
  ...
```

Ahora en el Proyectos **mf-shell** editamos el archivo `projects/mf-shell/webpack.config.js` de la siguiente manera:

```javascript
  ...
  remotes: {
...
    "mfShopping": "http://localhost:4201/remoteEntry.js",    
  },

...

  sharedMappings: ["@commons-lib"],
```

##### Exponer Componente

Para exponer el modulo editamos el key **expose** del archivo `projects/mf-payment/webpack.config.js` de la siguinete manera.

```javascript
...
  name: 'mfPayment',

  exposes: {
    './PaymentComponent': './projects/mf-payment/src/app/payment/payment.component.ts',
  },
  ...
```

Ahora en el Proyectos **mf-shell** editamos el archivo `projects/mf-shell/webpack.config.js` de la siguiente manera:

```javascript
  ...
  remotes: {
...
    "mfPayment": "http://localhost:4202/remoteEntry.js",    
  },

...

  sharedMappings: ["@commons-lib"],
```

##### Uso en mf-shell

Enrutamos o mapeamos el modulo y/o el componente editando el archivo `projects\mf-shell\src\app\app-routing.module.ts` de la siguiente manera:

```javascript
...
const routes: Routes = [
  { // modulo
    path:'',
    loadChildren: () => import('mfShopping/ProductsModule').then((m) => m.ProductsModule), 
  },
  { // componente
    path:'payment',
    loadComponent: () => import('mfPayment/PaymentComponent').then((c) => c.PaymentComponent), 
  }
];
...
```

### Como levantar los microfrontend

Desde la ruta raíz de nuestro workspace ejecutamos los siguientes comandos:

**Iniciar mf-shopping**

En una nueva terminal ejecutamos

```powershell
ng s mf-shopping
```

Resultado

```powershell
✔ Browser application bundle generation complete.

Initial chunk files   | Names                    |  Raw size
polyfills.js          | polyfills                | 362.75 kB | 
remoteEntry.js        | mfShopping               | 259.25 kB | 
styles.css, styles.js | styles                   | 258.86 kB | 
main.js               | main                     | 258.27 kB | 

                      | Initial total            |   1.11 MB

Lazy chunk files      | Names                    |  Raw size
580.js                | -                        |   1.41 MB | 
116.js                | -                        |   1.41 MB | 
595.js                | -                        | 414.79 kB | 
845.js                | -                        | 403.03 kB | 
705.js                | -                        | 347.78 kB | 
219.js                | -                        | 338.88 kB | 
691.js                | -                        | 297.26 kB | 
72.js                 | -                        | 297.25 kB | 
316.js                | -                        | 293.54 kB | 
935.js                | -                        | 293.54 kB | 
443.js                | -                        | 122.00 kB | 
824.js                | -                        | 122.00 kB | 
436.js                | -                        |  85.40 kB | 
55.js                 | -                        |  85.39 kB | 
689.js                | -                        |  20.94 kB | 
897.js                | -                        |  20.86 kB | 
505.js                | products-products-module |  18.93 kB | 
886.js                | -                        |  18.93 kB | 
520.js                | bootstrap                |   7.96 kB | 
134.js                | -                        |   6.57 kB | 

Build at: 2024-03-27T22:33:11.685Z - Hash: f02e0261b7231e4d - Time: 6070ms

** Angular Live Development Server is listening on localhost:4201, open your browser on http://localhost:4201/ **


√ Compiled successfully.
```

**Iniciar mf-payment**

En una nueva terminal ejecutamos

```powershell
ng s mf-payment
```

Resultado

```powershell
✔ Browser application bundle generation complete.

Initial chunk files   | Names         |  Raw size
polyfills.js          | polyfills     | 360.47 kB | 
styles.css, styles.js | styles        | 256.57 kB | 
remoteEntry.js        | mfPayment     | 256.41 kB | 
main.js               | main          | 255.68 kB | 

                      | Initial total |   1.10 MB

Lazy chunk files      | Names         |  Raw size
580.js                | -             |   1.41 MB | 
845.js                | -             | 402.04 kB | 
219.js                | -             | 338.02 kB | 
316.js                | -             | 293.54 kB | 
935.js                | -             | 293.54 kB | 
443.js                | -             | 121.99 kB | 
824.js                | -             | 121.99 kB | 
436.js                | -             |  85.39 kB | 
55.js                 | -             |  85.39 kB | 
689.js                | -             |  20.94 kB | 
756.js                | bootstrap     |  15.92 kB | 
184.js                | -             |  10.83 kB | 

Build at: 2024-03-27T22:34:04.760Z - Hash: d0adf0b1d6651710 - Time: 3113ms

** Angular Live Development Server is listening on localhost:4202, open your browser on http://localhost:4202/ **


√ Compiled successfully.
```

**Iniciar mf-shell**

En una nueva terminal ejecutamos

```powershell
ng s mf-shell
```

Resultado

```powershell
✔ Browser application bundle generation complete.

Initial chunk files   | Names         |  Raw size
polyfills.js          | polyfills     | 364.80 kB | 
main.js               | main          | 263.20 kB | 
styles.css, styles.js | styles        | 260.88 kB | 

                      | Initial total | 888.88 kB

Lazy chunk files      | Names         |  Raw size
580.js                | -             |   1.41 MB | 
116.js                | -             |   1.41 MB | 
595.js                | -             | 414.79 kB | 
845.js                | -             | 403.03 kB | 
705.js                | -             | 347.77 kB | 
219.js                | -             | 338.87 kB | 
691.js                | -             | 297.25 kB | 
72.js                 | -             | 297.25 kB | 
316.js                | -             | 293.54 kB | 
935.js                | -             | 293.54 kB | 
443.js                | -             | 122.00 kB | 
824.js                | -             | 122.00 kB | 
436.js                | -             |  85.39 kB | 
55.js                 | -             |  85.39 kB | 
689.js                | -             |  20.94 kB | 
897.js                | -             |  20.86 kB | 
932.js                | bootstrap     |  12.21 kB | 
134.js                | -             |   6.56 kB | 
                      | -             |   0 bytes | 
                      | -             |   0 bytes | 

Build at: 2024-03-27T22:36:16.888Z - Hash: 9e829d70487678d0 - Time: 5382ms

** Angular Live Development Server is listening on localhost:4200, open your browser on http://localhost:4200/ **


√ Compiled successfully.
```





---




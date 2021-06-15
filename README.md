Convierte tu sitio web a PWA

  ¿Qué es PWA?

La base son páginas webs, pero utilizan tecnologías que hacen que su estética y funcionamiento se asemejen enormemente a una App nativa, por ejemplo, mediante la ejecución en segundo plano. Se accede a ellas a través de un navegador, pero se puede anclar un acceso directo en nuestro dispositivo (en la pantalla de inicio o en el menú de aplicaciones). No dependen de sistemas operativos (se ejecutan en el navegador) y van incorporando funcionalidades nativas del dispositivo.

![image](https://user-images.githubusercontent.com/36380066/122099695-ecd55a00-cdd7-11eb-83b9-d6582315312d.png)



  * Lo que necesitamos es un sitio web, el cual será convertido a Progressive Web Apps
    
    En este caso yo tomare una simple landing page
    
   ![image](https://user-images.githubusercontent.com/36380066/122100754-22c70e00-cdd9-11eb-96ad-d120d02485c0.png)

  Añadiré 2 archivos que nos permitirán llevar ese cambio de sitio web a pwa
  
  * manifest.json
  * sw.js

----------------------------------------------------------------------------------------------------------------------------

Manifest.json
                                             
    {
    "name": "Prueba Creando PWA",
    "short_name": "Creando PWA",
    "description": "Este es un sitio de prueba para ver la funcionalidad de una PWA",
    "background_color": "#2B2B2B",
    "theme_color": "#F0DB4F",
    "orientation": "portrait", //Tenemos la posibilidad de que sea en vertical u horizontal. 
    "display": "standalone",
    "start_url": "./index.html", //Url de la pagina principal que cargará 
    "scope": "./", //Ruta que nos permite determinar donde se encuentra nuestro service worker
    "lang": "es-MX",
    "icons": [  //Iconos que se cargarán en el almacenamiento de nustro dispositivo.
      {
        "src": "./assets/img/icon_1024.png",
        "sizes": "1024x1024",
        "type": "image/png"
      },
      {
        "src": "./assets/img/bg-showcase-1.png",
        "sizes": "512x512",
        "type": "image/png"
      },
      {
        "src": "./assets/img/bg-showcase-2.png",
        "sizes": "384x384",
        "type": "image/png"
      },
      {
        "src": "./assets/img/bg-showcase-3.png",
        "sizes": "256x256",
        "type": "image/png"
      },
      {
        "src": "./assets/img/testimonials-1.png",
        "sizes": "192x192",
        "type": "image/png"
      },
      {
        "src": "./assets/img/testimonials-2.png",
        "sizes": "128x128",
        "type": "image/png"
      },
      {
        "src": "./assets/img/testimonials-3.png",
        "sizes": "96x96",
        "type": "image/png"
      }
    ]
  }
  
  ------------------------------------------------------------------------------------------------------------------
  sw.js
  

    //asignar un nombre y versión al cache
    const CACHE_NAME = 'v1_cache_Pwa',
      urlsToCache = [
        './',
        'https://cdn.jsdelivr.net/npm/bootstrap-icons@1.5.0/font/bootstrap-icons.css',
        'https://fonts.googleapis.com/css?family=Lato:300,400,700,300italic,400italic,700italic',
        './css/styles.css',
        './assets/img/icon_1024.png',
        './assets/img/bg-showcase-1.png',
        './assets/img/bg-showcase-2.png',
        './assets/img/bg-showcase-3.png',
        './assets/img/testimonials-1.png',
        './assets/img/testimonials-2.png',
        './assets/img/testimonials-3.png',
        './favicon.ico'
      ]

    //durante la fase de instalación, generalmente se almacena en caché los activos estáticos
    self.addEventListener('install', e => {
      e.waitUntil(
        caches.open(CACHE_NAME)
          .then(cache => {
            return cache.addAll(urlsToCache)
              .then(() => self.skipWaiting())
          })
          .catch(err => console.log('Falló registro de cache', err))
      )
    })

    //una vez que se instala el SW, se activa y busca los recursos para hacer que funcione sin conexión
    self.addEventListener('activate', e => {
      const cacheWhitelist = [CACHE_NAME]

      e.waitUntil(
        caches.keys()
          .then(cacheNames => {
            return Promise.all(
              cacheNames.map(cacheName => {
                //Eliminamos lo que ya no se necesita en cache
                if (cacheWhitelist.indexOf(cacheName) === -1) {
                  return caches.delete(cacheName)
                }
              })
            )
          })
          // Le indica al SW activar el cache actual
          .then(() => self.clients.claim())
      )
    })

    //cuando el navegador recupera una url
    self.addEventListener('fetch', e => {
      //Responder ya sea con el objeto en caché o continuar y buscar la url real
      e.respondWith(
        caches.match(e.request)
          .then(res => {
            if (res) {
              //recuperar del cache
              return res
            }
            //recuperar de la petición a la url
            return fetch(e.request)
          })
      )
    })
    
--------------------------------------------------------------------------------------------------------------------------------------
Al final solo mandamos llamar nuestro archivo manifest.json en nuestra pagina principal en nuestro caso se llama index.html

    <link rel="manifest" href="manifest.json">
    <script>
        //if browser support service worker
        if('serviceWorker' in navigator) {
          navigator.serviceWorker.register('sw.js');
        };
      </script>
      
---------------------------------------------------------------------------------------------------------------------------------------      
 Nuestro archivo index.html quedaría así
 
 
     <!DOCTYPE html>
    <html lang="en">
        <head>
            <meta charset="utf-8" />
            <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />
            <meta name="description" content="" />
            <meta name="author" content="" />
            <title>Landing Page - Start Bootstrap Theme</title>
            <!-- Favicon-->
            <link rel="icon" type="image/x-icon" href="assets/favicon.ico" />
            <!-- Bootstrap icons-->
            <link href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.5.0/font/bootstrap-icons.css" rel="stylesheet" type="text/css" />
            <!-- Google fonts-->
            <link href="https://fonts.googleapis.com/css?family=Lato:300,400,700,300italic,400italic,700italic" rel="stylesheet" type="text/css" />
            <!-- Core theme CSS (includes Bootstrap)-->
            <link href="css/styles.css" rel="stylesheet" />
            <link rel="manifest" href="manifest.json">
            <script>
            //if browser support service worker
            if('serviceWorker' in navigator) {
              navigator.serviceWorker.register('sw.js');
            };
          </script>

        </head> ......
        
        
----------------------------------------------------------------------------------------------------------------------------------------------------
La estructura de mis directorios es esta:
      
   ![image](https://user-images.githubusercontent.com/36380066/122106205-3f664480-cddf-11eb-9e58-f9cfafae6456.png)
   
-----------------------------------------------------------------------------------------------------------------------------------------------------
El resultado es este:

  ![image](https://user-images.githubusercontent.com/36380066/122108218-8d7c4780-cde1-11eb-9b5f-57acd36b31b1.png)
  
  ![image](https://user-images.githubusercontent.com/36380066/122108292-a553cb80-cde1-11eb-8f52-72f1f690b379.png)

  ![image](https://user-images.githubusercontent.com/36380066/122108513-e64be000-cde1-11eb-8072-bc8330757725.png)

  







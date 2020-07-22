# Bookings v2
 
# Descripción

Se requiere el desarrollo de una WebApp desarrollada usando Angular 6 o similar si prefiere este lenguaje, o en su defecto, Angular JS. Si no conoce alguno de estos lenguajes,
pregunte si se puede utilizar otro.


### Paso 1: Crear proyecto

### Paso 2: Crear componentes (Home, Login)
```
login/login  
home/home  
```

### Paso 3: Configurar rutas 

```
'use strict';
/*jshint esversion: 8 */
// import './services/API';
// Declare app level module which depends on views, and core components
angular.module('myApp', [
  'ngRoute',
  'myApp.login',
    'myApp.home'
]).
config(['$locationProvider', '$routeProvider', function($locationProvider, $routeProvider) {
  $locationProvider.hashPrefix('!');

  $routeProvider.otherwise({redirectTo: '/login'});
}]);

```



### Paso 4: login.ts y Login.html
Login.js
```
'use strict';
/*jshint esversion: 8 */
// import {API} from '../services/API';
/*global localStorage: false, console: false, $: false */

angular.module('myApp.login', ['ngRoute'])

    .config(['$routeProvider', function ($routeProvider) {
        $routeProvider.when('/login', {
            templateUrl: 'login/login.html',
            controller: 'LoginCtrl'
        });
    }])

    .controller('LoginCtrl', ['$scope', "$http", "$location", function ($scope, $http, $location) {

        $scope.error = '';

        $scope.user = {
            email: "",
            password: "",
            app: ""
        };

        let token = localStorage.getItem('token');
        console.log(token);
        if (token.toString() !== 'null') {
            $location.path("/home");
        }

        // validarUser();
        // console.log($scope.error);

        $scope.validarUser = () => {
            $scope.error ="";

            let email = $scope.user.email;
            let password = $scope.user.password;
            let app = $scope.user.app;
            if (email !== "" && password !== "" && app !== "") {
                var settings = {
                    url: 'https://dev.tuten.cl/TutenREST/rest/user/' + email,
                    method: 'PUT',
                    timeout: 0,
                    headers: {
                        password: password,
                        app: app,
                        Accept: 'application/json',
                    },
                    processData: false,
                    mimeType: 'multipart/form-data',
                    contentType: false,
                };


                $http(settings).then(function (response) {

                    console.log(response.data.sessionTokenBck != null);
                    if (response.data.sessionTokenBck != null) {
                        localStorage.setItem('token', response.data.sessionTokenBck);
                        console.log(localStorage.getItem('token'));
                        $location.path("/home");
                    }
                }).catch((error)=>{

                    $scope.error ="Datos incorrectos";
                });
            }else{
                $scope.error ="Todos los campos del formulario deben ser diligenciados";
            }

        };


    }]);
```

Login.html
```
<!DOCTYPE html>
<!--[if lt IE 7]>      <html lang="en" ng-app="myApp" class="no-js lt-ie9 lt-ie8 lt-ie7"> <![endif]-->
<!--[if IE 7]>         <html lang="en" ng-app="myApp" class="no-js lt-ie9 lt-ie8"> <![endif]-->
<!--[if IE 8]>         <html lang="en" ng-app="myApp" class="no-js lt-ie9"> <![endif]-->
<!--[if gt IE 8]><!-->
<html lang="en" ng-app="myApp" class="no-js">
<!--<![endif]-->

<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>Login</title>
    <meta name="description" content="">
    <meta name="viewport" content="width=device-width, initial-scale=1">

    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css"
        integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
    <link rel="stylesheet" href="../login/login.css">
</head>

<body ng-controller="LoginCtrl">

    <div class="container col-md-6">
        <h1 class="card-text"> Inicio de sesión</h1>
        <form>
            <div ng-if="error!=''" class="alert alert-danger" role="alert">
                {{error}}
            </div>
            <div class="form-group">
                <label for="email">Email  (<span style="color: red;">*</span>)</label>
                <input ng-model="user.email" type="email" class="form-control" id="email" aria-describedby="emailHelp"
                    required>
            </div>
            <div class="form-group">
                <label for="exampleInputPassword1">Password (<span style="color: red;">*</span>) </label>
                <input ng-model="user.password" type="password" class="form-control" id="password" required>
            </div>
            <div class="form-group">
                <label for="exampleInputPassword1">App  (<span style="color: red;">*</span>)</label>
                <input ng-model="user.app" type="text" class="form-control" id="app" required>
            </div>
            <button type="submit" class="btn btn-primary" ng-click="validarUser()">Iniciar sesión</button>
        </form>
    </div>

    <!--[if lt IE 7]>
      <p class="browsehappy">You are using an <strong>outdated</strong> browser. Please <a href="http://browsehappy.com/">upgrade your browser</a> to improve your experience.</p>
  <![endif]-->

    <!-- In production use:
  <script src="//ajax.googleapis.com/ajax/libs/angularjs/x.x.x/angular.min.js"></script>
  -->

    <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js"
        integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous">
    </script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js"
        integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous">
    </script>
</body>

</html>

```

### Paso 5: home.js y home.html

home.js
```
'use strict';
/*jshint esversion: 8 */
/*global localStorage: false, console: false, $: false */

angular.module('myApp.home', ['ngRoute'])

    .config(['$routeProvider', function ($routeProvider) {
        $routeProvider.when('/home', {
            templateUrl: 'home/home.html',
            controller: 'HomeCtrl'
        });
    }])

    .controller('HomeCtrl', ["$scope", "$http", "$location", function ($scope, $http, $location) {
        let data = [];
        let BookingList = [];
        $scope.BookingListFiltered = [];
        let token = localStorage.getItem('token');
        $scope.choosed = ">=";
        $scope.precio = 0;
        
        if (token.toString() == 'null') {
            $location.path("/login");
        } else {
            const adminemail = "testapis@tuten.cl";
            const email = "contacto@tuten.cl";
            const current = true;
            const app = "APP_BCK";


            var settings = {
                url: 'https://dev.tuten.cl/TutenREST/rest/user/' + email + '/bookings?current=' + current,
                method: 'GET',
                timeout: 0,
                headers: {
                    adminemail: adminemail,
                    token: token,
                    app: app,
                    Accept: 'application/json',
                },
                processData: false,
                mimeType: 'multipart/form-data',
                contentType: false,
            };


            $http(settings).then(function (response) {

                data = response.data;
                $scope.loadBookings();
            });
        }


        $scope.choosedMethod = (opcion) => {

            $scope.choosed = opcion;
          
            $scope.filtrar();
        };

        $scope.salir = () => {
            localStorage.setItem("token", null);
            $location.path("/login");
        };


        $scope.buscar = () => {
            let valor = "" + $scope.searchValue;

            if (valor != null) {
                $scope.filtrar();
            } else {
                $scope.searchValue = 0;
                $scope.filtrar();
            }
        };

        $scope.loadBookings = () => {

            /*Se recorren los bookings obtenidos y se crean nuevos objetos
             simplificados de la información que solicitan para visualizar*/
            data.forEach((element) => {
                console.log(element.bookingId);
                let booking = {
                    bookingID: 0,
                    cliente: '',
                    direccion: '',
                    fecha_creacion: new Date(),
                    precio: 0,
                };

                booking.bookingID = element.bookingId;
                booking.cliente =
                    element.tutenUserClient.firstName +
                    ' ' +
                    element.tutenUserClient.lastName;
                booking.direccion = element.locationId.streetAddress;
                booking.fecha_creacion = new Date(element.bookingTime);
                booking.precio = element.bookingPrice;

                BookingList.push(booking);
            });
            $scope.BookingListFiltered = BookingList;
            console.log($scope.BookingListFiltered);
        };

        $scope.filtrar = () => {
            $scope.BookingListFiltered = BookingList;

            if ($scope.searchValue > 0 || $scope.precio > 0) {
                //Se filtra por el bookingid
                if ($scope.searchValue > 0) {
                    $scope.BookingListFiltered = BookingList.filter((item) => {


                        if (item.bookingID === $scope.searchValue) {

                            return item;
                        }
                    });
                }

                //Se filtra por precio 
                if ($scope.choosed === "y") {

                    $scope.BookingListFiltered = $scope.BookingListFiltered.filter((item) => {
                        if (item.precio === $scope.precio) {
                            return item;
                        }
                    });
                } else {

                    if ($scope.choosed == ">=") {

                        $scope.BookingListFiltered = $scope.BookingListFiltered.filter((item) => {

                            if (item.precio >= $scope.precio) {
                                return item;
                            }
                        });
                    } else {
                        $scope.BookingListFiltered = $scope.BookingListFiltered.filter((item) => {
                            if (item.precio <= $scope.precio) {
                                return item;
                            }
                        });
                    }
                }


            } else {
                //En caso de que no se ingrese bookingId y/o precio, los datos son los iniciales.
                $scope.BookingListFiltered = BookingList;
            }
        };


    }]);

  
```


home.html
```
<!DOCTYPE html>
<!--[if lt IE 7]>      <html lang="en" ng-app="myApp" class="no-js lt-ie9 lt-ie8 lt-ie7"> <![endif]-->
<!--[if IE 7]>         <html lang="en" ng-app="myApp" class="no-js lt-ie9 lt-ie8"> <![endif]-->
<!--[if IE 8]>         <html lang="en" ng-app="myApp" class="no-js lt-ie9"> <![endif]-->
<!--[if gt IE 8]><!-->
<html lang="en" ng-app="myApp" class="no-js">
<!--<![endif]-->

<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>Home</title>
    <meta name="description" content="">
    <meta name="viewport" content="width=device-width, initial-scale=1">

    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css"
        integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
    <link rel="stylesheet" href="../home/home.css">
    <link rel="stylesheet" href="https://use.fontawesome.com/d9e9bf59ee.css">
</head>

<body ng-controller="HomeCtrl">

 
<div class="container">
    <div class="col" >
      <button class="btn btn-outline-dark center" ng-click="salir()" >Salir</button>
    </div>
      <div class=" container ">
        <div class="row filters">
          <div class="col">
            <label for="">BookingID</label>
            <form class="form">
              <div>
                <div class="input-group">
                  <div class="input-group-prepend">
    
                    <div class="input-group-text">
                      <!-- <fa-icon [icon]="faSearch"></fa-icon> -->
                      <i class="fa fa-search" aria-hidden="true"></i>
    
                    </div>
                  </div>
                  <input ng-model="searchValue" name="searchValue" class="form-control mr-sm-2" type="number" min="0"
                    placeholder="ingrese boking id" ng-change="buscar()" />
    
                </div>
              </div>
            </form>
    
          </div>
          <div class="col">
            <div class="row">
              <div class="col" style="margin-top: 2em">
                <div class="dropdown">
                  <button style="width: 8em;" class="btn btn-dark dropdown-toggle" type="button"
                    id="dropdownMenuButton" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
                    {{choosed}}
                  </button>
                  <div class="dropdown-menu">
                    <a class="dropdown-item" ng-click="choosedMethod('y')">y</a>
                    <a class="dropdown-item" ng-click="choosedMethod('>=')">>=</a>
                    <a class="dropdown-item" ng-click="choosedMethod('<=')"><=</a> 
                </div> 
            </div> 
        </div> 
                <div class="col">
                        <label for="">Precio</label>
                        <input ng-model="precio" class="form-control " type="number" min="0" ng-change="filtrar()">
                  </div>
                </div>
    
    
              </div>
            </div>
    
          </div>
    
    
    
    
          <table class="table">
            <thead>
              <tr>
    
                <th scope="col">BookingId</th>
                <th scope="col">Cliente</th>
                <th scope="col">Fecha de creación</th>
                <th scope="col">Dirección</th>
                <th scope="col">Precio</th>
    
              </tr>
            </thead>
            <tbody>
              <tr ng-repeat="booking in BookingListFiltered">
               
                <td>{{booking.bookingID}}</td>
                <td>{{booking.cliente}}</td>
                <td>{{booking.fecha_creacion.toDateString()}}</td>
                <td>{{booking.direccion}}</td>
                <td>{{booking.precio | currency:"$":0}}</td>
    
    
              </tr>
    
            </tbody>
          </table>
        </div>
       

    <!--[if lt IE 7]>
      <p class="browsehappy">You are using an <strong>outdated</strong> browser. Please <a href="http://browsehappy.com/">upgrade your browser</a> to improve your experience.</p>
  <![endif]-->

 
    <!-- In production use:
  <script src="//ajax.googleapis.com/ajax/libs/angularjs/x.x.x/angular.min.js"></script>
  -->
    <!-- <script src="https://use.fontawesome.com/d9e9bf59ee.js"></script> -->
        
    <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js"
        integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous">
    </script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js"
        integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous">
    </script>
</body>

</html>
```

### Paso 6: estilos al gusto. 


## Running

`npm start` 

## Tecnologías
```
    "angular": "^1.8.0",
    "bootstrap": "^4.4.0"
    "jquery": "^3.5.1",
    "popper.js": "^1.16.1"
```

# Personal learning notes on AngularJs

*index.html*
```
//ng-app designates the root element of the application. can be placed on html, body, or div.
<html ng-app="">
<head>
    <script src="lib/angular/angular.min.js"></script>
</head>
<body>
    //then "query" will be the variable name for this input value
    <input id="searchQuery" ng-model="query" />
    <button ng-click="click()"></button>
</body>
</html>
```
### modules
*app.js (should be imported in html)*
```
var myApp = angularmodule('myApp',[])  <- ng-app="" inside[] are dependencies
myApp.controller('MyController',function MyController($scope, $http){ <-ng-controller="MyController"
    $scope.data = {title: "title"} <- <div>{{data.title}}</div>
})
```
### booleans and loops
ng-show, ng-hide (`ng-show="query"` means if query exists, then show)
ng-if
ng-repeat `ng-repeat="item in list"` or `ng-repeat="(key,meeting) in meetings"`

### service
$http (runs with a server, returns a promise)
```
$http.get('data.json').then(function(response) {
    $scope.data = response.data
}
```

### filter (similar to pipe in angular) 
variable | currency, number, date, lowercase, uppercase
limitTo:qty:start `limitTo:4:1 -> second to fifth`
filter:keyword `filter: query -> filter result according to query`
orderBy: key: reverse `orderBy:'name':'reverse' -> order by name and reverse`

### route 
```
<script src="lib/angular/angular-route.min.js"></script>
```
ngRoute(js dependency), $routeProvider(js), ng-view(html)
```
//myControllers here is a module dependency
var myApp = angular.module('myApp'),['ngRoute','myControllers']
myApp.config([$'routeProvider', function($routeProvider){
    $routeProvider
        .when('/', {
            templateUrl: 'js/partials/search.html',
            //SearchController here is a controller inside my Controllers
            controller: 'SearchController'
        })
}])
```

params
`.when('details/:itemId',{tmplateUrl: '1.html', controller: 'SomeController'})`
-> 
```
myControllers.controller('DetailsController',
    function MyController($scope, $http, $routeParams){
        $scope.whichItem = $routeParams.itemId
    }
)
```

### href
link must have #
`ng-href="#/someroute"`

### form
ng-required="true" required field
`<input type="email" name="email" ng-model="user.email" ng-required="true">`
validation: $.valid
if !user.email.$valid and user.email.$touched, the input box class become `input.ng-invalid.ng-touched`
if $valid && $touched, `input.ng-valid.ng-touched`
ng-disable
`ng-disabled=myform.$invalid` disable if the form has any invalid field
ng-submit="login()" add this on form, and it is the function for submit the whole form

### include html
`ng-include="'views/nav.html'"`

### class or style
conditional style
```
ng-class="variableName"
```

### scope
(not the same as this)
```
$scope.$parent
$scope.$watch
$scope.$on
$scope.$root
$scope.$apply
```
```
(private)
$scope.$$watchers
$scope.$$postDigest
$scope.$$phase
$scope.$$destroyed
```

if no value in son scope, then read `_proto:parentScope`, if exists, reads value in son scope  
send scope from parent to son  
(js)
```
angular.module('learndigest'.controller('PlaygroundCtrol, function($scope){
	this.force = 30
})
angular.module('learndigest'.controller('InteralCtrl, function($scope){
	$scope.force = $scope.playground.force
})
```
(html) `ng-controller="PlaygroundCtrl as playground"`

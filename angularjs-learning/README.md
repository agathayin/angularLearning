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
track by `ng-repeat="task in $ctrl.tasks track by task.id"` can speed up loop recreation

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

`ng-change` this could be a good replacement for $watch

### select
ng-options
```
<select ng-model="vm.contentToAdd.boxId" ng-options='box.boxId as box.boxCode+" ("+box.boxId+") " for box in vm.selffulfillment.box'></select>
```

### include html
speed will be lower, component is recommended as a replacement
`ng-include="'views/nav.html'"`

### class or style
conditional style
```
ng-class="variableName"
```

### scope
(not the same as this) 

under `rootscope`, every scope has a parent scope.  
directives like ng-if create new scopes and won't use the scope of the controller they are in. Try using a this.variable instead of scope.

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
#### send scope from parent to son  
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

#### $watch
(html)`np-bind=""`
```
$scope.$watch('variableName', 
	function(newvalue, oldvalue){
    console.log(newvalue, oldvalue)
 })
```
deep watch (or $doCheck)  
```
$scope.$watch('variableName', 
	function(newvalue, oldvalue){
    console.log(newvalue, oldvalue)
 },true)
```
#### $digest loop
force $digest: ng-click, ng-model, $http, $timeout, $interval
import external library
`$scope.$apply(function(){})` forces digest to happen(all watches)  === $rootScope.$digest()
`$scope.$digest` only change the current scope

#### one time binding
only renders for one time.
```
<div ng-repeat="item in $ctrl.list">
	<div> {{ ::item.name }} </div>
</div>
```

### debugging
console
choose the right element and use `angular.element($0)` to check scope or other info.  
`angular.element($0).scope` 

force a digest
`angular.element($0).scope.$apply()` 

check performance
`console.time('time') <some digest> console.timeEnd('time')`

ng-stats: google to get docs. used to check performance and how many watchers

inject services to the console  
`$http = angular.element(document.body).injector().get('$http')`

### bindings
& binding: saves it for evaluation later  
```
angular.module('app').component('toggle',{
	templateUrl: '...',
    bindings: {
    	callback: '&'
        }
    controller: function() {
    	//...
        this.callback();
    }
})
```
`<toggle callback="$ctrl.myCallback(value)"></toggle>`  
`<div ng-click="$ctrl.off=true"></div>`

@ binding: allowing adding no extra quotes and adding object value
```
bindings: {
placeholeder:'@'
```
### services and factories
```
(ES6)
angular.module('app').service('SomeService', function(){
this.someFunction = function(){}
})
(ES5)
angular.module('app').factory('SomeService', function(){
	return {
    somefunction: function(){}
    }
});
```
### angular.equals, angular.toJson
angular.equals returns true if one of the property and its value are the same. ignores properties with $ sign. 
angular.toJson removes internal angular api properties. ignores properties with $$(private). e.g. `ng-repeat="item in $ctrl.items"`(loop object)

angular.fromJson() = JSON.parse()

### promises
q.defer()  
used for integrating with non-Angular libraries  
promise  
```
return $q.when(['detail','simple])
return $q.reject({error: 'something bad'})
return $http.get('options.json').then(funciton(res){
	return res.data;
})
return $timeout(function(){},5000)
```
### injector
avoid circular dependencies  
```
$httpProvider.interceptors.push(function($injector){
	return {
    response: function(res){
    $injector.get('AuthService').handleExpiredSession();
    }
})
```
### transclusion
(js)
```
app.component('modal',{
	template:[
    	'<div ng-transclude="title></div>,
        '<div ng-transclude="title></div>
    ].join(''),
    transclude: {
    	title: 'modalTitle',
        body: 'modalBody'
    },
    controller: function() {...}
})
```
(html)
`<modal-title></modal-title><modal-body></modal-body>

### div to safe links
ngSanitize. after linky, it means open a new tab and the second is custom attribution  
`div ng-bind-html="blog.post | linky:'_blank':{rel: 'nofollow}"></div>`

### $resource (REST api)
*regular usage* [tutorial](https://docs.angularjs.org/tutorial/step_13)  
* inject `ngResource` in the module
* add service and factory with resource
```
angular.
  module('core.phone').
  factory('Phone', ['$resource',
    function($resource) {
      return $resource('phones/:phoneId.json', {}, {
        query: {
          method: 'GET',
          params: {phoneId: 'phones'},
          isArray: true
        }
      });
    }
  ]);
```
* add module and service in index.html
* controllers injects the service and use `ServiceName.query()`
*ui-route*  
The steps of controllers and service are the same. No need to add link into html but in config/*.routes.js, must contains resove function
```
.state('monitors.priceLog',{
        parent: 'monitors',
        url: '/priceLog?SKU',
        templateUrl: 'modules/monitors/client/views/list-monitors-price.client.view.html',
        controller: 'MonitorsListPriceController',
        controllerAs: 'vm',
        data: {
          roles: ['user', 'admin'],
          pageTitle: 'Monitors Price List'
        },
        resolve: {
          monitorResolve: priceLogMonitor
        }
      })
...
priceLogMonitor.$inject = ['PriceLogService'];
function priceLogMonitor(PriceLogService) {
    return new PriceLogService();
}
```
*using fetch*
if has to use fetch, use `$scope.$apply()` to refresh this or vm, or the variable doesn't change. E.g.  
```
fetch('url',{method: 'GET'}).then((res)=>res.json()).then((res)=>{
    vm.logs = res;
    $scope.$apply();
})
```

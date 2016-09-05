---
title: Writing an Angular Factory for the Particle Cloud
header:
  image: combined_header.jpg
  teaser: combined_teaser.jpg
subtitle: ".Success == .FAIL and here's why"
excerpt: "The Ionic Framework is built on AngularJS so starting at the 'bottom' of the MVC stack we will begin with an Angular Factory to do all the heavy lifting of interacting with the Particle Cloud API"
---
{% include toc %}
**Warning:** I am not an expert in any of the things I write about except perhaps Infrastructure so be mindful when following advice and using code snippets found in this post.
{: .notice--warning}

I often run into outdated or "good enough" tutorials/code when trying to accomplish different things in my Lemmy Light project and this is one I really struggled to find the correct answer.  When you are writing the code behind an [Ionic Framework](http://ionicframework.com) app you are in essence working in [AngularJS](http://angularjs.org) so oftentimes you can find answers by searching for "code problem angular" instead of Ionic but Ionic will net results as well.  The Lemmy Light app needs to consume data from [Particle's](http://particle.io) REST API in order to perform a number of user initiated actions like setting schedules, reading sensor data and more so I needed to write an angularjs controller and factory to connect to Particle and consume their JSON responses in the app.  Let's start with the factory.
{: style="text-align: left;"}

**Note:** You will see me use factory and service interchangably throughout this post which isn't technically correct but both factories and services syntactic sugar on top of the Angular provider.
{: .notice--info}

### AngularJS Factory
I won't go into a detailed explanation of what a factory is or what it does because it is well documented in many locations, what made it click for me is this great blog post [AngularJS: Factory vs Service vs Provider by Tyler McGinnis](https://tylermcginnis.com/angularjs-factory-vs-service-vs-provider-5f426cfe6b8c#.o3xhbot7l), so let's get into the details of the factory I use to interact with the Particle API.  
{: style="text-align: left;"}

```js
appServices.factory('pDevices', function($http, localstorage) {
  //Factory Variables and Private Methods
  root_url = 'https://api.particle.io/v1/',
  auth_url = 'https://api.particle.io/oauth/token',
  clientID_pass = 'Basic cGFydGljbGU6cGFydGljbGU=',
  device_id = localstorage.getItem('device_id'),
  access_token = localstorage.getItem('access_token');
```

As you can see I am using an appServices variable that was defined in my controllers.js and the starter.services module is injected in my app.js so I can inject services/factories as needed and keep my code modular.

```js
<!--From controllers.js-->
var appServices = angular.module('starter.services', []);
<!--From app.js-->
angular.module('starter', ['ionic','ngIOS9UIWebViewPatch', 'starter.controllers', 'starter.services', 'ngMaterial', 'ngMessages', 'ngCordova'])
```
In this factory you can also see that I am injecting $http and another service named localstorage into my factory.  You will see where they become relevant shortly.  The first three variables should really be injected as angular constants but for now they are included in the service.  The device_id and access_token variables are two values we are looking to get from the [Particle Cloud API](https://docs.particle.io/reference/api/) but they could be any JSON result that you wish to use in your own app.  When the factory is instantiated it will attempt to locate a device_id by executing the localstorage.getItem method with 'device_id' as the key.  If no value exists device_id value will be null.  The same process is used to determine if an existing access_token has already been acquired.  

**Warning:** Depending on the type of data you are storing, localstorage may not be secure enough.  An appropriate secure storage solution will need to be used.  One example would be [cordova-plugin-secure-storage](https://github.com/Crypho/cordova-plugin-secure-storage).
{: .notice--warning}

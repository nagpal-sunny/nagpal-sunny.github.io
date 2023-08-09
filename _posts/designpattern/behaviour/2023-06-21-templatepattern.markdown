---
layout: post
title:  "Design Pattern Series: Decorator Pattern (Structure Pattern)"
date:   2023-06-24 20:00:10 +0530
categories: [Design Pattern, Clean Code]
tags: [Decorator Pattern, Class Wrapper]
author: Saurav Nagpal
pin: true
published: true
sitemap: true
content: true
---
<style type="text/css">
  body{
  font-family:Roboto;
  font-size: 14pt;
}
</style>

##  <img src="/Assest/common/overview.png" width="30" height="30"> Overview

---
<br>
<p style="text-align: center;"> Decorator Pattern - Decorate an object (wrapper of an object).
<br>
The decorator pattern is used to add new functionality to an existing object without changing its structure. A wrapper on the existing object is created to add new functionality.
</p>
<br>

---

<br>

## <img src="/Assest/common/problem.png" width="30" height="30"> Problem Statement

One day I woke up and as per my daily routine attended the daily scrum parallelly checking the customer email.  As usual a new requirement (part of Agile :)) was asked by the customer to add one more analytic tool to the application. We already integrated Firebase analytic tool into the application. The reason mentioned by the customer to add a new analytic tool is that it provides good support with their BI Tool. 

Now I sit with the team and check the current implementation. The current structure has an analytics event controller class which accepts an event object and sends the same to the google analytics framework.

<br>

<img src="/Assest/designpattern/decorator/decoratorProblem.png">

<br>
One quick common approach all the team members have is to add support for Adobe Analytics in the Analytic controller, so when the developer sends analytics from any screen it will by default capture both frameworks (Firebase, Adobe Analytics).

<br>

<img src="/Assest/designpattern/decorator/decoratorProblemBruteForce.png">

<br>

With this approach, the Analytic Controller code will become bloated. If there is a new requirement to add a new type of analytics, then it will not be easy to accommodate in the controller. 

We brainstormed more on this to save ourselves and future team members and then the decorator came into the picture.

## <img src="/Assest/common/implementation.png" width="30" height="30"> Implementation

The decorator pattern uses an interface/protocol(Swift) with the composition.

<br>

<img src="/Assest/designpattern/decorator/decoratorSolution.png">

<br>

#### ***Code Snippet***

An analytic event is a struct which will hold an analytic event and Analytic Delegate is protocol.

{% highlight swift %}

struct AnalyticEvent {
    let eventName: String
    var eventDetail: [String: String]
}


protocol AnalyticDelegate {
    func sendEvent(_ event: AnalyticEvent)
}

{% endhighlight %}


{% highlight swift %}

//Firebase Analytics

import Firebase

class AnalyticController: AnalyticDelegate {
    init() {
        FirebaseApp.configure()
    }
    
    func sendEvent(_ event: AnalyticEvent) {
        Analytics.logEvent(event.eventName, parameters:event.eventDetail)
    }
}

{% endhighlight %}

{% highlight swift %}

//Class Implementation before Decorator
class AddToCartController {
    let analyticDelegate: AnalyticDelegate
    
    init(analyticDelegate: AnalyticDelegate = AnalyticController()) {
        self.analyticDelegate = analyticDelegate
    }
    
    func startPayment(for product: Product, paymentMethod: SupportedPaymentFacade) {
        let paymentInfo = PaymentInfo(skuID: product.skuID, amount: product.amount, method: paymentMethod)
        facade.doPayment(paymentInfo)
    }
}

{% endhighlight %}

{% highlight swift %}

//Decorator class implement the Adobe Analytics and delegate firebase event to the existing controller.

class AnalyticControllerDecorator: AnalyticDelegate {
    let analyticDelegate: AnalyticDelegate
    init(analyticDelegate: AnalyticDelegate = AnalyticController()) {
        self.analyticDelegate = analyticDelegate
    }
    
    func sendEvent(_ event: AnalyticEvent) {
        MobileCore.track(action: event.eventName, data: event.eventDetail)
        self.analyticDelegate.logEvent(event.eventName, parameters:event.eventDetail)
    }
}

{% endhighlight %}

{% highlight swift %}

//Class Implementation before Decorator

class AddToCartController {
    let analyticDelegate: AnalyticDelegate
    
    init(analyticDelegate: AnalyticDelegate = AnalyticController()) {
        self.analyticDelegate = analyticDelegate
    }
    
    func startPayment(for product: Product, paymentMethod: SupportedPaymentFacade) {
        let paymentInfo = PaymentInfo(skuID: product.skuID, amount: product.amount, method: paymentMethod)
        facade.doPayment(paymentInfo)
    }
}

{% endhighlight %}

{% highlight swift %}

//Uses with or without Decorator
var controllerWithFirebaseAnalytic =  AddToCartController(AnalyticController())
var controllerWithFirebaseAnalytic =  AddToCartController(AnalyticControllerDecorator())

{% endhighlight %}

<br>

## <img src="/Assest/common/applicablity.png" width="30" height="30"> Applicability
---
<br>

<div class="row" style="height:auto;">
  <div class="column" style="width:40px;float: left;"><img src="/Assest/common/listStar.png" width="30" height="30"></div>
  <div class="column" style="margin-left:50px;">Use the decorator when you need to add additional behaviour to an existing object without impacting the current uses of the object.
<br>
<font size="3">
The decorator object and main objects share a common interface so both can be used interchangeably. In this way, existing applications can use additional business requirements with minimal changes.
</font>
</div>
</div>
<br>

<div class="row" style="height:auto;">
  <div class="column" style="width:40px;float: left;"><img src="/Assest/common/listStar.png" width="30" height="30"></div>
  <div class="column" style="margin-left:50px;"> Additional behaviour requires on the final class without changing anything on an existing object.
<br>
<font size="3">
The decorator object and main objects share a common interface so both can be used interchangeably. In this way, existing applications can use additional business requirements with minimal changes.
</font>
</div>
</div>
<br>

## <img src="/Assest/common/factIcon.png" width="30" height="30"> Facts
---
<br>

#### **Inheritance vs Decorator**

<div class="row" style="height:auto;">
  <div class="column" style="width:40px;float: left;"><img src="/Assest/common/listStar.png" width="30" height="30"></div>
  <div class="column" style="margin-left:50px;">The decorator can be used on different objects which share a common interface. However, inheritance only works for similar kinds of objects.
</div>
</div>

<br>

<div class="row" style="height:auto;">
  <div class="column" style="width:40px;float: left;"><img src="/Assest/common/listStar.png" width="30" height="30"></div>
  <div class="column" style="margin-left:50px;">Classes with final behaviour. In those cases, inheritance is not possible. so we can use a decorator pattern to add additional behaviour.
</div>
</div>

<br>
<div class="row" style="height:auto;">
  <div class="column" style="width:40px;float: left;"><img src="/Assest/common/listStar.png" width="30" height="30"></div>
  <div class="column" style="margin-left:50px;">Decorator is applicable to only a single object of a class. However, inheritance is applicable to the entire class.
</div>
</div>
<br>
<div class="row" style="height:auto;">
  <div class="column" style="width:40px;float: left;"><img src="/Assest/common/listStar.png" width="30" height="30"></div>
  <div class="column" style="margin-left:50px;">Use inheritance when you need additional behaviour on non-public members of the class.
</div>
</div>

<br>
#### **Adapter vs Decorator**
<br>
<div class="row" style="height:auto;">
  <div class="column" style="width:40px;float: left;"><img src="/Assest/common/listStar.png" width="30" height="30"></div>
  <div class="column" style="margin-left:50px;">An adapter pattern also holds an object and provides a simplified interface (familiar Interface) to the outer world for that object.
</div>
</div>

<br>
<div class="row" style="height:auto;">
  <div class="column" style="width:40px;float: left;"><img src="/Assest/common/listStar.png" width="30" height="30"></div>
  <div class="column" style="margin-left:50px;">Adapter Object does not provide an additional behaviour on the object.
</div>
</div>

<br>
<div class="row" style="height:auto;">
  <div class="column" style="width:40px;float: left;"><img src="/Assest/common/listStar.png" width="30" height="30"></div>
  <div class="column" style="margin-left:50px;">In the case of the adapter, the client (who calls the method) does not aware of the object interface. It only knows the Adapter interface.
</div>
</div>


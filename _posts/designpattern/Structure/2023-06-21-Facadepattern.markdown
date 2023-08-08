---
layout: post
title:  "Design Pattern Series: Facade Pattern (Structure Pattern)"
date:   2023-06-24 20:00:10 +0530
categories: [Design Pattern, Clean Code]
tags: [Facade Pattern, Face of a building]
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
<p style="text-align: center;"> Facade - Face of a building.
<br>
A pattern to provide a simple interface to a complex framework or library. It hides the complexity and interaction between different objects in the framework.
</p>
<br>

---

<br>

## <img src="/Assest/common/problem.png" width="30" height="30"> Problem Statement

Suppose you are working on an e-commerce application. You have to support multiple payment options like UPI, Credit card, Debit Card, Wallet etc. You have chosen a third-party payment gateway which provides support for all types of payments. The payment gateway has an initiation setup (API key, session) and multiple objects for payment methods. 

If you write all the logic in your application class then your class object require to manage all the payment objects and track the life cycle of those objects. In this way, your code will be tightly depend upon third-party payment SDK.

<center>
<img src="/Assest/designpattern/facade/facadeProblem.png" width="70%" height="70%">
</center>

To make things simple we can use here Facade Design Pattern. 

## <img src="/Assest/common/implementation.png" width="30" height="30"> Implementation

In the Facade pattern, a class is created to provide a simple interface for a complex system.
 
<br>
<center>
<img src="/Assest/designpattern/facade/facadeSolution.png" width="70%" height="70%">
</center>
<br>

#### ***Code Snippet***

{% highlight swift %}
//Enum to identify payment type.
enum SupportedPaymentFacade {
    case wallet
    case upi
    case card
}

//Payment Information.
struct PaymentInfo {
    let skuID: String
    let amount: Int
    let method: SupportedPaymentFacade
}
{% endhighlight %}


{% highlight swift %}
// sample class which represent the complex third party framework
class PaymentSDKUmbrella {
    let walletSDK: PSWallet
    let upiSDK: PSUPI
    let cardSDK: PSCard
}

// Facade class application interact with this object. Application will not aware about PaymentSDKUmbrella.
class PaymentFacade {
    var paymentSDKUmbrella: PaymentSDKUmbrella?
    func doPayment(_ paymentInfo: PaymentInfo) {
        switch paymentInfo.method {
        case .card:
            cardSDK.doPayment(paymentInfo.amount)
        case .upi:
            upi.doPayment(paymentInfo.amount)
        case .wallet:
            if walletSDK.hasSufficientAmount(paymentInfo.amount) {
                walletSDK.doPayment(paymentInfo.amount)
            } else {
                print("Insufficient balance")
                walletSDK.addPayment(paymentInfo.amount)
            }
        }
    }
}
{% endhighlight %}

{% highlight swift %}
//Application uses of facade class
class AppPaymentController {
    let facade = PaymentFacade()
    
    func startPayment(for product: Product, paymentMethod: SupportedPaymentFacade) {
        let paymentInfo = PaymentInfo(skuID: product.skuID, amount: product.amount, method: paymentMethod)
        facade.doPayment(paymentInfo)
    }
}
{% endhighlight %}

## <img src="/Assest/common/applicablity.png" width="30" height="30"> Applicability
---
<br>
#### **Complex Library**

Integrating a library which has multiple subsystems can be done through the facade pattern. Also, if the library is used at multiple places then to avoid the redundant code of init and setup the library facade can be used.  In the above example, payment SDK has multiple sub-systems like wallet, net banking and Card payment. So you can create a facade class in a way that your application has minimal knowledge of all the dependencies of the payment SDK.

<br>
#### **Small Interfaces**

Use a facade pattern when you want to use limited functionality from a complex library.  In the case of payment SDK, if you want to use only wallet and card payment in your application then you can create a facade class to support only those features. 

## <img src="/Assest/common/factIcon.png" width="30" height="30"> Facts
---
<br>
#### **Adapter vs Facade Pattern**

<div class="row" style="height:auto;">
  <div class="column" style="width:40px;float: left;"><img src="/Assest/common/listStar.png" width="30" height="30"></div>
  <div class="column" style="margin-left:50px;">The facade provides a new interface to interact with a complex framework. However, the adapter fits the new object in the existing interface.
</div>
</div>

<div class="row" style="height:auto;">
  <div class="column" style="width:40px;float: left;"><img src="/Assest/common/listStar.png" width="30" height="30"></div>
  <div class="column" style="margin-left:50px;">An adapter or wrapper pattern is usually for a single object. However, Facade also interacts with multiple objects.
</div>
</div>

<br>
#### **Mediator vs Facade Pattern**

<div class="row" style="height:auto;">
  <div class="column" style="width:40px;float: left;"><img src="/Assest/common/listStar.png" width="30" height="30"></div>
  <div class="column" style="margin-left:50px;">The mediator class is used for providing the interaction between sub-components. However, the facade class is used for providing access (simple interface) to sub-components of a large component.
</div>
</div>

<div class="row" style="height:auto;">
  <div class="column" style="width:40px;float: left;"><img src="/Assest/common/listStar.png" width="30" height="30"></div>
  <div class="column" style="margin-left:50px;">In the case of the mediator, sub-components are aware of the mediator class but the facade class is only visible to the application using sub-components.
</div>
</div>
<br>

## <img src="/Assest/common/advantage.png" width="30" height="30"> Advantages
---
<br>
You must have been feeling now that the use of the facade pattern is just that the responsibility of interaction to complex system is shifted to facade class. 
<br>

#### **Decoupling**
It is always considered good practice that your application code should not directly depend upon the third-party library interface. Facade patterns provide a simple interface to a complex framework, in a way you also achieve decoupling here.  Like in the above case your application code depends upon the Facade class, so if in the future library code changes then only the Facade class will be impacted.

#### **Reusability**
In some use cases, the same library needs to be used from multiple places. Like in the above case payment can be done from multiple screens, so instead of the user accessing the library code at each screen the facade class can be used which also reduces the library setup/init burden at every place.

#### **Readability**
Most of the time library/framework code is legacy code. Coding convention changes from time to time and the library interface might not fit your current coding convention, So the facade can be used to make code consistent in your application.  
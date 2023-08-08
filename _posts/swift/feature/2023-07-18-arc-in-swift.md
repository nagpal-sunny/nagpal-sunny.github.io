---
layout: post
title: ARC in Swift
date: 2023-07-15 14:22 +0530
description:
image:
categories: [iOS, ARC, iOS Memory Management]
tags: [ARC, Swift ARC]
published: true
sitemap: true
content: true
---

One of the key factors for the success of any application is its performance. Effective Memory Management in an application is crucial for better performance of the application. iOS applications developed using Swift language rely on ARC for tracking and managing application memory usage. ARC uses reference counting to manage the application memory. Reference counting is only applicable to instances of classes. 

# What is ARC?

ARC is a memory management tool which tracks and manages the application memory. It is a compiler-driven feature. It observes the reference count of object of a class to manage and free the memory. So the question is what is reference count?

Reference count in simple language is the "number of pointers to any object".

{% highlight swift %}

public typealias Amount = Int

public class MyTravel {
    public var city: String
    public var totalSpending: Amount
    
    public init(city: String, totalSpending: Int) {
        self.city = city
        self.totalSpending = totalSpending
    }
}

{% endhighlight %}

{% highlight swift %}
//Uses of My Travel Class
//My trip List
let goaTrip = MyTravel(city: "Panji", totalSpending: 50000) //init is one pointer to Object of
print("I spent Rs \(goaTrip.totalSpending) in goa trip")
var tripWithHigherSpending = [goaTrip] // One more pointer to goaTrip
var recentTrip = goaTrip              // One more pointer to goaTrip
print("I spent Rs \(recentTrip.totalSpending) during recentTrip")

{% endhighlight %}


Reference counting only applies to class instances, so struct & enum do not participate in reference counting. 

# How ARC Works?

As mentioned, ARC is a compiler-driven feature. It inserts retain and release statements during compilation. 

- Retain increase the reference count.
- Release decrease the reference count.

<div id="divActivites" name="divActivites" style="padding-left:10px; padding-right:10px; background-color: #FFFFE0; border:1px solid black;">

<b><span style="color:#A8571B">#TechShorts</span></b>
<br>

ARC is the compiler-driven feature and it injects retain/release statements in a code during compilation time.

</div>

#### How does the compiler decide when to insert retain/release?

> Object life cycle in Swift begins with init and ends at last use.

ARC decide based on the above when to add retain and release statement.

{% highlight swift %}

public typealias Amount = Int

public class MyTravel {
    public var city: String
    public var totalSpending: Amount
    
    public init(city: String, totalSpending: Int) {
        self.city = city
        self.totalSpending = totalSpending
    }
}

{% endhighlight %}

<img src="/Assest/iOS/arc/arcCompile.png">


The object life cycle in Swift is different from the other language. In most of the other languages, it ends at the closing brace.

However, code should not be written based on this assumption and as it might change depending upon the compiler optimisation/changes. 

{% highlight swift %}
func explainARCSample() {
    //My trip List
    let goaTrip = MyTravel(city: "Panji", totalSpending: 50000)
    weak var goaWeakTrip = goaTrip
    var recentTrip = goaTrip

    recentTrip.city = "Manali"
    recentTrip.totalSpending = 40000
    
    print("I used weak reference here: Goa Trip Rs \(goaWeakTrip?.totalSpending ?? 50000) during recent trip")
}

{% endhighlight %}

You should not write logic as written above as the result is unexpected in the above case.

# ARC need your help.

ARC cannot detect or break **Reference cycle **. If object holds a strong reference to each other directly or indirectly in that case memory cannot be released of the objects.

<img src="/Assest/iOS/arc/arcRefrencecycle.png">

In the above cases, even if the compiler inserts a release statement the object will not be released from memory as both object holds each other.

Prevention is the only cure here, so while writing code this need to take care of.

#### Weak & Unowned Reference Cycle

To break the reference cycle weak and unowned keywords can be used. Both reference type does not participate in reference counting. 

{% highlight swift %}

public class MyTravelDiary {
    fileprivate var travels: [MyTravel]
    public init() {
        self.travels = []
    }
    
    public func addTravel(_ travel: MyTravel) {
        self.travels.append(travel)
    }
}

{% endhighlight %}

Weak reference can be used when you are not sure about the life cycle of the referred object. It will automatically be set to nil if the object reference count becomes zero. The only constraint with weak is must be optional. 

{% highlight swift %}

public class MyTravel {
    public var city: String
    public var totalSpending: Amount
    weak var travelDiary: MyTravelDiary?
    
    public init(city: String, totalSpending: Int) {
        self.city = city
        self.totalSpending = totalSpending
    }
}

{% endhighlight %}

Unowned reference can be used when you are sure about the life cycle of the referred object and it will not be deallocated. It can be used with an object which has an equal or greater life cycle. Unowned references can be declared as non-optional and faster access as compared to weak ones. The weak need to unwrap to access the value.

{% highlight swift %}

public class MyTravel {
    public var city: String
    public var totalSpending: Amount
    unowned var travelDiary: MyTravelDiary
    
    public init(city: String, totalSpending: Int, travelDiary: MyTravelDiary) {
        self.city = city
        self.totalSpending = totalSpending
        self.travelDiary = travelDiary
    }
}

{% endhighlight %}

{% highlight swift %}

let travelDiary = MyTravelDiary()

//unowned uses
let manaliTrip = MyTravel(city: "Manali", totalSpending: 40000, travelDiary: travelDiary)

//weak uses
let manaliTrip = MyTravel(city: "Manali", totalSpending: 40000)
manaliTrip.travelDiary = travelDiary

{% endhighlight %}

<div id="divActivites" name="divActivites" style="padding-left:10px; padding-right:10px; background-color: #FFFFE0; border:1px solid black;">

<b><span style="color:#A8571B">#TechShorts</span></b>
<br>
A weak reference is automatically set to nil if no strong reference remains and a weak reference is always optional. Unowned references will refer to deallocated memory if no strong reference remains, so the application can crash. Unowned is always non-optional.

</div> 


# Bonus.

There are safe ways to access the weak refrence to avoid unexpected result or fatal excpetion (force unrawp) at run time.

{% highlight swift %}

public class MyTravel {
    public var city: String
    public var totalSpending: Amount
    public weak var travelDiary: MyTravelDiary?
    
    public var isExistInTravelDiary: Bool {
        return self.travelDiary!.travels.filter {
            $0.city == self.city
        }.first != nil
    }
    
    public init(city: String, totalSpending: Int) {
        self.city = city
        self.totalSpending = totalSpending
    }
}

public class MyTravelDiary {
    fileprivate var travels: [MyTravel]
    public init() {
        self.travels = []
    }
    
    public func addTravel(_ travel: MyTravel) {
        self.travels.append(travel)
    }
}

{% endhighlight %}

#### Option 1: withExtendedLifetime

{% highlight swift %}

func testTravelDiary() {
    //Manali Trip
    let travelDiary = MyTravelDiary()
    let manaliTrip = MyTravel(city: "Manali", totalSpending: 40000)
    manaliTrip.travelDiary = travelDiary
    
    withExtendedLifetime(travelDiary) {
        let existString = manaliTrip.isExistInTravelDiary ? "exist" : "not exist"
        print("Manali Travel \(existString) in Travel Diary")
    }
}

{% endhighlight %}

#### Option 2: Strong Reference Capture

{% highlight swift %}
public var isExistInTravelDiary: Bool {
     if let travelDiary = self.travelDiary {
           return travelDiary.travels.filter{
                $0.city == self.city
            }.first != nil
      }
      return false
 }
{% endhighlight %}
---
layout: post
author: Saurav Nagpal
title:  "Swift ?? (Nil Coalescing) Do's and Don't"
date:   2023-06-21 02:24:10 +0530
pin: true
categories: [Swift, Swift Safety]
tags: [Swift optional, Swift Nil Coalescing]
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

## What is it?
Optional is one of the fundamental safety features provided in Swift. It is a container for a value of a particular type. We are using the term container here because it means it may contain a value or not. Nil coalescing `??` is an operator used to provide a default if the optional is empty.


## How to use it?
Add default value after the `?? ` operator following Optional.

{% highlight swift %}
 var name: String?
 let displayName = name ?? "NA"
 print("name is:\(displayName)")
{% endhighlight %}
 
In this case, if the name is not available then "name is NA" will be printed. In Swift, we need to always take care while unwrapping an Optional as it might crash the application. The Nil coalescing `??` operator does the Job for you if it finds the optional has no value then it uses a default instead. 


## Do's and Don't with `??` (#My Opinion)

Following are some of the uses I have seen in the code and we go through them one by one and discuss the case:

#### (i) Requirement is if Name is not present then display "NA"
{% highlight swift %}
 var name: String?
 let displayName = name ?? "NA"
 print("name is:\(displayName)")
{% endhighlight %}

#### (ii) Requirement is to get number of rows in the selected section
{% highlight swift %}
    func indexPathOfTableView() {
        var selectedIndexPath: IndexPath?
        //Assing a value in code somewhere
        selectedIndexPath = IndexPath(section: 1, row: 0)
        
        //Uses of the indexPath
        tableView?.numberOfRows(inSection: selectedIndexPath?.section ?? 0)
    }
{% endhighlight %}

#### (iii) Requirement is to fetch a random student from the list
{% highlight swift %}
    func randomStudent() {
        var randomStudent: Student?
        //Assign Value to the random student in code
        
        //Somewhere in code
        let selectedStudent = randomStudent ?? Student()
        if selectedStudent.name.count > 0 {
        }
    }
{% endhighlight %}

#### (iv) Requirement is to fetch all student names.
{% highlight swift %}
    struct Student {
        var name: String
    }
    
    func fetchAllNames() {
        var students: [Student]?
        //All student Name
        let studentNames = students?.map { $0.name ?? "" }
        print("All Student Names:\(studentNames)")
    }
{% endhighlight %}

#### (v) Requirement is to check if the student list exists or not.
{% highlight swift %}
   func checkStudentExist() -> Bool {
        var students: [Student]?
        //check student exist
        if students?.count ?? 0 > 0 {
            return true
        }
        return false
    }
{% endhighlight %}

#### My Opinion

##### (i) Do - It is correct usage as we provide the default value if it does not exist.

##### (ii) Don't - 0 is not the default value for the section and it also can cause an unexpected issue If the index path in any case nil.

##### (ii) Don't - In this case, a student object without referring to any student is not a correct usage it is not a default value. It can cause an issue or crash if you are expecting a real object and get this value.

#### (iv) Don't - The requirement is to fetch all names of a student and in case the student does not exist in the record then you should not assign a default value like "". Either you have some agreed default value or raise an exception name not exist. 

#### (v) Don't - The requirement is to check if the student list exists or not. In the case of the, `if students?.count ?? 0 > 0` condition using the default value is reducing the condition. However, in my opinion, it reduces the readability. 

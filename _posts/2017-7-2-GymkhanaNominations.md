---
title:  "Gymkhana Nomination Portal"
subtitle: "Django Unchained"
author: "Aniket Pandey"
avatar: "img/authors/wferr.png"
image: "img/a.jpg"
date:   2017-07-02 12:12:12
---
<style>
    body{
        text-align: justify;
        font-family: 'Open Sans',serif;
    }
</style>
<link href="https://afeld.github.io/emoji-css/emoji.css" rel="stylesheet">
<link rel="stylesheet" type="text/css" href="http://fonts.googleapis.com/css?family=Open+Sans">

Summer Project

I got a project under Programming club in summers that turned out to be a great experience in creating a scalable web application in **Django**.
My project partner, Ashish and me started off by building a basic library portal through which it is possible to add users,
authenticate their account, and allow them to borrow books online. 

It does sound cool but it really is very frustrating if you try is by your own the very first time. Our mentor Pratham asked us to 
create this library so that we can be fairly comfortable while starting the main project. I searched for some tutorials on 
Django on the internet, and found one by MDN which coincidentally, was also focused on staring out with a library. 

### Writing my first server

I tried following that tutorial but somewhere in between I got distracted and couldn't complete the portal. I assumed I'll have enough
time to completely grasp the concepts and then work on our main project. But when I came back to campus in mid-May to start off 
the camp, I realized I should've given more time to learning basics as Ashish had already created his portal which looked 
stunning and it already had all the features that Pratham asked us to include.

With no option left, I pulled two all-nighters and tried reading the source code Ashish had written. Though it was tough initially,
I finally started understanding how things worked in Django.

### What is Django

Django is a web-framework written in Python which allows us to create dynamic and scalable web application through which, it is 
easy to manage users of various permissions. Also, Django being in Python makes it more intuitive and easy to understand. I had already 
done a lot of programming in Python while preparing for GSoC (in this blog). So picking up new concepts wasn't that hard.

There are three main components in Django

* Models
* Views
* Templates

#### Models

A model is the single, definitive source of information about your data. It contains the essential fields and behaviors
of the data you’re storing. Generally, each model maps to a single database table.

Model is like the brain of your application. It contains the information about all the columns of each table in the database.
 
#### Views

A view function, or view for short, is simply a Python function that takes a Web request and returns a Web response.
This response can be the HTML contents of a Web page, or a redirect, or a 404 error.
 
Through views, we can handle requests from clients and return a response afterwards. The response is generally in form of
rendered HTML page or in form of common data like JSON. JSON is extremely useful in creating web APIs through which we can 
let server and client communicate quickly. Also, both can be written in different languages and still work just fine.

#### Templates

A template contains the static parts of the desired HTML output as well as some special syntax describing how dynamic 
content will be inserted.

Templates are what let you create user friendly interface. Django has its own template system through which you can write
python inside of html. 
 
There are a lot of other functionalities offered in django like _forms_, _admin portal_, _user authentication_, _csrf_, etc.
We used a major part of what django had to offer. 

### IITK Authentication

The authentication system in django makes use of the users stored in database. Since we did not have anyone's account pre-
recorded, we had to rely on the fact that once someone logs in, we can ask them to fill the rest of their details like, name,
roll no, address etc. However, there was no way of limiting the users to the IITK junta. Since anybody can log in and pose
as a valid student, we had to override this authentication system. 

Fortunately, django allows us to have our own authentication system, so we wrote it by ourselves, using _new-webmail_'s database,
we were able to identify if someone who is logging in is actually a student of IIT Kanpur. This was the first major step in our project.


## Part 2

Creating the library portal was a boon for us. It had only been three weeks(less than half the time) since the camp started, 
we had completed our backend functionality.

This headstart gave us enough time to actually think about the features that we would be adding in to the portal, we often had
long discussion sessions with each other and also with our mentors. While Pratham would clear all our Django related doubts,
Yash had a decent knowledge of what was to be included in the portal. 

### The dynamic heirarchy

This is the flashing point of our project. Initially when Kunal was explaining us about the specifics of the project, he mentioned 
something called infinite level of heirarchy. What it means is that there should not be any limit on the number of posts 
that are possible. A club should be able to have as many posts as required, either 2 or 3 or even 50. 
 
Although it seems a daunting task to accomplish, it did not cause much problems in the end. Thanks to Ashish, we were able to 
figure out a way to do so. What we did was that we linked a _Post_ model to itself and named it _parent_. 
```
class Post(model.Models):
    post_name = models.CharField(max_length=500, null=True)
    club = models.ForeignKey(Club, on_delete=models.CASCADE, null=True, blank=True)
    parent = model.ForeignKey(self, null=True, blank=True)
    post_holders = models.ManyToManyField(User, related_name='posts', blank=True)
```

These are four of many columns in the Post table. The third line is what turned the table for us. Although it may seem too
trivial, we did not know abot self linking at that time. We had to research a lot to be able to get to something like this.

### The dynamic forms

This is another highlight of the portal. Mostly written by Ashish. It allowed us to create dynamic forms, that is, while
releasing nominations, the admins can create forms which the applicants have to fill. The filled forms can then be evaluated
by the interview panel, regarding which they can add few comments of their own. 

This is how the process goes underway, the only difference (and benefit of switching over to the new portal) is that everything 
could be done at a single place. No need to create a separate Google form and record comment in your own laptop and then finally 
create a list of selected applicants. All of it is taken care of in the portal. 
 
### The dynamic approval system

Most of the stuff that happens in gymkhana needs an approval from higher authority. Mostly by the Gen-Secs or the Chairperson.
While creating a Post or a Nomination You need a final approval from the concerned authority.

This was another challenge for us, as to accomplish it, we needed to create a ladder like functionality so that an approval 
can climb up the ranks and after getting a heads-up from the admin, it would be in effect. 

It was another genious effort from our side to get around this problem. We created a separate field in _Post_ and _Nomination_
models:
```
class Post(model.Models):
    ...
    post_approvals = models.ManyToManyField('self', related_name='approvals', symmetrical=False, blank=True)
    
class Nomination(model.Models):
    ...
    result_approvals = models.ManyToManyField(Post, related_name='result_approvals', symmetrical=False, blank=True)
    nomi_approvals = models.ManyToManyField(Post, related_name='nomi_approvals', symmetrical=False, blank=True)
```

You would observe there are three different approval fields `post_approvals`, `result_approvals`, `nomi_approvals`. These 
columns contain the list of all the Posts, above the post for which the nomination was released. Once it passes through a 
 certain level, the very next level gets added to the approvals list. So our Nomination knows where to go next for approval.
 This continues till it reaches the highest authority, on whose approval, the nomination is released for public. 
 
Same can be said for Posts, although it is highly unlikely that any additional posts would be created. But, to make it more dynamic,
we allowed separate posts to be created, just in case..

## Part 3

Finally after 7 weeks of reckless coding, our project is finished. I am writing this on the day we finally decided to 
conclude our project and inform the mentors to make certain changes and deploy it. With *779 commits* on GitHub 
 repository and more than *4000 lines of code*, the Nomination Portal comes to life.
 
### The UI Designing

I love front-end development. It is the only reason I started exploring other fields in programming. And I think I mentioned
this in one of my previous blogs. 

By the time we had almost finished most of the backend functionality, we thought a step ahead and decided rather than waiting
for a heads-up from the mentors about the backend features, we should start creating the User Interface. 

I took the responsibility of creating the base template. And since it was clearly instructed that the interface should be 
responsive and should work on all devices, we had no other option but to use Bootstrap. You would be thinking why am I upset
with Bootstrap. 
Well, to be honest, Bootstrap is great. It lets you create responsive UIs quickly. The only letdown is that 
I would not be able to write a custom CSS. Cause fixing margins and text-padding is out of option.  

After going through all the templates available at _bootswatch_, I chose a theme called **Cerulean**. It had this nice 
sky-blue navigation bar and various other features available. The basic design was supposed to have a profile sidebar,
a nav-bar and the main content. The index page will have the list of nominations out for application. Once the basic layout is
finalised, we'll work on adding more templates and quick links.

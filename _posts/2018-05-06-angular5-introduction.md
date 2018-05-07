---
layout: post
title: "Angular 5 Introduction"
description: ""
category: "Angular JS"
---

After a long hiatus from Angular, started building a website with Angular 5 recently and was pleasantly surprised by the complete overhaul of the framework compared to older versions that I was familiar with(Angular 2):

* Much easier object oriented support
* Easy to use HTTP Client
* Typescript which offers Dynamic Strong typing which makes it easier for compilers to spot erors but the strong typing is removed when translated to Javascript.

Getting Started:

* Install NodeJs and npm
* install angular CLI:

{% highlight java %}
npm install -g @angular/cli
{% endhighlight %}

* Create a new project:

{% highlight java %}
ng new <project-name>
{% endhighlight %}

* Serve it:

{% highlight java %}
npm start
{% endhighlight %}

Voila! Your Angular app is up and running.

Now let's do something useful aka build a component(e.g: a login component):


{% highlight java %}
ng g c login
{% endhighlight %}

This will generate a login component with the following files:

* login.html
* login.ts
* login.css
* login.spec

The structure is similar to earlier versions of Angular which used Javascript instead of Typescript.

To use this component, it has to be exported as an app module in app.module.ts:

{% highlight java %}
import { LoginComponent } from './login/login.component';
@NgModule({
  declarations: [
    LoginComponent
	]})
{% endhighlight %}

Provide routing for the same in app.routing.module.ts:

{% highlight java %}
import { LoginComponent } from './login/login.component';
const routes: Routes = [
 { path: '',
    redirectTo: '/login',
    pathMatch: 'full'
  },
  { path: 'login', component: LoginComponent }
 ];
{% endhighlight %}

Now to serve it on the app default webpage, edit app.component.html to contain:

{% highlight java %}
<router-outlet></router-outlet>
{% endhighlight %}

That's it. Easy as that. Now you can build slick interfaces using Angular and Angular Material. I will write more on this in subsequent posts.



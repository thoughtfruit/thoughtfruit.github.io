---
layout: post
title: "Backbone.js was the Backbone"
categories:
- blog
tags:
- webdev
---

I have been thinking about JavaScript a lot lately, and that is partially due to my job. My job entails being the sole front-end developer on a pretty large web application. It is a project for the second largest bidding site in the US, and as you can imagine employs a lot of JavaScript usage throughout. When I started there I was mainly a back-end guy who wanted to trasnfer to front-end out of passion for the languages (HTML5/CSS3/etc). I quickly came to realize I would have to pick up JavaScript rather quickly as a result of the framework already set up for the project I was working on. And as you can imagine, I have been focusing on learning as much jQuery, JavaScript and Backbone as I can. We use Backbone, and at first that was a pretty deep-water jump into the pool of JS, for a beginner. But it has really made me a better programmer overall.

Here is a sample snippit of something we use in Backbone pretty regularly:

```javascript
{
    setTemplates: function () {
        this.itemTemplate = $(".template[data-template-name=leaderboard]", this.$el).html();
    },

    bindRenderOnModelChange: function () {
        var _this = this;
        this.model.on("change reset", function () {
            _this.render();
        });
    },

    render: function () {
        if (this.itemTemplate == null || this.itemTemplate == undefined) return;
        var templateHtml = Mustache.render(this.itemTemplate, this.model.toJSON());
        $(this.$el).html(templateHtml);
    };
}
```

As you can tell, this is pretty beautiful code. We aren’t doing raw injection into the HTML, we are using Backbone models, we are using Mustache templating, and we are defining that updates only occur on model change or reset. That model then talks to the controller (in this case a C# controller), and does all the communication for us. Later on in this code we use an events call to say app.events.on instead of app.model.on, as we wanted to abstract all events through an events handler. This just gives you a small idea of how much fun I’ve been having with Backbone, and JavaScript in general. It is quickly growing to be my favorite programming language alongside Ruby. I am still rather new to jQuery methods and chaining, and such, but with Backbone I am really getting it. And that makes me a better JavaScript programmer, not just a ‘jQuery guy’. And that makes all the difference.

As you can tell, we do a lot of work with Objects; mostly in the form of Ajax calls and JSON results. Having Backbone to make sense of all that is really an amazing thing. It is like learning things the right way the first time, instead of the wrong way the first time. Though, I will admit when I first started there all of this 'backbone stuff' was so far over my head - I couldn't even grasp it. And I feel like that is how a lot of people who approach JavaScript for the first time feel. What I would say is you need to find a project that you are passionate about that employs the use of the language you are trying to learn, and go from there. It really does make all the difference. For instance, just yesterday we were working with a complex object result set in JSON that was sending back Arrays inside of objects, and inside of that array were more objects. That was a lot of fun, and using Mustache templating it was a total joy.

So what is the point of this post? Well, **to remind you all to learn backbone**. It will make you a better programmer. It will help you with conventions, and functionality, which at the same time will help you with code design and reusability. And as a sidenote: those two are practically unheard of going side by side. Go create an application, and play around. Backbone and JavaScript are amazing tools. And if you need any help, feel free to reach out and we can chat. I can perhaps give some advice on how to approach it so you know not to feel overwhelmed. Let's go build stuff.
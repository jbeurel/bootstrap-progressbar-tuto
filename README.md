How to code JavaScript using Twitter Bootstrap best practices
=============================================================

First of all, you can see this article with syntax highlighting on the [Theodo blog](http://www.theodo.fr/blog/2014/03/how-to-code-javascript-using-twitter-bootstrap-best-practices/).

Twitter Boostrap provides a lot of UI plugins easy to implement. You've probably already used it but have you looked at how it worked? Together, we will try to make another plugin using the same best practices as Twitter. Through this tutorial I will introduce some JavaScript concepts like prototype, data-api or object-oriented programming.

A great way to learn is to work on a concrete example so you will build a jQuery plugin in order to manipulate a Twitter Bootstrap progressbar.

You can see the [live code](http://codepen.io/jbeurel/pen/zuDAl) on my [CodePen account](http://codepen.io/jbeurel)

> Git: The code used in this tutorial is available on GitHub. You can follow the process step by step by using git tags:
>
>   1. Clone the [bootstrap-progressbar-tuto](https://github.com/jbeurel/bootstrap-progressbar-tuto "github.com") repository:
>
>         git clone https://github.com/jbeurel/bootstrap-progressbar-tuto.git
>
>   2. Change your current directory to `bootstrap-progressbar-tuto`
>
>         cd bootstrap-progressbar-tuto
>
>     This is your working directory for the rest of this tutorial
>
>   3. You can already see the rendering by opening the `index.html` file in your web browser


# step-0: The DOM

> Git: This command resets your working directory to the step 0 of the tutorial:
>
>     git checkout -f step-0
>

Let's begin by using basically the [Bootstrap Progressbar](http://getbootstrap.com/components/#progress "getbootstrap.com"):

    <html>
    <head>
        <title>Bootstrap Progress Bar</title>
        <link rel="stylesheet" href="//netdna.bootstrapcdn.com/bootstrap/3.0.0/css/bootstrap.min.css">
    </head>
    <body>
        <div class="container">
            <h1>Bootstrap Progress Bar</h1>

            <div class="progress">
              <div class="progress-bar" role="progressbar" aria-valuenow="40" aria-valuemin="0" aria-valuemax="100" style="width: 40%;">
                <span class="sr-only">40% Complete</span>
              </div>
            </div>
        </div>

        <script src="//code.jquery.com/jquery.js"></script>
        <script src="//netdna.bootstrapcdn.com/bootstrap/3.0.0/js/bootstrap.min.js"></script>
    </body>
    </html>

You've created a simple template that includes Twitter Bootstrap assets and displays the static progressbar example [provided by Bootstrap](http://getbootstrap.com/components/#progress-basic "getbootstrap.com")

> Git: [Code Diff](https://github.com/jbeurel/bootstrap-progressbar-tuto/compare/step-0...step-1 "github.com")

#step-1 - The jQuery plugin

> Git: Reset the workspace to step 1:
>
>     git checkout -f step-1
>

Next step: Javascript!

Create a new `js/progressbar.js` file with this code:

    !function ($) {

        "use strict";

        // PROGRESSBAR CLASS DEFINITION
        // ============================

        var Progressbar = function (element) {
            this.$element = $(element);
        }

        // PROGRESSBAR PLUGIN DEFINITION
        // =============================

        $.fn.progressbar = function (option) {
            return this.each(function () {
                var $this = $(this),
                    data = $this.data('jbl.progressbar');
                if (!data) $this.data('jbl.progressbar', (data = new Progressbar(this)));
            })
        };

    }(window.jQuery);

## What is the code doing?

  * Auto-execute Javascript function:

        !function ($) {
        }(window.jQuery);

    The `!` converts the function declaration to a function expression and allows to invoke it immediatly by adding the `(window.jQuery)` parentheses. You can find more information on [this Stackoverflow thread](http://stackoverflow.com/questions/3755606/what-does-the-exclamation-mark-do-before-the-function "stackoverflow.com").

  * Class definition

        var Progressbar = function (element) {
            this.$element = $(element);
        }

    This creates a Progressbar class that contains the DOM element that you'll be able to manipulate later.

  * jQuery plugin definition

        $.fn.progressbar = function () {
            return this.each(function () {
                var $this = $(this),
                    data = $this.data('jbl.progressbar');
                if (!data) $this.data('jbl.progressbar', (data = new Progressbar(this)));
            })
        };

    `$.fn` is an alias of the `jQuery.prototype`. You can use it to extend jQuery with your own `progressbar` function. You use each DOM element selected by a jQuery selector as a Singleton data storage to store an instance of a Progressbar object.

> Git: [Code Diff](https://github.com/jbeurel/bootstrap-progressbar-tuto/compare/step-1...step-2 "github.com")

#step-2 - The prototype

> Git: Reset the workspace to step 2:
>
>     git checkout -f step-2
>

The next step is to add an `update` function to your Progressbar to change its value. Methods can be added to a JS class by defining functions as children of the `prototype` class attribute (see [Object.prototype](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/prototype "mozilla.ord")). In this way, add this code to your `js/progressbar.js` file:

    Progressbar.prototype.update = function (value) {
        var $div = this.$element.find('div');
        var $span = $div.find('span');
        $div.attr('aria-valuenow', value);
        $div.css('width', value + '%');
        $span.text(value + '% Complete');
    }

This function changes the values of the aria-valuenow attribute, CSS width and the text. Using it will update all this elements at once. Let's improve the plugin definition to use your newly created function:

    $.fn.progressbar = function (option) {
        return this.each(function () {
            var $this = $(this),
                data = $this.data('jbl.progressbar');
            if (!data) $this.data('jbl.progressbar', (data = new Progressbar(this)));
            if (typeof option == 'number') data.update(option);
        })
    };

Add `option` parameter to your `progressbar` function and use it to call the `update` function. Try out this command in the browser console to test your code:

    $('#myProgressbar').progressbar(20)

Nice! You can see the Progressbar change.

> Git: [Code Diff](https://github.com/jbeurel/bootstrap-progressbar-tuto/compare/step-2...step-3 "github.com")

#step-3 - The use case

> Git: Reset the workspace to step 3:
>
>     git checkout -f step-3
>

Your plugin is technically working, but how do you want to use it ? The _Twitter Bootstrap style_ answer is to call it from your HTML markup. Update the `index.html` file by adding this HTML:

    <p>
        <button data-toggle="progressbar" data-target="#myProgressbar" data-value="0" class="btn btn-default">0%</button>
        <button data-toggle="progressbar" data-target="#myProgressbar" data-value="10" class="btn btn-default">10%</button>
        <button data-toggle="progressbar" data-target="#myProgressbar" data-value="30" class="btn btn-default">30%</button>
        <button data-toggle="progressbar" data-target="#myProgressbar" data-value="75" class="btn btn-default">75%</button>
        <button data-toggle="progressbar" data-target="#myProgressbar" data-value="100" class="btn btn-default">100%</button>
    </p>

  * `data-toggle="progressbar"` allows to mark this button as a Progressbar toggler.
  * `data-target="#myProgressbar"` determines which Progressbar is updated by this button.
  * `data-value="0"` defines next value of the Progressbar.

This HTML is the contract that you will try to honor in the next step.

> Git: [Code Diff](https://github.com/jbeurel/bootstrap-progressbar-tuto/compare/step-3...step-4 "github.com")

#step-4 - The DATA-API

> Git: Reset the workspace to step 4:
>
>     git checkout -f step-4
>

For each click on a button, you will use the jQuery object data-api to collect the information needed by the plugin:

    // PROGRESSBAR DATA-API
    // ====================

    $(document).on('click', '[data-toggle="progressbar"]', function (e) {
        var $this = $(this);
        var $target = $($this.data('target'));
        var value = $this.data('value');
        e.preventDefault();
        $target.progressbar(value);
    });

This listener is added to the `progressbar.js` file and listens to all the buttons in the application containing the `data-toggle="progressbar"` attribute. Finally, update the Progressbar with the correct value.

> Git: [Code Diff](https://github.com/jbeurel/bootstrap-progressbar-tuto/compare/step-4...step-5 "github.com")

#step-5 - The optional functions

> Git: Reset the workspace to step 5:
>
>     git checkout -f step-5
>

Your Progressbar jQuery plugin works! Last exercise to improve it. How could you use this new button?

    <button data-toggle="progressbar" data-target="#myProgressbar" data-value="reset" class="btn btn-default">Reset</button>

`typeof` javascript function allows to treat differently an integer and a string passed to the the `data-value` attribute and calls the correct function:

    if (typeof option == 'string') data[option]();

This line added to the plugin definition allows to call this function:

    Progressbar.prototype.reset = function () {
        this.update(0);
    }

The plugin definition is now ready to accept some improvements. You can simply add a new function to the plugin and call it by passing its name to the `data-value` attribute. It has never been easier to add a finish function. In the `progressbar.js`file:

    Progressbar.prototype.finish = function () {
        this.update(100);
    }

In the template:

    <button data-toggle="progressbar" data-target="#myProgressbar" data-value="finish" class="btn btn-default">Finish</button>

Works like a charm, doesn't it?

> Git: [Code Diff](https://github.com/jbeurel/bootstrap-progressbar-tuto/compare/step-5...step-6 "github.com")

#Conclusion

This code, inspired by [Bootstrap plugins](https://github.com/twbs/bootstrap/tree/master/js "github.com"), was to show you how to create a jQuery plugin using a Javascript object, its prototype and communicate between DOM element through the jQuery data-api.

I hope you read this tutorial with interest. Don't hesitate to comment for any questions or suggestions!

As you can see on [my GitHub page](http://jbeurel.github.io/bootstrap-annotated-source/docs/modal.html "github.io"), I started documenting Bootstrap source code thanks to [Docco](http://jashkenas.github.io/docco "github.io"). I invite you to contribute to this documentation on this GitHub repo [github.com/jbeurel/bootstrap-annotated-source](https://github.com/jbeurel/bootstrap-annotated-source "github.com") ;-) 

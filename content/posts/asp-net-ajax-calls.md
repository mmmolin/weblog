---
title: Simple jQuery AJAX calls in ASP.NET
date: 2021-06-24
---
I know jQuery is old news but I thought it would be fun to try out doing some AJAX calls using it. With AJAX, or Asynchronous HTTP request, we can exchange data with a server or update an ASP.NET view without having to reload it. There are newer and better libraries for doing this, maybe more on that topic in future posts.

So first off we got to create two simple methods in our Home Controller. We are going to call these methods from the Index View using GET and POST requests. We create "GetGreeting()" for out GET request and "AddName()" for our POST request.
```csharp
public class HomeController : Controller
    {
        public string GetGreeting(string name)
        {
            return $"Hello {name} !!";
        }

        public string AddName(string name)
        {
            if(String.IsNullOrEmpty(name))
            {
                return "Failed to add name";
            }

            // Save name to database
            return $"Added {name}";
        }
       
    }
```

Let's drop by our index view and see what we will have to do there to get this to work.
First of all, we create a paragraph element. This is where we will present the return data from the controller actions.
We create an input field, which data well send to the controllers, and a button to activate the HTTP requests.

This is where the fun part begins! Now we'll use jQuery to add a click event on the button, the event will read the text in the input field and send it as an argument to the controller action using a GET request. We then use the return result to change the content of the paragraph element.
```html
<p id="output"></p>
<input type="text" id="nameInput" />
<button id="greeting-btn">Get Greeting</button>

@section scripts
{
    <script type="text/javascript">
        $("#greeting-btn").click(function (data) {
            let input = $("#nameInput").val();
            $.get('@Url.Action("GetGreeting")', { name: input }, function (data) {
                $("#output").html(data)
            })
        })
    </script>
}
```

The same thing goes for doing a POST request to the "AddName()" action. Just change from "get" to "post" like in the following code block. 
```html
<p id="output"></p>
<input type="text" id="nameInput" />
<button id="post-btn">Post Name</button>


@section scripts
{
    <script type="text/javascript">
        $("#post-btn").click(function (data) {
            let input = $("#nameInput").val();
            $.post('@Url.Action("AddName")', { name: input }, function (data) {
                $("#output").html(data)
            })
        })
    </script>
}
```

So there you have it, a simple way to make AJAX calls using jQuery.
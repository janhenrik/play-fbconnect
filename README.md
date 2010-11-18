# play-fbconnect

Easily integrate Facebook authentication into any Play application. 

## Demo

[http://play-lists-fbconnect.appspot.com/](http://play-lists-fbconnect.appspot.com/) is an instance of Guillaume's lists-with-gae demo, modified to utilize this module for Facebook based authentication. The source code of the modified demo can be found in the `samples-and-tests` folder.

## Prerequisites

Before you can use Facebook authentication, you have to [register your application with Facebook](http://www.facebook.com/developers/createapp.php). Be sure to note your Application ID, API Key, and Application Secret.

## Installation

### Install the module

Install the fbconnect module from the modules repository:

    play install fbconnect

### Enable the module

After installing the module, add the following to your `application.conf` to enable it:

    module.fbconnect=${play.path}/modules/fbconnect

### Configure the module

And finally, you need to configure the module by setting these properties in your `application.conf`:

    # Facebook Connect
    # ~~~~~
    fbconnect.id=YOUR_APP_ID_HERE
    fbconnect.apiKey=YOUR_API_KEY_HERE
    fbconnect.secret=YOUR_APP_SECRET_HERE
    fbconnect.model=models.User
    fbconnect.landUrl=/home

All of the properties are required except for `fbconnect.landUrl`, which will default to `/` (Note: `fbconnect.landUrl` can be a URL like `/home` or a controller action like `Application.index`).

## Usage

### Define your OAuth callback

The first step is to implement a method called `facebookOAuthCallback` on the class that you specified as `fbconnect.model`. After a user has authenticated using Facebook, the module will call this method with a JsonObject that contains data about the user. This is your opportunity to add the user to your database, add the user to your session, or do anything else you want to do with the authentic data Facebook provides.

    public static void facebookOAuthCallback(JsonObject data){
        String email = data.get("email").getAsString();
        User user = findByEmail(email);
        if(user == null){
            user = new User();
            user.email = email;
            user.insert();
        }
        Session.current().put("user", user.email);
    }

### Use the `fbconnect.button` tag in your view

The module provides a tag called `fbconnect.button`. The tag outputs a link that will prompt your users to authenticate with Facebook when it is clicked.

	#{fbconnect.button label:'Login using your facebook account.' /}

The tag takes two optional parameters. `label` which defaults to *Sign in with Facebook*, and `cssClass` which defaults to *play-fbconnect-button*.

### Add some style

You are welcome to style the button however you like (heck, you can leave it as a plain link for all I care), but the fbconnect module provides some default styles that you can use to get a decent looking Facebook Connect button.

#### Before

![before](http://play-lists-fbconnect.appspot.com/assets/images/before.jpg)

#### After

![after](http://play-lists-fbconnect.appspot.com/assets/images/after.jpg)

These styles are in a file called `play-fbconnect.min.css` and they can be found in the module's `lib` directory. I should note that the styles will not work if you specify a custom `cssClass` when using the `fbconnect.button` tag.
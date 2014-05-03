# Expose yourself with ngrok

One of the more common tasks we have in web development involves the need to
share our web application with other developers and APIs. For a production web 
site, that’s already taken care of. You’ve got a web server, you deploy your 
application, and then sit back and wait for your buyout offer from Facebook. But
while developing, you’re probably working on your local development server where
sharing can be a bit more difficult.

This becomes more of an issue when you need to test something hitting your
application. As an example, GitHub supports something called a “web hook.” 
Basically, you can tell GitHub to run a URL whenever something happens to your 
project. In order for this to work right, GitHub needs a URL that it can hit as 
well. If you told it http://localhost than that obviously wouldn’t work because 
in context of the GitHub server, localhost is itself.

Enter [ngrok][2], a simple service that lets you expose a local web service (of
any sort,: Node.js, ColdFusion, PHP, etc) to the internet. Not only does it let 
you expose services, it lets you examine the requests coming in as well as 
replaying them (This by itself could be crucial. If you are testing a service 
with rate limits, you could ask ngrok to rerun a request without using the *real*
remote service).

ngrok is 100% free, but with additional services provided for people who
register as well as those who donate money. You don’t even have to donate a 
specific amount – just something. All in all, I think this is an *incredibly*
generous model and something they should be applauded for.


## Installation and Running

Bucking the status quo, this is one of the few tools I’ve installed lately
that does *not* required npm. Crazy, right? Instead, you’ll simply download a
zip (for Windows, Mac, and Linux), extract, and place the binary in your path. 
Once you’ve done that, fire up your service (again, ngrok isn’t going to care 
what server-side technology you’re using, so feel free to fire up those Perl 
CGIs), and type`ngrok 80` to begin the service. There’s plenty of other
options of course. Once you’ve started ngrok, it takes over your command prompt 
with a constantly updated status report.

![shot1][3]

Skipping over the status and version lines, look at the Forwarding lines. As
you can see, ngrok provides both a http and https alias based on a random 
subdomain (more on that in a bit). You can confirm that this is working by 
simply opening that URL in your browser. As soon as you do, you will see updates
in the status window.

![shot2][4]

In case you’re curious, the https version works just fine even if you don’t
have https set up on your local server.

![shot3][5]

Now make note of the Web Interface URL. Opening that gives you a nice web-based
report on your requests.

![shot4][6]

All in all, ngrok is very powerful out of the box.


## A Real World Example

Now let’s test with a real example. I mentioned GitHub webhooks earlier, let’s
take a look at that. I selected one of my repos by random
(<https://GitHub.com/cfjedimaster/BehanceAPI>), went into Settings, Webhooks &
Services, and clicked `Add webhook`.

For my URL, I’m going to use a simple ColdFusion script on my local server. I’m
not going to do anything fancy with it at all now, but I’ll just ensure it 
exists. I created a file `helloGitHub.cfm` and place it in a testing folder I’ve
got on my local Apache web server.

![shot5][7]

Note – I selected `Send me everything` as an option so that it would be easier
to test. GitHub lets you run the webhook only on pushes or individual events as 
well. Click `Add webhook` to complete the process.

Ok, now what? At this point I know GitHub is going to run the URL when stuff
happens. But I’ve got no idea *what* GitHub is going to send. I’m sure it will
send me lots of cool data and I’m sure I could find the docs to see what that is
– but why bother reading the docs when we can test right now with ngrok.

Since I told GitHub to run the webhook on all events, I added a random bug
report to my project to see what would happen.

As soon as I added my issue, the ngrok administrator updated in real time with
the event.

![shot6][8]

As you can see, you get a summary view, a headers view, as well as raw and
binary. Notice that the data is shown as is – without formatting of the JSON 
packet. ngrok actually supports formatting JSON but only when the content type 
is `application/json`. Since GitHub isn’t using that content type, ngrok doesn’t
recognize it as JSON.

At this point, I could start testing. I’d edit my server-side code to handle
the webhook and do something intelligent. In order to test though I’d need to 
keep doing “stuff” with my GitHub repo. ngrok though let’s me skip that and 
simply rerun their call locally. Unfortunately, this is where I ran into my 
first bug with ngrok. While I was able to rerun the initial hit GitHub sent to 
me when I registered the webhook, I was not able to rerun the one created when I
added an issue to my project. I’ve filed a bug report on this
([link][9]) and I’m sure it will be resolved soon.


## Additional Features

All in all, this is a pretty cool service, especially considering how easy it
is and how much you pay for it (nothing), but what do get above the free account?
The first “tier” above free is – well – still free – you just have to register. 
Once you register you can get custom subdomains, TCP tunneling, password 
protection, and multiple tunnels.

Registering is painless, and when you do, you will get an auth token. You
simply supply that auth token at the command line to make use of one of the 
reserved features. So for example, to create a custom subdomain, I’d do this
(auth token changed of course):

    ngrok -authtoken Ythisisrandomsecretcrap2 -subdomain mymilkshakeisbetterthanyours 80
    
The next level requires you to actually pay them, but again, what you pay is
totally up to you. That level gets you additional tunnling support as well as 
the ability to reserve subdomains (but, to be honest, if you are at a point 
where you feel the need to reserve a subdomain, I think you may want to use a 
real web server -obviously folks may disagree on that).


## Conclusion

All in all, this is a pretty powerful tool and fills a very specific need that
I think will be incredibly useful for web developers.

 [1]: http://flippinawesome.org/authors/raymond-camden
 [2]: http://ngrok.com
 [3]: img/shot1.png
 [4]: img/shot2.png
 [5]: img/shot3.png
 [6]: img/shot4.png
 [7]: img/shot5.png
 [8]: img/shot6.png
 [9]: https://GitHub.com/inconshreveable/ngrok/issues/118

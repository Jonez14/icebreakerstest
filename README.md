<<<<<<< HEAD
=======
# icebreakerstest

>>>>>>> ca833af86de6b7db406801d0e0dc849132c7e013
# Facebook-Bot

1. Install the Heroku toolbelt from here https://toolbelt.heroku.com to launch, stop and monitor instances. Sign up for free at https://www.heroku.com if you don't have an account yet.

2. Install Node from here https://nodejs.org, this will be the server environment. Then open up Terminal or Command Line Prompt and make sure you've got the very most recent version of npm by installing it again:

           `sudo npm install npm -g`
3. Create a new folder somewhere and let's create a new Node project. Hit Enter to accept the defaults.

           `npm init`

4. Install the additional Node dependencies. Express is for the server, request is for sending out messages and body-parser is to process messages.

          `npm install express request body-parser --save`
          
5. Create an index.js file in the folder and copy this into it. We will start by authenticating the bot.

```
var express = require('express')
var bodyParser = require('body-parser')
var request = require('request')
var app = express()

app.set('port', (process.env.PORT || 5000))

// Process application/x-www-form-urlencoded
app.use(bodyParser.urlencoded({extended: false}))

// Process application/json
app.use(bodyParser.json())

// Index route
app.get('/', function (req, res) {
    res.send('Hello world, I am a chat bot')
})

// for Facebook verification
app.get('/webhook/', function (req, res) {
    if (req.query['hub.verify_token'] === 'my_voice_is_my_password_verify_me') {
        res.send(req.query['hub.challenge'])
    }
    res.send('Error, wrong token')
})

// Spin up the server
app.listen(app.get('port'), function() {
    console.log('running on port', app.get('port'))
})
```
6.Make a file called Procfile and copy this. This is so Heroku can know what file to run.
       
       `web: node index.js`
       
7.Commit all the code with Git then create a new Heroku instance and push the code to the cloud.

```
git init
git add .
git commit --message 'hello world'
heroku create
git push heroku master
```

<<<<<<< HEAD

=======
#Facebook Setup

1. Configure the icebreaker Facebook App or Page here https://developers.facebook.com/apps/                                 ![alt tag](https://cloud.githubusercontent.com/assets/636431/14870334/ff008bf8-0c96-11e6-9ffb-da1cd977b61d.png)

2. In the app go to Messenger tab then click Setup Webhook. Here you will put in the URL of your Heroku server and a token. Make sure to check all the subscription fields. **MAKE SURE YOU PUT** /webhook/ in the call back URL                     ![alt tag] (https://cloud.githubusercontent.com/assets/636431/14870342/0705aff4-0c97-11e6-8d49-efde4a9d16d6.png)

3.  Get a Page Access Token and save this. You will be adding this token to the index file.                               ![alt tag] (https://cloud.githubusercontent.com/assets/636431/14870354/17880c78-0c97-11e6-9cd9-561cbb0ffad5.png)

4.  Open your Terminal and type in this command to trigger the Facebbook app to send messages. Remember to use the token you requested earlier.
      `curl -X POST "https://graph.facebook.com/v2.6/me/subscribed_apps?access_token=<PAGE_ACCESS_TOKEN>"`
 
#Let's party!!! Bot Set up

Heroku and Facebook should be talking now.

1. Add an API endpoint to index.js to process messages. **Remember to also include the token we got earlier.** (This is an echo test to prove its working. **Remove the echo when you push to prod**                                                  
```
app.post('/webhook/', function (req, res) {
    messaging_events = req.body.entry[0].messaging
    for (i = 0; i < messaging_events.length; i++) {
        event = req.body.entry[0].messaging[i]
        sender = event.sender.id
        if (event.message && event.message.text) {
            text = event.message.text
            sendTextMessage(sender, "Text received, echo: " + text.substring(0, 200))
        }
    }
    res.sendStatus(200)
})

var token = "<PAGE_ACCESS_TOKEN>"
```


2 Add a function to echo back messages
```
function sendTextMessage(sender, text) {
    messageData = {
        text:text
    }
    request({
        url: 'https://graph.facebook.com/v2.6/me/messages',
        qs: {access_token:token},
        method: 'POST',
        json: {
            recipient: {id:sender},
            message: messageData,
        }
    }, function(error, response, body) {
        if (error) {
            console.log('Error sending messages: ', error)
        } else if (response.body.error) {
            console.log('Error: ', response.body.error)
        }
    })
}
```

3 Commit code and push to Heroku
```
git add .
git commit -m 'this bot speaks'
git push heroku master
```
4 Go to the Facebook Page and click on Message to start chat test.

![alt tag] (https://cloud.githubusercontent.com/assets/636431/14872172/fe34b612-0ca6-11e6-9fc7-33294c6eb418.png)

# Control what your bot says

1. Copy the code below to index.js to send an test message back as two cards.                                            
```
function sendGenericMessage(sender) {
    messageData = {
        "attachment": {
            "type": "template",
            "payload": {
                "template_type": "generic",
                "elements": [{
                    "title": "First card",
                    "subtitle": "Element #1 of an hscroll",
                    "image_url": "http://i1178.photobucket.com/albums/x364/jonez1411/unicorn.png_zpsfassmfq5.jpeg",
                    "buttons": [{
                        "type": "web_url",
                        "url": "https://www.messenger.com",
                        "title": "web url"
                    }, {
                        "type": "postback",
                        "title": "Postback",
                        "payload": "Payload for first element in a generic bubble",
                    }],
                }, {
                    "title": "Second card",
                    "subtitle": "Element #2 of an hscroll",
                    "image_url": "http://icebreakersunicorn.tumblr.com/post/142628054856",
                    "buttons": [{
                        "type": "postback",
                        "title": "Postback",
                        "payload": "Payload for second element in a generic bubble",
                    }],
                }]
            }
        }
    }
    request({
        url: 'https://graph.facebook.com/v2.6/me/messages',
        qs: {access_token:token},
        method: 'POST',
        json: {
            recipient: {id:sender},
            message: messageData,
        }
    }, function(error, response, body) {
        if (error) {
            console.log('Error sending messages: ', error)
        } else if (response.body.error) {
            console.log('Error: ', response.body.error)
        }
    })
}
```
2 Update the webhook API to look for special messages to trigger the cards
```
app.post('/webhook/', function (req, res) {
    messaging_events = req.body.entry[0].messaging
    for (i = 0; i < messaging_events.length; i++) {
        event = req.body.entry[0].messaging[i]
        sender = event.sender.id
        if (event.message && event.message.text) {
            text = event.message.text
            if (text === 'Unicorn') {
                sendGenericMessage(sender)
                continue
            }
            sendTextMessage(sender, "Text received, echo: " + text.substring(0, 200))
        }
    }
    res.sendStatus(200)
})
```
3 You know what to....
```
git add .
git commit -m 'updated the bot to speak'
git push heroku master
```

# Share your bot

##_Adding the chat button to your page_##

Click [here](https://developers.facebook.com/docs/messenger-platform/plugin-reference) for the Facebook sdk for adding a chat button to your page


##_Creating a shortlink_##

You can use [https://m.me/](https://www.messenger.com/)to have someone start a chat

#How do get approved and learn more?

* Get your bot approved for public use [here] (https://developers.facebook.com/docs/messenger-platform/app-review)
* ***BOT MOCK UP*** [sketch](https://bots.mockuuups.com/) Make it sexy kids...
* Fool your friends with your own AI brain for the bot [here](https://wit.ai/)
* Nerd on all things chat bots with the ChatBots Magazine [here] (https://medium.com/chat-bots)
>>>>>>> ca833af86de6b7db406801d0e0dc849132c7e013

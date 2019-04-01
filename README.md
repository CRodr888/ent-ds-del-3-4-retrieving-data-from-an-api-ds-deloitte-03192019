
# Retrieving Data from an API


## Introduction
Now that we've learned the basics of working with XML and JSON files, the next step it to work with a real API. In this lesson, you'll go through the process of signing up for an account, creating an application (sometimes called an "app"), adding your API key, querying an API and saving and transforming the results. Don't worry if you don't know what most of that means - you will by the end of the lesson!


## Objectives
You will be able to:
* Explain why APIs often require authentication and unique keys for apps
* Create a developer account at the NY Times
* Query information from the NY Times API, retrieve the results and transform a subset of the data into DataFrames

## Welcome to the NY Times!

Today we're going to go through the process of creating an account, creating an app, and using your app to make API calls against a New York Times developer API and then clean up the resulting data.

###  Creating a Developer Account
When you want to call an API, it is often necessary to create some kind of account with the organization that is providing the API. It allows them to know what person (or at least what email address) is associated with all of the queries you run, allowing them to shut down your access to the API if your usage of their API doesn't follow their guidelines (excessive use, unacceptable use, etc).

Let's sign up for a developer account with the NY Times. Start by going [here](https://developer.nytimes.com).

You should see a page similar to this:

![screenshot-1](http://curriculum-content.s3.amazonaws.com/data-science/developer-initial-page.png)

Click on the "Get Started" option, and it'll show you the following steps:

![screenshot-1](http://curriculum-content.s3.amazonaws.com/data-science/get-started.png)

Let's go through those steps one at a time:
* Click the "Sign In" link
* Click to "Create an Account" - make sure to use an email address you can access easily
* Follow the prompts to register
* Click on the link in the email received to confirm your account

Congratulations - when you're done with this you should have a developer account with the NY Times!

### Register Your App
The next step that many API providers ask you to do is to "register your app". Why do they do that? Well, there isn't always a 1:1 correspondence between developers and applications. Imagine that after this course you decide to become a professional software developer, building web apps for various clients. You would want to use a different set of credentials for each of the applications that you build so if one of your clients started misusing the API your entire developer account wouldn't get suspended - just their app.

To create your new App:
- Click on your email address in the top right - a drop down menu should appear
- Select the "My Apps" option
- Click on the "+ NEW APP" button in the top right
- Give it an app name (e.g. "Test app for class")
- Enter a short description (e.g. "Test app for Python class")
- Select the API(s) it'll have access to - let's just pick the "Article Search API" for now
- Click the "CREATE" icon on the top right

When you do, it should show you the details of your app and they should look something like this:

![your-first-app](http://curriculum-content.s3.amazonaws.com/data-science/your-first-app.png)


## Calling the API

Next up, we need to go read some docs. Click on the "APIs" link at the top of the NY Times developer website to go [here](https://developer.nytimes.com/apis). Click on the "Article Search API" link to go [here](https://developer.nytimes.com/docs/articlesearch-product/1/overview).

It explains that you need to make a call that looks something like this:

`https://api.nytimes.com/svc/search/v2/articlesearch.json?q=election&api-key=yourkey`

Where you would replace election with whatever search term you wanted, and the `yourkey` at the end with the API key you just got for your app. 

Give it a try! Open a new tab in your browser, copy the whole URL string above, paste it into the new browser tab and hit return. You should get the following message returned:

```
{
    "fault": {
        "faultstring": "Invalid ApiKey",
        "detail": {
            "errorcode": "oauth.v2.InvalidApiKey"
        }
    }
}
```
Well, that's not much of a surprise. We said that we'd have to replace the end of the URL with your real key for your app. So go in and give that a try. Go back to the tab with your app information in *(or go [here](https://developer.nytimes.com/my-apps) if you navigated away from that)*, click on your app, and where it says "API Keys" and then "Key", mouse over the key, then click on the "copy to clipboard" link that pops up when you mouse over it.

![copy-to-clipboard](http://curriculum-content.s3.amazonaws.com/data-science/copy-to-clipboard.png)

Now go back to the end of the URL in your browser:

`https://api.nytimes.com/svc/search/v2/articlesearch.json?q=election&api-key=yourkey`

Delete `yourkey` from the end of the URL and paste from clipboard (control-v) to paste your key onto the end of the URL. Hit the enter key to go to the page, and you should get a valid response with articles relating to the election:

![valid-api-response](http://curriculum-content.s3.amazonaws.com/data-science/valid-api-response.png)

### Automating the API Call

The good news is that we were able to test this API call in the browser. But now we really need to figure out how to do this from Python. That way whether we want to run hundreds of calls a day or simply run a query every week or two, we can automate the process of asking for, receiving, processing and acting on the information - in whatever way we want to.

The first step, then, is to figure out how to call the API from Python, but first, lets just define a few terms...

### Key Terms
When we interact with an API, we talk about making **calls** *(hey, do you have that script that **calls** the Twilio API to send out a text every time someone signs up for our newsletter?)*.

The calls are most often sent using a **protocol** (a way of sending information from one computer to another) called **HyperText Transfer Protocol (HTTP)**. Yes - that's right - the http that you see most times you see a website address - e.g. [http://www.google.com](http://www.google.com). Strictly speaking these days, most of the calls we send actually use **HyperText Transfer Protocol Secure (https)**, but that's pretty similar to http - it just has a little more back and forth between the two computers to ensure all of the information is encrypted between them, making it harder for someone else to pretend to be the website you're trying to connect to.

We need to send the call somewhere - it needs an adddress. It's like making a phone call - you need to know someone's number to call them. On the internet, the actual location your call should be routed to is based on an **IP *(Internet Protocol)* address**. In practice though, we usually use a **Domain Name** that gets looked up by a **Domain Name Server** to find the right IP address. Think of it as a global address book that allows you to look up where you want to go using a human readable name like google.com instead of a set of numbers like 172.217.4.142.

Once you've decided on the domain name, you also need to enter the appropriate path to the script you want to access. It's the part of the URL after the domain name. In this case it is `svc/search/v2/articlesearch.json`.

Then finally, for some APIs there is a **query string** which is anything after a `?` towards the end of the URL. In this case it's the `q=election&api-key=yourkey`. It is a way of passing a set of variables in the URL, with an `&` between each key/value pair, so in this example we're setting a variable called `q` to `election` and a variable called `api-key` to `yourkey`. These are variables used by the remote server to better respond to your API call.

When you call an API you make a **request**. Assuming the server you are connecting to is working and you're not blocked, it will then typically send you a **response**. That's sometimes called the **request/response cycle**. With some API's you need to make an initial request to validate that you are someone the server is willing to talk to, with others (like the NY Times one we're starting with), you just have to pass a unique identifier (your API Key) in each call to let the server know who you are.

Another pair of terms that might come up are **serialization** and **deserialization**. In Python we store information in variables of various types (dicts, lists, sets, strings, etc). When we want to save that information outside of our running program, we often need to *serialize* the information to turn it into a string that can be stored in a file or sent to a web server. For a simple data type like a string, serialization is trivial *(there's nothing to do - it is already a string)*. For a numeric type, it needs to be transformed into a string so `12` becomes `"12"`. For something like a list or a dictionary, it has to follow some agreed serialization process to save it in a way that other programs will be able to re-import it successfully. For example, one way of serializing data is to save it to JSON.

When we talk about *deserialization*, it's the reverse process. It's the process of ingesting a string from a file or a web request and turning it back into native Python variables like lists and dictionaries.

### The Requests Library
In Python, there is a library called [Requests](http://docs.python-requests.org/en/master/) which makes it easy to interact with APIs. Try running the following code ***(substituting your key for the "your_key_here" value in the third line of code)***.


```python
# Let's start by creating variables to make it easy to understand the URL we're calling

base_url = "https://api.nytimes.com/svc/search/v2/articlesearch.json"
query= "election"
key = "your_key_here"

# Let's build the full URL from it's constituent parts
full_url = base_url + "?q=" + query + "&api-key=" + key

import requests
response = requests.get(full_url)
print(response)
print(response.text[:500])
print(full_url)
print(type(response.text))
```

OK, a few things to note here.
* Firstly, note that when you print out the response it displays `<Response [200]>`. That is telling you that (a) it is a Response object and (b) that the response code *(how did the back and forth work out)* is 200 - which means it all worked out fine
* If you ever get a response other than 200, you probably want to look up [common http response codes](https://www.digitalocean.com/community/tutorials/how-to-troubleshoot-common-http-error-codes) to try to figure out what went wrong
* Note that if you want to check out the content returned by the server, that is in `response.text`
* Notice that we only printed out the first 500 characters of the response text (`response.text[:500]`) just to get an idea of what it includes without taking up too much space displaying potentially a really large file.
* Finally, notice that the data type of the response is a string - so we're going to need to deserialize it

We have the data from the response in a string, but that isn't going to allow us to work with it the way we want to so need to **deserialize** it into native Python data types (lists, dictionaries, etc) to explore it further. The code to do that is pretty similar to what we used in the last lesson when we used the Python json library to load the data from a file into the appropriate data types in Python:


```python
import json
data = json.loads(response.text)
# print(data)
type(data)
print(data.keys())
```

Now we should be back in more familiar territory. We have the information from the JSON file deserialized into native Python data types stored in the variable called `data`. Next we probably want to examine and it and then transform some of the information into a List or a DataFrame. 

Take a few minutes to explore the `data` variable. There are a couple of sample commands commented out in the cell below that you might want to try . . . 

*And look out, keys are case sensitive, so `data.get("Status")` is **not** the same as `data.get("status")*


```python
# print(data.keys()) # The high level structure
# print(data.get("Status")) # This may not work!
# print(data.get("status")) # But this should
# print(data.get("response"))
# print(data.get("response").keys())
# Your turn now!
```

Try to create a DataFrame called `articles` containing the URLs, abstracts, word counts and types of materials for all of the articles!

Now have a look at the structure of the data frame - lets start with looking at the first 5 articles . . .


```python
articles.head()
```

Now, check out the type of materials field to get a sense of the break down of the various types of values:


```python
articles['Type of Material'].value_counts()
```

Next up, create a new DataFrame called `news_articles` that only contains News articles.

## Extra Credit (1)

Have a quick look at the `articles` DataFrame using the `info()` method. Notice that the word count is being stored as an object. Try to change it into an integer instead and then run the describe() method to get the mean number of words.

# Extra Credit (2)

If you run the cell below, you'll see that each of the docs contains a collection of multimedia. Write a script that creates a DataFrame containing the rank, subtype, caption and URL for each of the pieces of multimedia.


```python
docs = data.get("response").get("docs")
print(docs[0].get("multimedia")[0].keys())
```

## Summary

Now we're really starting to pull things together. We introduced a lot of new concepts and terminology in this lesson, but all we're really doing different from the last lesson is that we're getting our JSON file from the NY Times instead of from a file! And now we've practices exploring a new JSON file, and retrieving and transforming various subsets of the data, storing them in DataFrames. In the final lab of the day, we'll give you a lot more lattitude to explore the NY Times APIs!


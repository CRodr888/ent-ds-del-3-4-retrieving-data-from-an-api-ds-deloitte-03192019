
# Retrieving Data from an API


## Introduction
Now that we've learned the basics of working with XML and JSON files, the next step it to work with a real API. In this lesson, you'll go through the process of signing up for an account, creating an application (app), adding your API key, querying an API and saving and transforming the results. Don't worry if you don't know what most of that means - you will by the end of the lesson!


## Objectives
You will be able to:
* Explain why APIs often require authentication and unique keys for apps
* Create a developer account at the NY Times
* Query information from the NY Times API, retrieve the results and transform a subset of the data into Lists or DataFrames

## Welcome to the NY Times!

Today we're going to go through the process of creating an account, creating an app, and using your app to make API calls against the New York Times.

###  Creating a Developer Account
When you want to call an API, it is often necessary to create some kind of account with the organization that is providing the API. It allows them to know what person (or at least what email address) is associated with all of the queries you run, allowing them to shut down your access to the API if your usage of their API doesn't follow their guidelines (excessive use, unacceptable uses, etc).

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

Congratulations - when you're done with this you should have a developer account with the NY Times.

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

It explains that you need to make a call to a URL that looks something like this:

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
Well, that's not much of a surprise. We said that we'd have to replace the end of the URL with your real key for your app. So go in and give that a try,. Go back to the tab with your app information in *(or go [here](https://developer.nytimes.com/my-apps) if you navigated away from that)*, click on your app, and where it says "API Keys" and then "Key", mouse over the key, then click on the "copy to clipboard link that pops up when you mouse over it.

![copy-to-clipboard](http://curriculum-content.s3.amazonaws.com/data-science/copy-to-clipboard.png)

Now go back to the end of the URL in your browser:

`https://api.nytimes.com/svc/search/v2/articlesearch.json?q=election&api-key=yourkey`

Delete `yourkey` from the end of the URL and paste from clipboard (control-v) to paste your key onto the end of the URL. Hit the enter key to go to the page, and you should get a valid response with articles relating to the election:

![valid-api-response](http://curriculum-content.s3.amazonaws.com/data-science/valid-api-response.png)

### Automating the API Call

The good news is that we were able to test this API call in the browser. But now we really need to figure out how to do this from Python. That way whether we want to run hundreds of calls a day or simply run a query every week or two, we can automate the process of asking for, receiving, processing and acting on the information - in whatever way we want to.

The first step, then, is to figure out how to call the API from Python.





```python
import requests
response = requests.get("https://api.nytimes.com/svc/search/v2/articlesearch.json?q=election&api-key=xxx")
print(response)
print(response.text[:500])
```

## Summary



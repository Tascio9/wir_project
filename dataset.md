---
layout: default
title: Dataset
---
# -- Dataset

A first obstacle of our project was the absence of a dataset which contains tweets classified with our labels (AdministrativeArea, Book, ...).

So, in order to test our Knowledge Base (KB), we had to create this dataset and labeled it.
How? First of all, we used Twitter's API to obtain tweets. We've researched them using a query with some particular features:

```python
'https://api.twitter.com/1.1/search/tweets.json',
                  {'q': 'from:@' + search[k], 'count': '20', 'result_type': 'popular', 'tweet_mode': 'extended',
                   'lang': 'en'})

# Inside URL, you will put all the parameters in which you are interested:
# -- q:             is the term searched (in our case, we've added 'from:@' because I wanted all the tweets
#                   written by a specific user, you can modify it as you like. Otherwise if you use only
#                   search[k] it produces tweets with that term);
# -- count:         limits the number of tweets;
# -- result_type:   choose between 'popular', 'recent', 'mixed';
# -- tweet_mode:    with this parameter, we have the COMPLETE text of the tweet, otherwise, it's limited by 120
#                   chars;
# -- lang:          only english tweets;

# More details: https://developer.twitter.com/en/docs/tweets/search/api-reference/get-search-tweets.html
```
In addiction, we built the dataset by mixed up some tweets that randomly are taken just using a term and in some cases we use the clause "from:@" in order to get focus on a specific person/group and consequently classify them with the relevant label.
As we can see, this script create a request to call the Twitter's API and catch all the tweets with that parameters. But to complete this request in a secured way, we need the following function to make it:

```python
def augment(url, parameters):
    secrets = hidden.oauth()
    consumer = oauth.OAuthConsumer(secrets['consumer_key'], secrets['consumer_secret'])
    token = oauth.OAuthToken(secrets['token_key'], secrets['token_secret'])

    oauth_request = oauth.OAuthRequest.from_consumer_and_token(consumer,
                                                               token=token, http_method='GET', http_url=url,
                                                               parameters=parameters)
    oauth_request.sign_request(oauth.OAuthSignatureMethod_HMAC_SHA1(), consumer, token)
    return oauth_request.to_url()
```

Notice that ```secrets = hidden.oauth()``` is a function where we stored our keys to make the request on Twitter (you should sign up to have it).

Once we do it, we received a JSON file with a lot of information; we weren't interested on it totally so we filtered it and saved only the main features like:

1. **id**
2. **screen_name.**
3. **created_at.**
4. **full_text.**

Give a look to this code
```python
full_text = remove_emoji(jsonData['statuses'][i]['full_text'].encode('ascii', 'ignore'))
                regex = re.compile(r'[\n\r\t]')
                strFull_text = regex.sub(" ", full_text)
                f.write(
                    "\n\t{\n\t\"id\":" + "\"" + jsonData['statuses'][i]['id_str'] + "\"" + "," +
                    "\t\"screen_name\":" + "\"" + jsonData['statuses'][i]['user']['screen_name'] + "\"" + "," +
                    "\t\"created_at\":" + "\"" + jsonData['statuses'][i]['created_at'] + "\"" + "," +
                    "\t\"full_text\":" + "\"" + strFull_text + "\"" + "}\n")
```
We striped all the tabs and newline in order to don't have any problem when we save them inside a JSON file, in fact, we've used ```f.write``` to write it.

Here there is an example of the output:
```JSON
{
  "tweet": [
    {
       "id":"1153004571129630721",
       "screen_name":"pottermore",
       "created_at":"Sun Jul 21 18:11:26 +0000 2019",
       "full_text":"Wingardium Quiz-iosa! How much do this year's Comic Con attendees know about Harry Potter? Cursed Child's Nicholas Podany took to the streets of San Diego to find out. #SDCC2019 https:t.cogH5zB9BWUm"
    },

    {
      "id":"1150737091296468993",
      "screen_name":"MotoGP",
      "created_at":"Mon Jul 15 12:01:16 +0000 2019",
      "full_text":" @marcmarquez93 did a bit of cross country running back in Argentina   The reigning World Champion seems to do a lot of running   #MotoGP https:t.coLYikFuOOB0"
    }
	]
}
```

That it's all! :D

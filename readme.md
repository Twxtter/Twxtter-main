# Twxtter/sixFix
(A fork of TwitFix)
Basic flask server that serves fixed twitter video embeds to desktop discord by using either the Twitter API or Youtube-DL to grab tweet video information.

Regarding what happened to TwitFix: http://blog.hirob.in/2022-05-16-Goodbye-TwitFix/

## How to use (discord side)

```
https://twxtter.com/[twitter video url] or [last half of twitter url])
```

You can also simply type out `s/i/x` on PC and iOS after sending a twitter link to discord, and it will edit the last message (The link) to replace the first i with x.

**Note**: If you enjoy this service, please considering donating via [Ko-Fi](https://ko-fi.com/twxtter) to help cover server costs

## How to run (server side)

this script uses the youtube-dl python module, along with flask, twitter and pymongo, so install those with pip (you can use `pip install -r requirements.txt`) and start the server with `python twitfix.py`

I have included some files to give you a head start on setting this server up with uWSGI, though if you decide to use uWSGI I suggest you set up mongoDB link caching 

### Config

Twxtter generates a config.json in its root directory the first time you run it, the options are:

**API** - This will be where you put the credentials for your twitter API if you use this method

**database** - This is where you put the URL to your mongoDB database if you are using one

**link_cache** - (Options: **db**, **json**)

- **db**: Caches all links to a mongoDB database. This should be used it you are using uWSGI and are not just running the script on its own as one worker
- **json**: This saves cached links to a local **links.json** file

**method** - ( Options: **youtube-dl**, **api**, **hybrid** ) 

- **youtube-dl**: the original method for grabbing twitter video links, this uses a guest token provided via youtube-dl and should work well for individual instances, but may not scale up to a very large amount of usage

- **api**: this directly uses the twitter API to grab tweet info, limited to 900 calls per 15m
- **hybrid**: This will start off by using the twitter API to grab tweet info, but if the rate limit is reached or the api fails for any other reason it will switch over to youtube-dl to avoid downtime

**color** - Accepts a hex formatted color code, can change the embed color

**appname** - Can change the app name easily wherever it's shown

**repo** - used to change the repo url that some links redirect to

**url** - used to tell the user where to look for the oembed endpoint, make sure to set this to your public facing url

This project is licensed under the **Do What The Fuck You Want Public License**


## Other stuff

Going to `https://twxtter.com/latest/` will present a page that shows the all the latest tweets that were added to the database, use with caution as results may be nsfw! Current page created by @DorukSaga

Using the `/dir/<video-url>` endpoint will return a redirect to the direct MP4 link, this can be useful for downloading a video

Using the `/dl/<video-url>` or appending a `.mp4` will make the server download the video and return a static, locally hosted copy

Using the subdomain `d.twxtter.com/<video-url>` will redirect to a direct MP4 url hosted on Twitter

Using the `/info/<video-url>` endpoint will return a json that contains all video info that youtube-dl can grab about any given video

Using `/other/<video-url>` will attempt to run the twitter embed stuff on other websites videos - This is mostly experimental and doesn't really work for now 

Using `/api/latest/` will return a json with the latest tweet added to the database. Takes params `?tweets=INT&=pageINT` to return multiple

Using `/api/top/` will return a json with the most hit tweet in the database. Takes params `?tweets=INT&=pageINT` to return multiple

Using `/api/stats/` will return a json with some stats about TwitFix's activity (embeds, new cached links, API hits, downloads). Takes param `?=date"YYYY-MM-DD"` to return a specific day, otherwise will return today's stats to far

Advanced embeds are provided via a `/oembed.json?` endpoint - This is manually pointing at my server in `/templates/index.html` and should be changed from `https://twxtter.com/` to whatever your domain is

# NOTICE
## This is _**NOT**_ actively monitored by anyone working on Twxtter. All tweets are public ally accessible.

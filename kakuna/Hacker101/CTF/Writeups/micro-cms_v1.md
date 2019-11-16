# Micro-CMS v1

## Recon

As soon as you open the challenge you are greeted with three links.
![Image](/resources/images/kakuna/hacker101/ctf/micro-cms_v1/homepage.png)

Two of them `Testing` and `Markdown Test` already have content in them, along with a link to go back and another to edit this page

`Testing` has the directory `/page/1`

![Image](/resources/images/kakuna/hacker101/ctf/micro-cms_v1/testing.png)

`Markdown Test` has the directory `/page/2`

![Image](/resources/images/kakuna/hacker101/ctf/micro-cms_v1/markdown_test.png)

Finally `Create a new page` takes us to a form under `/page/create` where we can input text. 

![Image](/resources/images/kakuna/hacker101/ctf/micro-cms_v1/create.png)


Some important details:

- In the form to create a new page, **Markdown** is supported. You can find more information and a cheatsheet that I always have to Google [here](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet). Anyways, this could be huge since Markdown tends to be processed as HTML, probably giving us the ability to inject HTML code.

- We can edit the content of the pages `Testing` and `Markdown Test`. This is definitely important and I will come back to it later.

- nother interesting detail is that you never go to the homepage which I found strange, notice the weird directory `831d744d86` as soon as you hit **Go** in the Hacker101 website ([*see this*](#description)). When you removed the directory in an attempt to visit home/index, I found a **nginx** welcome page. 

![Image](/resources/images/kakuna/hacker101/ctf/micro-cms_v1/nginx_welcome.png)


This could be useful because the welcome page could be used to reference particular versions and find vulnerabilities from there. However it could also be outside the scope, a quick nmap scan shows the following

```bash
~$ nmap -sS -sV -T4 34.94.3.143
```
![Image](/resources/images/kakuna/hacker101/ctf/micro-cms_v1/nmap.png)



The open ports are

```bash
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    nginx 1.14.0 (Ubuntu)
```



### nginx

We already knew it was some sort of **nginx** version, this just confirms it for us, it is never a bad idea to double check since the web admin could have put any sort of welcome message to *hide* the real service. You will be surprised how much a little ofuscation or red herrings could fool an attacker. 

Let's learn a little about this version. maybe there are known vulnerabilities or exploits. A [quick search](https://www.cvedetails.com/vulnerability-list/vendor_id-10048/product_id-17956/version_id-267430/year-2018/Nginx-Nginx-1.14.0.html) shows

![Image](/resources/images/kakuna/hacker101/ctf/micro-cms_v1/nginx_cve.png)

I explored the three vulnerabilities detailed in 2018

1. [**CVE-2018-16845:**](https://www.cvedetails.com/cve/CVE-2018-16845/) Affects service availability by causing an infinte loop, which crashes the process. However utilizes a specially crafted `mp4` file. No known metasploit modules. Does not seem relevant.

2. [**CVE-2018-16844:**](https://www.cvedetails.com/cve/CVE-2018-16844/) Implementation of HTTP allows excessive CPU usage. Seems like a denial of service again. No known metasploit modules. Does not seem relevant.

3. [**CVE-2018-16843:**](https://www.cvedetails.com/cve/CVE-2018-16843/) Implementation of HTTP allows excessive memory consumption. Another DOS vulnerability. No known metasploit modules. Does not seem relevant.

Well at least now we can be somewhat confident that we do not need to exploit the webs service using a known CVE. Bug bounties and CTFs tend to exclude from the scope any sort of Denial of Service attacks. Additionally there are no CVEs from 2019.

### SSH

The SSH version `OpenSSH 7.6p1` is from 2017-10-03 according to the [release notes](https://www.openssh.com/txt/release-7.6). A quick look at the [CVEs](https://www.cvedetails.com/vulnerability-list.php?vendor_id=97&product_id=585&version_id=259116&page=1&hasexp=0&opdos=0&opec=0&opov=0&opcsrf=0&opgpriv=0&opsqli=0&opxss=0&opdirt=0&opmemc=0&ophttprs=0&opbyp=0&opfileinc=0&opginf=0&cvssscoremin=0&cvssscoremax=0&year=2018&cweid=0&order=1&trc=1&sha=79762f49da5ec91d2d86536a8f69fa82628f7748) 

![Image](/resources/images/kakuna/hacker101/ctf/micro-cms_v1/ssh_cve.png)

[**CVE-2018-15919:**](https://www.cvedetails.com/cve/CVE-2018-15919/) Describes the possibility to enumerate users. Could be useful.

Tenable also has [more information](https://www.tenable.com/plugins/nessus/103781) about the OpenSSH version, describing the possibility to allow unauthenticated attackers to create zero-length files.

Honestly I do not think SSH is an attack vector at this point but it is necessary to do your due diligence whenever you engage with any client's system. The more you know the less likely you could fuck up!


---

I decided to open the hints, this way I could know a bit more where to focus. After all, I still was not sure of the scope and I have made mistakes in the past :(. 

Some time ago I participated in another CTF, it was also a website but with hidden login portal. After enumerating the directories I found a suspicious `/_notes` folder that contained admin notes. I thought these were hints to find the flag and found references to a different site which had a login portal. Surprisingly I managed to get credentials but due to several indicators (DNS owner, different location, etc) I thought it could be outside the scope. After sending an email to the CTF team, they replied indicating that the `/_notes` I found were leftovers they didn't intend me to find...

![Image](/resources/images/surprised_pikachu.png)

Welp ¯\\\_(ツ)_/¯

My point is: You *can* and *will* mess up, just try your best not to, after all I don't want a visit from law enforcement or a lawyer at my door. Hence my extra care to pick my target and attack vector properly. 


## Flag #0
**Hints**

```
- Try creating a new page
- How are pages indexed?
- Look at the sequence of IDs
- If the front door doesn't open, try the window
- In what ways can you retrieve page contents?
```

I did just what the first hint said and created a new page.

![Image](/resources/images/kakuna/hacker101/ctf/micro-cms_v1/create_mine.png)

I have the hints displayed above, however at the time I only had the first one but instantly I was surprised to end up with the directory `/page/12`. Where are pages 3 - 11?

Manipulating the URL to try the numbers from 3 to 11 resulted in a `Not Found` except for `/page/7` where I got `Forbidden`.

![Image](/resources/images/kakuna/hacker101/ctf/micro-cms_v1/page_7.png)

Interesting... Why seven?
>either read-protected or not readable by the server
hmm

Well if it cannot be read, can it be written? 

Further inspection of the `Edit this page` feature showed that the pages were edited under the path `/page/edit/#` 

![Image](/resources/images/kakuna/hacker101/ctf/micro-cms_v1/edit_12.png)

Upon trying `/page/edit/7` we are greeted with the first flag!


![Image](/resources/images/kakuna/hacker101/ctf/micro-cms_v1/flag_0.png)



## Flag #1 
**Hints**
```
- Make sure you tamper with every input
- Have you tested for the usual culprits? XSS, SQL injection, path injection
- Bugs often occur when an input should always be one type and turns out to be another
- Remember, form submissions aren't the only inputs that come from browsers
```

I started with the obvious SQL injection in the form, I typed `'` in the title and body then hit save, but ended up feeling dumb as I looked at

![Image](/resources/images/kakuna/hacker101/ctf/micro-cms_v1/sql_form.png)

Hmmmmmm.. well it said it supported markdown so it has to support characters that normally we use for testing SQL injections.

I needed to see the requests and responses made so I fired up Burpsuite, moved a post request made when clicking saved to repeater and took a look at it deeper.

Now I had a better look at the request including the parameters `title` and `body`. I tested a normal response by asigning the value `mybody1` to `body` and found out the response is actually a redirection with an `href` pointing to `/page/12`.

![Image](/resources/images/kakuna/hacker101/ctf/micro-cms_v1/burp_edit_12.png)

I tested to check if the value of `href` was dependant on the POST value by modifying it to `1` instead of `12` and sure enough, `href` was `/page/1`.

![Image](/resources/images/kakuna/hacker101/ctf/micro-cms_v1/burp_edit_1.png)

Testing SQL injection in the URL i.e. appending a `'` to the url, results in the flag!


![Image](/resources/images/kakuna/hacker101/ctf/micro-cms_v1/flag_1.png)

Trying it in the web browers as well shows the same result too as expected.

![Image](/resources/images/kakuna/hacker101/ctf/micro-cms_v1/flag_1_browser.png)

## Flag #2 

**Hints**
```
- Sometimes a given input will affect more than one page
- The bug you are looking for doesn't exist in the most obvious place this input is shown
```
We know so far that the body supports Markdown but not scripts. What does this mean exactly? It could mean that only Markdown will work as expected but scripts wont't. 

I decided to test for a XSS vulnerability by adding a script in the `body` and ended with a scrubbed version, notice the tags used in the response.

``` html
<script> alert 'XSS' </script>
```

![Image](/resources/images/kakuna/hacker101/ctf/micro-cms_v1/burp_scrubbed.png)

Not very surprised since I already knew scripts were not supported. What about the title?

![Image](/resources/images/kakuna/hacker101/ctf/micro-cms_v1/xss.png)

Nothing happened! However our code is in the tab's title and page's body. Once we click `<-- Go Home` we find our flag!

![Image](/resources/images/kakuna/hacker101/ctf/micro-cms_v1/flag_2.png)


After clicking ok, the alert I put before appeared too.

![Image](/resources/images/kakuna/hacker101/ctf/micro-cms_v1/xss_home.png)



## Flag #3 -- Not Found

**Hints**
```
Script tags are great, but what other options do you have?
```

`Markdown Test` which has the directory `/page/2` has something very unique that we haven't really talked about yet. There is a button!

![Image](/resources/images/kakuna/hacker101/ctf/micro-cms_v1/markdown_test.png)

Viewing the page source shows the following line of code.

```html
<p>Just testing some markdown functionality.</p>
<p><img alt="adorable kitten" src="https://static1.squarespace.com/static/54e8ba93e4b07c3f655b452e/t/56c2a04520c64707756f4267/1493764650017/" /></p>
<p><button>Some button</button></p>
```

Interestingly there is an image that should be loaded, however my browsers did not really showed anything, I verified the link and it was empty so that explains why no image was loaded. ¯\\\_(ツ)_/¯ The image is hosted in a third party server `squarespace.com` so I think there is nothing else to see here. 
Previously the `<p>...</p>` tag contained the value for the `body` parameter so I assumed that the button's and image's code should be in the form.

Checking the edit feature reveals html code to create a button as well as the markdown for images

![Image](/resources/images/kakuna/hacker101/ctf/micro-cms_v1/edit_2.png)

What is happening here is that Markdown supports html which means you can inject html code through this form which is great news. 

Can we use this button in any way? Previously I did an alert using scripts, but html can create alerts with the attribute `onclick`, the new button code is:

```html
<button onclick="alert('xss')">Some button</button>
```
Saving did not cause any issues which means I am a happy boi. I verified my modifications by looking at the source code and found the flag!

![Image](/resources/images/kakuna/hacker101/ctf/micro-cms_v1/flag_3.png)

I think the button triggers some event we can't really see.

Clicking the button ends up with an alert as intended.

![Image](/resources/images/kakuna/hacker101/ctf/micro-cms_v1/xss_markdown.png)

Of course, just like before, clicking ok and going home results in flag #2 again...

---




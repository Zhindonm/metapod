# Micro-CMS v1

## Description

As soon as you open the challenge you are greeted with three links.
![Image](/resources/images/kakuna/hacker101/ctf/micro-cms_v1/homepage.png)

Two of them `Testing` and `Markdown Test` already have content in them.

`Testing` has the directory `/page/1`

![Image](/resources/images/kakuna/hacker101/ctf/micro-cms_v1/testing.png)

`Markdown Test` has the directory `/page/2`

![Image](/resources/images/kakuna/hacker101/ctf/micro-cms_v1/markdown_test.png)

Finally `Create a new page` takes us to a form under `/page/create` where we can input text. 

![Image](/resources/images/kakuna/hacker101/ctf/micro-cms_v1/create.png)


Some important details:

- You never go to the homepage which I found strange, notice the weird directory `831d744d86` as soon as you hit **Go** in the Hacker101 website ([*see this*](#description)). When you removed the directory in an attempt to visit home, I found a **nginx** welcome page. 

![Image](/resources/images/kakuna/hacker101/ctf/micro-cms_v1/nginx_welcome.png)

This could be useful or outside of the scope because the welcome page could be used to reference particular versions and find vulnerabilities from there. 

- **Markdown** is supported. You can find more information and a cheatsheet that I always have to Google anyways [here](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)

- anther



## Flag #0
**Hints**

```
- Try creating a new page
- How are pages indexed?
- Look at the sequence of IDs
- If the front door doesn't open, try the window
- In what ways can you retrieve page contents?
```

**Methodology**
Creating a new page opens a form with a text box for a body and a tittle. 

## Flag #1 
**Hints**
```
- Make sure you tamper with every input
- Have you tested for the usual culprits? XSS, SQL injection, path injection
- Bugs often occur when an input should always be one type and turns out to be another
- Remember, form submissions aren't the only inputs that come from browsers
```
## Flag #2 
**Hints**
```
- Sometimes a given input will affect more than one page
- The bug you are looking for doesn't exist in the most obvious place this input is shown
```

## Flag #3 -- Not Found
**Hints**
```
Script tags are great, but what other options do you have?
```
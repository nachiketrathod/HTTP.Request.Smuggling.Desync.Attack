# HTTP.Request.Smuggling.Lab

<p align="center">
      <a href="http://nachiketrathod.com">
	     <img src="/Images/request.png" height=190 width=1000"></a>
</p>
								  

<p align="center">   
     <a href="https://nachiketrathod.com">
	    <img alt="Website" src="https://img.shields.io/website?style=flat-square&up_color=%2300ff00&up_message=nachiketrathod.com&url=https%3A%2F%2Fnachiketrathod.com"></a>
      <a href="https://www.twitter.com/4ccess0denie1">
           <img alt="Twitter Follow" src="https://img.shields.io/twitter/follow/4ccess0denie1?color=%2300acee&label=Follow%20%404ccess0denie1&logo=Twitter&logoColor=%2300acee&style=flat-square"></a>
      <a href="https://www.linkedin.com/in/nachiketrathod">
           <img alt="linkedin nachiketrathod" src="https://img.shields.io/badge/LinkedIn-nachiketrathod-0077B5?style=flat-square&logo=linkedin&logoColor=00acee"></a>
           <img alt="GitHub last commit" src="https://img.shields.io/github/last-commit/nachiketrathod/HTTP.Request.Smuggling.Lab?logo=github&style=flat-square">
	   <img alt="GitHub repo size" src="https://img.shields.io/github/repo-size/nachiketrathod/HTTP.Request.Smuggling.Lab?logo=Github&style=flat-square">
</p>

<h2><a id="user-content-tldr" class="anchor" href="#tldr"><span class="octicon octicon-link"></span></a>TL;DR:</a></h2>

HTTP request smuggling is a technique for **`interfering`** with the way of website process the sequences of HTTP requests that are received from one or more users.
This page discusses all techniques used for request smuggling **/** desync attack. `E.g.` **`CL.TE`**,**`TE.CL`**,**`CL.CL`** and **`TE.TE`** .
This vulnerabilities are often **`critical`** in nature, allowing an attacker to bypass **security controls**, gain **unauthorized access** to sensitive data, and directly compromise other application users.

**Lab** : Exploiting HTTP request smuggling to bypass front-end security controls via **TE.CL** vulnerability.

<h2><a id="user-content-tldr" class="anchor" href="#tldr"><span class="octicon octicon-link"></span></a>1. Introduction</a></h2>
<blockquote>
<p>"Smashing into the Cell Next Door"</p>
<p>"Hiding Wookiees in HTTP"</p>
</blockquote>

### **What is HTTP Request Smuggling?**

1.  If you picturised any website as an **end user** it would probably look like this, why because that's all that you can directly see.

<p align="left">
      <a href="http://nachiketrathod.com">
	   <kbd>
	     <img src="/Images/1.png" height=300 width=290"></a>
            </kbd>
</p>
							   
2.  Morden websites communicate to each other via chain of `web-servers` speaking HTTP over `stream based transport layer proctols` like **`TCP or TLS`**.

<p align="left">
      <a href="http://nachiketrathod.com">
	   <kbd>
	     <img src="/Images/2.png" height=300 width=550"></a>
	    </kbd>
</p>

These streams(**TLS/TCP**) are heavily reused and follows the HTTP 1.1 `keepalive` protocol.


#### ***`Question, what dose it even mean?`***

- That means that every reqests are placed back to back on these streams and every server parses `HTTP-Headers` to workout where each one ends and the next one starts.
    
- So from all over the world request are coming and passing through this tiny tunnel of **TLS/TCP** streams and passing to the backend and then split up into individual requests.

#### ***`Question, what could possibly go wrong here?`***

- what if an attacker sends an ambiguous reqest which is deliberately crafted and so that `front-end` and `back-end` disagree about how long this messages is. 
- let's understand this with below example,

<p align="left">
      <a href="http://nachiketrathod.com">
	<kbd>   
		<img src="/Images/3.png" height=300 width=800"></a> 
	</kbd>
</p>

### `Example,`

**Front-end** will thinks that this `Blue + Orange` block of data is one request, so immediately it will send the whole thing to backend.

But for some reason **Back-end** thinks that this message will finishes with second blue block and therefore it thinks that orange bit of data is the start of the next request and it's just gonna wait for that second request to be finished until that request is completed.

**And what's gonna complete that request?** 

<p align="left">
      <a href="http://nachiketrathod.com">
	   <kbd>
	     <img src="/Images/4.png" height=400 width=750"></a>
	    </kbd>
</p>

Well, it could be someone else sending a request to the application. So an attacker can apply **`arbitary prefix/content`** to someone else request via smuggling and That's the core primitive of this technique.

### `Desynchronizing: the classic approach`

**`Example`**


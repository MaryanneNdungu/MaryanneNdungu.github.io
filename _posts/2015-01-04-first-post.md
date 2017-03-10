---
layout: post
title: First post!
image: /img/hello_world.jpeg
tags: [random, exciting-stuff]
---

As it is now, when I tried various Cross-Origin Resource Sharing (CORS) , I noticed that there are more patterns than I thought, so I tried again how to handle it.

<h3> In the first place</h3>

In current Web browsers, Same-Origin Policy (same generation policy in Japanese) is applied to prevent information on one Web site from being abused by another malicious Web site .

For example, when displaying a certain Web site https://guiltysite.com in a browser, you can use HTTP (S ( XHR)) from this Web page from another Web site https://innocentsite.net with XMLHttpRequest (XHR) or Fetch API ), An error occurs when trying to read data.

However, it is inconvenient to limit the Web site where trust is established as a partner of data collaboration, as long as the access source is a malicious Web site, so it is inconvenient for a Web site that can allow data access There is CORS as a mechanism to enable access beyond Origin.

<h3>Example of how to use CORS</h3>

Here, some Web site https://trustedsite.com against, another Web site https://usefulapis.net states the case that you want to allow access on the HTTP (S) to as an example.

When you want to allow data to be read in a simple way
If you simply want to allow GET or POST with XHR or Fetch API, do as follows. First of all, on the client side, there is no need for any special effort in the case of XHR, and in the case of the Fetch API, declare to use CORS depending on the option.

<h5>Client JavaScript (XHR)</h5>
<pre><code>var  Xhr  =  New  XMLHttpRequest ();
Xhr . Open ( 'GET' ,  'Https://Usefulapis.Net/api' );
Xhr . addEventListener ( 'Load' ,  OnLoadFunc ,  False ); 
Xhr . Send ( Null );
</code></pre>

<h5>Client JavaScript (Fetch)</h5>
<pre><code>Fetch ( 'Https://Usefulapis.Net/api' ,  { 
Mode :  'Cors' 
.}) Then ( OnLoadFunc );
</code></pre>

On the other hand, on the Web server side, in order to explicitly inform the browser that access beyond Origin is permitted, it is necessary to add appropriate information to the HTTP response header.

First, the HTTP request header sent from the browser to the server includes Origina field for access exceeding Origin .

<code>
GET  / api  HTTP / 1.1 <br>
Origin :  https://trustedsite.com
</code>

If Origin the content of Origin is a reliable Web site Origin, in the HTTP response header,

<code>
HTTP / 1.1  200  OK <br>
Access-Control-Allow-Origin :  https://trustedsite.com
</code>

If you add content like this, access will be allowed on the browser side. In such a simple example, it is possible to specify wildcards to permit access to any website beyond Origin (partial designation such as subdomain can not be done).

<code>
HTTP / 1.1  200  OK <br>
Access-Control-Allow-Origin :  *
</code>

<h3>If you also want to allow cookies</h3>
If you want to allow sending and receiving of cookies during HTTP (S) communication, you need to do a bit more work on both the browser and the server. First of all, in the JavaScript of the browser, do as follows. Note that in this example and later onwards Access-Control-Allow-Origin, wild card specification is not permitted in, so be careful.

<h5>Client JavaScript (XHR)</h5>
<pre><code> var  Xhr  =  New  XMLHttpRequest (); 
Xhr . Open ( 'GET' ,  'Https://Usefulapis.Net/api' ); 
Xhr . WithCredentials  =  True ; 
Xhr . addEventListener ( 'Load' ,  OnLoadFunc ,  False ); 
Xhr . Send ( null );
</code></pre>

<h5>Client JavaScript (Fetch)</h5>
<pre><code>Fetch ( 'https://usefulapis.net/api' ,  { 
  mode :  'cors' , 
  credentials :  'include' 
}) then ( onLoadFunc );
</pre></code>

On the other hand, on the server side, add the following contents to the HTTP response header.

<code>HTTP / 1.1  200  OK <br>
Access-Control-Allow-Origin :  https://trustedsite.com <br>
Access-Control-Allow-Credentials :  true<br>
</code>
If you want to use more elaborate HTTP communication
Specification of CORS in, If you can apply one of the following conditions, prior to performing the actual HTTP request (GET or POST), Preflight Request as OPTIONS has it has been established to carry out the request. In this case, care must be taken on the server side because similar CORS correspondence is required in OPTIONS in addition to GET and POST.

The method of the HTTP request is other than GET, POST, HEAD.
A field other than Accept, Accept-Language, and Content-Language is included in the HTTP request header, or a field other than application / x-www-form-urlencoded, multipart / form-data, text / plain Content is specified.
The preflight request contains the following HTTP request header:

OPTIONS  / api  HTTP / 1.1 
Access-Control-Request-Method :  (HTTP method of subsequent request (GET, POST, etc.))
As a response to this preflight request, for example, it is necessary to specify a method of HTTP request to allow as access exceeding Origin at least as follows, for example.

HTTP / 1.1  200  OK 
Access-Control-Allow-Origin :  https://trustedsite.com 
Access-Control-Allow-Methods :  GET, POST, HEAD, OPTIONS
If you want to add your own HTTP request header to the request

For example, suppose you add a header called X - MyRequest and X - MyOption on the browser side.

Client JavaScript (XHR)
var  Xhr  =  New  XMLHttpRequest (); 
Xhr . Open ( 'GET' ,  'Https://Usefulapis.Net/api' ); 
Xhr . WithCredentials  =  True ; 
Xhr . setRequestHeader ( 'X-MyRequest' ,  'This-Is- Test-Cors' ); 
Xhr . setRequestHeader ( 'X-MyOption' ,  'My-Option' ); 
Xhr . addEventListener ( 'Load' ,  OnLoadFunc ,  False );
Xhr . Send ( null );
Client JavaScript (Fetch)
Fetch ( 'https://usefulapis.net/api' ,  { 
  method :  'GET' , 
  mode :  'cors' , 
  credentials :  ' include ' , 
  headers :  { 
    ' X - MyRequest ' :  ' this - is - cors - test ' , 
    ' X - MyOption ' :  ' my - option ' 
  } 
}) then ( onLoadFunc );
In this case, the browser sends a preflight request containing the following HTTP request header to the server.

OPTIONS  / api  HTTP / 1.1 
Origin :  https://trustedsite.com 
Access - Control - Request - Method :  GET 
Access - Control - Request - Headers :  X - MyRequest, X - MyOption
On the server side, it decides whether to allow methods and headers indicated in these request headers, and returns a response header. The method specified in Access - Control - Allow - Methods and the header specified in Access - Control - Allow - Headers are then allowed for HTTP requests actually sent by the browser. (The corresponding header is required for both preflight and actual request.)

HTTP / 1.1  200  OK 
Access-Control-Allow-Origin :  Https://Trustedsite.Com 
Access-Control-Allow-Credentials :  True 
Access-Control-Allow-Methods :  GET, POST, HEAD, OPTIONS 
Access-Control-Allow- Headers :  X-MyRequest, X-MyOption
When you want to add a unique HTTP response header to the response and read it from the browser

In the case of access exceeding Origin, for example, the code on the browser side,

Client JavaScript (XHR)
var  Xhr  =  New  XMLHttpRequest (); 
Xhr . Open ( 'GET' ,  'Https://Usefulapis.Net/api' ); 
Xhr . WithCredentials  =  True ; 
Xhr . setRequestHeader ( 'X-MyRequest' ,  'This-Is- Test-Cors' ); 
Xhr . setRequestHeader ( 'X-MyOption' ,  'My-Option' ); 
Xhr . addEventListener ( 'Load' ,  OnLoadFunc ,  False );
Xhr . Send ( null );

Function  OnLoadFunc ()  { 
  var  MyResponse  =  Xhr . getResponseHeader ( 'X-MyResponse' ); 
  var  MyOption  =  Xhr . getResponseHeader ( 'X-MyOption' ); 
}
Client JavaScript (Fetch)
Fetch ( 'https://usefulapis.net/api' ,  { 
  method :  'GET' , 
  mode :  'cors' , 
  credentials :  ' include ' , 
  headers :  { 
    ' X - MyRequest ' :  ' this - is - cors - test ' , 
    ' X - MyOption ' :  ' my - option ' 
  } 
}) then ( onLoadFunc );

Function  OnLoadFunc ( Response )  { 
  var  MyResponse  =  Response . Headers . Get ( 'X-MyResponse' ); 
  var  MyOption  =  Response . Headers . Get ( 'X-MyOption' ); 
}
On the other hand, from the server side,

HTTP / 1.1  200  OK 
X - MyResponse :  this - is - successful - response 
X - MyOptions :  good - result
When trying to return a unique response header like the browser to the browser, if the browser tries to acquire the contents of these response headers, it is regarded as trying to access a non-secure header and access is not permitted It is getting. (It seems that the response header that access is permitted is Cache-Control, Content-Language, Content-Type, Expires, Last-Modified, Pragma.)

To allow the browser to access such a custom response header, specify the response header you want to allow with Access - Control - Expose - Headers.

HTTP / 1.1  200  OK 
Access-Control-Allow-Origin :  Https://Trustedsite.Com 
Access-Control-Allow-Credentials :  True 
Access-Control-Allow-Methods :  GET, POST, HEAD, OPTIONS 
Access-Control-Allow- Headers :  X - MyRequest, X - MyOption 
Access - Control - Expose - Headers :  X - MyResponse, X - MyOption
By the way, are preflight requests done every time?

In the preflight request, you can specify the expiration date to be cached by the browser from the server side. Within this deadline, the first preflight request will also be applied to subsequent HTTP requests for the same URL.

HTTP / 1.1  200  OK 
Access-Control-Allow-Origin :  Https://Trustedsite.Com 
Access-Control-Allow-Credentials :  True 
Access-Control-Allow-Methods :  GET, POST, HEAD, OPTIONS 
Access-Control-Allow- Headers :  X - MyRequest, X - MyOption 
Access - Control - Expose - Headers :  X - MyResponse, X - MyOption 
Access - Control - Max - Age :  864000
For Access-Control-Max-Age, specify the expiration time in seconds. In the above example, it is 10 days (10 (days) × 24 (hours) × 60 (minute) × 60 (second) = 864,000 (sec)).

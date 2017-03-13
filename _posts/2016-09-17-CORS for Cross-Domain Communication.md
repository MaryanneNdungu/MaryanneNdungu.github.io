---
layout: post
title: CORS for Cross-Domain Communication!
image: /img/hello_world.jpeg
tags: [CORS, cross-origin, cross-domain]
---

Not until recently, when I tried various Cross-Origin Resource Sharing (CORS) ,did I noticed that there are more CORS patterns than I thought.

<h3> First...</h3>

In current web browsers, Same-Origin Policy is applied to prevent information on one website from being abused by another malicious website.

For example, when displaying a certain website https://guiltysite.com in a browser, you can use HTTP (S ( XHR)) from this webpage from another website https://innocentsite.net with XMLHttpRequest (XHR) or Fetch API ), An error occurs when trying to read data.

However, it is inconvenient to limit a website access where trust is established as a partner of data collaboration. As long as the access source is not a malicious website, it is also inconveniencing for a website that wants to allow data access. CORS is a mechanism that enables access beyond Origin.

<h3>Example of how to use CORS</h3>

Here, some website https://trustedsite.com and another website https://usefulapis.net represents a case where you would want to allow access via HTTP(S).

<h3>When you want to allow data to be read in a simple way</h3>

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

On the other hand, on the web server side, in order to explicitly inform the browser that access beyond Origin is permitted, it is necessary to add appropriate information to the HTTP response header.

First, the HTTP request header sent from the browser to the server includes Origin field for access beyond Origin.

<code>
GET  / api  HTTP / 1.1 <br>
Origin :  https://trustedsite.com
</code>

If the content of Origin points to a reliable website, the HTTP response header:

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
If you want to allow sending and receiving of cookies during HTTP(S) communication, you need to do a bit more work on both the browser and the server. First of all, in the JavaScript of the browser, do as follows. Note that in this example and later onwards Access-Control-Allow-Origin, wild card specification is not permitted, so be careful.

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
</code></pre>

On the other hand, on the server side, add the following contents to the HTTP response header.

<code>HTTP / 1.1  200  OK <br>
Access-Control-Allow-Origin :  https://trustedsite.com <br>
Access-Control-Allow-Credentials :  true<br>
</code>

<h3>If you want to use more elaborate HTTP communication</h3>

Specification of CORS in Preflight Request as OPTIONS as it has been established to carry out the request:- if you can apply one of the following conditions prior to performing the actual HTTP request (GET or POST).In this case, care must be taken on the server side because similar CORS correspondence is required in OPTIONS in addition to GET and POST.

<ul>
<li>The method of the HTTP request is other than GET, POST, HEAD.</li>
<li>A field other than Accept, Accept-Language, and Content-Language is included in the HTTP request header, or a field other than application / x-www-form-urlencoded, multipart / form-data, text / plain Content is specified.</li>
<li>The preflight request contains the following HTTP request header:</li>
</ul>

<code> OPTIONS  / api  HTTP / 1.1 <br>
Access-Control-Request-Method :  (HTTP method of subsequent request (GET, POST, etc.))<br>
</code>

As a response to this preflight request, for example, it is necessary to specify a method of HTTP request to allow access beyond Origin, for example.

<code>
HTTP / 1.1  200  OK <br>
Access-Control-Allow-Origin :  https://trustedsite.com <br>
Access-Control-Allow-Methods :  GET, POST, HEAD, OPTIONS<br>
</code>

<h3>If you want to add your own HTTP request header to the request</h3>

For example, suppose you add a header called X - MyRequest and X - MyOption on the browser side.

<h5>Client JavaScript (XHR)</h5>
<pre><code>var  Xhr  =  New  XMLHttpRequest (); 
Xhr . Open ( 'GET' ,  'Https://Usefulapis.Net/api' ); 
Xhr . WithCredentials  =  True ; 
Xhr . setRequestHeader ( 'X-MyRequest' ,  'This-Is- Test-Cors' ); 
Xhr . setRequestHeader ( 'X-MyOption' ,  'My-Option' ); 
Xhr . addEventListener ( 'Load' ,  OnLoadFunc ,  False );
Xhr . Send ( null );
</code></pre>

<h5>Client JavaScript (Fetch)</h5>
<pre><code>Fetch ( 'https://usefulapis.net/api' ,  { 
  method :  'GET' , 
  mode :  'cors' , 
  credentials :  ' include ' , 
  headers :  { 
    ' X - MyRequest ' :  ' this - is - cors - test ' , 
    ' X - MyOption ' :  ' my - option ' 
  } 
}) then ( onLoadFunc );
</code></pre>

In this case, the browser sends a preflight request containing the following HTTP request header to the server.

<code>
OPTIONS  / api  HTTP / 1.1 <br>
Origin :  https://trustedsite.com <br>
Access - Control - Request - Method :  GET <br>
Access - Control - Request - Headers :  X - MyRequest, X - MyOption <br>
</code>

On the server side, it decides whether to allow methods and headers indicated in these request headers, and returns a response header. The method specified in Access - Control - Allow - Methods and the header specified in Access - Control - Allow - Headers are then allowed for HTTP requests actually sent by the browser. (The corresponding header is required for both preflight and actual request.)

<code>
HTTP / 1.1  200  OK <br>
Access-Control-Allow-Origin :  Https://Trustedsite.Com <br>
Access-Control-Allow-Credentials :  True <br>
Access-Control-Allow-Methods :  GET, POST, HEAD, OPTIONS <br>
Access-Control-Allow- Headers :  X-MyRequest, X-MyOption <br>
</code>

<h3>When you want to add a unique HTTP response header to the response and read it from the browser</h3>

In the case of access beyond Origin, for example, the code on the browser side,

<h5>Client JavaScript (XHR)</h5>
<pre><code>
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
</code></pre>

<h5>Client JavaScript (Fetch)</h5>
<pre><code>Fetch ( 'https://usefulapis.net/api' ,  { 
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
} </code></pre>

On the other hand, from the server side,

<code>
HTTP / 1.1  200  OK <br>
X - MyResponse :  this - is - successful - response <br>
X - MyOptions :  good - result<br>
</code>

When trying to return a unique response header like the browser to the browser, if the browser tries to acquire the contents of these response headers, it is regarded as trying to access a non-secure header and access is not permitted. (It seems that the response header that access is permitted is Cache-Control, Content-Language, Content-Type, Expires, Last-Modified, Pragma.)

To allow the browser to access such a custom response header, specify the response header you want to allow with Access - Control - Expose - Headers.

<code>
HTTP / 1.1  200  OK <br>
Access-Control-Allow-Origin :  Https://Trustedsite.Com <br>
Access-Control-Allow-Credentials :  True <br>
Access-Control-Allow-Methods :  GET, POST, HEAD, OPTIONS <br>
Access-Control-Allow- Headers :  X - MyRequest, X - MyOption <br>
Access - Control - Expose - Headers :  X - MyResponse, X - MyOption<br>
</code>

<h3>By the way, are preflight requests done every time?</h3>

In the preflight request, you can specify the expiration date to be cached by the browser from the server side. Within this deadline, the first preflight request will also be applied to subsequent HTTP requests for the same URL.

<code>
HTTP / 1.1  200  OK <br>
Access-Control-Allow-Origin :  Https://Trustedsite.Com <br>
Access-Control-Allow-Credentials :  True <br>
Access-Control-Allow-Methods :  GET, POST, HEAD, OPTIONS <br>
Access-Control-Allow- Headers :  X - MyRequest, X - MyOption <br>
Access - Control - Expose - Headers :  X - MyResponse, X - MyOption <br>
Access - Control - Max - Age :  864000 <br>
</code>

For Access-Control-Max-Age, specify the expiration time in seconds. In the above example, it is 10 days (10 (days) × 24 (hours) × 60 (minute) × 60 (second) = 864,000 (sec)).

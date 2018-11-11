---
ID: 101
post_title: 'PowerShell &#8211; Web services, WSDL, and SOAP'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/powershell/powershell-web-services-wsdl-and-soap
published: true
post_date: 2014-05-11 00:00:00
---
A WSDL (Web Services Description Language) is a url web page that describes a web service. WSDL documents the web service in the form of an xml, here is an example of a WSDL:

 http://www.webservicex.net/globalweather.asmx?wsdl
 
The wsdl contains 4 main element tags:

<ul>
	<li><strong>portType</strong> - this element can be thought of as housing a collection of operations elements (aka functions). Hence each portype element in a wsdl 
is a logical grouping for a collection of functions. the portype's name is given as an attribute.</li>
	<li><strong>operation</strong> - these elements are child elements of the porttype element. Their "name" attribute is equivalent to a function's name. Each operation can 
contain upto 2 child elements "input" and "output". 
<ul>
	<li><strong>input</strong> - this is basically a function's input parameter</li>
	<li><strong>output</strong> - this is basically parameter.</li>
</ul>
The input and output elements have an attribute called "message" the value of this attribute is actually a pointer to another element called: 
</li>
	<li><strong>message</strong> - this has a "name" attribute that matches the value of the output/input element's "message" attribute's value. 

The "message" element has a child element called "part"</li>
	<li><strong>part</strong> - this explains the data type that the parameter (or output) can be of and also attaches a name (using attributes again) to the parameter/output. 

the word "part" is used because each functions "whole message" is a combination of both the input and output.</li>
</ul>



So far, we know that a porttype (aka function library) house a collection of operations (aka functions). Each functions input(aka parameter)/outputs is defined in the message-element, this include the parameter's name, and the output variable's name, since I think the output is given in the form of a variable that stores the output. 

However we haven't look at how you can access a function (e.g. do you use http, soap, etc). This information is stored in an element called <strong>binding</strong>. A binding element should be available for each port type, since all the operations (functions) in a porttype uses the same network-mechanisn. 

Each binding element has 2 attributes:

<ol>
	<li><strong>name</strong> - this helps to refer to a bind in particular</li>
	<li><strong>type</strong> - this is the name of the porttype that this binding is associated to.</li>
</ol>


Eaching binding element contains the following child elements:

<ul>
	<li><strong>soap:binding</strong> - this is actually a one line element (e.g. like html's <br />). (Note, this can also be "http:binding", if http is used instead of SOAP.) It has the following attributes:</li>
<ul>
	<li><strong>style</strong> - This defines the soap protocol to use. It is either equal to "rpc" or "document". In nearly all cases it is "document", which means that we use the http protocol to deliver the soap "envelope". I think this is optional</li>
	<li><strong>transport</strong> - this is pointer to the document that defines the protocol to be used, this should be equal to "http://schemas.xmlsoap.org/soap/http" . Note, in the case of "http:binding", we have the "verb" attribute instead of "transport", and this is equal to either "get" or "post".  </li>
</ul>


	<li><strong>wsdl:operation</strong>: There are several of these. Each one is a reference to each function that are in the given porttype. Here "http://www.w3schools.com/webservices/ws_wsdl_binding.asp" it shows that an operation doesn't have a name, this is a typo because it should have a name that correspond to the given porttype's operation's name attribute's value. Each operation here, has these elements:</li>
	<li>Each "wsdl:operation" (aside from the name attribute, which matches the operation attribute's name) have the following child elements:
</li>
<ul>
	<li><strong>Soap:operation</strong> - This has 2 attributes:</li>
<ul>
	<li><strong>soapaction</strong> - This I think is the url location to where to send the SOAP message to. 
</li>

</ul>

	<li><strong>wsdl:input</strong> - This I think is the encoding to use to send the entire SOAP message</li>
	<li><strong>wsdl:output</strong> - this I think is the encoding to use to read the SOAP message received from the web service. </li>
</ul>



</ul>

Hence without the binding element, it won't be able to know where to send/recieve soap-messages. 

<h2>Here's an Example</h2>

If you analyse the following wsdl:

http://www.webservicex.net/globalweather.asmx?wsdl

You will find that it contains 3 porttype elements, and each porttype contains 2 operations. So across all 3 porttypes there are 6 operations (functions). If you look closely you will discover that each porttype contains the functions called:

<ul>
	<li>1st porttype (GlobalWeatherSoap) has the following functions
<ul>
	<li>GetWeather</li>
	<li>GetCitiesByCountry</li>
</ul>


</li>
	<li>2nd porttype (GlobalWeatherHttpGet) has the following functions


<ul>
	<li>GetWeather</li>
	<li>GetCitiesByCountry</li>
</ul>


</li>
	<li>3nd porttype (GlobalWeatherHttpPost) has the following functions

<ul>
	<li>GetWeather</li>
	<li>GetCitiesByCountry</li>
</ul>

</li>

</ul>




	
So why do we have 3 porttypbes with 3 identical pair of functions? That's because this wsdl can accept 3 types of communications, SOAP, HTTPGet, and httpPost. In most case you will just use the SOAP porttype, but web designers could also use the other 2.  
	
Since each function has an input and output, it means that each function is accompanied by 2 message elements (one for each part), and since we have 6 functions, it means that we have a total of 12 (6*2) message elements to cover off all the functions. 

Each porttype's communication details (e.g. for putty connection, the communication details are, hostname, protocol, e.g. ssh, port number,..etc) are covered in the "binding" elements.

Since there are 3 porttypes, there should be 3 binding elements. However there is 4 (2 of which releates to SOAP). I think this could be for load balancing purposes, but not sure.   

Now in powershell, if we do the following:

<pre>
PS C:\> $url = "http://www.webservicex.net/globalweather.asmx?wsdl"
PS C:\> $webservicex = New-WebServiceProxy -Uri $url -namespace WebServiceProxy -Class GlobalWeatherSoap
</pre>

Note: I think the -class declaration is optional because I think the New-WebServiceProxy will default to the SOAP protocol anyway. 

This results in the "$webservicex" object. You can think of this object as our personal messenger that will send and receive data from the webservicex.net's globalweather service. If you do:

<pre>
PS C:\> $webservicex | gm


   TypeName: WebServiceProxy.GlobalWeather

Name                                 MemberType Definition
----                                 ---------- ----------
Disposed                             Event      System.EventHandler Disposed(System.Object, System.EventArgs)
GetCitiesByCountryCompleted          Event      WebServiceProxy.GetCitiesByCountryCompletedEventHandler GetCitiesByC...
GetWeatherCompleted                  Event      WebServiceProxy.GetWeatherCompletedEventHandler GetWeatherCompleted(...
Abort                                Method     void Abort()
BeginGetCitiesByCountry              Method     System.IAsyncResult BeginGetCitiesByCountry(string CountryName, Syst...
BeginGetWeather                      Method     System.IAsyncResult BeginGetWeather(string CityName, string CountryN...
CancelAsync                          Method     void CancelAsync(System.Object userState)
CreateObjRef                         Method     System.Runtime.Remoting.ObjRef CreateObjRef(type requestedType)
Discover                             Method     void Discover()
Dispose                              Method     void Dispose(), void IDisposable.Dispose()
EndGetCitiesByCountry                Method     string EndGetCitiesByCountry(System.IAsyncResult asyncResult)
EndGetWeather                        Method     string EndGetWeather(System.IAsyncResult asyncResult)
Equals                               Method     bool Equals(System.Object obj)
<strong>GetCitiesByCountry                   Method     string GetCitiesByCountry(string CountryName)</strong>
<strong>GetCitiesByCountryAsync              Method     void GetCitiesByCountryAsync(string CountryName), void GetCitiesByCo...</strong>
GetHashCode                          Method     int GetHashCode()
GetLifetimeService                   Method     System.Object GetLifetimeService()
GetType                              Method     type GetType()
GetWeather                           Method     string GetWeather(string CityName, string CountryName)
GetWeatherAsync                      Method     void GetWeatherAsync(string CityName, string CountryName), void GetW...
InitializeLifetimeService            Method     System.Object InitializeLifetimeService()
ToString                             Method     string ToString()
AllowAutoRedirect                    Property   bool AllowAutoRedirect {get;set;}
ClientCertificates                   Property   System.Security.Cryptography.X509Certificates.X509CertificateCollect...
ConnectionGroupName                  Property   string ConnectionGroupName {get;set;}
Container                            Property   System.ComponentModel.IContainer Container {get;}
CookieContainer                      Property   System.Net.CookieContainer CookieContainer {get;set;}
Credentials                          Property   System.Net.ICredentials Credentials {get;set;}
EnableDecompression                  Property   bool EnableDecompression {get;set;}
PreAuthenticate                      Property   bool PreAuthenticate {get;set;}
Proxy                                Property   System.Net.IWebProxy Proxy {get;set;}
RequestEncoding                      Property   System.Text.Encoding RequestEncoding {get;set;}
Site                                 Property   System.ComponentModel.ISite Site {get;set;}
SoapVersion                          Property   System.Web.Services.Protocols.SoapProtocolVersion SoapVersion {get;s...
Timeout                              Property   int Timeout {get;set;}
UnsafeAuthenticatedConnectionSharing Property   bool UnsafeAuthenticatedConnectionSharing {get;set;}
Url                                  Property   string Url {get;set;}
UseDefaultCredentials                Property   bool UseDefaultCredentials {get;set;}
UserAgent                            Property   string UserAgent {get;set;}
</pre>

You will find that that the "GetWeather" and "GetCitiesByCountry" appear as object methods. Note, anything that have "string" method can be called on.

This means that you can now do this:

<pre>
PS C:\> [XML]$AustralianCities = $webservicex.GetCitiesByCountry("Australia")
</pre>
 
Here we are basically instructing the "$webservicex" object (messenger) to go to the webservicex.net and request for a list of all the cities in Australia, which we have requested by using the GetCitiesByCountry, and passing the parameter "Australia" into it. The web service recieves the request and gives a response, this response is an output that is in SOAP (xml) format. Since the output is in xml, we can't output it directly into the powershell terminal, instead we have to capture it in the a preformatted variable object.

We can then pull the info out of this variable instead, just like we do for any other object. E.g.:

<pre>
PS C:\> $australiancities 

NewDataSet                                                                                                                     
----------                                                                                                                     
NewDataSet                                                                                                                     

PS C:\> $australiancities.NewDataSet

Table                                                                                                                          
-----                                                                                                                          
{Table, Table, Table, Table...}                                                                                                

PS C:\> $australiancities.NewDataSet.Table

Country   City
-------   ----
Australia Archerfield Aerodrome
Australia Amberley Aerodrome
Australia Alice Springs Aerodrome
Australia Brisbane Airport M. O
Australia Coolangatta Airport Aws
Australia Cairns Airport
Australia Charleville Airport
Australia Gladstone
Australia Longreach Airport
Australia Mount Isa Amo
Australia Mackay Mo
Australia Oakey Aerodrome
Australia Proserpine Airport
Australia Rockhampton Airport
Australia Broome Airport
Australia Townsville Amo
Australia Weipa City
Australia Gove Airport
Australia Tennant Creek Airport
Australia Yulara Aws
Australia Albury Airport
Australia Devonport East
Australia Goldstream Aws
Australia East Sale Aerodrome
Australia Hobart Airport
Australia Launceston Airport
Australia Laverton Aerodrome
Australia Moorabbin Airport Aws
Australia Mount Gambier Aerodrome
Australia Mildura Airport
Australia Melbourne Airport
Australia Macquarie Island
Australia Wynyard West
Australia Adelaide Airport
Australia Albany Airport
Australia Broken Hill Patton Street
Australia Ceduna Airport
Australia Derby
Australia Darwin Airport
Australia Bullsbrook Pearce Amo
Australia Edinburgh M. O.
Australia Forrest Airport
Australia Geraldton Airport
Australia Kalgoorlie Boulder Amo
Australia Kununurra Kununurra Aws
Australia Leigh Creek Airport
Australia Learmonth Airport
Australia Meekatharra Airport
Australia Port Hedland Pardoo
Australia Parafield Airport
Australia Belmont Perth Airport
Australia Katherine Aerodrome
Australia Woomera Aerodrome
Australia Bankstown Airport Aws
Australia Canberra
Australia Coffs Harbour Mo
Australia Cooma
Australia Camden Airport
Australia Dubbo
Australia Norfolk Island Airport
Australia Nowra Ran Air Station
Australia Richmond Aus-Afb
Australia Sydney Airport
Australia Tamworth Airport
Australia Wagga Airport
Australia Williamtown Aerodrome
</pre>



When we send a request to a web service, we can send it in many forms, e.g. using html's "post" or "get". However the most powerful and popular approach is to send the request in the form of xml? The structure of this xml (i.e. schema), is that of a schema called "SOAP"

You can find the structure of SOAP here:

http://www.w3schools.com/webservices/ws_soap_syntax.asp

Here is a diagram of the structure:

http://en.wikipedia.org/wiki/SOAP

In the case of the above powershell example, the object we created called $webservicex will automatically generate the SOAP request xml behind the scenes and send it to the webservicex.net web service. e.g. when we run:

<pre>
PS C:\> [xml][/xml]$AustralianCities = $webservicex.GetCitiesByCountry("Australia")
</pre>

then the GetCitiesByCountry method for the $webservicex will generate the following xml (which I got from soapui):

[code language="xml"]
&lt;soapenv:Envelope xmlns:soapenv=&quot;http://schemas.xmlsoap.org/soap/envelope/&quot; xmlns:web=&quot;http://www.webserviceX.NET&quot;&gt;
   &lt;soapenv:Header/&gt;
   &lt;soapenv:Body&gt;
      &lt;web:GetCitiesByCountry&gt;
         &lt;!--Optional:--&gt;
         &lt;web:CountryName&gt;Australia&lt;/web:CountryName&gt;
      &lt;/web:GetCitiesByCountry&gt;
   &lt;/soapenv:Body&gt;
&lt;/soapenv:Envelope&gt;
[/code]

And the method then sends this xml (SOAP) request to the webservice. After a few moments, the web service returns the following soap (xml) response:

[code language="xml"]
&lt;soap:Envelope xmlns:soap=&quot;http://schemas.xmlsoap.org/soap/envelope/&quot; xmlns:xsi=&quot;http://www.w3.org/2001/XMLSchema-instance&quot; xmlns:xsd=&quot;http://www.w3.org/2001/XMLSchema&quot;&gt;
   &lt;soap:Body&gt;
      &lt;GetCitiesByCountryResponse xmlns=&quot;http://www.webserviceX.NET&quot;&gt;
         &lt;GetCitiesByCountryResult&gt;&lt;![CDATA[&lt;NewDataSet&gt;
  &lt;Table&gt;
    &lt;Country&gt;Australia&lt;/Country&gt;
    &lt;City&gt;Archerfield Aerodrome&lt;/City&gt;
  &lt;/Table&gt;
  &lt;Table&gt;
    &lt;Country&gt;Australia&lt;/Country&gt;
    &lt;City&gt;Amberley Aerodrome&lt;/City&gt;
  &lt;/Table&gt;
  &lt;Table&gt;
    &lt;Country&gt;Australia&lt;/Country&gt;
    &lt;City&gt;Alice Springs Aerodrome&lt;/City&gt;
  &lt;/Table&gt;
  &lt;Table&gt;
    &lt;Country&gt;Australia&lt;/Country&gt;
    &lt;City&gt;Brisbane Airport M. O&lt;/City&gt;
  &lt;/Table&gt;
  .
  .
  .
  .
  &lt;Table&gt;
    &lt;Country&gt;Australia&lt;/Country&gt;
    &lt;City&gt;Tamworth Airport&lt;/City&gt;
  &lt;/Table&gt;
  &lt;Table&gt;
    &lt;Country&gt;Australia&lt;/Country&gt;
    &lt;City&gt;Wagga Airport&lt;/City&gt;
  &lt;/Table&gt;
  &lt;Table&gt;
    &lt;Country&gt;Australia&lt;/Country&gt;
    &lt;City&gt;Williamtown Aerodrome&lt;/City&gt;
  &lt;/Table&gt;
&lt;/NewDataSet&gt;]]&gt;&lt;/GetCitiesByCountryResult&gt;
      &lt;/GetCitiesByCountryResponse&gt;
   &lt;/soap:Body&gt;
&lt;/soap:Envelope&gt; 
[/code]

To summarise, A SOAP message is an ordinary XML document. The main elements of an a soap's xml request can be found here:

http://en.wikipedia.org/wiki/SOAP#SOAP_Building_Blocks

http://www.w3schools.com/webservices/default.asp
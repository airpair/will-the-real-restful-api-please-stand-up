I’ve interviewed about ~>150 engineers so far for various engineering roles,but during  all of the interviews, no one could really define RESTful API’s correctly. I realized that developers have a very convoluted understanding of what RESTful API’s are and in fact many of them use these API’s blindly. I know a lot has been written about it but most of it is either very large documentation or just shows best practices.

So I want to debunk some myths about what RESTful API’s are.

## Myth 1 - REST is a protocol like HTTP 
###<i>Incorrect, REST != HTTP</i>
Let me explain
* REST stands for Representational State Transfer. It is a framework – this can get confusing again, what the hell is a framework. So think of REST as a SET OF CONSTRAINTS(we’ll get to what those constraints are) to build highly scalable fault tolerant client server systems. The world wide web is actually a great example(and the biggest, but again slightly flawed) of REST.
* It IS NOT A PROTOCOL. A protocol in web parlance is a communication interface or rules that define how components communicate on the interweb (from types of data to the interface types)
* HTTP is a protocol. Hence REST ! = HTTP. But its the most used transport protocol with REST. You could as well use FTP or SMTP, but nobody likes those.
But most probably in your case and in most cases 

## Myth 2 - I almost always write pure REST API's
###<i>HTTP API == REST API, i.e. almost certainly your REST API’s aren’t pure REST, hence you really cant call them those</i>

* Let me drill down the idea again – REST is a set of constraints. What are those constraints
* CLIENT – SERVER – Quite obvious
* Stateless – Every request you make must contain state information, this is the clients responsibility and the server is stateless
* Cache – Response messages from the service to its consumers are explicitly labeled as cacheable or non-cacheable.This way, the service, the consumer, or one of the intermediary middleware components can cache the response for reuse in later requests.
* Layered architecture – A REST-based solution can be comprised of multiple architectural layers, and no one layer can “see past” the next. Layers can be added, removed, modified, or reordered in response to how the solution needs to evolve.
* Code on Demand(optional) -This optional constraint is primarily intended to allow logic within clients (such as Web browsers) to be updated independently from server-side logic. Code-On-Demand typically relies on the use of Web-based technologies, such as Web browser plug-ins, applets, or client-side scripting languages (i.e. JavaScript).
* Uniform contract – Probably the most important of the lot and defines the REST aspect of REST API’s.
Lets look at Uniform contract and what that means.The diagram essentially shows the important concepts. 

![uniform](https://i2.wp.com/whatisrest.com/static/images/rest_constraints/fig4.jpg)

*  Lets now focus our attention on the word REST shall we. Representational State transfer. So clearly there It’s about transferring representations of the state. Of what though?  Of Resources. Now what the hell are resources. A resource is a contextual mapping of entities that can fit into the URL model of the client – confused yet ? Example . Lets say I want to view a bank account from a database table on a remote server. My resource entity here is an account. Now if I want to get the detail of my account, how do I get it? I make a call over HTTP like this  => serverurl/v1/account/acc_number which inturn is doing this
`GET /account/acc_number HTTP/1.1`
`Accept: application/json`
The resource here is account, the Accept:application/json portion is basically the representation of data that is being transferred. And we are doing this using the HTTPVerb GET.
So most important concepts here are Resources, Representations(Media Types) and HTTPMethods

* Now REST Calls for HATEOAS – a really weird acronym that stands for Hypertext As The Engine Of Application State. This basically means that a main constraint of any restful api is that the responses drive the API usage directions. i.e. because the server is stateless and the client has to know what the endpoints are to call , this data must be transferred back in the response as hypertext i.e. URL’s. Confused. Lets take the previous example.
`GET /account/12345 HTTP/1.1`
`Accept: application/json`
Now with HATEOAS the expected response would be something like this

`HTTP/1.1 200 OK `

`<?xml version="1.0"?> <account> `

`<account_number>12345</account_number>` 

`<balance currency="usd">100.00</balance> `

`<link rel="deposit" href="/account/12345/deposit" /> `

`<link rel="withdraw" href="/account/12345/withdraw" /> `

`<link rel="transfer" href="/account/12345/transfer" />`

`<link rel="close" href="/account/12345/close" /> </account>`

* As you can see, the reponse, the “link”-tags allows us to find out the URLs that are needed for the specified actions for say Deposit or withdraw. THIS INFORMATION YOU WOULD HAVE NO CLUE ABOUT AT THE BEGINNING unlike most “REST API’s” today which document these. Getting the point?
So for a RESTful API to be truly RESTful it has to have the uniform structure of Resources, Representation and Methods as well as conform to HATEOAS.
IN GIST – HATEOAS basically implies that the only thing the client knows is the URI endpoint and everything from then on has to be driven by hypertext responses. This is violated the moment you document and expose individual. 

* Another example is, lets say I am on STACKOVERFLOW. The only thing I know is the URL, I know who/what a user or a question is (resource), I know the media types(Representation) and the website itself provides you hyperlinks to navigate through the website. This pretty much represents HATEOAS and lends the architecture to decoupling of logic of client server, and this is what I mean by www i.e. the web is RESTful in itself. If STACKOVERFLOW was not built conforming to HATEOAS, and like most of the documented “REST API’s” these days, then instead of links, you would have to type in stackoverflow.com/questions/<id>, replace id with the Question.id and paste that on your browser. MEH, see what I mean?

##Myth 3 - I am using JSON , that makes my API RESTful
###<i> Incorrect </i>

* If anything using JSON violates constraint 6 of REST, hence killing the fact that your API is RESTful
If you look at the HATEOAS component defined before, its clear that JSON is not Hypermedia, and hence one of the constraints is lost and essentially your API is not pure REST anymore. There is work going on this area – particularlyJSON Linked Data – http://json-ld.org/ that makes the attempt to confirm to HATEOAS.
So you could use XHTML to make your REST API Purely RESTful

##I should be using REST because SOAP sucks – or thats what they tell me.  

###<i>Incorrect</i>

This might come as a shock to many who seem to have convinced themselves that REST is in someway the absolute holy grail of Webservices. 
* Firstly REST and SOAP are two very different things. 
* Here I go again – REST is a set of Constraints for good service architecture while SOAP is a protocol. Simple object Access Protocol. 
* I think the whole REST is a protocol nonsense stems from the constant comparisons made between these two words. Its highly dependent on your business needs. 
* SOAP is tightly coupling, both the client and server need to be clear on an interface of communication, but in large systems that have tightly coupled business logic and legacy applications, this is quite useful to have and certainly wouldn’t be needed to be replaced by REST.

So to sum up

REST – Representational State Transfer – Set of Constraints for building scalable fault tolerant client server systems, architectural framework

RESTful – Webservices that use the REST architecture

SOAP – Simple object access protocol

But here’s the thing, its hard to follow all the principles of RESTful services to the letter, and in many cases harmful. But hopefully this clears up some of the myths of what defines pure REST API’s. Its not ok when developers basically follow the myths I’ve presented, and are in the dark about what RESTful services really are. As long as what developers build are HTTP API’s that follow some best practices(another post on this to follow just for best practices for writing API’s- RESTful or otherwise) things should be fine in the webservices world.

##Bonus Reading and Credits:

[A word on Richardson Maturity Model](http://martinfowler.com/articles/richardsonMaturityModel.html)
 
 ![Overview](https://i2.wp.com/martinfowler.com/articles/images/richardsonMaturityModel/overview.png)
 
[The original Fielding dissertation on Rest](http://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm#sec_5_2_1_1)

Some useful [SO links](http://stackoverflow.com/questions/19843480/s3-rest-api-and-post-method/19844272#19844272) 

[REST Cook Book](http://restcookbook.com/)

Originally published on my [blog](https://whizmodo.wordpress.com/2015/01/10/will-the-real-restful-api-please-stand-up/)


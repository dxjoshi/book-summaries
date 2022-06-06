- **Chapter-1:Surfing the Web** 
    - Each URL identifies a resource. When a client makes an HTTP request to a URL, it gets a representation of the underlying resource. The client never sees a resource directly.     
    - A URL identifies one and only one resource. If a website has two conceptually different things on it, we expect the site to treat them as two resources with different URLs.  
    - **Short Sessions:** HTTP sessions last for one request. The client sends a request, and the server responds. This means Alice could turn her phone off overnight, and when her browser restored the page from its internal cache, she could click on one of the two links on this page and it would still work. (Compare this to an SSH session, which is terminated if you turn your computer off.) This principle is sometimes called **statelessness**. I think this is a confusing term because the client and the server in this system both keep state; they just keep different kinds of state. The term “statelessness” is getting at the fact that the server doesn’t care what state the client is in.  
    - **Because HTTP sessions are so short, the server doesn’t know anything about a client’s application state**. The client has no direct control over resource state—all that stuff is kept on the server. And yet, the Web works. It works through REST—representational state transfer.    
    - **Application state is kept on the client, but the server can manipulate it by sending representations— HTML documents, in this case—that describe the possible state transitions. Resource state is kept on the server, but the client can manipulate it by sending the server a representation—an HTML form submission, in this case—describing the desired new state.**
    - **Connectedness:** The Web as a whole works on the principle of connectedness, which is better known as “hypermedia as the engine of application state,” sometimes abbreviated HATEOAS.   

- **Chapter-3:Resources and Representations**
    - **GET**
        - Get a representation of this resource.
        - GET is defined as a safe HTTP method. It’s just a request for information. Sending a GET request to the server should have the same effect on resource state as not sending a GET request—that is, no effect at all. Incidental side effects like logging and rate limiting are OK, but a client should never make a GET request hoping that it will change the resource state. 
    - **DELETE**
        - Destroy this resource. DELETE is obviously not a safe method. 
        - **Idempotent:** Once you delete a resource, it’s gone. The resource state has permanently changed. You can send another DELETE request, and you might get a 404 error, but the resource state is exactly as it was after the first request. The resource is still gone.
        - If a DELETE request succeeds, the possible status codes are 204 (No Content, i.e., “it’s deleted, and I don’t have anything more to say about it”), 200 (OK, i.e., “it’s deleted, and here’s a message about that”); and 202 (Accepted, i.e., “I’ll delete it later”).
        - If a client tries to GET a resource that has been DELETEd, the server will return an error response code, usually 404 (Not Found) or 410 (Gone).
    - **POST**
        - The POST method has two jobs. The first is POST-to-append, in which sending a POST request to a resource creates a new resource underneath it.    
        - The most common response code to a POST-to-append request is 201 (Created). 
        - Another common response code is 202 (Accept ed), which means that the server intends to create a new resource based on the given representation, but hasn’t actually created it yet.
        - **The POST method is neither safe nor idempotent**.
        - **2nd usage: Overloaded POST** HTTP POST is used to convey any kind of change. It’s PUT, DELETE, PATCH, LINK, and UNLINK all rolled into one. 
        - Since an overloaded POST request can do anything at all, the POST method is neither safe nor idempotent. One particular overloaded POST request may turn out to be safe, but as far as HTTP is concerned, POST is unsafe. 
    - **PUT**
        - Replace this state of this resource with the one described in the given representation.
        - **PUT is idempotent, just like DELETE.** If I send that PUT request five times, it won’t create five posts with the same text (the way five POST requests might).
    - **PATCH**
        - Modify part of the state of this resource based on the given representation. If some bit of resource state is not mentioned in the given representation, leave it alone. 
        - PATCH is like PUT, but allows for fine-grained changes to resource state.
        - “Modify the representation and PUT it back” is a simple rule, but if you just want to change one little bit of resource state, it can be pretty wasteful. The PUT rule can also lead to unintentional conflicts with other users who are modifying the same document. It would be nice if you could just send the server the parts of the document you want to change.    
        - The PATCH method allows for this. **Instead of PUTting a full representation, you can create a special “diff ” representation and send it to the server as the payload of a PATCH request.**
        - **PATCH is neither safe nor idempotent**. A PATCH request might turn out to be idempotent, so that if you accidentally apply the same patch twice to the same document, you get an error the second time. But that’s not in the standard. As far as PATCH’s protocol semantics are concerned, it’s an unsafe operation, like POST.
    - **LINK and UNLINK** 
        - These manage the hypermedia links between resources.
        - **LINK and UNLINK are idempotent, but not safe.**
    - These two methods are mostly used as a client explores an API:
        - **HEAD**
            - Get the headers that would be sent along with a representation of this resource, but not the representation itself.
            - **HEAD is a safe method, just like GET**. In fact, it’s best to think of HEAD as a lightweight version of GET. The server is supposed to treat a HEAD request exactly the same as a GET request, but it’s not supposed to send a an entity-body—only the HTTP status code and the headers.
            - Using HEAD instead of GET may not save any time (the server still has to generate all the appropriate HTTP headers), **but it will definitely save bandwidth**.
        - **OPTIONS**
            - Discover which HTTP methods this resource responds to.     
            - OPTIONS is a primitive discovery mechanism for HTTP. **The response to an OPTIONS request contains the HTTP Allow header, which lays out which HTTP methods the resource supports**.

- **Chapter-4: Hypermedia**
    - URI Templates standard defines a shortcut for URLs that include a query string.
        - http://www.youtypeitwepostit.com/messages/{?query} OR
        - http://www.youtypeitwepostit.com/messages/?query={query}
    - **URI vs URL**
        - A URL is a short string used to identify a resource. A URI is also a short string used to identify a resource. **Every URL is a URI**.    
        - The difference is: there’s no guarantee that a URI has a representation. A URI is nothing but an identifier. A URL is an identifier that can be dereferenced.
    - **Hypermedia controls have three jobs:**
        - They tell the client how to construct an HTTP request: what HTTP method to use, what URL to use, what HTTP headers and/or entity-body to send.
        - They make promises about the HTTP response, suggesting the status code, the HTTP headers, and/or the data the server is likely to send in response to a request.
        - They suggest how the client should integrate the response into its workflow. HTML GET forms and URI Templates feel similar because they do the same job. They both tell the client how to construct a URL for use in an HTTP GET request(**Workflow Control**).
    - **Workflow Control:**
        - Here’s an HTML 
            
               <a> tag: <a href="http://www.example.com/">An outbound link</a>
          If you click this link in your web browser, the browser will move to the web page mentioned in the link’s href attribute. The old page will become completely irrelevant, except as an item in your browser history. The <a> tag is an outbound link: a hypermedia control that, when activated, replaces the client’s application state with a brand new state.
        - Compare this to the following tag in HTML: 
                
                <img src="http://www.example.com/logo.png" />
          This is a link, but it’s not an outbound link; it’s an **embedded link**. Embedded links don’t replace the client’s application state. They augment it. If you visit a web page whose HTML includes this <img> tag, the image is automatically loaded in a separate HTTP request (without you having to click anything), and displayed in the same window as the web page itself. You’re still on the same page, but now you have more information.
        - An HTML document can embed more than images. Here’s some HTML markup that downloads and runs some executable code written in JavaScript: 
  
              <script type="application/javascript" src="/my_javascript_application.js"/>   
        - Here’s some markup that downloads a CSS stylesheet and applies it to the main document: 
          
              <link rel="stylesheet" type="text/css" href="/my_stylesheet.css"/> 
        - Here’s some markup that embeds another full HTML document inside this one: 
          
              <frameset> <iframe src="/another-document.html" /> </frameset> 
        - All of these are **embedding links**. The process of embedding one document in another is also called transclusion.
                   
- **Chapter-8:Profiles**
    - A **profile** is defined to not alter the semantics of the resource representation itself, but to allow clients to learn about additional semantics… associated with the resource representation, in addition to those defined by the media type.
    
- **Chapter-9:The Design Procedure**    
    - In its simplest form, the procedure has two steps:
        - Choose a media type to use in your representations. This puts constraints on your protocol semantics (the behavior of your API under the HTTP protocol) and your application semantics (the real-world things your representations can refer to).
        - Write a profile that covers everything else.             
    - This won’t necessarily give you a good API. In fact, this version of the procedure describes every API ever designed. If you wanted a really generic design that’s hard to learn, you’d blaze through step 1 by choosing application/json as your representation format. Since JSON puts no constraints on your protocol or application semantics, you’d spend most of your time in step 2, defining a fiat standard and describing it with humanreadable API documentation. That’s what most APIs do today, and that’s what I’m trying to stop.  
    - **Adding Hypermedia to an Existing API:** 
        - Suppose you already have an API designed and deployed. It’s an API typical of today’s designs, a fiat standard serving ad hoc JSON or XML representations, with no hypermedia:
            {
                "name": "Jennifer Gallegos",
                "bday": "1987-08-25"
            }
        - You should be able to get your API up to the level of quality I advocate in this book, without breaking your existing clients. Here’s a modified version of the seven-step process I laid out earlier, for fixing up a JSON-based API:
            1. Document all your existing representations. Each one will contain a number of semantic descriptors. You can’t change these, but you should be able to add new ones.  
            2. Draw a state diagram for your API. The boxes on the diagram are your existing representations. You probably won’t have any state transitions, because most existing APIs don’t have any hypermedia links. Now’s the time to add some. Use arrows to connect representations in ways that make sense. The names of the arrows are your link relations.
            At this point it may turn out that some of your semantic descriptors are actually link relations:
                { "homepage": "http://example.com" }
            You can convert them to link relations at this point, but be sure not to rename them when you get to the next step.
            3. You can’t change the name of anything you wrote down in step 1, because that would break your existing clients. But you can go through the link relations you created in step 2, and make sure their names come from the IANA and other well-known sources whenever possible.
            4. You can’t change your media type, because that would break your clients. It’ll have to stay application/json (or whatever it is now).
            5. Since you can’t change the media type, all your application semantics and protocol semantics must be defined somewhere else. You’ve got two choices: an ALPS profile or a JSON-LD context. If you wrote down any unsafe link relations in step 2, your best choice is JSON-LD with Hydra (see Chapter 12). You should be able to take your human-readable descriptions of API calls and convert them into machine-readable Hydra operations.
            6. You’ve already got most of the code written. You’ll just need to extend each representation by serving appropriate links.
            7. Your billboard URL will be the same as before. If you didn’t have one before, because your API was a group of discrete API calls, you can create a new resource to act as your home page, and know that only hypermedia-aware clients will access it.    
    
- **Chapter-11:HTTP for APIs**
    - **HTTP Performance:** HTTP defines several optimizations for discouraging requests that are likely to be pointless (caching), for reducing the cost of a request that turns out to be pointless (conditional requests), and for reducing the cost of a request in general (compression).            
        - **Caching:**
            - The simplest way to add caching to web APIs, using the HTTP header Cache-Control. The max-age directive says how long the client should wait before making this HTTP request again. If a client gets this response and half an hour later, it wants to send the request again, it should hold off. The server said to check back in an hour (3,600 seconds), and not before.
            
                    HTTP/1.1 200 OK
                    Content-Type: text/html
                    Cache-Control: max-age=3600
                    
            - Another common use of Cache-Control is for the server to tell the client not to cache a response, even if it would otherwise. This indicates that the resource state is so volatile that the representation probably become obsolete during the time it took to send it.   
            
                    HTTP/1.1 200 OK
                    Content-Type: text/html
                    Cache-Control: no-cache
                    
        - **Conditional GET:**
            - Sometimes you just don’t know when a resource’s state will change, you can’t decide on a value for max-age, so you can’t tell the client to stop making requests for that resource for a while. Instead, you can let the client make its request whenever it wants, and eliminate the server’s response if nothing has changed. This client-side feature is called a **conditional request**, and to support it, you’ll need to serve the Last-Modified or ETag header with your representations (better yet, serve both). The Last-Modified header tells the client when the state of this resource last changed. 
                                                                      
                    HTTP/1.1 200 OK
                    Content-Length: 41123
                    Content-type: text/html
                    Last-Modified: Mon, 21 Jan 2013 09:35:19 GMT        
            
            - The client makes a note of the Last-Modified value, and the next time it makes a request, it puts that value in the HTTP header If-Modified-Since. If the resource state has changed since the date given in If-Modified-Since, then nothing special happens. The server sends the status code 200, an updated Last- Modified, and a full representation. But if the representation hasn’t changed since the last request, the server sends the status code 304 (Not Modified), and no entity-body 
            
                    GET /some-resource HTTP/1.1
                    If-Modified-Since: Mon, 21 Jan 2013 09:35:19 GMT
                    
                    HTTP/1.1 200 OK
                    Content-Length: 44181
                    Content-type: text/html
                    Last-Modified: Mon, 27 Jan 2013 07:57:10 GMT
                    
                    HTTP/1.1 304 Not Modified
                    Content-Length: 0
                    Last-Modified: Mon, 27 Jan 2013 07:57:10 GMT        
            
            - There’s another strategy that is easier to implement than Last-Modified, and that avoids
              some race conditions. The **ETag header** (it stands for “entity tag”) contains a nonsensical
              string that must change whenever the corresponding representation changes.
              
            - When the client makes a second request for the same resource, it sets the If-None- Match header to the ETag it got in the original response. If the ETag in If-None-Match is the same as the representation’s current ETag, the server sends 304 (Not Modified) and an empty entity-body. If the representation has changed, the server sends 200 (OK), a full entity-body, and an updated ETag.  
             
                    HTTP/1.1 200 OK
                    Content-Length: 44181
                    Content-type: text/html
                    ETag: "7359b7-a37c-45b333d7"
                    
                    GET /some-resource HTTP/1.1
                    If-None-Match: "7359b7-a37c-45b333d7"
              
        - **Compression:**       
            - Textual representations like JSON and XML documents can be compressed to a fraction of their original size.   
            - When a client sends a request, it includes an Accept-Encoding header that says which compression algorithms the client understands.
            
                    GET /resource.html HTTP/1.1
                    Host: www.example.com
                    Accept-Encoding: gzip
                
            - If the server understands one of the compression algorithms mentioned in Accept- Encoding, it can use that algorithm to compress the representation before serving it. The server sends the same Content-Type it would send if the representation wasn’t compressed. But it also sends the Content-Encoding header, so the client knows the document has been compressed:
              
                    HTTP/1.1 200 OK
                    Content-Type: text/html
                    Content-Encoding: gzip   

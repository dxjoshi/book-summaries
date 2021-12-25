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
        - Here’s an HTML <a> tag: <a href="http://www.example.com/">An outbound link</a>
          If you click this link in your web browser, the browser will move to the web page mentioned in the link’s href attribute. The old page will become completely irrelevant, except as an item in your browser history. The <a> tag is an outbound link: a hypermedia control that, when activated, replaces the client’s application state with a brand new state.
        - Compare this to the <img> tag in HTML: <img src="http://www.example.com/logo.png" />
          This is a link, but it’s not an outbound link; it’s an **embedded link**. Embedded links don’t replace the client’s application state. They augment it. If you visit a web page whose HTML includes this <img> tag, the image is automatically loaded in a separate HTTP request (without you having to click anything), and displayed in the same window as the web page itself. You’re still on the same page, but now you have more information.
        - An HTML document can embed more than images. Here’s some HTML markup that downloads and runs some executable code written in JavaScript: 
          <script type="application/javascript" src="/my_javascript_application.js"/> 
          Here’s some markup that downloads a CSS stylesheet and applies it to the main document: 
          <link rel="stylesheet" type="text/css" href="/my_stylesheet.css"/> 
          Here’s some markup that embeds another full HTML document inside this one: 
          <frameset> <iframe src="/another-document.html" /> </frameset> 
        - All of these are **embedding links**. The process of embedding one document in another is also called transclusion.
                   
- **Chapter-8:Profiles**
    - A **profile** is defined to not alter the semantics of the resource representation itself, but to allow clients to learn about additional semantics… associated with the resource representation, in addition to those defined by the media type.
             
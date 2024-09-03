# Web Fuzzing

**Web fuzzing is a critical technique that every penetration tester should master. Unlike traditional methods that rely on predictable inputs, 
fuzzing systematically explores the vast input space to uncover hidden vulnerabilities, often revealing weaknesses that would otherwise remain unnoticed.**

## Fuzzing vs. Brute-forcing
Fuzzing casts a wider net. It involves feeding the web application with unexpected inputs, including malformed data, invalid characters, and nonsensical combinations. The goal is to see how the application reacts to these strange inputs and uncover potential vulnerabilities in handling unexpected data. Fuzzing tools often leverage wordlists containing common patterns, mutations of existing parameters, or even random character sequences to generate a diverse set of payloads.

Brute-forcing, on the other hand, is a more targeted approach. It focuses on systematically trying out many possibilities for a specific value, such as a password or an ID number. Brute-forcing tools typically rely on predefined lists or dictionaries (like password dictionaries) to guess the correct value through trial and error.

Here's an analogy to illustrate the difference: Imagine you're trying to open a locked door. Fuzzing would be like throwing everything you can find at the door - keys, screwdrivers, even a rubber duck - to see if anything unlocks it. Brute-forcing would be like trying every combination on a key ring until you find the one that opens the door.

## FFUF

FFUF (Fuzz Faster U Fool) is a fast web fuzzer written in Go. It excels at quickly enumerating directories, files, and parameters within web
applications. Its flexibility, speed, and ease of use make it a favorite among security professionals and enthusiasts.

## wfuzz/wenum

wenum is a actively maintained fork of wfuzz, a highly versatile and powerful command-line fuzzing tool known for its flexibility and customization options. 
It's particularly well-suited for parameter fuzzing, allowing you to test a wide range of input values against web applications and uncover potential vulnerabilities in how they process those parameters.

# ffuf commands

Directory Fuzzing:

`ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http: /IP:PORT/FUZZ`

File Fuzzing:

`ffuf -w /usr/share/seclists/Discovery/Web-Content/common.txt -u http: /IP:PORT/w2ksvrus/FUZZ.html -e .php,.html,.txt,.bak,.js -v`

# Recursive Fuzzing

It  starts as a normal fuzzing, but once it finds any directory it creates a sub task and starts fuzzing for that directory, untill a requirment is meet or till there are no further dir left.

## Commands 

`ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -ic -v -u http://IP:PORT/FUZZ -e .html -recursion`

`-ic` option proves invaluable. By enabling this option, ffuf intelligently ignores commented lines during fuzzing, preventing them from being treated as valid inputs.

While recursive fuzzing is a powerful technique, it can also be resource-intensive, especially on large web applications. 
Excessive requests can overwhelm the target server, potentially causing performance issues or triggering security mechanisms

To mitigate these risks, ffuf provides options for fine-tuning the recursive fuzzing process:

`-recursion-depth`: This flag allows you to set a maximum depth for recursive exploration. For example, -recursion-depth 2 limits fuzzing to two levels deep (the starting directory and its immediate subdirectories).

`-rate`: You can control the rate at which ffuf sends requests per second, preventing the server from being overloaded.

`-timeout`: This option sets the timeout for individual requests, helping to prevent the fuzzer from hanging on unresponsive targets.

Example `ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -ic -u http://IP:PORT/FUZZ -e .html -recursion -recursion-depth 2 -rate 500`

# Parameter (Get, Post, etc) and Value Fuzzing

This technique focuses on manipulating the parameters and their values within web requests to uncover vulnerabilities in how the application processes input.

Parameters are the messengers of the web, carrying vital information between your browser and the server that hosts the web application. They're like variables
in programming, holding specific values that influence how the application behaves.

## Get parameter
You'll often spot GET parameters right in the URL, following a question mark (?). Multiple parameters are strung together using ampersands (&). For example:

Code: http
`https://example.com/search?query=fuzzing&category=security`

`query` is a parameter with the value "fuzzing"

`category` is another parameter with the value "security"

`GET` parameters are like postcards – their information is visible to anyone who glances at the URL. They're primarily used for actions that don't change the server's state, like searching or filtering.

## POST Parameters: Behind-the-Scenes Communication

While GET parameters are like open postcards, POST parameters are more like sealed envelopes, carrying their information discreetly within the body of the HTTP request. They are not visible directly in 
the URL, making them the preferred method for transmitting sensitive data like login credentials, personal information, or financial details.

**Encoding**: This data is encoded into a specific format, typically application/x-www-form-urlencoded or multipart/form-data:

**application/x-www-form-urlencoded**: This format encodes the data as key-value pairs separated by ampersands (&), similar to GET parameters but placed within the request body instead of the URL.

**multipart/form-data**: This format is used when submitting files along with other data. It divides the request body into multiple parts, each containing a specific piece of data or a file.

# Fuzzing GET request method using WENUM 

To begin, we will use `curl` to manually interact with the endpoint and gain a better understanding of its behavior

`curl http://IP:PORT/get.php`

Invalid parameter value

x: 

The response tells us that the parameter x is missing. Let's try adding a value:

`curl http://IP:PORT/get.php?x=1`

Invalid parameter value

x: 1

**wenum command** 

`wenum -w /usr/share/secLists/Discovery/Web-Content/common.txt --hc 404 -u "http://IP:PORT/get.php?x=FUZZ"`


`-w`: Path to your wordlist.

`--hc 404`: Hides responses with the 404 status code (Not Found), since wenum by default will log every request it makes.

`http://IP:PORT/get.php?x=FUZZ`: This is the target URL. wenum will replace the parameter value FUZZ with words from the wordlist.

# Fuzzing the POST method

Our target application also features a POST parameter named "y" within the post.php script. Let's probe it with curl to see its default behavior:

`curl -d "" http://IP:PORT/post.php`

Invalid parameter value

y: 

The `-d` flag instructs curl to make a POST request with an empty body.

`ffuf -u http://IP:PORT/post.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "y=FUZZ" -w /usr/share/seclists/Discovery/Web-Content/common.txt -mc 200 -v`

`curl -d "y=ABC" http://IP:PORT/post.php`

# Virtual Host & Sub-Domain

**Virtual hosting** enables multiple websites or domains to be served from a single server or IP address. Each vhost is associated with a unique domain name or hostname. When a client sends an HTTP request, the web server examines the Host header to determine which vhost's content to deliver. This facilitates efficient resource utilization and cost reduction, as multiple websites can share the same server infrastructure.

**Subdomains**, on the other hand, are extensions of a primary domain name, creating a hierarchical structure within the domain. They are used to organize different sections or services within a website. For example, blog.example.com and shop.example.com are subdomains of the main domain example.com. Unlike vhosts, subdomains are resolved to specific IP addresses through DNS (Domain Name System) records.

# Adding IP & Domain to /etc/hosts

`echo "IP DOMAIN" | sudo tee -a /etc/hosts`

`tee` tell to add stuff to a file

`-a` to add the stuff at the end of a file rather than rewriting the file

# Gobuster

Gobuster is a versatile command-line tool renowned for its directory/file and DNS busting capabilities. It systematically probes target web servers or domains to uncover hidden directories, files, and subdomains

Gobuster's flexibility extends to fuzzing for various types of content:

1. Directories: Discover hidden directories on a web server.

2. Files: Identify files with specific extensions (e.g., .php, .txt, .bak).

3. Subdomains: Enumerate subdomains of a given domain.

4. Virtual Hosts (vhosts): Uncover hidden virtual hosts by manipulating the Host header.

## Gobuster VHost Fuzzing

`gobuster vhost -u http://DOMAIN:81 -w /usr/share/SecLists/Discovery/Web-Content/common.txt --append-domain`


`gobuster vhost`: This flag activates Gobuster's vhost fuzzing mode, instructing it to focus on discovering virtual hosts rather than directories or files.

`-u http://DOMAIN:81`: This specifies the base URL of the target server. Gobuster will use this URL as the foundation for constructing requests with different vhost names. In this example, the target server is located at inlanefreight.htb and listens on port 81.

`-w /usr/share/SecLists/Discovery/Web-Content/common.txt`: This points to the wordlist file that Gobuster will use to generate potential vhost names. The common.txt wordlist from SecLists contains a collection of commonly used vhost names and subdomains.

`--append-domain`: This crucial flag instructs Gobuster to append the base domain ( DOMAIN ) to each word in the wordlist. This ensures that the Host header in each request includes a complete domain name (e.g., admin.DOMAIN), which is essential for vhost discovery.

## Gobuster Subdomain Fuzzing

`gobuster dns -d inlanefreight.com -w /usr/share/SecLists/Discovery/DNS/subdomains-top1million-5000.txt`


1. `gobuster dns`: Activates Gobuster's DNS fuzzing mode, directing it to focus on discovering subdomains.

2. `-d inlanefreight.com`: Specifies the target domain (e.g., inlanefreight.com) for which you want to discover subdomains.

3. `-w /usr/share/SecLists/dDiscovery/DNS/subdomains-top1million-5000.txt`: This points to the wordlist file that Gobuster will use to generate potential subdomain names. In this example, we're using a wordlist containing the top 5000 most common subdomains.

# Identifying Endpoints (API's)

## REST API

REST APIs are built around the concept of resources, which are identified by unique URLs called endpoints. These endpoints are the targets for client requests, and they often include parameters to provide additional context or control over the requested operation.

Parameters are used to modify the behavior of API requests or provide additional information. In REST APIs, there are several types of parameters:

     Parameter Type 	                                            Description 	                                                       Example
     Query Parameters 	Appended to the endpoint URL after a question mark (?). Used for filtering, sorting, or pagination. 	  /users?limit=10&sort=name
     Path Parameters 	  Embedded directly within the endpoint URL. Used to identify specific resources. 	                      /products/{id}pen_spark
     Request Body      Sent in the body of POST, PUT, or PATCH requests. Used to create or update resources. 	            { "name": "New Product", "price": 99.99 }
       Parameters 	

### Discovering REST Endpoints and Parameters


**API Documentation**: The most reliable way to understand an API is to refer to its official documentation. This documentation often includes a list of available endpoints, their parameters, expected request/response formats, and example usage. Look for specifications like Swagger (OpenAPI) or RAML, which provide machine-readable API descriptions.

**Network Traffic Analysis**: If documentation is not available or incomplete, you can analyze network traffic to observe how the API is used. Tools like Burp Suite or your browser's developer tools allow you to intercept and inspect API requests and responses, revealing endpoints, parameters, and data formats.

**Parameter Name Fuzzing**: Similar to fuzzing for directories and files, you can use the same tools and techniques to fuzz for parameter names within API requests. Tools like ffuf and wfuzz, combined with appropriate wordlists, can be used to discover hidden or undocumented parameters. This can be particularly useful when dealing with APIs that lack comprehensive documentation.

## SOAP API

SOAP (Simple Object Access Protocol) APIs are structured differently from REST APIs. They rely on XML-based messages and Web Services Description Language (WSDL) files to define their interfaces and operations.

Unlike REST APIs, which use distinct URLs for each resource, SOAP APIs typically expose a single endpoint. This endpoint is a URL where the SOAP server listens for incoming requests. The content of the SOAP message itself determines the specific operation you want to perform.

SOAP parameters are defined within the body of the SOAP message, an XML document. These parameters are organized into elements and attributes, forming a hierarchical structure. The specific structure of the parameters depends on the operation being invoked. The parameters are defined in the Web Services Description Language (WSDL) file, an XML-based document that describes the web service's interface, operations, and message formats.

Imagine a SOAP API for a library that offers a book search service. The WSDL file might define an operation called SearchBooks with the following input parameters:

**keywords (string)**: The search terms to use.
**author (string)**: The name of the author (optional).
**genre (string)**: The genre of the book (optional).

     <soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:lib="http://example.com/library">
      <soapenv:Header/>
        <soapenv:Body>
      <lib:SearchBooks>
         <lib:keywords>cybersecurity</lib:keywords>
         <lib:author>Dan Kaminsky</lib:author>
      </lib:SearchBooks>
     </soapenv:Body>
    </soapenv:Envelope>

### Discovering SOAP Endpoints and Parameters

**WSDL Analysis**: The WSDL file is the most valuable resource for understanding a SOAP API. It describes:

Available operations (endpoints)

Input parameters for each operation (message types, elements, and attributes)

Output parameters for each operation (response message types)

Data types used for parameters (e.g., strings, integers, complex types)

The location (URL) of the SOAP endpoint

You can analyze the WSDL file manually or use tools designed to parse and visualize WSDL structures.

**Network Traffic Analysis**: Similar to REST APIs, you can intercept and analyze SOAP traffic to observe the requests and responses between clients and the server. Tools like Wireshark or tcpdump can capture SOAP traffic, allowing you to examine the structure of SOAP messages and extract information about endpoints and parameters.

**Fuzzing for Parameter Names and Values**: While SOAP APIs typically have a well-defined structure, fuzzing can still be helpful in uncovering hidden or undocumented operations or parameters. You can use fuzzing tools to send malformed or unexpected values within SOAP requests and see how the server responds.

## Identifying GraphQL API Endpoints and Parameters

GraphQL APIs are designed to be more flexible and efficient than REST and SOAP APIs, allowing clients to request precisely the data they need in a single request.

Unlike REST or SOAP APIs, which often expose multiple endpoints for different resources, GraphQL APIs typically have a single endpoint. This endpoint is usually a URL like /graphql and serves as the entry point for all queries and mutations sent to the API.

GraphQL uses a unique query language to specify the data requirements. Within this language, queries and mutations act as the vehicles for defining parameters and structuring the requested data.

### GraphQL Queries

Queries are designed to fetch data from the GraphQL server. They pinpoint the exact fields, relationships, and nested objects the client desires, eliminating the issue of over-fetching or under-fetching data common in REST APIs. Arguments within queries allow for further refinement, such as filtering or pagination.

      Component 	|                               Description 	                                                 |        Example
        Field 	     |       Represents a specific piece of data you want to retrieve (e.g., name, email). 	    |      name, email
     Relationship 	|      Indicates a connection between different types of data (e.g., a user's posts). 	    |      posts
     Nested Object 	|   A field that returns another object, allowing you to traverse deeper into the data graph. | 	  posts { title, body }
       Argument 	|      Modifies the behavior of a query or field (e.g., filtering, sorting, pagination). 	    |posts(limit: 5) (retrieves the first 5 posts of a user)

        query {
     user(id: 123) {
       name
       email
     posts(limit: 5) {
      title
        body
       }
      }
    }

### GraphQL Mutations

Mutations are the counterparts to queries designed to modify data on the server. They encompass operations to create, update, or delete data. Like queries, mutations can also accept arguments to define the input values for these operations.

   Component 	|                                   Description 	                              |                            Example
   Operation 	|  The action to perform (e.g., createPost, updateUser, deleteComment). 	     |                          createPost
   Argument 	|  Input data required for the operation (e.g., title and body for a new post). |     title: "New Post", body: "This is the content of the new post"
   Selection 	|  Fields you want to retrieve in the response after the mutation completes     |                           id, title

                      mutation {
                   createPost(title: "New Post", body: "This is the content of the new post") {
                      id
                       title
                     }
                  }

### Discovering Queries and Mutations

There are a few ways to discover GraphQL Queries and Mutations:

**Introspection**: GraphQL's introspection system is a powerful tool for discovery. By sending an introspection query to the GraphQL endpoint, you can retrieve a complete schema describing the API's capabilities. This includes available types, fields, queries, mutations, and arguments. Tools and IDEs can leverage this information to offer auto-completion, validation, and documentation for your GraphQL queries.

**API Documentation**: Well-documented GraphQL APIs provide comprehensive guides and references alongside introspection. These typically explain the purpose and usage of different queries and mutations, offer examples of valid structures, and detail input arguments and response formats. Tools like GraphiQL or GraphQL Playground, often bundled with GraphQL servers, provide an interactive environment for exploring the schema and experimenting with queries.

**Network Traffic Analysis**: Like REST and SOAP, analyzing network traffic can yield insights into GraphQL API structure and usage. By capturing and inspecting requests and responses sent to the graphql endpoint, you can observe real-world queries and mutations. This helps you understand the expected format of requests and the types of data returned, aiding in tailored fuzzing efforts.
































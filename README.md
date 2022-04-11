## CS3700 Project 5 Web Crawler

#### High Level Approach: 

First of all, the program does not use any http and URL library (except the HTMLParser from http.parser).

Therefore, all connections are conducted by the socket connect and HTTP requests are sent through the socket (plain text formatted with ASCII). The program will first send a GET message to establish the login process. 

HTTP request for getting into the login page. 

```
GET / HTTP/1.1
Host: SERVER:PORT
Connection: keep-alive
```

Then, the program will use POST request to login into the website. 

```
POST (URL) HTTP/1.1\n
Host: SERVER:PORT
Cookie: csrftoken= (Cookie received in the previous GET's response)
Connection: keep-alive
Content-type: application/x-www-form-urlencoded
Content-Length: xx
```

Then, the program will dig all links posted on the HTTP response. 

```
GET / HTTP/1.1
Host: SERVER:PORT
Cookie: xxxxx //  (two tokens: csrftoken and sessionid)
Connection: keep-alive
```

The program uses BFS to explore the website to find the secret flag. 

It will start with one link and record all href link posted on the website and put them in unvisited list (queue). When visiting one page, that page will be popped from the queue and put into visited list. Therefore, when one branch is completed searched, it will go to the next unvisited page in the queue until finding all five secret flags. 

The HTTPParser helps analyze the HTTP content and find all href links under /a/ and secret flags under /h3/. 

#### Challenge Faced: 

Debugging in this project is hard since both HTTP headers and HTTP context need to be checked. Therefore, when some problems appeared, all these messages need to be printed out, which is huge after the loop (the login debugging is much more simple). 

Also, the code might run well, but cannot find the secret flags. Since all secret flags are under /h3/, wrong finding logic will cause the flag not to appear (debugging at this point requires to print all HTTP contexts out , but | grep secret flag can make it a little bit easier to find the problem). If a secret flag is found in a HTTP context, using that string and run in another simple code to test will be much easier to do. 

At last, the server will automatically close the connection, which is confusing at first. We thought it is the HTTP1.1's chunked encoding problem. However, only closing connection causes receiving empty message in the socket, not HTTP1.1's chunked encoding. Finding this problem from the HTTP header (the last message before the socket closed is marked as "Connection: close") costed some time. 

#### How to Test the Code:

We will run the given script and print out all HTTP headers and contexts to find out bugs. If all five secret flags are printed out, the code is considered as running successfully. 

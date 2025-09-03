# This is a simple mcp server tool to explore how mcps work under the hood and their architecture
#to build an mcp we need 3 core concepts:
	- mcp client (eg : claude desktop etc)
	- JSON-RPC (stdio/sockets) --> a transport layer so the mcp client and the server can talk to each other
	- mcp server (mcp program in our case) that can connects to other data sources/tools etc

                ┌───────────────────────────────┐
                │        MCP Client             │
                │  (AI assistant, e.g. Claude)  │
                └───────────────┬──────────────┘
                                │
                       JSON-RPC 2.0 (stdio/sockets)
                                │
                ┌───────────────┴──────────────┐
                │        MCP Server            │
                │   (your custom program)      │
                └───────────────┬──────────────┘
                                │
         ┌───────────────┬───────────────┬─────────────────┐
         │               │               │                 │
   ┌──────────┐    ┌───────────┐    ┌──────────┐    ┌───────────────┐
   │  Tools   │    │ Resources │    │ Prompts  │    │ Subscriptions │
   │ (funcs)  │    │ (data)    │    │ (templates)│   │ (event feeds) │
   └──────────┘    └───────────┘    └──────────┘    └───────────────┘

Examples:

- Tool:    ping(host), run_sql(query), say_hello(name)
- Resource: notes/, logs/, database tables
- Prompt:   "Summarize today's logs"
- Event:    "New CVE found", "Disk usage > 90%"

#this way we can build a lot more complex setups and context specific ai tools beyond llms or chatbots
#before diving in, we need first to understand what's the transport layer that makes this possible, which is the JSON-RPC layer:
 - JSON-RPC follows a simple request - response model where both the request and the response are both single json objects
 - a request object specifies the method to be called, and the parameters to be passed to it

- JSON-RPC vs REST : are both similar in way that both use the request-response model, however the json-rpc is action oriented, the client needs to know the function name and its parameters in advance,
							 where the restful method is ressource oriented, using a uniform interface with http verbs, (PUT/GET/UPDATE..)

- while both can use http as a transport, JSON-RPC is more flexible because its transport agnostic(doesn't care how the data is being sent), it can be implemented over any protocol, websockets, http, tcp or other data transport protocols,
this makes it a popular choice for microservices and applications like blockchain networks where a single, specific action needs to be performed remotely.

-JSON-RPC operates on the Remote procedure protocol(RPC, a way to call a function that isn't in your computer as it is on your own computer in your program), the client's request is a command to execute a specific method on the server, the request includes the method's name and its parameters
	Example: To update a user's name, you'd send a request with { "method": "updateUserName", "params": { "id": 123, "name": "Jane" } }. The server understands this as "call the updateUserName function."


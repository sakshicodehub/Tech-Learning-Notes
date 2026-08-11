Platform as a Service

Now imagine you don't even want to manage the operating system and server.

You just want to say:

"Here is my application. Please run it."

That's where PaaS comes in.

YOU
│
└── Your Application / Code
       ↓
☁️ PaaS
├── Runtime
├── OS
├── Servers
├── Storage
└── Networking

The cloud provider manages much more for you.

You generally don't worry about:

Physical server
Operating system
Server installation
Runtime configuration
Many infrastructure details

You focus primarily on building and deploying your application.

Example

You create a Python web application.

Instead of:

Get server
↓
Install Linux
↓
Install Python
↓
Configure server
↓
Configure networking
↓
Deploy application

With a PaaS, the process can be closer to:

Write application
       ↓
Deploy
       ↓
PaaS runs it

# PaaS solves:

> "I want to build and run an application without managing the underlying infrastructure."

## Examples:

Heroku
Google Cloud App Engine
Microsoft Azure App Service
Vercel
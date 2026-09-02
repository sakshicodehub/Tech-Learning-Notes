Technical Questions:🔹 Hotwire / Turbo  

What is Turbo Frame?

Why was Hotwire introduced in Rails?

What is Turbo Frame & Turbo Stream?

Difference: Turbo Frame vs Turbo Stream

Enable Turbo Drive to a specific page

Real-time notifications with Turbo Stream (post/comment example)

Inline editing form with Turbo  

🔹 Rails 7 / Rails 8  

Rails 7 features

Rails 8 features

Rails 8 authentication changes

Why upgrade Rails 3 → 7?

Why did Rails eliminate CanCanCan and Devise in Rails 8?

Solid Cable, Solid Cache, Solid Queue explained

Active Record 8 updates

Active Record Encryption  

🔹 Architecture / Design  

Advantages of loosely coupled architecture

Communication medium between microservices

Eager Loading vs Lazy Loading (and which join they use)  

🔹 Database Concepts  

ACID properties (explain)

Stored Procedures

Example: Comment & Post model with Turbo Stream

Which SQL joins are used in eager loading  

🔹 API / HTTP  

HTTP methods in RESTful APIs

CORS explained  

🔹 Performance & Scalability  

App has huge data (1L users), API too slow → measures to improve performance

Heavy loaded website → performance improvement measures  

🔹 Security  

Security features in Rails (DDoS, SQL Injection, CSRF, XSS, etc.)

DDoS handling in Rails

Mass assignment protection

CSRF token handling  

🔹 Deployment  

Rails deployment in Heroku, AWS, GCP (pros/cons & approach)

CI/CD pipelines (general & Rails-specific best practices)  

🔹 Frontend / CSS  

Tailwind CSS advantages

Flowbite advantages

Bootstrap overview  

RESTful APIs – design and structure.  
Structuring Rails codebase when building APIs.  
Background jobs / queues – e.g., Sidekiq, ActiveJob, SOLID queues  
Hotwire & Stimulus experience.  
Turbo Streams (example with comments).  
Using Turbo Streams without WebSockets.  
Submitting forms using Turbo Streams.  
Live dashboards – e.g., patient dashboard, improving data access speed.  
Handling reports that take a long time to generate.  
Synchronous reports that take a long time – handling them efficiently.  
Tailwind CSS and Flowbite components.  
Advantages and disadvantages of Tailwind.  
CSS knowledge.  
Minitest.  
Minitest vs RSpec – which is better and why.  
Deployment tools: Heroku, Render, Capistrano, Kamal.  
Two-factor authentication (2FA) on top of Devise.  
Implementing 2FA without using a gem.  
SSO (Single Sign-On) – explanation and Rails implementation.  
Node.js frameworks – comparison and usage.  
CI/CD pipelines – explanation and Rails example.  
GitHub Actions for CI/CD – example and workflow explanation.  
SOC – Separation of Concerns (SoC) or Security Operations Center.  
Ways to prevent N+1 queries without using a gem.  
Microservice Architecture – How services connect

Performance Optimization Approaches in Rails

ActiveRecord Associations / N+1

Difference between includes, preload, eager_load

Will joins solve N+1 query?

Where joins will be needed?

HTTP / REST in Rails

OPTIONS calls and why used

PUT vs PATCH differences

Callbacks in Rails

Callback order: before_validation, after_save, etc.

Including before_commit and after_commit

Migrations

How to revert a migration (db:rollback, db:migrate:down)

Scenario: table in schema.rb missing in structure.sql and running db:structure:load

Ruby / Rails Internals

attr_accessor

Manual getters and setters without attr_accessor

Custom to_s for a class

Metaprogramming in Ruby (define_method, method_missing, send)

Rails Schema Management

schema.rb vs structure.sql
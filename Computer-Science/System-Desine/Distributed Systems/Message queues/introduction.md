  
            Scheduler
                │
                ▼
      Create Email Jobs
                │
                ▼
        Queue (Redis/SQS/Kafka)
                │
      ┌─────────┼─────────┐
      ▼         ▼         ▼
   Worker 1  Worker 2  Worker 3
      │         │         │
      └─────────┼─────────┘
                ▼
      Email Provider (SES, SendGrid...)
                │
                ▼
           User Mailboxes

### What is a Queue? 
    A queue is a data structure that stores tasks (called jobs or messages) to be processed later.
    It follows the FIFO (First In, First Out) principle.

### Basic Queue Architecture

           Producer
      (Web Application)
               │
               ▼
        +---------------+
        |     Queue     |
        +---------------+
               │
        ┌──────┴──────┐
        ▼             ▼
    Worker 1      Worker 2
        │             │
        ▼             ▼
     Process Job   Process Job

There are three main parts:

    Producer – creates jobs.
    Queue – stores jobs.
    Consumer (Worker) – processes jobs.

Priority Queues


### Which queue should you choose?
    Small application (up to ~100,000 emails/day)
        Redis + Sidekiq
        Amazon SES
    Medium application (100,000–5 million emails/day)
        Redis + Sidekiq or Amazon SQS
        Amazon SES or SendGrid
    Large application (10 million+ emails/day)
        Apache Kafka or Amazon SQS
        Hundreds of worker processes
        Amazon SES or SparkPost
        Rate limiting
        Retry queues
        Dead-letter queues
        Monitoring and alerting


### Interview Question:

# Q: Why use a queue for bulk email instead of sending emails directly from the application?
Answer:
A queue decouples email sending from the user request. Instead of making users wait while emails are sent, the application enqueues jobs and returns immediately. Background workers process the queue asynchronously, allowing the system to scale horizontally, handle retries, respect provider rate limits, and continue operating even if the email service is temporarily unavailable.


# 1. Why do we use a queue?

Answer:
A queue allows long-running tasks (email sending, image processing, report generation, etc.) to run asynchronously. This keeps user requests fast, improves scalability, and enables retries and fault tolerance.

# 2. What is the difference between synchronous and asynchronous processing?

Answer:

Synchronous: The client waits until the task finishes before receiving a response.
Asynchronous: The application enqueues the task and responds immediately. A background worker completes the task later.
# 3. Why are multiple workers useful?

Answer:
Multiple workers process jobs in parallel, increasing throughput and reducing the total processing time for large workloads.

# 4. What is a Dead Letter Queue (DLQ)?

Answer:
A Dead Letter Queue stores jobs that continue to fail even after the maximum number of retries. It prevents endless retry loops and allows engineers to inspect and fix problematic jobs.

# 5. How does a queue guarantee that two workers don't process the same job?

Answer:
When a worker retrieves a job, the queue marks it as reserved, locked, or in-flight. Other workers cannot take that job until it is either acknowledged as complete or returned to the queue if the worker fails or the visibility timeout expires. The exact mechanism depends on the queue implementation (Redis, RabbitMQ, SQS, Kafka, etc.), but the goal is to ensure a single worker owns the job at a time.
# [Tutorial] How to Create Signadot Sandboxes for Services that Use Google Pub/Sub

## Links
- [Selective Consumption with Kafka Example Repository](https://github.com/signadot/examples/tree/main/selective-consumption-with-kafka)
- [Kafka Step-by-Step Tutorial](https://www.signadot.com/blog/kafka-step-by-step-tutorial)

## Introduction
### Overview of Google Pub/Sub:
- Google Pub/Sub is a fully-managed messaging service for asynchronous communication between microservices.
- It enables event-driven architectures with decoupled publishers and subscribers.

### Challenges in Testing Pub/Sub-Based Microservices:
- Shared staging environments can lead to interference between developers' tests.
- Ensuring test isolation while maintaining shared infrastructure can be challenging.

### Need for Sandboxes:
- Sandboxes provide isolated environments for feature testing without impacting other services.
- They ensure selective message consumption and routing based on specific identifiers.

### Signadot Sandboxes as a Solution:
- Signadot Sandboxes allow developers to test Pub/Sub workflows in isolated environments.
- By leveraging routing keys and the Routes API, messages are dynamically routed to sandbox-specific services.

[This section can be concise as this is a tutorial is about using Sandboxes with Google Pub/Sub and the focus is on the code and getting it to work end-to-end. The "why sandbox" aspect is assumed to be known and it out of scope for this tutorial.]

## Components of the Demo Application

### Publisher:
- Sends messages to a Pub/Sub topic.
- Adds sandbox-specific routing keys to message attributes.

### Subscriber:
- Consumes messages from Pub/Sub topics.
- Processes messages only if the routing key matches its sandbox environment.

### Signadot Operator:
- Manages the sandbox environment and provides routing information using the Routes API.

### Frontend:
- A user interface to:
  - Send messages.
  - View logs for processed messages (both baseline and sandbox).

## Request Flow

### Baseline Flow (No Routing Key):
- Publisher sends messages without routing keys.
- Only the baseline subscriber processes these messages.
- Sandbox subscribers ignore the messages due to the absence of routing keys.

### Sandbox Flow (With Routing Key):
- Publisher sends messages with sandbox-specific routing keys.
- Sandbox subscribers process these messages if the routing key matches their sandbox.
- Baseline subscribers ignore these messages.

---

## Step-by-Step Guide

### Step 1: Deploy the Demo Application

1. **Clone the Repository:**
   ```bash
   git clone <repository-link>
   cd <repository-folder>
   ```

2. **Deploy the Application:**
   Deploy all necessary components, including the Publisher, Subscriber, Redis, and Frontend, to your Kubernetes cluster.

3. **Verify the Deployment:**
   Ensure all components are running properly in the Kubernetes cluster.

4. **Expose the Frontend:**
   Forward the frontend service to enable interaction with the demo application.

   ```bash
   kubectl port-forward svc/frontend-service 8080:80
   ```

### Step 2: Test Baseline Behavior Without Sandboxes

1. **Access the Frontend:**
   Open the frontend in a browser: `http://localhost:8080`.

2. **Send a Message:**
   Use the frontend to send a message without a routing key.

3. **Observe Results:**
   Verify that the baseline subscriber processes the message.
   Confirm that sandbox subscribers ignore the message.

### Step 3: Update the Publisher for Routing Key Propagation

1. **Extract Routing Key:**
   Modify the Publisher to extract routing keys from incoming requests.

2. **Attach Routing Key to Messages:**
   Ensure the routing key is added to Pub/Sub message attributes before publishing.

### Step 4: Update Subscriber for Selective Message Consumption

1. **Assign Unique Subscription Names:**
   Dynamically generate subscription names using the sandbox environment variable provided by Signadot.

2. **Fetch Routing Keys Using the Routes API:**
   Periodically update routing keys from the Signadot Routes API.

3. **Filter Messages Based on Routing Keys:**
   Implement logic to process messages only if the routing key matches the subscriber's sandbox.

4. **Ensure Message Context:**
   Retain and propagate the routing key in subsequent inter-service communications to maintain context.

### Step 5: Create Sandboxes

1. **Create Publisher Sandbox:**
   Use Signadot to create a sandbox for the Publisher, enabling it to send sandbox-specific messages.

2. **Create Subscriber Sandbox:**
   Use Signadot to create a sandbox for the Subscriber, ensuring it processes only relevant sandbox-specific messages.

### Step 6: Test Sandbox Behavior

1. **Scenario 1: Baseline Publisher and Baseline Subscriber**
   - Send a message without a routing key.
   - Verify that only the baseline subscriber processes the message.

2. **Scenario 2: Baseline Publisher and Sandbox Subscriber**
   - Enable the sandbox environment for the Subscriber.
   - Send a message with a routing key.
   - Verify that the sandbox Subscriber processes the message while the baseline Subscriber ignores it.

3. **Scenario 3: Sandbox Publisher and Sandbox Subscriber**
   - Enable sandbox environments for both the Publisher and Subscriber.
   - Send a message with a routing key.
   - Verify that only the sandbox Subscriber processes the message.

[Note that each consumer that is sandboxed will need to create a new "subscription" as per https://cloud.google.com/pubsub/docs/pubsub-basics as it needs to get a copy of all the msgs that the baseline consumer receives.]
### Step 7: Monitor and Debug

1. **Log Observations:**
   Monitor logs in the frontend or Signadot dashboard to verify routing behavior.

2. **Validation:**
   Confirm that routing keys effectively isolate messages between baseline and sandbox environments.

---

## Conclusion

1. Highlight how Signadot Sandboxes enable isolated testing of Google Pub/Sub workflows.
2. Emphasize the scalability and cost-efficiency of sandbox environments in reducing test interference.
3. Explain how this approach improves developer productivity by enabling isolated feature testing without duplicating infrastructure.

---

## Resources

1. **GitHub Repository:** [Link to Demo Application Repository](<repository-link>)
2. **Signadot Documentation:** [Guide for Using Signadot Sandboxes](https://docs.signadot.com/)
3. **Google Pub/Sub Documentation:** [Configuring Pub/Sub Topics and Subscriptions](https://cloud.google.com/pubsub/docs/overview)

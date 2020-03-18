## Sample code for GCP pubsub

### Sequence diagram

- Option-1
![Seq img](docs/img/cv-case-seq-2.png)


```js

const fetch = require("node-fetch");
// code to create case
async function createCase(subject, at, priority) {
    const sobj = 'Case';
    // TODO: change the url as required
    const url = `https://mohansun-fsc-21.my.salesforce.com/services/data/v46.0/sobjects/${sobj}`;

    // TODO: design the fields needed for the case creation and get it a part of the message data
    const postbody = { "Subject": `${subject}`, "Priority": `${priority}`} ;

    const params = {
        method: "POST",
        mode: "cors",
        headers: {"Content-Type":"application/json", "Authorization": `Bearer ${at}` },
        body: JSON.stringify(postbody)
    };
    try { await fetch(url, params)
    }
    catch (err) {
      console.log('Fetch ERROR!' + err);
    }
}


 const subscriptionName = 'CV19_CASE';
 const timeout = 60;

// Imports the Google Cloud client library
const {PubSub} = require('@google-cloud/pubsub');

// Creates a client; cache this for further use
const pubSubClient = new PubSub();

function listenForMessages() {
  // References an existing subscription
  const subscription = pubSubClient.subscription(subscriptionName);

  // Create an event handler to handle messages
  let messageCount = 0;
  const messageHandler = message => {
    console.log(`Received message ${message.id}:`);
    console.log(`\tData: ${message.data}`);
    console.log(`\tAttributes: ${message.attributes}`);
    messageCount += 1;

    // CREATE Case here
     const cst = await createCase(message.data.subject, message.data.at, message.data.priority);

    // "Ack" (acknowledge receipt of) the message
    message.ack(message.data.);
  };

  // Listen for new messages until timeout is hit
  subscription.on('message', messageHandler);

  setTimeout(() => {
    subscription.removeListener('message', messageHandler);
    console.log(`${messageCount} message(s) received.`);
  }, timeout * 1000);
}

listenForMessages()
```
- Option-2
![Seq img2](docs/img/seq-2.png)

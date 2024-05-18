# NATS Policies

## Introduction

NATS serves as a central messaging queue utilized by numerous consumers within a large organization such as Snapp!. To maintain a stable NATS cluster, it is essential to manage the subscription methods employed by the teams. Below is some general advice for consuming from NATS effectively.

- **Decide on Quality of Service**: 
  Determine if your service can tolerate event loss. If tolerable, using NATS to connect to a Jetstream cluster may be more appropriate than utilizing a Jetstream client directly.

- **Setting Message Acknowledgement Thresholds**: 
  It's important to establish limits for pending acknowledgements to prevent message overload and ensure smooth operation. Monitor and adjust these thresholds in accordance with your system's performance and reliability requirements.

- **Define Stream Retention Policies**:
  Carefully specify retention policies for your streams. This will control how long messages are kept and what action to take when a limit is reached, balancing resource usage with the need for historical data.

- **Determining Consumer Starting Points**:
  In the event of losing a consumer or creating a new one, you need to specify a practical starting point. This could be the beginning of the stream, the most recent message, or a specified historical message, depending on your service's ability to handle message replay and its consistency requirements.

- **Having Metrics over Consumers and Producers**:
  Using NATS (specially NATS Core) we don't have any detailed metric on server-side about the number of produced or consumed messages so it should be implemented over producers and consumers. These metrics should have cooresponding alerts to notify about the times that consumers and producers has zero message per second.

## What clients should have?

- Define following metrics:
  - Latency (using defined message header in the organization)
  - Number of published / consumed messages
- QoS (Quality of service)
- Using pull-based consumers (having it using NATS SDK upgrade)
- Have more than one cluster to seperate steams and their load into different clusters
- Authentication
- Configuration

## Quality of Service

When your application (referring to consumer applications) can tolerate message loss (note that messages are not typically lost under normal conditions;
loss occurs when the consumer is either not processing data or cannot handle it properly), it is advisable to use NATS core subscriptions.
To use NATS core subscriptions, simply avoid upgrading your connection to JetStream.

```go
package main

import (
	"fmt"
	"log"
	"runtime"

	"github.com/nats-io/nats.go"
)

func main() {
	// Connect to a NATS server (127.0.0.1:4222), server
	// has Jetstream enabled and "my.subject" belongs to a stream.
	nc, err := nats.Connect(nats.DefaultURL)
	if err != nil {
		log.Fatal(err)
	}
	defer nc.Close()

	// Async Subscriber using NATS core
	nc.Subscribe("my.subject", func(m *nats.Msg) {
		fmt.Printf("Received a message: %s\n", string(m.Data))
	})

	// Keep the connection alive
	runtime.Goexit()
}
```

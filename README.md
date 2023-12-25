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

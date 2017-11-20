# Introduction

Using a 0-tlog client, a user can store and replicate logs (blocks of data). In case the user endures a failure, data (blocks) can be recovered by replaying transaction logs, recorded by the 0-tlog server. The 0-tlog client employs one or multiple 0-tlog servers, to store blocks it receives from the user asynchronously. The 0-tlog client-server infrastucture has to be designed for storing and replication transaction logs in a secure and fast way.

# Main design aspects

- 0-tlog server should be stateless. Any application can work with any number of 0-tlog servers. To spread the workload and/or provide greater reliability, a t-log client can employ multiple t-log servers at once, for the same user. 0-tlog servers in the system are independent from each other, allowing for asynchronous replication of the transaction logs. 

- 0-tlog client ensures that the multiple 0-tlog setup works by keeping markers for each 0-tlog server. The function of markers is to memorize which aggregation has been sent to which 0-tlog server. Due to the markers the client knows the state of each 0-tlog server. This way markers play role of the only point of synchronizathetion in the system.

- Reply-request communication between the 0-tlog server and the 0-tlog client must be avoided when possible. The application should send logs as fast as possible and, therefore, should not wait for the 0-tlog server to acknowledge every log sent to it. For this sake client has to keep separate sockets open for sending data and for control channel.

- All stored (meta)data is expected to be encrypted using the key given by the user via the configuration given to the 0-tlog client. Because of this transport of private keys, it is recommended, but not enforced, to use a secure (e.g. TLS) connection between the different 0-stor and 0-0-tlog components.

- Each 0-tlog server can be used by multiple 0-stor clients, and each connected client lives within its own session on that server:
  - This session is established using a handshake protocol which is used to ensure compatibility and share configurations and any required info. 
  - After the handshake protocol, the session stays open as long as is required, and as long as the 0-tlog client wishes to send data (blocks) to be aggregated.

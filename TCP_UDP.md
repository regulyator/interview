First of all, both [TCP](https://en.wikipedia.org/wiki/Transmission_Control_Protocol) and [UDP](https://en.wikipedia.org/wiki/User_Datagram_Protocol) are transport layer protocols according to the [OSI model](https://en.wikipedia.org/wiki/OSI_model), and they are part of the [IP protocol suite](https://en.wikipedia.org/wiki/Internet_protocol_suite).

There is a key difference between TCP and UDP that makes sense to us as developers:

### Connection

TCP is connection-oriented, which means it requires a connection to be established between the sender and the receiver before data is sent. In simple words, for establishing a connection and starting to send data, you need to:

- **Client**: Sends a `SYN` packet to the server.
- **Server**: Sends back a `SYN-ACK` packet to the client.
- **Client**: Sends an `ACK` packet back to the server.

Where `SYN`, `SYN-ACK`, and `ACK` are special flags in the TCP header. This process is called the [three-way handshake](https://en.wikipedia.org/wiki/Transmission_Control_Protocol#Connection_establishment).

You also need to perform a [four-way handshake](https://en.wikipedia.org/wiki/Transmission_Control_Protocol#Connection_termination) to close the connection. It is a bit more complex than the three-way handshake, but the idea is the same.

On the other hand, UDP is connectionless, which means it does not require a connection to be established before sending data. You can just send the data to the receiver without any prior setup. UDP is considered a fire-and-forget protocol; you only need to know the IP address and the port of the receiver.

### Reliability

You might wonder why, in the case of TCP, so many steps are needed to establish a connection, and why a four-way handshake is required to close it. The answer is reliability. TCP is reliable, ensuring that data will be delivered to the receiver in the correct order and without errors. If the data is not delivered correctly, TCP will retransmit it until it is.

This is achieved using [sequence numbers](https://en.wikipedia.org/wiki/Transmission_Control_Protocol#Sequence_numbers) and [acknowledgment numbers](https://en.wikipedia.org/wiki/Transmission_Control_Protocol#Acknowledgment_numbers). The sender assigns a sequence number to each segment it sends, and the receiver assigns an acknowledgment number to each segment it receives. If the sender does not receive an acknowledgment within a certain time period, it retransmits the segment.

Conversely, UDP is unreliable because it does not guarantee that the data will be delivered to the receiver, nor does it ensure the order or the integrity of the data.

### Security

Both TCP and UDP have their own vulnerabilities in terms of security. For example, TCP is susceptible to [SYN flood attacks](https://en.wikipedia.org/wiki/SYN_flood) and [TCP reset attacks](https://en.wikipedia.org/wiki/TCP_reset_attack), while UDP is vulnerable to [UDP flood attacks](https://en.wikipedia.org/wiki/UDP_flood_attack).

### Speed

It might seem there is no reason to use UDP, given that TCP is reliable and UDP is not. However, there is a trade-off between reliability and speed. TCP is slower than UDP because it requires a connection to be established and acknowledgment of data, among other processes. UDP, on the other hand, is faster as it does not require these processes.

However, in scenarios like a network with hundreds of UDP applications running simultaneously and sending data, network congestion can occur, leading to data loss or out-of-order delivery. This phenomenon is known as [congestion collapse](https://en.wikipedia.org/wiki/Congestion_collapse). In such cases, TCP can be faster than UDP because it can retransmit data until it is delivered correctly and also has mechanisms like [congestion control](https://en.wikipedia.org/wiki/Transmission_Control_Protocol#Congestion_control).

### Overhead, Size

TCP has more overhead than UDP, especially in the size of the header, due to its mechanisms for connection establishment, reliability, and congestion control. For example, the size of the TCP header is 20-60 bytes, while the size of the UDP header is only 8 bytes.

### Usage

TCP is used for applications that require reliable and ordered delivery of data, such as web browsing, email, and file transfer. UDP is used for applications that require fast and efficient delivery of data, tolerating some data loss, such as online gaming, video streaming, and Voice over IP (VoIP).

### Conclusion

In conclusion, TCP is reliable, connection-oriented, slower, and has more overhead but guarantees that the data will be delivered to the receiver with a lot of mechanisms like congestion control and retransmission. UDP, on the other hand, is connectionless, faster, and has less overhead but does not guarantee that data will be delivered.

So, when you need to choose between TCP and UDP, there is no perfect answer; as always, it depends on the requirements of your application, network conditions, and other factors. But now you know the key differences between them, and you can make a more informed decision. And remember, there is also a [hybrid approach](https://en.wikipedia.org/wiki/Reliable_User_Datagram_Protocol) or protocols like [DNS](https://en.wikipedia.org/wiki/Domain_Name_System) that use both TCP and UDP.

## Additional

### TCP Segment Header Format

<table>
<tbody>
    <tr>
        <th colspan="2"><i>Offsets</i></th>
        <th colspan="8" style="border-left:1px; text-align:left;">0</th>
        <th colspan="8" style="border-left:1px; text-align:left;">1</th>
        <th colspan="8" style="border-left:1px; text-align:left;">2</th>
        <th colspan="8" style="border-left:1px; text-align:left;">3</th>
    </tr>
    <tr>
        <th>Octet</th>
        <th>Bit</th>
        <th style="text-align:left;">0</th>
        <th>1</th>
        <th>2</th>
        <th>3</th>
        <th>4</th>
        <th>5</th>
        <th>6</th>
        <th>7</th>
        <th>0</th>
        <th>1</th>
        <th style="text-align:left;">2</th>
        <th>3</th>
        <th>4</th>
        <th>5</th>
        <th>6</th>
        <th>7</th>
        <th>0</th>
        <th>1</th>
        <th>2</th>
        <th>3</th>
        <th style="text-align:left;">4</th>
        <th>5</th>
        <th>6</th>
        <th>7</th>
        <th>0</th>
        <th>1</th>
        <th>2</th>
        <th>3</th>
        <th>4</th>
        <th>5</th>
        <th style="text-align:left;">6</th>
        <th>7</th>
    </tr>
    <tr>
        <th>0</th>
        <th>0</th>
        <td colspan="16">Source port</td>
        <td colspan="16">Destination port</td>
    </tr>
    <tr>
        <th>4</th>
        <th>32</th>
        <td colspan="32">Sequence number</td>
    </tr>
    <tr>
        <th>8</th>
        <th>64</th>
        <td colspan="32">Acknowledgment number (if ACK set)</td>
    </tr>
    <tr>
        <th>12</th>
        <th>96</th>
        <td colspan="4">Data offset</td>
        <td colspan="4">Reserved<br><b>0 0 0 0</b></td>
        <td>CWR</td>
        <td>ECE</td>
        <td>URG</td>
        <td>ACK</td>
        <td>PSH</td>
        <td>RST</td>
        <td>SYN</td>
        <td>FIN</td>
        <td colspan="16">Window Size</td>
    </tr>
    <tr>
        <th>16</th>
        <th>128</th>
        <td colspan="16">Checksum</td>
        <td colspan="16">Urgent pointer (if URG set)</td>
    </tr>
    <tr>
        <th>20</th>
        <th>160</th>
        <td colspan="32" rowspan="3">Options (if <i>data offset</i> &gt; 5. Padded at the end with "0" bits if necessary.)</td>
    </tr>
    <tr>
        <th>⋮</th>
        <th>⋮</th>
    </tr>
    <tr>
        <th>56</th>
        <th>448</th>
    </tr>
</tbody>
</table>


### UDP Datagram Header Format

<table style="margin: 0 auto; text-align: center;">
<caption>UDP datagram header</caption>
<tbody>
    <tr>
        <th style="border-bottom:none; border-right:none;"><i>Offsets</i></th>
        <th style="border-left:none;">Octet</th>
        <th colspan="8">0</th>
        <th colspan="8">1</th>
        <th colspan="8">2</th>
        <th colspan="8">3</th>
    </tr>
    <tr>
        <th style="border-top: none">Octet</th>
        <th>Bit</th>
        <th>0</th>
        <th>1</th>
        <th>2</th>
        <th>3</th>
        <th>4</th>
        <th>5</th>
        <th>6</th>
        <th>7</th>
        <th>8</th>
        <th>9</th>
        <th>10</th>
        <th>11</th>
        <th>12</th>
        <th>13</th>
        <th>14</th>
        <th>15</th>
        <th>16</th>
        <th>17</th>
        <th>18</th>
        <th>19</th>
        <th>20</th>
        <th>21</th>
        <th>22</th>
        <th>23</th>
        <th>24</th>
        <th>25</th>
        <th>26</th>
        <th>27</th>
        <th>28</th>
        <th>29</th>
        <th>30</th>
        <th>31</th>
    </tr>
    <tr>
        <th>0</th>
        <th>0</th>
        <td colspan="16">Source port</td>
        <td colspan="16">Destination port</td>
    </tr>
    <tr>
        <th>4</th>
        <th>32</th>
        <td colspan="16">Length</td>
        <td colspan="16">Checksum</td>
    </tr>
</tbody>
</table>













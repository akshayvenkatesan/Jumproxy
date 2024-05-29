To implement this code, follow the below steps:
1. Create a password txt file to pass as one of the parameters. 
2. Build the code using : go build - o jumproxy jumproxy.go
3. Run the server on reverse proxy mode using the sample command : go run jumproxy.go -k pwdfile.txt -l 2222 localhost 22
4. Run the client using the command (can either use same VM instance and another terminal or another VM instance too): ssh -o "ProxyCommand go run jumproxy.go -k pwdfile.txt 192.168.100.5 2222" kali@localhost
5. Enter the password of the user@connection in the client to access data using the Jumproxy tunnel layer. 
6. Entered commands in the input stream of client are encrypted and sent to server, which then decrypts these commands. (initiateServer, initiateClient and manageConnection functions are used for creation and management of server and client connections)
7. The response from server is then encrypted and decrypted at the client side. (using functions secureData and decodeData)
8. Encryption and decryption happens using the symmetric key, which is generated using the passphrase provided in the password txt file and harcoded salt value(generated using the function deriveKey)
9. For detailed log references, refer to JumproxyLog.log file (which is created in the same directory)

Sample input/output on server: 

go run jumproxy.go -k pwdfile.txt -l 2222 localhost 22
Server is listening on port  2222
Connection established with client:  [::1]:40602


Sample input/output on client:

ssh -o "ProxyCommand go run jumproxy.go -k pwdfile.txt localhost 2222" kali@localhost
kali@localhost's password: 
Linux kali 6.6.9-amd64 #1 SMP PREEMPT_DYNAMIC Kali 6.6.9-1kali1 (2024-01-08) x86_64

The programs included with the Kali GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Kali GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Sun Apr 14 15:23:25 2024 from ::1
┌──(kali㉿kali)-[~]
└─$ cd Downloads
                                                                                                   
┌──(kali㉿kali)-[~/Downloads]
└─$ cat hw2.txt 
CSE508: Network Security, Spring 2024

Homework 2: Network Monitoring with Scapy
-------------------------------------------------------------------------------

Submission deadline: 3/8/2024 11:59pm EDT
Submission site: https://mycourses.stonybrook.edu/d2l/home/1135717


In this assignment you will get familiar with the Scapy network traffic
capture/analysis/generation framework, and use it to develop two simple
network monitoring tools. As a minimum, your tools should work on Linux - you
are welcome to implement and test support for other platforms (Python makes
this easy). Both tools will be tested on the 64-bit Kali Linux 2023.4 virtual
machine, so make sure your code works in this environment:
https://www.kali.org/get-kali/#kali-virtual-machines


1) HTTP/TLS connection monitoring

Your program should conform to the following specification:

mysniffer.py [-i interface] [-r tracefile] expression

-i  Live capture from the network device <interface> (e.g., eth0). If not
    specified, the program should automatically select a default interface to
    listen on. Capture should continue indefinitely until the user terminates
    the program.

-r  Read packets from <tracefile> (tcpdump format). Useful for analyzing
    network traces that have been captured previously.

The optional <expression> argument is a BPF filter that specifies a subset of
the traffic to be monitored (similar to tcpdump).

Your tool should perform the following operations:

1) For HTTP traffic, it should parse GET and POST requests and print the
method used (GET or POST), the destination host name contained in the "Host:"
HTTP header (e.g., "www.cs.stonybrook.edu"), and the Request URI.

2) For TLS traffic, it should parse the Client Hello message and print the TLS
version number, and the destination host name (e.g., "www.cs.stonybrook.edu")
that is present in the Server Name Indication field.

For both HTTP and TLS, it should also print a timestamp and the source and
destination IP addresses and ports.

Example output:

2020-02-04 13:14:33.224487 HTTP 192.168.190.128:57234 -> 23.185.0.4:80 www.cs.stonybrook.edu GET /research/area/Security-and-Privacy
2020-02-04 13:14:24.494045 TLS v1.3 192.168.190.128:59330 -> 104.244.42.193:443 google.com

Your tool should be able to recognize HTTP and TLS traffic *irrespectively* of
the destination port number used. It is possible that "hidden" HTTP/TLS
servers may be listening to ports other than 80/443, and your tool will be
useful for detecting them.



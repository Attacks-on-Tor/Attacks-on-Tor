---
abstract: |
    **Tor is the largest anonymous communication network. Recent papers
    discuss the vulnerabilities of Tor’s Onion Router design and question
    the effectiveness of Tor. These vulnerabilities are increasingly
    exploited by de-anonymizing attacks. Over the years the attacks have
    grown to be more complex and effective, increasing the need for hybrid
    attacks that can be deployed at the network layer, protocol layer or
    application layer. We will discuss published attacks on Tor and
    categorize them for further analysis. Tor’s principles of freedom and
    privacy have also introduced some ethical vulnerabilities. The cover
    that the network provides attracts criminal behavior and has led to a
    bad reputation. This has caused lawyer-based attacks and adjustments on
    the fourth amendment to be a point of discussion. Additionally, Tor
    deals with financial insecurities and a dependency on volunteers. To
    ensure the continuity of Tor, a dynamic ecosystem should be built around
    the network by stimulating further development and research in anonymous
    communication services.**

    **KEYWORDS:** Tor, onion routing, de-anonymization, attacks,
    vulnerabilities, lawyer-based attack, fourth amendment
author:
- |
    B. Evers, J. Hols, E. Kula, J. Schouten, M. den Toom, R.M. van der
    Laan,\
    J.A. Pouwelse (course supervisor)\
    Computer Science, Delft University of Technology, The Netherlands
bibliography:
- 'bibliography.bib'
title: '**Thirteen Years of Tor Attacks**'
---

Introduction
============

We live in an information age in which any person with an Internet
connection has all the information in the world at their fingertips.
While the Internet has extended the possibility to share information, it
has also led to many users worrying that their own private information,
including their browse activity, may be snooped without their permission
and knowledge. With these rising concerns about privacy and security,
Internet users seek ways to anonymize their network traffic. To provide
an extensive anonymous communication service, researchers developed the
onion routing based system Tor @toruses. It is the largest anonymous
communication network in existence, with more than 7000 distinct server
nodes around the world @tordesigntunnel. It provides anonymous
communication services for hundreds of thousands of Internet users and
carries terabytes of traffic each day @locatingservers.

Tor was originally developed for the U.S. Navy to protect government
communications @tornavy. Nowadays it is an open-source project used for
a large variety of purposes by the military, journalists, law
enforcement agencies, activists, and many others @toruses. The anonymity
of the Tor network is appealing to anyone who wants to protect their
communications from others, search sensitive topics, avoid surveillance,
circumvent censorship and protect their privacy from identity thieves
@toruses. It has become a tool to keep privacy and freedom of expression
alive in the Information Age @navigatetor. Despite the Tor Project’s
good intentions, it has developed a bad reputation. Just as any large,
growing city attracts criminals, the growth of Tor and the anonymity it
provides has made the network a hideaway for illegal activities called
the Dark Web @mccoy2008shining. A well-known example of a hidden service
is Silk Road, a site for selling drugs which was shut down by the FBI in
2013 @navigatetor. The administrator Ross Ulbricht was arrested under
the charges of being the site’s pseudonymous founder “Dread Pirate
Roberts” and he was sentenced to life in prison @silkroad2 @silkroad3.

The dark side of Tor has drawn the attention from government
organizations like the NSA and FBI, that consider Tor a target of
particular interest @navigatetor. NSA documents that were leaked by
former NSA contractor Edward Snowden have revealed that the organization
monitors inexperienced people using Tor, who may not be aware of
Internet security and through whom the NSA can gain footholds in the Tor
network @navigatetor. Now government organizations are even looking for
a way to adjust the legal checks of the Fourth Amendment in order to be
able to legally hack users connected to Tor @fourthamendment1.

At the same time, the growing popularity of Tor has lead to the
development of an increasing number of de-anonymizing attacks on the
network. These attacks become increasingly more advanced and effective
@monitoringtor. Among the most notable attacks is the Sybil attack,
which is based on the idea that any system that relies on distributed
trust entities can impersonate multiple identities @monitoringtor
@margolin2008quantifying. This involved adding about 115 subverted
computer servers to Tor and ensuring they became used as entry guard
@relayearly. The servers took over more than 6% of the network’s guard
capacity @bbcsybil. This attack caused a big stir in the Tor network
since the information obtained by the adversary was enough to link some
users to specific hidden sites @relayearly.

These recent developments raise questions about the anonymity and
security of Tor. The fact that Tor is not 100 percent anonymous is no
shocker, but it might be far less secure than most people believe. We
will analyze the technical, ethical and financial vulnerabilities of the
deployed Tor network. In the first part of this survey we will have a
look at Tor’s network design and communication protocols. Then we will
discuss the attacks on Tor that are currently known and make an effort
to categorize them for further analysis. This will be concluded with a
section on the way Tor can detect attacks more quickly and how it should
protect itself against adversaries in the future. In the second part of
this survey we will have a look at the ethical concerns surrounding Tor.
We will focus on the ethical issues around the misuse of Tor for a wide
range of criminal use and illegal content. In the last section we will
discuss the financial insecurities of Tor and the dependence of the
network’s continuity on financial and non-financial volunteers.

Section 2 presents a high-level overview of the Tor network design and
highlights a number of vulnerabilities that are embedded into Tor’s
protocol. Section 3 analyzes these weaknesses and presents them in a
threat model. Section 4 gives an overview of Tor attacks that are
published, where each attack is categorized based on assumed goal.
Section 5 summarizes how attacks can be detected quicker and how Tor can
protect itself in the future. Section 6 summarizes the countermeasures
that can be taken to prevent a number of attacks. Section 7 introduces a
number of ethical vulnerabilities in Tor. We discuss the implications of
the Dark Web on Tor. Section 8 analyzes the financial vulnerabilities
and continuous resource starvation of Tor.

The Onion Router
================

The Tor network is based on a low-latency onion-routing design, where
traffic is forwarded through randomly selected , wrapping data in
multiple layers of encryption (onion skins) to maintain unlinkability
@locatingservers. An is also called a *relay*, *node* or simply a router
in this context. Each stream can be anonymously channeled through the
network in a *telescoping fashion*, meaning that each router only knows
the previous and the next relay in the path @monitoringtor. Only the
first relay, the *entry node*, knows the source of the stream. The last
relay, the *exit node*, is the only relay that knows the destination of
the client. The onion router(s) in between only exchange encrypted
information @salo2010recent. Data is wrapped in layers using symmetric
cryptography and a relay unwraps one layer of encryption and forwards
the message to the next relay in the circuit @reed1998anonymous. A
circuit usually consists of three relays.

\[img:onion\] ![Overview of Tor’s Onion Routing Design
@salo2010recent](Images/overviewtor "fig:"){width="50.00000%"}

Tor Protocol
------------

A list of trusted and available is advertised on central servers, which
are called . Furthermore, all relays maintain a connection to every
other relay @dingledine2008tor.

A user that wants to connect to the Tor network can use a Tor Bundle.
This package contains all necessary components to access the Tor
network. A client can connect to the Tor network using an @onionrouter,
which uses the SOCKS protocol @leech1996socks to tunnel the client’s TCP
connections through the Tor network. The streams of the client are sent
over the Tor network through *circuits*. Whenever a client wants to
create a circuit they can choose a list of and incrementally build a
circuit along all those relays. The first relay in the list, the entry
node, is contacted and a session key is negotiated. The second relay in
the list is contacted via the first relay and the client and the second
relay negotiate a session key. This process is repeated until the last
relay in the list is reached; the exit node. Session keys are negotiated
using a Diffie-Hellman handshake @onionrouter.

Sending messages to a server via the Tor network is done through the
circuit @onionrouter. The client encrypts the message with the session
keys of all relays in the circuit beginning with the last relay and
working up to the first relay. The encrypted packets, called *relay
cells*, are then sent over the circuit. Each relay along the circuit is
able to de-encrypt, or *peel* off, the outermost layer of encryption. At
the exit node, the encryption for each relay is peeled off and the raw
message is sent to the server. Sending a message backwards works the
other way around. Each relay on the circuit encrypts the message it
receives with the session key that is negotiated with the client and the
client is able to peel off all encryption layers.

Since all relays are listed in the directory servers, access to Tor can
easily be blocked by blocking the IP-addresses of all relays. To give
access to the Tor network even if all relays are blocked, *bridges* are
introduced @mclachlan2009risks. A bridge is an that is not listed in the
directory servers. A Bridge Authority lists all bridges. The Bridge
Authority limits access to the bridges’ information to prevent the
bridges from being blocked.

![Circuit creation @wiangsripanawan2007design<span
data-label="img:onion2"></span>](Images/proxytor){width="50.00000%"}

![A normal setup of hidden service communication in Tor
@locatingservers<span
data-label="img:hiddenservice"></span>](Images/locateserver){width="50.00000%"}

Hidden Service Protocol
-----------------------

A is a network service for which the location of its servers are hidden
by the Tor network. In order to connect to a , two relays are selected
to perform a special task @overlier2007improving. The ** is a relay that
is tracked by the hidden server for connections to the . The ** is a
relay that is known to the as well as to the client. The details of the
basic architecture and the entities can be found in the original paper
@onionrouter as well as on the Tor website @onionrouter2.

A normal arrangement of communication when a client wants to access a
resource offered by a hidden server is shown in Figure
\[img:hiddenservice\].

First the Hidden Server connects (1) to a relay in the Tor network and
asks whether it wants to act as an for his service @locatingservers. If
the relay allows this, the circuit is kept open. Otherwise, the tries
another relay until it found an . The connections are kept open, until
one of the nodes restarts or decides to take it down @syverson2. There
can be multiple s per service @locatingservers.

Then the Hidden Server contacts (2) the Hidden Servers Directory Servers
and requests it to publish the contact information of its , including
its s @locatingservers. The is now available to receive connection
requests from clients.

In order to use a the client contacts (3) a Hidden Servers Directory
Server requesting the address of an of the , which acts a mediator for
initial setup @torclockskew.

Then the client selects a relay in the network as a , connects (4) to it
and asks it to listen for connections from a @locatingservers. The
clients retries this until a has accepted and then contacts (5) the to
request for information about the selected @toreternity.

The sends (6) the request to the which determines whether to connect to
the or not @locatingservers. If everything is okay, the connects (7) to
the and requests to be connected to the rendezvous circuit
@locatingservers. The then advances (8) this connection request to the
client. Now the can start handing over (9) data between the client and
the @torclockskew. The result is an anonymous data link from the client
to the Hidden Server through the . All message-flows between these nodes
are routed through at least two or more anonymizing relays on their path
towards their destination @torclockskew.

From the description of the communication between a client and we can
make the following observations @locatingservers:

-   The client does not know the location of the Hidden Server, but
    knows the location of the .

-   The does not know the location of the client, but knows the location
    of the .

-   The does not know the location of the client and the , and also does
    not know the content of the service he is offering and the messages
    transmitted through him.

-   There are at least two or more anonymizing nodes between the and the
    and between the client and the .

-   Any node of the network which claims to offer stability can be used
    by the to form an anonymous link to the .

Threat Model
============

Most attacks on Tor focus on identifying a relationship between a client
and a server that are using the Tor network to communicate
@dingledine2004tor. This process is known as de-anonymization
@sun2015raptor. The client has created a circuit in the Tor network to
an exit node and the exit node communicates with the server. The
attacker wants to confirm that the client and the server are
communicating and wants to link a pseudonym (under which a hidden
service is being offered) to the operator’s real identity, either
directly or through some intermediate step (e.g. a physical location or
IP address) @pries2008new @torclockskew.

The most commonly assumed threat is based on a passive adversary that
can observe part of the Tor network and is able to compromise and
operate his own onion routers @salo2010recent@torclockskew. Such an
attacker simply observes inputs and outputs of the network and
correlates their patterns, so called *traffic analysis* @torclockskew.
The attacker tries to measure similarities in the traffic that the
client sends and the traffic that the server receives @pries2008new.
Traffic analysis is commonly used in attacks on hidden services that try
to de-anonymize users @barker2011using @sun2015raptor @pries2008new. Tor
does not protect against a global passive adversary. Its focus is to
prevent attacks where an attacker tries to determine in which points in
the network a traffic pattern based attack should be executed. By making
it difficult for an attacker to determine where to attack, a precision
attack is difficult @salo2010recent.

An active adversary is also a common assumption in Tor’s threat model
@dingledine2004tor. Such an attacker guesses who is communicating with
whom and can analyze individual network links in order to validate this
suspicion @torclockskew. They have the ability to inject, delete or
modify traffic that is propagated through (compromised) ORs
@monitoringtor. Since active adversaries are more easily detected, there
have been a number of research efforts to develop various
countermeasures to defend against these threats. We will discuss these
countermeasures and their effectiveness in sections 4 and 6.

In systems like Tor, which is run by volunteers under limited control,
it is also a valid concern that an attacker controls a part of the
anonymity network @torclockskew. However, it is unrealistic that such a
person controls all of the nodes @monitoringtor. Therefore this type of
attacks is not in the focus of Tor’s threat model @salo2010recent. The
Tor developers are careful, but they still warn their users against
using Tor in crucial situations through an announcement upon startup of
the Tor client: “This is experimental software. Do not rely on it for
strong anonymity.” @locatingservers

Categories of De-Anonymizing Techniques and Attacks
---------------------------------------------------

According to existing de-anonymizing techniques on the Tor network, we
can sort these techniques into two groups from two different
perspectives @yang2015anonymizing:

-   **Passive and active attacks** The adversary can passively observe
    the network’s traffic or actively manipulate traffic.

-   **Single-end and end-to-end attacks** The attacker can impose the
    network’s anonymity by monitoring or controlling Tor circuits at
    either the enter relay or exit relay side, or at both edges of
    the circuit.

Based on their method and goal, attacks can be categorized into seven
groups:

-   **Correlation Attacks** End-to-end Passive Attack

-   **Congestion Attacks** End-to-end Active Attack

-   **Timing Attacks** End-to-end Active Attack

-   **Fingerprinting Attacks** Single-end Passive Attack
    @defabbia2011analyzing

-   **Denial of Service Attacks** Single-end Active Attack

-   **Supportive Attacks** Not classified

-   **Revealing Hidden Services Attacks** Not classified

In this case, the label ’not classified’ means that the attacks that
belong to the corresponding category often combine both types of
techniques.

In the following, we give you an overview of attacks on Tor that have
been published and discuss them. In Table \[tab:torattacks\] all attacks
are listed in chronological order. Figure \[fig:M1\] shows a mind map
that contains the attacks related to their category.

Attacks on Tor
==============

Extensive research is done into the vulnerabilities of Tor. In this
section we discuss a number of attacks on Tor that have been published.
There is a lot of interest in attacks on Tor. For example, there are
rumors that the FBI paid the Carnegie Mellon University (CMU) to develop
an attack against the Tor network @CMU. Payment from the FBI to the CMU
has, however, been denied by the CMU @CMU1. The attack developed by the
CMU was the relay early traffic confirmation attack @CMU. This attack
will be explained first in this section, followed by other recent or
important attacks.

Correlation Attacks
-------------------

Correlation attacks are well-known de-anonymization attacks. In this
category of attacks it is assumed that the attacker controls both the
entry node and the exit node of the circuit between the client and the
server. The attacker is looking for a correlation in traffic between the
entry node and the exit node, because then he can conclude that the
entry node and the exit node participate in the circuit. The entry node
knows the client, the exit node knows the server, so the attacker can
confirm that the client and the server are communicating.

#### Relay Early Traffic Confirmation Attack

The relay early traffic confirmation attack aims to de-anonymize Tor
clients that are using a hidden service @relayearly. It is known that
this attack was actually performed on the real Tor network. Measures
against the attackers have been taken.

This attack is a combination of a correlation attack and the Sybil
attack. The Sybil attack is explained in Section \[sub:sybil\]. The
Sybil attack was used by the malicious s to become an entry guard and a
hidden service directory. Then a correlation attack is executed to
confirm the relation between a client and a hidden service.

For this attack to succeed, the attacker needs control over a hidden
service directory relay and the entry node of the client. If the client
wants to connect to a hidden service it requests its introduction points
at the hidden service directory. The directory relay then sends the name
of the hidden service over the circuit encoded in a pattern of relay and
relay-early cells. Relay early cells are used to prevent that a client
builds long circuits, which can be used in congestion attacks. The entry
node can decode the name of the hidden service from the traffic pattern
and associate the hidden service with the client.

#### Replay Attack

Another attack that performs an intervention in the communications is
the Replay Attack. This attack is described in @pries2008new published
in 2008. Assumed is that the length of the circuit from the client to
the server is 3.

The attacker selects a cell at the entry router and duplicates this
cell. The duplicated cell is also sent to the second node in the
circuit. To ensure that the circuit has been created, the duplicated
cell should be chosen after the circuit has been established. The
selected cell should therefore be a relay cell. The attacker can detect
relay cells at the exit node, which he also controls, and notifies the
entry node.

Each Tor layer is encrypted with in counter mode. The duplicated cell
causes the encryption and decryption counters to go out of sync,
resulting in decryption errors. The adversary can detect these
decryption errors at the exit node. To confirm that the error is caused
by the duplicated cell, the attacker should check that the time for the
errors to be detected after the duplicated cell has been sent, is about
the same as the time it takes a cell to propagate trough the network.
When the exit node detects the error and the timing is correct, the
communication between the client and the server is confirmed.

#### Cell Counter Based Attack

A paper by Ling et al. @ling2012cellcounter published in 2012 describes
an attack in which manipulating the timing of sending relay cells and
the cell counter of an enter or exit relay allows the attacker to embed
a signal in the traffic of a client or server. This signal can then be
recognized by the relay on the other end of the circuit to confirm that
a client communicates with a server.

Traffic is sent via cells, which are stored temporarily in a queue, then
flushed to the output buffer before entering the network @circuitqueues.
A signal can be embedded in the traffic by manipulating the cell counter
of the output buffer (the amount of cells flushed from the queue to the
buffer). For example, three cells means “1” and one cell means “0”. The
timing between sending each ’symbol’ should be carefully chosen, since
waiting too short will cause cells to be combined by other relays in the
circuit and waiting too long may look suspicious and will increase the
latency which may cause the user to create a new circuit.

Unfortunately, the cell pattern might also be injected at the middle
OR(s) due to the natural congestion or delay of the network. Therefore,
the amount of cells per symbol should be chosen in such a way that
combined cells can still be recognized as symbols at the receiving
relay. An advanced recovery mechanism was developed to recover these
distorted signals by analyzing the types of combinations and divisions
of cells.

![Workflow of Cell Counter Based Attack @somase2014analysis<span
data-label="img:cellcounter"></span>](Images/cellcounter){width="50.00000%"}

This attack is very difficult to detect since the signal can be very
short and can have many different properties, which makes it difficult
to distinguish from normal traffic. The timing between two symbols can
be controlled by a pseudo noise code only known the the attackers. It
also has a detection rate close to 100% and can confirm over half of the
communication sessions by injecting around 10% malicious onion routes on
Tor.

#### Correlation-based Traffic-analysis Attack

In 2010, Zhu et al. @zhu2010correlation introduced an attack that aims
to match an input to a mix in a mix network to an output link of that
mix. Zhu et al. focused on mix networks that use explicit batching
(i.e., an explicit batching algorithm) in their nodes. Tor does not do
explicit batching but rather relies on TCP-style feedback-based
protocols for perturbing traffic patterns. Although Zhu et al. have not
focused on batching methods like the one Tor uses, they do not rule out
that the attack could work on systems using such batching methods, so
the attack (or a variation thereof) is still relevant.

The aim of the attack will now be stated more precisely. The attack
tries to match a known input stream of a mix to one of the output links
of the mix. The input stream is assumed to not be further divisible. The
challenge lies in finding the input stream in one of the output links
that possibly carry multiple streams.

Zhu et al. note that although the attack is described for one mix in
their article, the attack could be extended to work on a network of
mixes (i.e., a mix network). They describe this in @zhu2005anonymity.
They also note that if we view a mix network (which could be (a part of)
the Tor network) as one super mix, the techniques in their article can
be directly applied to the super mix. One could imagine that matching an
input stream of the Tor network to an output link of the Tor network
could lead to the identification of the communicating parties.

For the attack to be executed, the adversary requires a means to
intercept packets flowing through the output links. Malicious ISPs or
governments may have the capabilities to intercept packets when they
control (part of) a network. Corrupt mixes may also be used, for
example.

The attack works as follows. First, the adversary records the packet
interarrival times on all output links of the targeted mix.

Second, the interarrival times are transferred into so called pattern
vectors that effectively contain the number of packets per batching
interval as their elements. This is also done for the known input
stream. The transformation process that is used depends on the batching
strategy that is used. For example, if packets are sent per specified
time-out interval, the number of packets per unit of time for each
time-out interval are the elements of the vector.

Third, the distance between the input stream and all the output links is
calculated using the pattern vectors. Zhu et al. propose two measures
for doing this. The first one is mutual information, which Zhu et al.
proposed in @zhu2005anonymity2. The second one is frequency analysis.

Fourth and last, the output link which has the minimum distance to the
known input stream is selected as the output link that corresponds to
the known input stream.

One last thing worth noting about this attack is that the amount of
available data (i.e., the amount of recorded packet interarrival times)
is important. Large amounts of data can lead to detection rates near 100
percent. This is still true when a lot of cross traffic is present.

##### Related work

In 2007, Wang et al. @wang2007network described an attack in which
packet streams were also compared based on the inter-arrival times of
packets. As opposed to the attack by Zhu et al., they use an “Interval
Centroid Based Watermarking Scheme” to influence the inter-arrival times
of packets themselves, instead of only recording the inter-arrival
times. In this sense, the attack by Wang et al. could be classified as
active.

#### Low-Resource Routing Attack

Back in 2007 in a paper by Bauer et al. @bauer2007low an attack is
described that has the purpose to compromise the users identity by
correlating client request to server responses through Tor. The aim is
to let clients construct a Tor circuit containing malicious entry and
exit nodes. To achieve this the setup procedure of the attack is to
enroll or compromise a number of high-bandwidth, high-uptime Tor routers
that have a high likelihood of being selected by a client. The paper
describes that the resources needed to execute this attack can be
significantly reduced by exploiting the fact that a malicious node can
report incorrect uptime and bandwidth advertisements to the trusted
directory servers as these advertisements are not verified. If only a
single malicious node is part of the circuit, it can disrupt the path
resulting in the client constructing a new circuit thus increasing the
chance to select 2 malicious nodes.

The malicious routers log enough information to correlate client request
to server responses. They implemented a circuit linking algorithm that
recognizes a circuit request from a Tor proxy. This is were this attack
differs from others as it is able to compromise anonymity of a Tor Route
before the client starts to transmit any payload data. The researcher
validated the attack using an experiment on a realistic test environment
network. From the results it is estimated that by contributing less than
1% of the network’s aggregate bandwidth they are able to compromise up
to 46% of the circuit-building requests for new Tor proxies.

In the paper it is mentioned that the attack can be extended to existing
clients. If an attacker can observe a client and make the entry guards
unreachable this will result in the selection of new entry guard list
with the possibility of selecting a malicious router. An attacker can
also perform an denial-of-service attack on a few key stable entry
guards, resulting in a large number of clients having to replace the
unusable entry guard with a potential malicious one. As defense to this
attack it is proposed to verify resource claims such as uptime and
bandwidth. To mitigate Sybil attacks they propose limiting the number of
routers on a single IP address (which Tor adopted by limiting this to
3). At last alternate routing strategies are proposed to provide
adequate load balancing while preserving the networks anonymity.

#### HTTP-based Application-level Attack

In the paper by Wang et al. @wang2011potential they present a HTTP-based
application level attack against Tor to identify Tor clients. The attack
is not specific to web browsing on Tor but rather to the problem of low
latency applications based on TCP streams. They assume the attacker can
control multiple routers, the entry and the exit router of the circuit.
This is possible since Tor is operated in a voluntary manner. They make
use of HTTP’s vulnerability to man-in-the-middle attacks.

By exaggerating about the resource claims of their routers they can make
it likely for a client to select their entry and exit router in a
circuit. If the client then issues a HTTP request they can apply their
forged web page attack or a targeted web page modification attack. The
idea is to let a client’s browser initiate malicious web connections to
generate a distinctive traffic pattern. This pattern can then be
detected by the entry router to expose the client’s identity. They also
mention that the requirement of a malicious entry router is not
necessary in the attack if an adversary can sniff the packets
transmitted via the link between the client and the entry router.

![Forged webpage injection attack @wang2011potential<span
data-label="img:httpbased"></span>](Images/httpbased){width="50.00000%"}

As countermeasures they mention minimizing the chance of choosing
malicious routers in a circuit. This can be done by increasing the total
number of Tor routers and by evolving the circuit construction algorithm
to select only fully trusted and dedicated routers through strict
authentication and authorization e.g. using a reputation system.
Additional countermeasures are abnormal traffic detection using web
browser plugins and the use of HTTPS to effectively defend against the
attack.

##### Related work

Before the article of Wang et al. @wang2011potential described in this
section, Wang et al. @wang2009novel published an article that is largely
the same, except for some rewording and minor additions in
@wang2011potential. Compared to @wang2009novel, the notable additions of
@wang2011potential are another variation of the attack, a more detailed
explanation of the experiments and their results and more elaboration
regarding the requirements of controlling ORs. They mention that
controlling the entry router of the circuit is not strictly necessary
for performing the attack.

It is worth noting and also strange that the article from Wang et al.
published in 2011 @wang2011potential does not reference the article from
Wang et al. published in 2009 @wang2009novel. In other words, a very
large part of the 2009 article @wang2009novel has been copied over to
the 2011 article @wang2011potential without reference.\
A paper released in 2015 by Arp et al. @arp2015torben describes a
similar attack to that of Wang et al. @wang2009novel. The biggest
difference is in the way of providing web content to users. Where Wang
et al. @wang2009novel provided content by controlling an exit router and
manipulating HTTP results, Arp et al. @arp2015torben mainly mentioned
providing the side channel content through banner advertisements or
cross-site scripting. This makes it unnecessary for the adversary to
control an exit router. Additionally Arp et al. @arp2015torben assumes
that an attacker is able to monitor the encrypted communication between
a Tor client and the entry node which is used to recognize the generated
network traffic pattern, while Wang et al. is in control of a entry node
to achieve this.

A paper released in 2007 by Abbot et al. @abbott2007browser also
describes a very similar attack against Tor. It exploits the same flaws
as Wang et al. did in 2011, such as a HTTP man-in-the-middle attack
using an malicious exit node to insert javascript code .\
The use of man in the middle attacks to the HTTP protocol is used quite
often and can be quite a powerful attack technique. A paper by Chaabane
et al. from 2010 @chaabane2010digging uses the flaw to rewrite HTTP
responses from BitTorrent tracker servers, to let BitTorrent clients
connect to their logging client. This way they could estimate the amount
of encrypted BitTorrent traffic on the Tor network.

#### Bad Apple Attack

The Bad Apple Attack introduced in @blond2011one is another
application-level attack. This attacks requires a malicious application
that is installed on the client’s computer, to retrieve the IP-address
of the user. This application should also use Tor to communicate. How
the IP-address of the client is retrieved is not Tor specific and
therefore will not be discussed in detail in this paper. The malicious
application can just send it’s IP-Address via the Tor network to a
malicious server, for example.

To correlate traffic from the malicious application with other traffic
the attacker should be able to observers the exit nodes of the client’s
circuits. Since Tor combines multiple streams, possibly from different
applications, in one circuit the exit node can correlate traffic from
the malicious application with other network traffic. For example, if
the malicious exit node first observes traffic from the malicious
application, and later observes traffic from website *abc.com* on the
same circuit, the attacker can correlate the client with website
*abc.com*

#### Probabilistic Models

There have been recent papers on correlation attacks based on
probabilistic models. According to Troncoso @bayesian2009 these attacks
have an important advantage: they enable optimal use of all information
available about who is talking to whom. They provide an a-posterior
probability over all scenarios of interest, whereas most attacks without
a probabilistic component only provide the most probable solution. It
also means that these models are not concerned with traffic analysis
techniques, but effectively assume that the traffic analysis is done.
The adversary already has a correct distribution of a user’s behavior
and communication partners. Most probabilistic models are still research
based and have not been deployed to actually attack Tor.

#### The Bayesian Traffic Analysis of Mix Networks

A probabilistic attack on anonymity mix networks presented in
@bayesian2009 casts the traffic analysis in the context of Bayesian
inference. The model is based on an Markov Chain Carlo inference engine,
that calculates the probabilities of a router being connected to another
router given an observation of network traces. The analysis includes
conventional aspects of mix networks, e.g. node selection, and
complements incomplete observations, erratic users and social network
information @salo2010recent. In the end it comes down to calculating an
a-posterior distribution $Pr[HS | O, C]$ of a set of hidden state user
variables $HS$ given an observation $O$ and a set of constraints $C$
based on the user’s choice of mixes to relay messages and the user’s
behavior. However, it is computationally unfeasible to calculate this
distribution since the number of possible hidden states is very large.
Therefore sample sets $HS_0, ..., HS_n \sim Pr[HS | O, C]$ are used to
extract the characteristics of the user variables and to infer the
distributions that describe events of interest in the system. The
sampling methods estimate the probabilities
$Pr[i_x \rightarrow o_y | O, C]$ of an incoming message $i_x$
corresponding to any of the outgoing messages $o_y$ as follows:

$$Pr[i_x \rightarrow o_y | O, C] \approx \frac{\sum\limits_{j \in N_{MH}} I_{i_x \rightarrow o_y}(HS_j)}{N_{MH}},$$

where $I_{i_x \rightarrow o_x}$ is an indicator expressing if messages
$i_x$ and $o_y$ are linked to each other in the hidden state $HS_j$, and
$N_{MH}$ is the number of samples available to the attacker.

This approach enables the attacker to extract information from
anonymized traffic traces optimally if he tracks 50 messages of the user
he wants to de-anonymize. In all examples, approximately 95% of the
samples fall into the confidence interval. The results of the
experiments also show that when more messages travel through the
network, the attacker is less certain about their destination
@murdoch2007sampled. An attacker cannot link incoming $i_x$ and outgoing
$o_y$ messages with a probability higher than 0.4 when 100 messages have
been observed and with a probability higher than 0.1 if more messages
are detected.

Danezis @danezis2003statistical presents the **Statistical Disclosure
Attack**, an improvement over the original disclosure attack using
statistical methods to effectively de-anonymize users of a mix network.
The formal model of the disclosure attacks assumes a single mix used by
$b$ participants each round, one of them always being Alice, while the
$(b-1)$ others are chosen randomly out of a total number of $N-1$
possible ones. The attacker observes the recipient anonymity sets
$R_1, ..., R_t$ corresponding to $t$ messages sent out by Alice during
$t$ different rounds of mixing. The goal of the attack is to find the
set of potential recipients of Alice and in turn to find the recipients
of particular messages sent out by Alice. The model defines a vector
$\vec{v}^t$ in which the $m$ elements correspond to each potential
recipient of messages in the system. The value of each element is set to
$\frac{1}{m}$, meaning that $\vec{v}^t$ is the probability distribution
that Alice uses to select a recipient. Next to this $\vec{u}^t$ is
defined to be equal to the uniform distribution over all $N$ potential
recipients, meaning that $\vec{u}^t$ is the probability distribution
that is used by others to select their recipients.

The attacker observes a sequence of vectors
$\vec{o}_1^t, ..., \vec{o}_t^t$ expressing the recipient anonymity sets
observed in the $t$ messages sent by Alice. Each $\vec{o}_i^t$
represents the probability distribution assigning potential recipients
to Alice’s message during round $i$. Using this sequence, the batch size
$b$ of the mix and the model $\vec{u}^t$ of other senders the attacker
can infer $\vec{v}^t$ and can find an indication on the communication
partners of Alice as following:

$$\vec{v}^t = b \frac{\sum\limits_{i=1...t} \vec{o}_{i}^t}{t} - (b-1)\vec{u}^t$$

The statistical disclosure attack provides important improvements over
the original attack. The main bottleneck of the original disclosure
attack is its reliance on solving an NP-problem. The statistical
disclosure attack only relies on trivial operations on vectors and
therefore provides a computational improvement. Next to this, the
applicability and effectiveness of the statistical attack are also more
predictable because of its closed algebraic form. Another advancement is
its extension from being applicable to anonymity systems that create
discrete anonymity sets, to probabilistic systems that provide anonymity
based on the entropy of the anonymity sets.

##### Probabilistic Analysis of Onion Routing in a Black Box Model

A probabilistic model to evaluate how much an attacker can discover
about users by exploiting knowledge of their probabilistic behavior is
examined in @blackbox2009. The analysis is based on a black-box model of
anonymous communication so it could be adapted to anonymous
communication networks other than Tor. An active adversary that controls
a portion of the network is considered. The abstraction captures the
relevant properties of a protocol execution that the adversary can infer
from his observations. The model is based on two assumptions. First, a
user is responsible for one input and one output. Second, the attacker
is able to link network traffic to a user if the input and output are
both observable. The mathematical model indicates that user anonymity is
worst either when the user shows unique behavior by choosing a
destination node other users are unlikely to choose or when other users
always visit the user’s actual destination. Which case is worse depends
on how likely the user was to visit his destination in the first place.
This worst-case anonymity with an attacker that observes a fraction $b$
of the network is comparable to the best case anonymity against an
attacker that observes a fraction $\sqrt{b}$. In case of common behavior
and group joining decisions the anonymity can be kept as the best
possible. Feigenbaum expects future research in probabilistic models to
focus more on detailed design decisions, such as the impact of entry
guards on Tor’s anonymity.

#### Raptor Attack

The Raptor attack published in 2015 assumes a powerful adversary
@sun2015raptor. It is assumed that the attackers can use autonomous
systems (ASes). There is already evidence that intelligence agencies are
cooperating with ASes @schneider2013how.

The Raptor attack is a combination of three individual attacks and
exploits the Border Gateway Protocol (BGP) @rekhter1995border. First,
the Raptor attack uses asymmetric traffic analysis. This means that
client and server can be de-anonymized as long as the attacker can
observe incoming or outgoing traffic at both the client and the server.
Sequence numbers of data packets and/or sequence numbers of
acknowledgments can be correlated. This is possible because the TCP
headers of the packets are not encrypted at both ends of the client’s
circuit, and therefore are visible when intercepted by the malicious AS.
Asymmetric traffic analysis can be advantageous, because the incoming
and outgoing traffic between the client and the entry node or between
the exit node and the server might go through different ASes. With
asymmetric traffic analysis, only one direction of traffic at both ends
of the circuit is needed to correlate the client and the server.

Second, the Raptor attack exploits that BGP paths change due to for
example link or router failures. This means that communications between
the client and the entry node might go via different ASes over time.
Every change in the BGP paths might include a malicious AS into the path
between the client and the entry node, which can then perform asymmetric
traffic analysis. Asymmetric traffic analysis is only needed once to
correlate the client and the server. This means that the chance that the
client and the server have been correlated increases over time.

Third, the malicious AS can perform a BGP hijack or BGP interception
attack. In a BGP hijack, the malicious AS advertises an IP prefix that
does not belong to that AS, as its own. This results in some network
traffic intended for that prefix to be captured by the malicious AS. A
problem with BGP hijack is that the captured traffic is not forwarded.
In a BGP interception attack the malicious AS also advertises an IP
prefix that does not belong to that AS. The intercepted traffic is
analyzed and then forwarded to the actual destination. BGP Interception
might be useful to relate the client with the entry node, when the entry
node is known. Paragraph \[sec:congestionattack\] describes an attack to
retrieve the entry node of a circuit. BGP Interception might then be
used with an IP prefix of the entry node to find all the IP addresses
that communicate with the entry node. Asymmetric traffic analysis can
then be used to find the client that is communicating via the circuit.

##### Related work

A realistic comprehensive analysis was done of the security of Tor
against traffic analysis by Johnson et al. @johnson2013users for a more
generalized attack. It focused on how to make Tor safer for its users,
and showed that there are greater risks than previous studies suggested.
It discusses how Tor’s security can be improved and how users themselves
can increase their security against this kind of attack.

Congestion Attacks
------------------

In a congestion attack an adversary tries to determine the identities of
the Onion Routers that make up a circuit constructed by a targeted Tor
client. To achieve this goal, the adversary congests relays one by one
and listens for latency differences in the traffic flow of the target.
An adversary could measure latency differences by congesting relays
while a client is downloading a large file from a by the adversary
controlled website, until the adversary detects the download slowing
down. Another method is to inject a script that will periodically
perform HTTP requests and start congesting relays until the request
frequency decreases.

#### Congestion Attack by Modulating Traffic {#sub:modulating}

Not long after the introduction of Tor, Murdoch et al. @murdoch2005low
introduced a traffic-analysis attack with the goal of uncovering the
onion routers (ORs) on a targeted circuit. The attack is not practical
at the time of writing @evans2009practical, because the Tor network has
become so large that the attack is not feasible anymore. The Tor network
consisted of only 13 ORs at the time of the article of Murdoch et al.
compared to 7121 ORs as of 27-03-2016 @tormetrics. The attack will still
be discussed here because it is well known.

As stated, the purpose of this attack is to reveal some or all of the
ORs that form the circuit established by a client to a corrupt server.
In order to achieve this, the adversary needs multiple computers under
his control: one corrupt server and one or more corrupt ORs. Hence, the
adversary only has a partial view of the Tor network. Note that this
attack does not aim to uncover the identity of the client, but only the
identities of the ORs that make up the circuit.

To execute the attack, the adversary first needs to get the client to
connect to the corrupt server. Murdoch et al. do not specify how they
would achieve this. One possibility would be intercepting unencrypted
HTTP traffic. When the client has connected to the corrupted server, the
server will send modulated traffic to the client (i.e., it will send the
traffic using a specific pattern).

Then the adversary’s corrupt ORs will come into play. Each of the ORs
will subsequently make connections through legitimate ORs in order to
check if they are on the path from the target client to the corrupt
server. This check consists of filling the connection through the legit
OR with probe traffic and recording the latency of the connection. If
the latency pattern that is being recorded matches the pattern that the
corrupt server sends to the client, the OR is probably part of the
circuit from the client to the corrupt server. If the pattern is not
detected there is a high chance that the OR is not in the circuit. Using
this technique can lead to the discovery of all ORs on the path from
client to server. One can imagine that using more corrupt ORs for
probing the legitimate ORs leads to faster results.

![This figure shows that the latency of the cells varies more during the
congestion attack, compared to the control measurements
@evans2009practical.<span
data-label="img:congestion"></span>](Images/congestion_latency_diagram){width="50.00000%"}

There is one additional use of this attack that is worth mentioning.
When the ORs on the circuits of two different streams have been
discovered and some of those ORs match between the two streams, one can
say with quite some confidence that the two circuits originate at the
same client. This is because the chance of picking the same ORs is
fairly low. The more ORs the streams have in common, the higher the
chance of the streams originating at the same client. It is interesting
to note that this variant of the attack give better results when a
larger pool of ORs in the Tor network is used or when the circuit
lengths are greater. The results improve in both situations because in
both situations the chance of picking the same ORs randomly when
building a circuit decreases.

##### Related work

This attack is used to discover the bridge a client is using in
@mclachlan2009risks.

#### A Practical Congestion Attack {#sec:congestionattack}

In 2009 a practical congestion attack on the Tor network was introduced
@evans2009practical. This attack improves the congestion attack
introduced in Paragraph \[sub:modulating\] published in @murdoch2005low,
which is no longer reliable because of the growth of the Tor network.

The aim of the attack is to confirm that a node, the entry node,
participates in the circuit from the client to the exit node. Assumed is
that the attacker controls the exit node. The first step of this attack
is to inject some JavaScript code into a HTML response at the exit node.
This JavaScript code causes the user’s browser to send an HTTP request
at regular intervals of 1 second. The HTTP requests contain the time the
request was send, so that the attacker at the exit node can correlate
the difference in arrival time of the requests, with the difference in
send time of the requests. When the attacker does not introduce
congestion, he can measure the difference in send time and arrival time
of the requests and calculate the average latency of the circuit.

The attacker introduces congestion by creating a circuit from a
malicious client to a malicious server. This malicious circuit is a long
circuit of length $m$ that repeatedly includes the assumed entry node on
its path. Because a relay should not extend a circuit to the previous
relay in that circuit, the attacker includes two high bandwidth relays
in the malicious circuit and then loops back to the assumed entry node.
24 hops would be effective, according to @evans2009practical, but as a
result of that paper Tor now limits the number of hops to 8. The
supposed entry node now has $\frac{m}{3}$ additional circuits to send
packets for. Since packets for different circuits are send in
round-robin fashion in Tor relays, this causes congestion at this relay.
Assuming that the high bandwidth relays are not a bottleneck and the
malicious server uses his full bandwidth $p$ to send packets over the
circuit, a long path would result in the assumed entry server having to
route a bandwidth of $\frac{m \cdot p}{3}$ for the malicious circuit.

The HTTP requests, done by the JavaScript injected at the exit node,
will experience delay due to the congestion, which can be measured at
the exit node. The exit node will find out that the latency of the
circuit varies more. By repeating the process of measuring the average
latency and then introduce congestion to measure whether the latency
varies more, several times, the attacker’s confidence that the entry
node participates in the circuit increases.

Timing Attack
-------------

Timing attacks are another form of de-anonymizing attacks. During a
timing attack an adversary manipulates both the entry and the exit relay
of a targeted client. By correlating flow patterns in traffic flowing
from the entry node to traffic flowing to the exit node, the adversary
can determine which server a client is communicating with
@feigenbaum2010preventing.

#### The Indirect Rate Reduction Attack

Gilad and Herzberg introduced this timing attack in 2012
@gilad2012spying. The attack uses the predictability of the exit nodes
that an OP chooses and the congestion control algorithm in the TCP
protocol @allman2009tcp to its advantage. Gilad and Herzberg note that
the attack has not been fully tested, but they did some initial
experiments which turned out to go well.

The attack aims to check which clients are communicating with a
predefined server through the Tor network. The adversary must choose the
clients that will be monitored beforehand, because the communication
with the entry node of their circuits has to be intercepted. Gilad and
Herzberg give example adversaries like governments or employers that
control the network their citizens, respectively, their employees are
using. Besides having the means to intercept the communication of the
targeted clients, the adversary needs a device for sending spoofed TCP
packets to the exit nodes of the circuits of the targeted clients.

To execute the attack, the adversary will use the congestion control
behavior of TCP @allman2009tcp. When a device receives three of the same
ACK packets, the congestion window of the device will scale down. How
much the window will scale down depends on the TCP implementation that
is used.

The adversary will also use the observation that which exit node an OP
chooses is quite predictable. Gilad and Herzberg found out in an
experiment that in 20 percent of the cases, one of the same 7 exit nodes
was chosen.

To use those two behaviors to his advantage, the adversary sends three
packets with wrong sequence numbers to all exit nodes that are likely to
connect to the server and to a lot of different ports. The IP addresses
of these packets are spoofed so that it appears they come from the
server. This will cause the exit node to send three ACK packets to the
server. The behavior described above will cause the server to scale down
its congestion window.

Note that three packets are send to all exit nodes that have a high
probability of having a connection to the server, to a lot of ports. In
other words, packets will be send to exit nodes that do not actually
have a connection to the server and to ports that do not have a
connection to the server. This is not very efficient. Gilad and Herzberg
propose using two other kinds of attacks described in their article,
along with this attack, to first identify the exit nodes that actually
have a connection to the server, along with the right port numbers.
These two additional attacks will not be discussed here.

The scaling down of the congestion window can be detected by the target
clients, because their connection to the entry nodes will be scaled down
too. Repeating these steps multiple times, the adversary can say with
high confidence which clients are communicating with the server.

It is important for the adversary to let the congestion window of the
server recover between iterations of the attack. To this end, the
adversary has to wait some time before sending the three fake packets to
the exit nodes again. Because enough time is needed to execute a
sufficient amount of iterations of the attack, the connections that are
targeted have to last long enough. According to Gilad and Herzberg,
several minutes should be sufficient. Also, the connections under attack
should be active, because the congestion window will not recover when no
data is being transmitted and data transmission is needed in order to
measure the rate reduction caused by scaling down the congestion window.

#### Bandwidth Estimation Attack

In 2010, Chakravarty et al. @chakravarty2010traffic introduced a
traffic-analysis attack that aims to uncover the identity of users of an
onion proxy (OP), the identity of hidden services, or the identity of
onion routers (ORs) by using bandwidth estimation techniques. The
novelty of the attack is that it does not require any intervention into
the Tor network itself. Previous timing attacks, as Chakravarty et al.
say, required intervention into the Tor network by using compromised ORs
or a global adversary.

It is worth noting that this attack only works well if the Tor network
does not influence the bandwidth of its users too much, because the
attack depends on tracing a bandwidth pattern. Chakravarty et al. expect
that the Tor network will not influence bandwidth much in the future.

The aim of the attack is to uncover the autonomous system (AS) which
contain the target and possibly also the precise identity of the target.
The target can be either an OP, an OR or a hidden service. The attack
will be described for the case of an OP, but the description for the
latter two possibilities is very similar.

The adversary needs to follow the following three steps for executing
the attack. First, a server is needed that is colluding with the
adversary. This server may be controlled by the adversary (which will
make the attack easier to execute), but this is not strictly necessary.
Second, several network bandwidth probing nodes are required. Third,
maps containing ingress and egress routers of ASes are needed. Ingress
traffic is traffic in the Tor network that originates outside of it.
Egress traffic originates inside the Tor network and is sent outside the
network. According to Chakravarty et al., such maps are constructed by
several projects and are easy to acquire for an adversary
@chakravarty2010traffic.

For discovering the precise location of an OP, internal AS maps are
needed. Chakravarty et al. propose using services like Rocketfuel
@rocketfuel from the University of Washington to acquire such more
low-level maps.

The following actions comprise the execution of the attack. First, the
adversary places the bandwidth probing nodes close to ingress and egress
routers at the boundary of ASes. The most useful location for the
probing nodes is determined using the acquired network maps.

Second, the adversary must get the target client to connect to the
colluding server. Once the connection is established the server will
vary the bandwidth of the connection to the client, leaving a distinct
pattern along the path of the connection. This is the pattern that will
trace back to the OP of the client.

Third, the bandwidth probing nodes at the AS boundaries will probe
ingress and egress routers in order to find the bandwidth pattern used
by the colluding server. The routers may be regular routers or ORs. The
probing is done using software like LinkWidth @chakravarty2008linkwidth,
which checks the available bandwidth on the probed router. LinkWidth was
also introduced by Chakravarty et al. If the pattern is detected on a
router, the corresponding AS is probably part of the path from client to
server. Using this technique, the adversary can trace the route back to
the client.

Fourth and last, when the AS containing the OP of the client has been
found, the final part of the path can be traced using the same
technique, only this time using the more specific internal AS map.

![Attacker investigating the fluctuations in available bandwidth of ORs
participating in a Tor circuit @chakravarty2010traffic<span
data-label="img:bandwidth"></span>](Images/bandwidth){width="50.00000%"}

Chakravarty et al. tested the attack in an emulated environment, an
in-lab experiment and on the real Tor network. They executed the attack
on 50 circuits on the live Tor network and tried to identify the ORs on
the circuit. They detected three, two, one and zero of the ORs 11, 14,
12 and 13 times respectively. Also, 22 of the 150 probed ORs filtered
all of the probe traffic.

Using this attack to uncover the identity of a hidden service can be
done in a similar way using a colluding OP instead of a server for
creating the bandwidth pattern. The identity of an OR can also be
discovered using a similar technique.

##### Related work

The variant of this attack that tries to uncover ORs in a Tor circuit
was described in more detail, along with practical experiments and its
results, by Chakravarty et al. @chakravarty2008identifying. The
experiments described in the article are the first to use an actual
implementation of the LinkWidth @chakravarty2008linkwidth program, of
which a prototype was built especially for this attack.

Another related attack was described by Wang et al. @wang2007network in
2007. The attack is similar to the attack described in this section in
the sense that it also tries to impose a specific pattern upon a
connection in order to identify that connection later. Wang et al. try
to influence the packet inter-arrival times instead of the bandwidth of
the connection.

Fingerprinting Attack
---------------------

In a fingerprinting attack an adversary utilizes the fact that traffic
often has very distinct characteristics. These traffic fingerprints can
be used to identify which webpage a client is requesting, whether a
client is connecting to a hidden service or to gain knowledge about the
path along which traffic is traveling through the network.

##### Website fingerprinting

In 2009 an attack on the anonymity of Tor users was published that uses
an adversary that monitors the victim’s browsing behavior
@shi2009fingerprinting. This attack is a realistic threat since it
requires only the entry point of the victim to be occupied and
furthermore it requires very few resources.

A typical webpage consists of many different files that are all
downloaded once a browser sends a requests to view a webpage. In most
browsers, each file would be downloaded via a separate TCP connection.
Since every TCP flow uses a different port, a listening attacker can
determine the size of each file being returned to the client by counting
the total size of the packets on each port. Not all webpages require the
same amount of resource files and besides that, resource files all have
different files sizes. Therefore, the set of file sizes of a certain
webpage creates a fingerprint that can be used to identify this webpage.
The attacker will first build a collection of fingerprints of webpages.
Next, he can compare the recorded fingerprint against his fingerprint
collection which enables him to monitor the user’s browsing behaviour.

This attack was first designed against SafeWeb @hintz2003fingerprinting,
but can also be used against Tor. However, Tor’s design employs two
significant characteristics which prevents the fingerprinting attack to
some extend.

First, Tor employs fixed sized data cells of 512 bytes. So for an
attacker it is difficult to detect the precise size of a file.

Second, Tor uses multiplexing to combine all the TCP streams into one
connection which makes it harder for an attacker to distinguish the
different files.

This second characteristic proves to be the most troublesome factor. A
solution for distinguishing the files is to count the number of incoming
packets between 2 out flowing packets. The more packets are received in
between 2 out flowing packets, the larger the file is assumed to be. The
fingerprint can now be represented by a vector $V = (v_1,v_2,...,v_n)$
where $v_i$ means “the number of occurrences of $i$ subsequent incoming
packets”. For stable network conditions, webpages with different files
and loading process can be distinguished by using for instance the
jaccard or cosine similarity between two fingerprint vectors
@liberatore2006inferring.

##### Circuit fingerprinting

Besides Website Fingerprinting there are also ways for an adversary to
detect whether a client is using a hidden service. This type of
fingerprinting attack is known as Circuit Fingerprinting. During the
circuit construction and communication phase between a client and a
hidden service, Tor exhibits traffic patterns that form a fingerprint
which enables an adversary to determine whether a circuit is involved in
communicating with a hidden service @kwon2015circuit. Therefore, by
using circuit fingerprinting an attacker can distinguish the regular
from suspicious circuits. Next, the attacker can apply a form of website
fingerprinting to gain knowledge about which particular hidden service a
suspicious circuit is communicating with @cai2012touching
@wang2014effective @wang2013improved @panchenko2011website.

##### Throughput fingerprinting

By observing the throughput of a Tor flow an adversary could learn
information about the path via which the traffic has traveled through
the Tor network. The throughput of a Tor flow can be used as a
fingerprint of the bottleneck relay, which is the relay with the minimal
forwarding capacity in a Tor flow. Two circuits that share the same
bottleneck relay will have a highly correlated throughput. Based on
throughput information, an attacker can identify the bottleneck relay,
identify the guard relay(s) and determine whether concurrent TCP
connections belong to the same Tor user. Also servers can infer they are
communicating with the same client by comparing characteristics of
datastreams. Namely streams that are multiplexed over the same circuit
have the characteristic that the throughput of those streams repeatedly
drop to zero during mutually exclusive periods of time, resulting into a
strong negative correlation @mittal2011stealthy.

DoS Attacks
-----------

Denial of Service (DoS) attacks are not used to de-anonymize users, but
flood the network resource of a victim resulting in very slow
connections or making it unavailable. It can also be used to force
honest users to use malicious relays, since the honest relays can be
made available. Normally the victim of a Distributed Denial of Service
(DDoS) attack is sent a lot of UDP packets from many sources, but since
Tor only transports TCP streams, this type of DDoS is not possible on
Tor.

#### Botnet abuse

When the command & control (C&C) of a botnet ran as a Tor Hidden Service
in August 2013, the number of connected users increased from 1 million
to 6 million. This attack was not aimed at specific victims, but had an
impact on the whole Tor network. While the amount of traffic did not
increase dramatically, it doubled download times for small files because
of increased processing loads on relays. The bottleneck was the key
exchange protocol that is needed to build encrypted circuits. Since the
C&C ran as a hidden service, all systems in control of the botnet were
periodically creating circuits to the hidden service.

In 2014, a paper by N. Hopper @hopper2014botnet was published in which
some solutions are discussed that limit or discourage the use of Tor for
botnets. These solutions mostly serve to encourage more research in this
area. In particular, four technical approaches are described, each with
its own challenges:

-   Resource based throttling: This solution aims to limit the rate of
    requests from the botnet by making it costly to build circuits,
    either economically (e.g. paying bitcoins) or computationally (e.g.
    solving a puzzle). This solution fulfills its purpose but also
    inconveniences normal Tor users.

-   Guard node throttling: When a client connects to the Tor network, it
    first starts a key exchange at a guard node. By limiting the rate
    that guard nodes accept connection requests, it does not prevent
    bots from flooding the network but makes it ineffective to run a
    botnet C&C via Tor. This could be a valid solution if other
    verifiable services that require a high rate can request permission
    for a higher connection rate.

-   Reuse of failed partial circuits: When a circuit times out, it is
    destroyed entirely. By reusing partially built circuits a
    substantial reduction in load for the network can be accomplished if
    the failure rate of creating a circuit is high enough.

-   Hidden service isolation: By isolating the processing of hidden
    service traffic from ordinary traffic, regular users can be
    protected from this kind of attack. This could work by introducing
    new cell types to recognize the type of traffic. It does however
    intensify the effect for legitimate hidden services.

#### The Sniper Attack

In 2014 a novel and destructive DoS attack against Tor that can be used
to anonymously disable arbitrary Tor relays was published
@jansenetall2014sniper. Since the attacker remains hidden while
disabling relays in a targeted manner, the attack is called the Sniper
Attack. This attack works by utilizing Tor’s application level
congestion and flow control mechanisms to cause a target relay to buffer
a large amount of data in application queues.

To understand the principles of the attack, it is important to know the
working of Tor’s level congestion and flow control mechanisms. Tor
implements an end-to-end sliding window mechanism to control the amount
of data directed into the network. The exit relay manages a package
window counter for each out flowing stream initiated at 500 and a total
package window counter for all streams combined initiated at 1000. For
each package the exit relay injects into the circuit, the corresponding
stream package window counter, as well as the circuit package window
counter, are decremented by one. The exit relay will stop injecting
cells from any multiplexed stream whose package window reaches zero, and
will stop injecting cells from all streams when the circuit package
window becomes zero. The receiving node maintains a sliding window of
received packets and once this window is full, the receiver sends a
SENDME cell to the exit relay causing the exit relay to increase the
package window counters and to restart the transmitting process.

![Basic idea of the Sniper Attack: (a) The client creates a circuit with
the target as entry. (b) The exit sends data through the circuit,
ignoring package window limits. (c) The client stops reading from the
TCP stream to the target entry. (d) The target entry buffers the data
until termination of the Tor process by the OS.
@jansenetall2014sniper<span
data-label="img:sniper"></span>](Images/sniper){width="50.00000%"}

The paper @jansenetall2014sniper describes a few different versions of
the attack, of which the most efficient variant (i.e. the one using the
least resources) will be explained below. An adversary starts the attack
by creating a circuit that uses the target node as the circuit entry,
and initiates the download of two very large files over the circuit.
This will result into two streams which can together cause the exit
relay to inject up to the 1000 package window limit. By sending SENDME
cells to the exit node, the attacker ensures that the exit’s package
windows does not reach zero and it continues to inject packages into the
circuit. However the attacker never reads the cells that arrive at the
entry node, the target of the attack. Therefore, cells will continue to
flow to and be buffered by the entry node in its application queue,
until the entry’s Tor process is killed by the OS due to the process
consuming too much memory.

#### CellFlood Attack

In contrast to the Sniper attack, that targets relays by downloading
large files, the CellFlood attack hinders Tor relays by flooding the
relays with difficult to execute circuit setup requests
@barbera2013cellflood. This enables the attacker to reduce the
processing capacity of the targeted relays using little bandwidth. The
attacker uses the fact that processing a *create* command takes 4 times
longer than generating it. The *create* command is used by a client to
extend a circuit. The attacker generates a continuous stream of *create*
commands for the targeted relays, which consumes all their computational
resources. This results in *create* commands from honest clients that
are rejected.

#### Attacks on hidden services

An adversary can also aim to bring down a Hidden Service. A hidden
service relies on a collection of introduction points that can be used
to introduce a client to the hidden service. The list of introduction
points associated with a certain hidden service is stored on Hidden
Service Directory Servers (HSDirs). Therefore, HSDir servers are in a
position to make a hidden service unreachable by refusing to answer a
client’s request to receive the list of introduction points
@biryukov2013trawling. However, an attacker needs to control multiple
HSDir servers in order to stop clients from creating connections to the
hidden service completely. Another way to impede anyone of creating a
connection with the hidden service is by DoSing the introduction points
of a particular hidden service @asn2013hiddenservices. A way to defend
against such attacks is proposed by Syverson and Øverlier
@overlier2006valet. This paper describes the strategy of adding a large
number of contact points between a client and the introduction points,
which prevents an attacker from identifying the introduction points.

##### Related work

In 2014 B. Conrad and F. Shirazi published a paper which analyzes the
effectiveness of DoS attacks on Tor @conrad2014analyzing. Multiple
scenarios are simulated by using different strategies in choosing which
OR to attack. The effectiveness is measured by looking at download times
for files and the amount of compromised circuits.

DoS attacks that aim to make the Tor network unavailable, might improve
by targeting so called *supernodes* @li2011super. Supernodes are relays
that excel in both availability and bandwidth. If those relays are
attacked with a DoS attack, other relays may not handle all additional
traffic. It might be very difficult to detect a DoS attack on supernodes
if the strength of the attack is gradually increased, a so called loop
attack @li2011super.

Supportive Attacks
------------------

In this section we describe a number of attacks that do not directly aim
to de-anonymize Tor users or disrupt the Tor network but rather are
helpful to perform a de-anonymization attack or a disruptive attack at a
later point in time.

#### Influencing Tor’s Guard Selection

Most attacks on Tor are traffic correlation attacks, where both the
entry node and the exit node are required to perform the attack, it can
be beneficial for an attacker to force a client to choose a malicious
node as guard node, or entry guard. A client only uses guards nodes as
Tor entry nodes. This means that if none of the guard nodes are
malicious the user can never connect to a malicious entry node @torfaq.
Guard nodes are usually replaced after 30 - 60 days @li2015stealthy.
Replacement is done in a so called guard selection round, where a set of
non-selected guard nodes is chosen to be included in the guard list. The
chance that a guard node is included in the guard list is bigger for
long-running or high bandwidth nodes.

A paper published in 2015 introduces an attack that aims to shorten the
the time interval between guard selection rounds @li2015stealthy.
Assumed is that the attacker controls multiple guard nodes. The attacker
should also be able to identify and manipulate Tor traffic between the
client and an entry node. Identifying Tor traffic can be done by an AS
using the method described in Paragraph \[sub:distinguished\].

By analyzing the entry nodes the client connects to, the attacker is
able to get the clients guard list. The attacker blocks Tor traffic from
the client to all guard nodes except for one. Leaving one guard node
reachable makes sure that the client’s communications are not disturbed.
Since a new guard selection round is performed when less than 2 guards
are online @elahi2012changing, blocking all guard nodes except for one
results in a new guard selection round. This is an opportunity for one
of the malicious guard nodes to be included in the guard list. This
process continues until a malicious guard node is included in the guard
list. Experiments confirm that in 80% of the cases a malicious guard
node is included in the guard list within 20 guard selection rounds.
Experiments also show that this attack forces an new guard selection
round every 1.5 minutes. This would include a malicious guard node into
the client’s guard list within 30 minutes.

#### The Sybil attack {#sub:sybil}

In June 2010, the number of active Tor relays suddenly increased in a
matter of hours. It turned out that somebody set up several hundred Tor
relays on PlanetLab machines @winter2016sybil. This may look harmless,
but it can actually be used as an attack on the Tor network called a
Sybil attack.

In the Sybil attack, an adversary controls many virtual identities in
order to obtain disproportionately large influence in the network. The
effectiveness of many attacks on Tor depends on the consensus weight of
the attacker, which is the amount of traffic an attacker can observe. As
the consensus weight grows, a number of other Tor attacks become easier
to execute. Examples of attacks that are easier in combination with a
Sybil attack are the fingerprinting and correlation attacks.

Besides simplifying other attacks, the Sybil attack poses risks on the
usage of the Tor network and therefore on the anonymity of its users.
The effectiveness of Tor depends on the reliability of the Tor relays.
Unreliable relays can both degrade the user experience and impair the
anonymity guarantees provided by Tor. Certain users will refrain from
using the system when encountering issues caused by unreliable Tor
relays. Less users means a decrease in the overall anonymity of the
network. The remaining users will continue using the network with a
lower anonymity, presenting better opportunities for observation. This
problem can be exploited by adding malicious relays and strategically
affecting the reliability of anonymous communications to increase the
odds of an adversary compromising user anonymity @asiaccs2014sybil.

![Setup of Sybil attack: two datasets as input to the attacker,
consensus and server descriptors; malicious relays together with the
exitmap @winter2016sybil<span
data-label="img:sybil"></span>](Images/sybil){width="50.00000%"}

Practical defenses against Sybil attacks are challenging, these attacks
will probably always be possible in anonymity networks without a central
authority @winter2016sybil. However, since Sybil relays typically behave
and appear similarly, there are some heuristics that can be used to
detect a Sybil attack to some extends. Relays that are part of a Sybil
attack often join and leave the network simultaneously, they have common
configuration parameters, and may frequently change their identity
fingerprint to manipulate Tor’s distributed hash table.

#### Packet Size Analysis Attack {#sub:distinguished}

In 2011 a low-cost technique that distinguishes Tor traffic from non-Tor
encrypted (HTTPS) traffic was published @barker2011using. For this
attack, it is only needed to intercept traffic and analyze it.
Therefore, this attack can be performed by a passive adversary.

After analyzing Tor traffic, it is concluded in @barker2011using that
the size of the third packet is about 140 bytes and the size of the
fifth packet is about 920 bytes. Using these simple heuristics 98% of
actual Tor traffic can be classified as Tor traffic. Furthermore, a
large fraction of the packets is just bigger than 512 bytes, the size of
a Tor cell. The experiments have been performed in controlled
conditions, but these patterns might be visible in the real world Tor
network. By analyzing the packet sizes of intercepted traffic, an
adversary could distinguish Tor traffic from regular traffic.

#### Tor Authentication Protocol Attack

In 2009, Y. Zhang published a paper for an attack on the Tor
Authentication Protocol (TAP) if a user has multiple concurrent sessions
@zhang2009effective of TAP running.

The TAP forms the basis of Tor’s security and is used to negotiate
session keys between a user and the ORs in a circuit. A vulnerability
was discovered if a user runs multiple concurrent sessions of TAP.

This attack works if the attacker has control over one malicious OR $A$.
When a user connects to OR $A$ and negotiates a session key, then
negotiates a session key with another OR $B$, the attacker is able to
trickily interleave the messages from both sessions to make different
session keys between the user and the non-malicious OR $B$. While this
attack does no direct harm, it violates the original object of the TAP
protocol.

Revealing Hidden Services
-------------------------

It might be interesting for an attacker to reveal a hidden service. This
kind of attack is especially interesting for governments that try to
pinpoint the location of a hidden service. There are a number of
research papers that describe ways to reveal hidden services.

#### First Node Attack

In the first paper that aims to reveal hidden services the attackers’
relays try to become the relay in a circuit that is directly connected
to the hidden service’s server @locatingservers. This would immediately
reveal the location of the hidden service to that node.

To become the first node from the hidden service’s server in a circuit,
the attacker needs a malicious node and a client that connects to a
hidden service. The client will connect to the hidden service and send a
certain timing pattern in the communication. If the malicious node is on
the circuit it will detect this pattern. Since the client knows the
IP-addresses of all nodes until the rendezvous point, the malicious node
can detect whether it is one of those nodes. Usually, there are three
nodes between the Rendezvous point and the Hidden Service. If the
malicious node is next to the rendezvous point, this can be detected,
since the rendezvous point’s IP-address is known by the attacker. If the
malicious node is in the circuit, but not next to the rendezvous point
or between the rendezvous point and the client, it has to be first or
the second node after the hidden service. By using timing analysis the
malicious node can detect whether it is the first or the second node. If
the malicious node is the first node, the location of the hidden service
is revealed. Otherwise, the attack will be run again until the malicious
node becomes the first node in a circuit.

The chances that this attack succeeds is reduced by introducing guard
nodes. Since the hidden service only creates circuits to guard nodes, if
your node is not a guard node of the hidden service, it will never be
selected.

##### Related work

More recent, in 2013 an attack that used similar techniques was
presented @biryukov2013trawling. This attack technique is modified and
used by Abbott et al. to pinpoint clients of a web service
@abbott2007browser.

#### Clock Skew Attack

Another approach is taken in @torclockskew, which is improved in
@zander2008improved. These attacks are able to pick the hidden services
from a list of candidate servers. By creating a lot of requests to a
Hidden Service the temperature of the server will rise, resulting in a
different clock skew. This clock skew can be derived from the timestamps
that are received from the hidden service. By comparing the timestamps
from all the candidate servers with their sample they can detect a
matching clock skew. This reveals the location of the hidden service.

Attack Detection
================

Many papers describe attacks, but give no way to detect or expose them.
A paper by Winter et al. @winter2014exposing published in 2014 describes
how malicious exit relays can be exposed for many common kinds of
attacks. Two tools were developed, one to detect active attacks and the
other for detecting credential sniffing.

The ’man in the middle’ in Tor circuits are the exit nodes. There the
final layer of encryption is removed and the traffic is sent to its
actual destination. This allows the owner of this exit node to see and
even actively modify the traffic. This is often used to exploit
vulnerabilities in order to make the connection insecure or inserting
malicious code in web pages. The tool ’exitmap’ can detect popular MitM
attacks. It runs on a single machine and asynchronously creates circuits
with a set of exit relays as end points. Most detection methods work by
comparing the fingerprint of a certificate fetched through Tor with the
expected one that is hard-coded in the client. Examples of attacks that
can be detected are HTTPS tempering, sslstrip (rewriting HTTPS to HTTP)
and DNS query censoring.

Credential sniffing is a passive MitM attack. Rather than actively
modifying traffic it simply looks for credentials in the traffic. The
other tool, ’HoneyConnector’, can detect credential sniffing. It creates
bait connections over Tor using randomly generated unique credentials
over FTP and IMAP. By monitoring which accounts were accessed, the
malicious exit relay that was sent a unique account could be identified
and exposed.

Over a period of several months, all exit relays ($\sim$ 950 at the
time) were monitored. Using ’exitmap’, 40 malicious exit relays were
identified. Using modified FTP and IMAP servers, ’HoneyConnector’ was
deployed on multiple hosting providers. A total of 255 login attempts
were made tracing back to 27 sniffing relays, only two of which were
also caught by ’exitmap’.

While it is possible for the Tor project to blacklist certain relays as
exit relays, since most attackers do not publish contact information or
other hints, it is difficult to identify the actual attacker. They might
just shut down their malicious exit node and set up another one if they
notice that their relay is blacklisted. Therefore, a patch for the
TorButton extension of the TorBrowser was developed to detect some
attacks automatically for regular users.

Attacks and Countermeasures
===========================

To defend against the threat posed by de-anonymizing attacks, there have
been a number of research efforts by academic and industrial agencies on
developing various countermeasures. In general, countermeasures can be
deployed from three perspectives: network layer, protocol layer and
application layer @yang2015anonymizing @snader2008tune.

#### Network Layer

Since network traffic characteristics can be exploited to de-anonymize
users, a basic idea of defense is to remove or falsify the features of
traffic correlated with users @yang2015anonymizing. These features
include packet size distribution, packet order, traffic volume, traffic
time, and so on. *Packet padding techniques* can be used to alter packet
sizes in order to prevent that features such as packet length and packet
order can be inferred @bernaille2006traffic. For example, the size of
each packet can be fit into the same size with a maximum transmission
unit (MTU). The traffic time can be obfuscated by adding delay between
each packet to increase the traffic time @back2001traffic. Besides,
*dummy traffic techniques* can be used to inject dummy packets into
original traffic in order to bewilder the traffic volume
@back2001traffic. Moreover, *traffic morphing techniques* can be applied
to alter traffic patterns to look like other traffic patterns. For
example, to confuse a web based fingerprinting attack, the web server
can select a target page and then imitate the packet size distribution
of that web page @yang2015anonymizing @juarez2014critical. Mainly,
countermeasures at the network layer are more general and can also be
applied in anonymous communication systems other than Tor
@back2001traffic.

#### Protocol Layer

Protocol-level padding and dummy techniques can be applied to obfuscate
traffic features and thereby hinder de-anonymizing attacks
@dyer2012peek. This should be done with a random amount of padding in
order to improve security. Actually, secure shell, TLS and IPsec apply
such protocol-level padding techniques to line plaintext up with block
cipher boundaries, causing some obfuscation in the packet size
@yang2015anonymizing. Tor does not use circuit-level padding techniques
because it can significantly decrease the performance of the circuit.
But it is certainly possible to design protocol-level padding and dummy
techniques in such a way that it reduces the overhead caused by itself.

#### Application Layer

HTTP features and background traffic can be exploited to hide traffic
features from user flows. HTTP pipelining and HTTP ranges can be used to
modify the packet sizes of incoming and outgoing messages
@luo2011httpos. Additionally, the order of HTTP requests at the client
side can be adjusted to alter the traffic pattern. Methods based on
background traffic can be applied at the application level by loading a
fake web page in the background while a user is browsing the target web
page. Generally, countermeasures at the application layer are specific
for some applications and cannot be widely applied @ling2012cellcounter.

From the description of the various countermeasures, we can conclude
that there is an increasing need for hybrid techniques that can be
deployed at multiple layers simultaneously @yang2015anonymizing. This
way various attacks of different types can be obstructed effectively.
Moreover, the trade-off between security and performance need to be
taken into account to provide an overall and secure solution to various
attacks @cai2014systematic.

Ethical Vulnerabilities of Tor
==============================

Tor has been proven to be used for real evil content. We consider this
an often neglected vulnerability. According to CloudFlare 94% of the
requests that are done across the Tor network are intrinsically
malicious @prince2016troublewithtor. Recently, evidence has been found
that al-Qaeda and other terrorist organizations are using the Tor
network to propagate their causes @terror. Due to this illegal content,
Tor does not have an unquestionable moral high ground.

Values and Principles of Tor
----------------------------

As explained on the Tor Project website, the Tor Project is based on the
values and principles of net neutrality, right to anonymity online,
freedom of speech and the right to privacy @onionrouter2. This makes Tor
a powerful tool for many morally right uses. The Tor network empowers
freedom of speech to those living under repressive governments and in
countries with restrictions on Internet @ethicaltor. Countries like
China are known for censoring their citizens’ access to the Internet;
Tor provides a way around this control @toruses. For informers, Tor
provides a safe way to leak information to journalists. In fact, Edward
Snowden released information on the NSA’s PRISM program to news
organizations via Tor @navigatetor @schneier2013attacking. However, the
values and principles of Tor also introduce some ethical issues that
will be looked into more closely next.

Tor and Criminal Behaviour
--------------------------

Nowadays, freedom is one of the keywords of the internet. How far should
this freedom go? Should we allow Tor users to perform illegal activities
with a small chance of being convicted? The type of actions we are
encouraging by providing anonymization services should be handled very
carefully. This makes it necessary to reflect on the impact that Tor’s
freedom has on its users @corrigan2013welcome.

Tor lets people with evil intentions provide and use illegal services in
a relatively secure way, meaning that it is not easy to trace the source
of an illegal service and the location of the users of the service.
Illegal activities can be hidden using Tor’s hidden service protocol
@Torndhidden. This makes them only accessible via the Tor network and
they do not get indexed like the rest of the Internet. Among the hidden
services of Tor are drug marketplaces, weapon marketplaces, contract
killers, hackers and child pornography @yetter2015darknets. A recent
study shows that about 44% of the websites hosted as a hidden service
are of criminal intent @biryukov2014content. This study also shows that
more than 50% of hidden service addresses can be accessed by a port that
is used by “SkyNet", a botnet that can be used for DDoS attacks or
Bitcoin generation @skynet. This suggests that a lot of servers that
host hidden services are part of “SkyNet".

The fact that Tor allows all kinds of communities to grow makes many Tor
users uneasy. It may even undermine the network’s user base since the
criminal activities on Tor might prevent potential users from using Tor.
Merely using Tor can make you an attractive target for the government,
even if you only use the service for legal purposes @navigatetor.
Because of this, users might worry to be associated with these illegal
activities and therefore decide not to use Tor. As one of our team
members noted on Tor during a discussion: “I would not use it at home.”
Innocent users do not want to be generalized with people that use Tor
for illegal purposes.

The Dark Web: from Snowden to Silk Road
---------------------------------------

Not all hidden services remain hidden. Silk Road is a well-known example
of a hidden service that got exposed and whose accused administrator
Ross Ulbricht got arrested @silkroad2 @silkroad3. According to the FBI,
the Silk Road servers were pinpointed to Iceland after a CAPTCHA field
in the login page of the Silk Road website was not configured to be used
via the Tor network @silkroad1. However, several security experts claim
that this story is not true @phpadmin @phpadmin1. According to them, the
FBI was able to locate the Silk Road servers by accessing a PHPmyadmin
configuration file. Access to the configuration file would have been
gained by password sniffing @phpadmin @phpadmin1. More recently, in 2015
the FBI was able to seize the servers of “Playpen”, a bulletin board
that was used to distribute child pornography @playpen. The FBI then ran
the service for two weeks after exposure but included identification
software and were able to identify 1500 users of “Playpen”. This case
started the debate whether government organizations are allowed to hack
Tor users in order to identify them @playpen1.

In 2013 former NSA contractor Edward Snowden released thousands of
classified NSA documents on their PRISM Program to news organizations
via Tor @navigatetor @schneier2013attacking. The PRISM Program is the
NSA’s surveillance program to track online communication for which they
tapped many Internet users and nine internet firms, including Facebook,
Google, Microsoft and Yahoo @snowdensurveillance. The documents that
Snowden leaked revealed that Tor users have also been targeted by the
NSA for years. In a presentation acquired by Snowden titled “Tor
Stinks”, the NSA admits that it will “never be able to de-anonymize all
Tor users all the time” @snowdentormonitored. The files exposed the
organization’s struggles with deciphering emails and encrypted chat logs
on Tor, despite its abilities to hack into online communication systems.
Snowden was charged with two counts of violating the Espionage Act and
theft of US Government property @snowdencharges. On June 21, 2013, the
U.S. Department of Justice dropped charges against Snowden and made an
exception for political offenses. A subject of controversy, Snowden has
been called a hero and a traitor by the public @snowdenhero. His actions
have triggered debates over mass surveillance and the tension between
national security and privacy. But that is exactly what Snowden aimed
for: “I didn’t want to change society. I wanted to give society a chance
to determine if it should change itself. All I wanted was for the public
to be able to have a say in how they are governed." @snowdenquote
Snowden has always publicly supported Tor. According to him Tor is a
critical technology in defense of our publication right. “The design of
the Tor system is structured in such a way that even if the US
Government wanted to subvert it, it couldn’t because it’s a
decentralized authority @snowdentor." Whether government organizations
are allowed to use identification software on Tor and the consequences
this has for Tor users is discussed in Paragraph \[sub:rethinkprivacy\].

Tor and Informed Consent
------------------------

There is a major ethical issue going along with the principle of freedom
of speech: uninformed consent of the Tor users @ethicaltor. In reality
most users have no knowledge of what is being downloaded by their
connection on the Tor network. It could include the illegal activities
of someone else on the network. This is a major problem with the
Darknet: the user’s nodes will often be used for the propagation of data
that the majority do not approve of @toruses. The idea of any depravity
or illegality being routed through or being stored on your system makes
many users uneasy. The counter side of a liberal view towards freedom of
information and speech is that you cannot choose what to approve. There
is enough criminal behavior on Tor that most users want no part in
proliferating. Should society give up its standards for anonymity?

Tor and Violations of Privacy {#sub:rethinkprivacy}
-----------------------------

From a technical perspective Tor can provide a high level of privacy.
However, privacy might be reduced as Tor users are outlawed by the
government. Can the government intrude the Tor network and its users’
computers to retrieve information about hidden services? This would also
be an intrusion of Tor user’s privacy. What makes the Silk Road case
interesting, is the discussion whether the privacy of Mr. Ulbricht has
been violated during the exposure of Silk Road.

After the arrest of Mr. Ulbricht his lawyers accused the FBI of
violating the right of privacy by the fourth amendment of their client
when the FBI accessed his servers in Iceland @fourthamendment4. The
fourth amendment ensures the privacy of every American citizen
@fourthamendmentdef. According to the judge the FBI did not violate Mr.
Ulbricht’s fourth amendment right of privacy by hacking his servers in
Iceland @fourthamendment1. The judge argued that Mr. Ulbricht had not
timely shown that the servers belonged to him. However, if Mr. Ulbricht
would have shown that the servers were his, this might get him a
conviction. This seems like an impossible situation for Mr. Ulbricht.
However, according to the judge Mr. Ublricht should have released a
statement that the servers where his, without risking that this
statement would have been used against him in a court of law.

With hidden electronic data there is always the discussion if it is
legal for law enforcement agencies to hack the users in order to
identify them and if it is not a violation of the fourth amendment
@fourthamendment2. In the case of Silk Road the government argued that
the servers of Mr. Ulbricht were foreign property where evidence of a
criminal act could be found, which allows them to hack the servers. A
warrant to search electronic data is allowed if the government cannot
tell where the data is located @fourthamendment3. However, Tor works in
such a way that the government could have never known where the Silk
Road servers where located without compromising the Tor network.
Therefore government organizations are seeking a way to adjust the legal
checks of the Fourth Amendment in order to be able to legally hack users
connected to Tor @fourthamendment1. The requested change would allow
government organizations to acquire a warrant to search electronic data
without providing any specific details as long as the target computer
location has been hidden through a technical tool like Tor or a virtual
private network @fourthamendment3. This kind of discouragements of Tor
use by governments or other third parties, for example by not protecting
data sent over Tor by the fourth amendment, are labeled as *lawyer-based
attacks*.

Illegality as a Consequence
---------------------------

Balancing the ethical and moral uses of Tor against the opportunity for
misuse by criminals poses the question ’Should users be allowed to be
anonymous online?’. It is important to note that criminal activity on
Tor is a consequence, not a goal, of the network’s commitment to freedom
of speech. Just as large, growing cities attract criminals, it is
unavoidable that the growth of Tor has made the network appealing for
shady activities @navigatetor. Therefore the use of Tor should be
regulated in cooperation with law enforcement agencies. Some hidden
services are immoral and should be punished, just like they would be in
‘the real world’. And if that would not be the case anymore, if Tor
would become a platform that does not make any judgments of its use, how
do we then judge the acts of a Tor volunteer?

Financial Vulnerabilities of Tor
================================

The Tor network is suffering from continuous starvation. While they have
a loyal fan-base, they have no business model and no devoted Tor
developers to rely on. In the last two years Tor’s annual revenue was
reported holding steady at about $2.5$ million @torannualrevenue. This
is a moderate budget considering the number of Internet users involved
in the network and the impact they have. Since 2012 the Tor network
continues to grow steadily with an average rate of 18% per year in Tor
relays @torreport. This year Tor reported to support about 20
contractors and to have a user base that is up to several million people
each day @torannualrevenue.

Aside from their donation campaign, Tor’s services are made possible by
more than 7,000 volunteers running as relay operators and by the huge
amount of analysis Tor gets from research groups and individual
programmers @torannualrevenue. This constant peer review has become one
of their strengths over the past years. As a result, the network’s
success and continuity mainly depends on the thousands of financial and
non-financial volunteers that contribute to everything from system
administration to global outreach and education @torreport2. Tor
survives because of grants from foundations and individuals, but the
important contributions are time, research and user commitment, instead
of money. Therefore the more significant concerns are around global
outreach, the social dynamics within the Tor community and collaborative
practices.

The network’s security and effectiveness may be harmed by the lack of
staff and continuous code integration. In order to prevent this, a pile
of the network’s features need to be researched, implemented and
deployed on a voluntary basis @asn2013hiddenservices:

-   Hidden Service operators need to be made aware of the shortcomings
    of the Tor architecture in order to solve scaling issues and to
    improve its security.

-   Researchers need to be introduced to various research topics and
    questions regarding anonymous communication services to stimulate
    further research in Tor’s protocol, cryptography and the mechanisms
    of countermeasures against de-anonymizing attacks.

-   Software developers need to be introduced to the pile of coding
    tasks left to be done and the issues that involve Tor’s codebase.

The statements above only touch issues that involve Tor’s codebase or
its security, but if Tor wants to be truly successful and influential it
is also essential to build a dynamic ecosystem around the network.
Extensions like privacy-preserving archiving systems, anonymous file
sharing, easy-to-use publishing platforms and chat systems would give a
boost to the network’s growth and its institutional funding
@asn2013hiddenservices.

Conclusion
==========

In this survey, we analyzed the technical, ethical and financial
vulnerabilities of the Tor network.

#### Technical Vulnerabilities

The technical vulnerabilities of Tor are increasingly exploited by
de-anonymizing attacks. We have elaborated on attacks that have been
published, including end-to-end and single-end attacks proposed by
active or passive adversaries. Based on their method and goal, we
categorized the existing attacks on Tor into seven groups: *Correlation
attacks*, *Congestion attacks*, *Timing attacks*, *Fingerprinting
attacks*, *Denial of Service attacks*, *Supportive attacks* and
*Revealing Hidden Services attacks*. Most de-anonymizing techniques that
are applied in these categories are based on the concepts of traffic
analysis, traffic confirmation and forgery of node identities. With
traffic analysis an adversary simply observes inputs and outputs of the
network and correlates their timing patterns. The attacker distinguishes
data flow patterns from normal traffic caused by Tor’s encryption.
Similarly, with traffic confirmation an attacker uses the weaknesses of
the constructed Tor nodes and other related services to control or
observe the relays of both ends of a Tor circuit. Then he analyzes
individual network links in order to validate his suspicion. More recent
attacks use a totally different approach that is based on the forgery
and manipulation of node identities and since these attacks often work
in a distributed manner, they can pose a big threat to the Tor network.

Each academic and industrial agency leverages unique methods to attack
Tor because of different goals. Therefore different countermeasures need
to be taken to mitigate the risks posed by each attack. Generally, the
countermeasures can be deployed from three perspectives: *network layer*
(to de-anonymize the communication between users), *protocol layer* (to
hide traffic features associated with users) and *application layer* (to
remove traffic features from user flows). However, in the last thirteen
years the attacks on Tor have grown to be more complex and effective.
There is an increasing need for hybrid techniques that that can be
deployed at multiple layers simultaneously - while taking the trade-off
between security and performance into account - to provide an overall
and secure solution to various attacks. Additionally, a new method has
been developed by @winter2014exposing to detect most de-anonymizing
attacks more easily and to expose their malicious exit relays more
quickly. By checking certificates and creating bait accounts on decoy
connections, malicious nodes can be exposed and blacklisted as exit
nodes in the Tor network. Detection is the first step in the obstruction
of attacks so further research on this should be investigated.

The race of developing new attacks to compromise the anonymity of
Internet users will continue. Therefore it is important to establish a
strong theoretical background in both the academic and industrial world
in order to be able to keep studying interactions between attacks and
countermeasure mechanisms.

#### Ethical Vulnerabilities

The Tor Project is based on the principles of net neutrality, right to
anonymity online, freedom of speech and the right to privacy. But these
values of Tor also introduce a number of ethical issues. The freedom and
cover Tor provides attracts criminal behavior. A recent study shows that
about 44% of the websites hosted as a hidden service is of criminal
intent @biryukov2014content. Because of this Tor has developed a bad
reputation, which may prevent potential users from using Tor since they
do not want to be associated with illegal activities. Another ethical
issue that goes along with freedom of speech is uninformed consent of
users who might not always agree with the content that is being
downloaded by their connection on the Tor network. These recent
developments have forced law enforcement agencies to intrude the Tor
network and its users to retrieve information about hidden services.
This has caused lawyer-based attacks and adjustments on the fourth
amendment to be a point of discussion. The ethical issues also raise
questions about the privacy of Tor. Do these issues justify third
parties to hack Tor and pose a threat to its privacy in the name of the
public good? Illegality is a consequence, not a goal, of the network’s
commitment to freedom of speech. Therefore the services provided by Tor
need to be regulated and controlled in cooperation with government
organizations to guarantee safety and justice in the cyber world.

#### Financial Vulnerabilities

The Tor project deals with financial insecurities and continuous
resource starvation because of lack of a business model. The network’s
success and continuity mainly depend on the thousands of financial and
non-financial volunteers. To ensure the security and effectiveness of
Tor in the future, the network’s features need to be researched,
implemented and deployed on a voluntary basis. Hidden Service operators
and developers need to be made aware of the shortcomings of the Tor
architecture and researchers need to be introduced to various research
questions regarding anonymous communication services. This way further
research and development can be stimulated to create a dynamic ecosystem
around Tor and to boost the network’s growth and institutional funding.

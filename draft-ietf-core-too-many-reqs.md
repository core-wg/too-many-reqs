---
stand_alone: true
ipr: trust200902
docname: draft-ietf-core-too-many-reqs-latest
keyword: Internet-Draft
cat: std
pi:
  toc: 'yes'
  tocompact: 'yes'
  tocdepth: '3'
  tocindent: 'yes'
  symrefs: 'yes'
  sortrefs: 'yes'
  comments: 'yes'
  inline: 'yes'
  strict: 'no'
  compact: 'no'
  subcompact: 'no'
title: Too Many Requests Response Code for the Constrained Application Protocol
abbrev: Too Many Requests Response Code for CoAP
date: 2018
author:
- ins: A. Keranen
  name: Ari Keranen
  org: Ericsson
  email: ari.keranen@ericsson.com
normative:
  RFC2119:
  RFC7252:
informative:
  I-D.ietf-core-coap-pubsub:
  RFC6585:
  RFC7230:

--- abstract

A Constrained Application Protocol (CoAP) server can experience
temporary overload because one or more clients are sending requests to
the server at a higher rate than the server is capable or willing to
handle. This document defines a new CoAP Response Code for a server
to indicate that a client should reduce the rate of requests.


--- middle


# Introduction {#introduction}

The Constrained Application Protocol (CoAP) {{RFC7252}} Response Codes
are used by a CoAP server to indicate the result of the attempt to
understand and satisfy a request sent by a client.

CoAP Response Codes are similar to the HTTP {{?RFC7230}} Status Codes
and many codes are shared with similar semantics by both CoAP and
HTTP. HTTP has the code "429" registered for "Too Many Requests"
{{RFC6585}}.  This document registers a CoAP Response Code "4.29" for
similar purpose and uses the Max-Age option (see Section 5.10.5 of
{{RFC7252}}) to indicate a back-off period after which a client can
try the request again.

While a server may not be able to respond to one kind of request, it
may be able to respond to a request of different kind, even from the
same client. Therefore the back-off period applies only to similar
requests. For the purpose of this response code, a request is similar
if it has the same method and Request-URI. Also if a client is sending
a sequence of requests that are part of the same series (e.g., a set
of measurements to be processed by the server) they can be considered
similar even if request URIs may be different. Because request
similarity is context-dependent, it is up to the application logic to
decide how the similarity of the requests should be evaluated.

The 4.29 code is similar to the 5.03 "Service Unavailable" {{RFC7252}}
code in a way that the 5.03 code can also be used by a server to
signal an overload situation. The 5.03 code also uses the Max-Age
option to indicate the time after which a client can retry. However
the 4.29 code indicates that the too-frequent requests from the
requesting client are the reason for the overload.

# Terminology

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
"SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and
"OPTIONAL" in this document are to be interpreted as described in
BCP 14 {{!RFC2119}} {{!RFC8174}} when, and only when, they appear in
all capitals, as shown here.

Readers should also be familiar with the terms and concepts discussed
in {{RFC7252}}.


# CoAP Server Behavior

If a CoAP server is unable to serve a client that is sending
CoAP request messages more often than the server is capable or willing
to handle, the server SHOULD respond to the request(s) with the
Response Code 4.29, "Too Many Requests". The Max-Age option is used
to indicate the number of seconds after which the server assumes it is
OK for the client to retry the request.

An action result payload (see Section 5.5.1 of {{RFC7252}}) can be
sent by the server to give more guidance to the client, e.g., about
the details of the overload situation.

The 4.29 Response Code is only returned to the client(s) sending
requests too frequently; if other clients are sending requests that
cannot be served due to server overload, the 5.03 Response Code is
more appropriate.

If a client repeats a request that was answered with 4.29 before Max-
Age time has passed, it is possible that the client sent multiple
requests before receiving the first answer or that the client did not
recognize the Response Code. To slow down clients that do not
recognize the 4.29 code, the server MAY respond with a more generic
error code (e.g., 5.03). Server MAY also limit how often it answers to
a client, e.g., to once every estimated RTT (if such estimate is
available). However, both of these methods add per-client state to the
server which may be counterproductive to reducing load.


# CoAP Client Behavior

If a client receives the 4.29 Response Code from a CoAP server to a
request, it SHOULD NOT send a similar request to the server before the
time indicated in the Max-Age option has passed. If the 4.29 response
does not contain a Max-Age option, the default value (60 seconds, as
defined in Section 5.10.5 of {{RFC7252}}) is assumed.

Note that a client may receive a 4.29 Response Code already on a first
request to a server. This can happen, for example, if there is a proxy
on the path and the server replies based on the load from multiple
clients aggregated by the proxy, or if a client has restarted recently
and does not remember its recent requests.

A client MUST NOT rely on a server being able to send the 4.29
Response Code in an overload situation because an overloaded server
may not be able to reply at all to some requests.


# Security Considerations {#SecurityConsiderations}

Security considerations of {{RFC7252}} apply also to this Response
Code.

Replying to CoAP requests with a Response Code consumes resources from
a server. For a server under attack it may be more appropriate to
simply drop requests without responding at all. However, dropping
requests is likely to cause also well-behaving clients to simply
retry the requests.

As with any other CoAP reply, a client should trust this Response
Code only to extent it trusts the underlying security mechanisms
(e.g., DTLS {{?RFC6347}}) for authentication and freshness. If a CoAP
reply with the Too Many Requests Response Code is not authenticated
and integrity protected, an attacker can attempt to spoof a reply and
make the client wait for an extended period of time before trying
again.

If the Response Code is sent without encryption, it may leak
information about the server overload situation and client traffic
patterns.


# IANA Considerations {#iana}

IANA is requested to register the following Response Code in the "CoRE
Parameters Registry", "CoAP Response Codes" sub-registry:

* Response Code: 4.29

* Description: Too Many Requests

* Reference: [[This document]]



# Acknowledgements {#acks}

This Response Code definition was originally part of the "Publish-
Subscribe Broker for CoAP" document {{I-D.ietf-core-coap-pubsub}}.
Author would like to thank Abhijan Bhattacharyya, Carsten Bormann,
Daniel Migault, Gyorgy Rethy, Jana Iyengar, Jim Schaad, Klaus Hartke,
Mohit Sethi, and Sandor Katona for their contributions and reviews.


--- back

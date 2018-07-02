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
similar purpose and also defines use of the Max-Age option to indicate
a back-off period after which a client can try the request again.

While a server may not be able to response to a one kind of request,
it may be able to respond to a different request, even from the same
client. Therefore the back-off period applies only to similar
requests. For the purpose of this response code, a request is similar
if it has the same method, Request-URI, and payload. Also if a client
is sending a sequence of requests that are part of the same series
(e.g., a set of measurements to be processed by the server) they can
be considered similar even if request payloads or URIs may be
different. Because request similarity is context-dependent, it is up
to the application logic to decide how similar requests should be
suppressed.

The 4.29 code is similar to the 5.03 "Service Unavailable" {{RFC7252}}
code in a way that the 5.03 code can also be used by a server to
signal an overload situation. However the 4.29 code indicates that the
too frequent requests from the requesting client are the reason for
the overload.

# Terminology

The key words 'MUST', 'MUST NOT', 'REQUIRED', 'SHALL', 'SHALL NOT',
'SHOULD', 'SHOULD NOT', 'RECOMMENDED', 'MAY', and 'OPTIONAL' in this
specification are to be interpreted as described in {{RFC2119}}.

Readers should also be familiar with the terms and concepts discussed
in {{RFC7252}}.


# CoAP Server Behavior

If a CoAP server is unable to serve a client that is sending
CoAP request messages more often than the server is capable or willing
to handle, the server SHOULD respond to the request(s) with the
Response Code 4.29, "Too Many Requests". The Max-Age option is used
to indicate the number of seconds after which the server assumes it is
OK for the client to retry the request.

An action result payload (see Section 5.5.1 in {{RFC7252}}) can be
sent by the server to give more guidance to the client, e.g., about
the details of the overload situation.

# CoAP Client Behavior

If a client receives the 4.29 Response Code from a CoAP server to a
request, it SHOULD NOT send similar request to the server before the
time indicated in the Max-Age option has passed.

A client MUST NOT rely on a server being able to send the 4.29
Response Code in an overload situation because an overloaded server
may not be able to reply to all requests at all.


# Security Considerations {#SecurityConsiderations}

Replying to CoAP requests with a Response Code consumes resources from
a server. For a server under attack it may be more appropriate to
simply drop requests without responding.

If a CoAP reply with the Too Many Requests Response Code is not
authenticated and integrity protected, an attacker can attempt to
spoof a reply and make the client wait for an extended period of time
before trying again.


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
Gyorgy Rethy, Klaus Hartke, and Sandor Katona for their contributions
and reviews.


--- back

---
title: "ADEM - Distribution and Discovery over DNS"
abbrev: "ADEM over DNS"
category: info

docname: draft-linker-diem-adem-dns-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: "Applications and Real-Time"
workgroup: "Digital Emblems"
keyword:
venue:
  group: "Digital Emblems"
  type: "Working Group"
  mail: "diem@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/diem"
  github: "adem-wg/adem-dns-spec"
  latest: "https://adem-wg.github.io/adem-dns-spec/draft-linker-diem-adem-dns.html"

author:
 -
    fullname: Felix Linker
    email: linkerfelix@gmail.com

normative:

informative:

...

--- abstract

The ADEM Core Specification defines tokens that mark digital assets as protected under international humanitarian law.
This document defines the DNS `IHLE` resource record for distributing those tokens and CWT-encoded public key material and specifies how authoritative name servers include them in responses concerning the same owner name.


--- middle

# Introduction

The ADEM Core Specification {{!I-D.linker-diem-adem-core}} specifies how a set of *tokens*, encoded using the Concise Binary Object Representation (CBOR) {{!RFC8949}}, can be used as a digital emblem to signal that digital assets enjoy specific protections under International Humanitarian Law (IHL).
This document defines the `IHLE` DNS resource record (RR) for distributing and discovering ADEM tokens and public key material encoded as CBOR Web Tokens (CWTs) {{!RFC8392}}.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

DNS terminology and message fields are used as defined in {{!RFC1035}}.

# The IHLE Resource Record

The International Humanitarian Law Emblem (`IHLE`) DNS resource record (RR) is used to publish an ADEM token or CWT-encoded public key material at a domain name.
The type value for the `IHLE` RR is defined in {{iana-rrtype}}.

The `IHLE` RR is class independent and has no special Time to Live (TTL) requirements.

## IHLE RDATA Wire Format

The RDATA for an `IHLE` RR consists of a single variable-length Token field.
The Token field MUST contain either:

* The exact CBOR serialization of one ADEM token encoded as specified by the ADEM Core Specification {{!I-D.linker-diem-adem-core}}; or
* The exact CBOR serialization of one COSE_Key {{!rfc9052}} containing public key material.

The field consumes all octets indicated by RDLENGTH.
It MUST contain exactly one complete CBOR data item and MUST NOT contain trailing data.

## IHLE RDATA Presentation Format

The presentation format of the RDATA portion MUST represent the Token field as a sequence of case-insensitive hexadecimal digits.
Whitespace is allowed within the hexadecimal text.

## Additional Section Processing

When a name server receives a query whose QNAME is the owner name of an `IHLE` RRset in the QCLASS, it MUST include that RRset in the Additional section of the response, unless the RRset is already included in the Answer section.
This requirement applies regardless of the query's QTYPE.

The server MUST NOT include only a subset of the `IHLE` RRset.
If the transport's size limit prevents the complete RRset from being included, the server MUST set the TC bit; the complete response returned following retry over a transport that permits a larger response MUST include the entire RRset.

## DNSSEC Considerations

ADEM tokens are signed objects.
Zone operators thus SHOULD NOT sign an `IHLE` RRset using DNSSEC.
An `IHLE` RRset MAY nevertheless be signed with DNSSEC, for example, because it occurs in a zone whose operational policy is to sign all RRsets.
DNS software MUST process such signatures according to the normal DNSSEC rules in {{!RFC4033}}, {{!RFC4034}}, and {{!RFC4035}}.
Successful DNSSEC validation of the RRset MUST NOT be treated as successful validation of any ADEM token it contains or as establishing trust in public key material for ADEM validation.

# Security Considerations

TODO


# IANA Considerations

TODO


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.

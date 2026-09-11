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
* The exact CBOR serialization of one CWT {{!RFC8392}} containing public key material.

Public key material MAY be distributed alongside ADEM tokens in the same `IHLE` RRset.
The field consumes all octets indicated by RDLENGTH.
It MUST contain exactly one complete CBOR data item and MUST NOT contain trailing data.

Each ADEM token or CWT containing public key material MUST be carried in a separate `IHLE` RR.
Consequently, an owner name associated with multiple such items has an `IHLE` RRset containing one RR per item.
DNS does not preserve the order of records in an RRset; the `IHLE` RR therefore does not indicate an order among ADEM tokens or CWTs containing public key material.

## IHLE RDATA Presentation Format

The presentation format of the RDATA portion, as visible in a master file, consists of the Token field represented as a sequence of uppercase hexadecimal digits.
Each RDATA octet is represented by exactly two hexadecimal digits, most significant digit first.
The letters `A` through `F` MUST be uppercase.
For readability, whitespace MAY be inserted between octets when the RDATA is enclosed in continuation parentheses as described in {{!RFC1035}}; this whitespace is not part of the Token field.

## Additional Section Processing

When an authoritative name server receives a query whose QNAME is the owner name of an `IHLE` RRset in the QCLASS, it MUST include that RRset in the Additional section of the response, unless the RRset is already included in the Answer section.
This requirement applies regardless of the query's QTYPE.

The server MUST NOT include only a subset of the `IHLE` RRset.
If the transport's size limit prevents the complete RRset from being included, the server MUST set the TC bit; the complete response returned following retry over a transport that permits a larger response MUST include the entire RRset.

## DNSSEC Considerations

ADEM tokens are signed and are validated independently of the DNS channel.
Zone operators SHOULD NOT sign an `IHLE` RRset specifically to establish the authenticity of its tokens, and they SHOULD NOT enable DNSSEC solely for that purpose.

An `IHLE` RRset MAY nevertheless be signed with DNSSEC, for example, because it occurs in a zone whose operational policy is to sign all RRsets.
The presence of an RRSIG covering an `IHLE` RRset is not an error.
DNS software MUST process such signatures according to the normal DNSSEC rules in {{!RFC4033}}, {{!RFC4034}}, and {{!RFC4035}}.
Successful DNSSEC validation of the RRset MUST NOT be treated as successful validation of any ADEM token it contains or as establishing trust in public key material for ADEM validation.

# Security Considerations

The presence of an `IHLE` RR does not establish that its ADEM token is valid, that its public key material is trusted, or that the owner name is protected under IHL.
Validators MUST validate each ADEM token as specified by the ADEM Core Specification {{!I-D.linker-diem-adem-core}}.
Public key material obtained from an `IHLE` RR MAY be used to verify token signatures, but validators MUST independently establish the key's identifier and trust as required by the ADEM Core Specification.

An attacker able to alter DNS responses can add, remove, or replay `IHLE` records.
Token signatures and the validation procedure in the ADEM Core Specification detect invalid tokens, while token validity periods limit replay; they do not prevent removal of records.
DNSSEC can provide origin authentication and integrity for the RRset, but, as described in {{dnssec-considerations}}, does not replace ADEM token validation.

Automatically adding an `IHLE` RRset can increase the size of DNS responses and their usefulness in reflection attacks.
Authoritative name servers implementing this specification SHOULD employ the same amplification mitigations they apply to other large DNS responses.


# IANA Considerations

## IHLE RR Type {#iana-rrtype}

IANA is requested to allocate a code from the "Resource Record (RR) TYPEs" subregistry of the "Domain Name System (DNS) Parameters" registry, as follows:

| TYPE | Value | Meaning | Reference |
| --- | --- | --- | --- |
| IHLE | TBD1 | International Humanitarian Law Emblem token or CWT-encoded public key material | This document |


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.

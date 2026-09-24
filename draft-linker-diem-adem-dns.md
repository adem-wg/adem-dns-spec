---
title: "An Authentic Digital, Distinctive EMblem (ADEM) - Distribution and Discovery over the DNS"
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

The ADEM Core Specification specifies the message format and authorization model for digital, distinctive emblems.
This document specifies how such emblems can be used to mark domain names and how they can be conveyed over the DNS.

--- middle

# Introduction

{{!I-D.linker-diem-adem-core}} specifies the message format and authorization model of digital emblems recognized under International Humanitarian Law (IHL); in particular, it specifies the use of CBOR Web Tokens (CWTs) {{!RFC8392}} and COSE_Key structures {{!RFC9052}} for digital, distinctive emblems.
This draft specifies how such emblems can mark fully qualified domain names (FQDNs) using the DNS and the new `IHLE` DNS resource record (RR) type.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

DNS terminology and message fields are used as defined in {{!RFC1035}}.

# The IHLE Resource Record

The `IHLE` (International Humanitarian Law Emblem) DNS RR type is used to mark an FQDN with a digital, distinctive emblem {{!I-D.linker-diem-adem-core}}.
The type value for the `IHLE` RR is TODO.

## IHLE RDATA Wire Format

The RDATA for an `IHLE` RR consists of a single variable-length Token field.
The Token field MUST contain a CWT that either encodes an ADEM token or a COSE_Key structure containing ADEM-related public key material as specified in {{!I-D.linker-diem-adem-core}}.
The field consumes all octets indicated by RDLENGTH.
It MUST contain exactly one complete CBOR data item and MUST NOT contain trailing data.

When an `IHLE` RR contains an emblem, the emblem's `assets` claim MUST be an array (major type 4) of UTF-8 strings (major type 3).
Each string in this array MUST be an FQDN {{!RFC9499}}.
Relative names, i.e., without a terminating empty label, are permitted and MUST be interpreted relative to the empty root label.

## IHLE RDATA Presentation Format

The presentation format of the RDATA portion MUST represent the Token field as a sequence of case-insensitive hexadecimal digits.
Whitespace is allowed within the hexadecimal text.

## Additional Section Processing

When a name server receives a query whose QNAME is the owner name of an `IHLE` RRset in the QCLASS, it MUST include that RRset in the Additional section of the response, unless the RRset is already included in the Answer section.
This requirement applies regardless of the query's QTYPE.

The server MUST NOT include only a subset of the `IHLE` RRset.
If the transport's size limit prevents the complete RRset from being included, the server MUST set the TC bit; the complete response returned following retry over a transport that permits a larger response MUST include the entire RRset.

## DNSSEC Considerations

ADEM tokens already are signed objects.
Thus, signing `IHLE` RRsets using DNSSEC is optional.
An `IHLE` RRset may be signed with DNSSEC, for example, because it is part of a signed zone.
Successful DNSSEC validation of the RRset, though, MUST NOT be treated as successful validation of any ADEM token it contains or as establishing trust in public key material for ADEM validation.

# Discovering and Validating IHLE Emblems

For distribution over the DNS, assets are identified by FQDNs.
To retrieve an asset's digital emblems, validators can perform a general DNS lookup, as specified in {{!RFC1034}}, with the asset's FQDN as QNAME, an arbitrary QTYPE, and the QCLASS `IN`.
When validators receive a set of tokens and public keys over the DNS, they validate it as follows:

1. Validate the set of tokens and public keys according to {{!I-D.linker-diem-adem-core}}.
2. If the validation procedure returns a result other than `INVALID`, verify that the queried FQDN occurs in the emblem's `assets` claim value using simple, case-insensitive string comparison.

# Semantics of IHLE Emblems

A valid emblem that marks an FQDN signals that this domain name and services associated with this domain name are used for purposes protected under IHL.
It signals that the domain name and services associated with that domain name should be respected, protected, and not disrupted.
Intuitively speaking, the emblem has the function of a stop sign and signals that one should not use a domain name as part of an operation to disrupt services.

The emblem does not signal that systems which are identified by the domain name, e.g., via `A` or `AAAA` records, enjoy the same specific protections as the domain name itself.
For example, it could be that a domain name has an `A` record which contains an IP address that routes to a multi-tenant database server, and tenants are identified by the domain name.
That database server requires queries to include the domain of the tenant and, by itself, may not be protected.
Nevertheless, if one were to only discover this database server via a domain name that is marked with an emblem, there should be no reason to disrupt that database server.

# Security Considerations

TODO


# IANA Considerations

TODO


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.

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
 - next generation
 - unicorn
 - sparkling distributed ledger
venue:
  group: "Digital Emblems"
  type: "Working Group"
  mail: "diem@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/diem"
  github: "felixlinker/adem-dns-spec"
  latest: "https://felixlinker.github.io/adem-dns-spec/draft-linker-diem-adem-dns.html"

author:
 -
    fullname: Felix Linker
    email: linkerfelix@gmail.com

normative:

informative:

...

--- abstract

TODO Abstract


--- middle

# Introduction

The ADEM Core Specification specifies how a set of *tokens*, encoded as JSON Web Signatures (JWSs) {{?RFC7515}}, can be used as a digital emblem to signal that digital assets enjoy specific protections under International Humanitarian Law (IHL).
This document describes a DNS-based distribution and discovery for ADEM tokens.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# DNS Distribution

Given a set of tokens containing exactly one emblem and zero or more associated endorsements, issuers can distribute this set via DNS TXT records {{!RFC1035}}, as follows.

For each such set, issuers MAY choose a unique *identifier* string.
If multiple sets of tokens are associated with a given domain name, issuers SHOULD choose such a string.

Each token is distributed as its own TXT record, which includes a key and a value.
The value encodes the token in JWT compact serialization.
To prevent redundancy and keep tokens short, issuers MAY provide verification keys using TXT records as well instead of encoding them in the headers or bodies of tokens.
In that case, the value of the record MUST be a public key in base64 ASN.1 encoding, i.e., the key's `SubjectPublicKeyInfo` as per {{!RFC5280}}, Section 4.1, in base64 encoding as per {{!RFC4648}}.

Each record's key MUST be formatted as:

~~~~
key := type [ "-" identifier ] | "adem-key"

type := "adem-emb" | "adem-end"

identifier := CHARACTER-NO-HYPEN+

record := key "=" value
~~~~

`CHARACTER-NO-HYPEN` is any printable ASCII character as specified in {{!RFC0020}} except for `"-"`.
`DIGIT` is the range of ASCII characters `"0"` to `"9"`.
`type` MUST coincide with the token's "cty" claim.
If present, `identifier` MUST coincide with the string identifying the token's set.

# DNS Querying

To effectively query a DNS record for emblems, verifiers need to distinguish different sets of tokens.
In this section, we detail how to extract all a domain name's associated sets of tokens.

First, a verifier MUST resolve a domain name's TXT records.

Second, for each record, starting with one of the values of `type`, the verifier parses the record according to the above.
Records for which parsing fails MUST be ignored.

Third, for each emblem identified in the previous step, the verifier assembles a set of tokens and verification keys.
Any token which either has the same identifier as the emblem, or has no identifier MUST be considered as belonging to that emblem's set of tokens.
Any key included in TXT record MUST be considered for verification purposes.

Finally, for each of these sets, the verifier can proceed to verify them as specified in.

# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.

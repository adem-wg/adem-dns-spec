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

Each record's key MUST be formatted as:

~~~~
key := "adem" [ "-" identifier ]

identifier := CHARACTER-NO-HYPEN+

record := key "=" value
~~~~

`CHARACTER-NO-HYPEN` is any printable ASCII character as specified in {{!RFC0020}} except for `"-"`.
If present, `identifier` MUST coincide with the string identifying the token's set.

# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.

# Static Data
To verify the data that has been created, you can navigate to your FTP site and view the list of published files.

# Source
In the `STATIC_DATA` folder, you will find a `rulebook.json` file:

```json
{
  "rulebookId" : "urn:rulebook:29a655983776d9cd7b4be696ed4cd773e63e6d640241e05c3a40b5d81f5d1f1c",
  "description" : {
    "name" : "Trustworthy-Sources-Rulebook",
    "simpleDescriptionEN" : "New sources can be included in the network whitelist by following the standards for selecting and evaluating Advocates.",
    "production" : false
  },
  "rules" : [ {
    "id" : "urn:rule:0:protected:f291700cfcf950552b1625f2f0023fd3bee9c2f1d8d64887ac5301575d23845b:b5ad98c66835bb37",
    "description" : "Host rulebooks that offer _[0]protections_ to consumers, without unnecessarily limiting the _[1]freedoms_ of producers.",
    "interpretations" : [ {
      "modifier" : "public",
      "definition" : "protections"
    }, {
      "modifier" : "public",
      "definition" : "freedoms"
    } ]
  }, {
    "id" : "urn:rule:1:protected:079f08418197576e015964fe1f1f97bef2960a3c9bd222ee76de3f0b10ec60b9:b5ad98c66835bb37",
    "description" : "List only _[0]honest_ Advocates with respect to the scope of the rulebook.",
    "interpretations" : [ {
      "modifier" : "public",
      "definition" : "honest"
    } ]
  }, {
    "id" : "urn:rule:2:public:10a7650e29c4e6a0cc9f8675571005769491d076960755b482d555d8ea181be3:b5ad98c66835bb37",
    "description" : "Only implement rulebooks _[0]with a narrow scope of applicability to a Producer's online activities_.",
    "interpretations" : [ {
      "modifier" : "public",
      "definition" : "with a narrow scope of applicability to a Producer's online activities"
    } ]
  }, {
    "id" : "urn:rule:3:protected:c8e3cb518192cf88aa11cf0d93072730c9e33c2713f187e1e2e402d8ad6f51ff:b5ad98c66835bb37",
    "description" : "Restrict the possibilities of malicious actors to _[0]manipulate the rules for their own benefit_ through interpretations.",
    "interpretations" : [ {
      "modifier" : "public",
      "definition" : "manipulate the rules for their own benefit"
    } ]
  } ],
  "ruleExtensions" : [ ],
  "acceptsSybilClasses" : [ "all" ],
  "penalties" : {
    "accountabilityTypes" : [ "test-net" ]
  }
```

Before a Source or an Advocate is utilized, their static data is inspected. If the information is found to be invalid, any tokens produced under it will fail to verify. Additionally, if a node is corrupted, it is not considered part of the Trust Network.

In the `STATIC_DATA/x-source` folder you will find the following files: 

| File Name                                     | Description                                                            |
|-----------------------------------------------|------------------------------------------------------------------------|
| signatures.xml                                | Binds the files together and ensures secure modifications.             |
| `<rulebook-hash>`.c.xml                       | `CredentialSpecification` for the rulebook.                             |
| `<org-name>-<source-name>`.`<rulebook-hash>`.ni.xml | `TrustNetwork` file defining the location of this node's resources.      |
| `<org-name>-<source-name>`.`<rulebook-hash>`.pp.xml | `PresentationPolicy` file identifying trusted Advocates for this Source. |

> `CredentialSpecification` can be fully defined from inspecting the rulebook.json file and is here for convenience.

### Credential Specification
```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<abc:CredentialSpecification xmlns:abc="http://abc4trust.eu/wp2/abcschemav1.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:idmx="http://zurich.ibm.com" xmlns:xs="http://www.w3.org/2001/XMLSchema" Version="1.0" KeyBinding="true" Revocable="true">
    <abc:SpecificationUID>urn:rulebook:29a655983776d9cd7b4be696ed4cd773e63e6d640241e05c3a40b5d81f5d1f1c:c</abc:SpecificationUID>
    <abc:AttributeDescriptions MaxLength="256">
        <abc:AttributeDescription Type="http://abc4trust.eu/wp2/abcschemav1.0/revocationhandle" DataType="xs:integer" Encoding="urn:abc4trust:1.0:encoding:integer:unsigned"/>
    </abc:AttributeDescriptions>
</abc:CredentialSpecification>
```

### Node Information
```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<exonym:TrustNetwork xmlns:exonym="urn:exonym:rulebookschema1.0">
    <exonym:NodeLastUpdatedUTC>2023-05-15T09:26:39Z</exonym:NodeLastUpdatedUTC>
    <exonym:NodeInformation>
        <exonym:BroadcastAddress>computer-name:2024</exonym:BroadcastAddress>
        <exonym:NodeName>unique</exonym:NodeName>
        <exonym:NodeUID>urn:rulebook:unique:29a655983776d9cd7b4be696ed4cd773e63e6d640241e05c3a40b5d81f5d1f1c</exonym:NodeUID>
        <exonym:Jurisdiction>DE</exonym:Jurisdiction>
        <exonym:RulebookNodeURL>http://computer-name:8080</exonym:RulebookNodeURL>
        <exonym:SourceUID>urn:rulebook:unique:29a655983776d9cd7b4be696ed4cd773e63e6d640241e05c3a40b5d81f5d1f1c</exonym:SourceUID>
        <exonym:StaticNodeURL0>https://trust.exonym.io/nu3/unique/x-source</exonym:StaticNodeURL0>
        <exonym:StaticNodeURL1>https://exonym.io/nu3/unique/x-source</exonym:StaticNodeURL1>
        <exonym:StaticSourceURL0>https://trust.exonym.io/nu3/unique/x-source</exonym:StaticSourceURL0>
        <exonym:StaticSourceURL1>https://exonym.io/nu3/unique/x-source</exonym:StaticSourceURL1>
    </exonym:NodeInformation>
    <exonym:NodeInformationUID>urn:rulebook:unique:29a655983776d9cd7b4be696ed4cd773e63e6d640241e05c3a40b5d81f5d1f1c:ni</exonym:NodeInformationUID>
</exonym:TrustNetwork>
```

### Presentation Policy

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<abc:PresentationPolicy xmlns:abc="http://abc4trust.eu/wp2/abcschemav1.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:idmx="http://zurich.ibm.com" xmlns:xs="http://www.w3.org/2001/XMLSchema" PolicyUID="urn:rulebook:unique:29a655983776d9cd7b4be696ed4cd773e63e6d640241e05c3a40b5d81f5d1f1c:pp">
    <abc:Pseudonym Exclusive="false" Scope="urn:io:exonym" Alias="urn:io:exonym"/>
</abc:PresentationPolicy>
```


### Signatures.xml
Binds the files together and ensures secure modifications.
```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<exonym:KeyContainer xmlns:exonym="urn:exonym:rulebookschema1.0">
    <exonym:KeyPairs>
        <type>xkey</type>
        <exonym:KeyUID>urn:keys:rsa:trust-network-root</exonym:KeyUID>
        <exonym:PublicKey>MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA4+gnq1d+JlJtdTSXHtjkg1pfbz00fylDteQF8aCUmG2OdebjUq1IcoV3gmaoeh2UXUhCJLT3djENhHwxdSrcLlEdFxTAXZsh1OZN1JW+LngWpDc4eHy4eyuHwLMwo48u4kjPcZAbALB7MojHXl/W9sOc2NB4c+vkoicPX5ys7MVdgAdROYYOekZV3lBcgkDBJICmU6Jxk+HbigHzqrFwTqsHQMkuL/d17p3/sBnLiq4x3UMtLv65LILALQZyw8Mpa22Uc8MwaBHFujepoXu9E2BU18h3xiFN7s4tl2b/jwy+Vm1ZN4tgI2mPydUQJwFut1E+VzNdgnkWleQHmd+4mQIDAQAB</exonym:PublicKey>
        <exonym:Signature>XDhTJiy3sok2kxsMMD4UVpecKjXFROufXo5UTjZlIlgGa+IatLzIHuoEgbek8poZ1aF+9JnNzdcklKdVkkcu7NbMjZMhmmZNHn9dq12j6cZvJbLGcDotxWvBXq1Jdvziso71OCOnciIXNoGmNHJvRp/fFbt1QCD+AmmOGHG1rR9l7nDKHruNMuWUepN7HOa7oaHM7sDUB/i5w07RofHupSWcK8Gg/r93p1Izgk17WO2Ei5vrckxDmDeTJrW1h5gwl8BsQFKmmM602jM7Xn4j2UTuwL5UxVwAsutWQzypyLRfZAjWBZFeB83l0ft6Pgzom65n3+/x8aG+MomDlJGsXA==</exonym:Signature>
    </exonym:KeyPairs>
    <exonym:KeyPairs>
        <type>xkey</type>
        <exonym:KeyUID>urn:rulebook:unique:29a655983776d9cd7b4be696ed4cd773e63e6d640241e05c3a40b5d81f5d1f1c:pp</exonym:KeyUID>
        <exonym:Signature>bpPITGvVFi9rzsrdvx/T8OgsHouZ0c46l+kERJ1Q0yRFEqk7cBEF9wXqURogL5iUZaEoS1RLh57KgDbM6OqWttryCMPX4gZuju3uw3+xEO4JgUyCXA8aAOjIfi5DR3IOTrjfkQlV9+n8YMHo71xjFqffJ5yTUzC+I/lkQD89QDojVHTziYW3dhSAwYMiZQ1vd5iwjfwWDBrBfvsGVPiDP+EnngY8qsrQKvufeHz38sJTwEIblgDhdoBA1+QJn1ZPFCZ4H6myNLMuwYOmm4/Gc93yyMu9LbNkegh/fh7dBkLfHv0ebf6nBoDLRfBn8JRhplqXHNnAwsxJwbSiakMVRA==</exonym:Signature>
    </exonym:KeyPairs>
    <exonym:KeyPairs>
        <type>xkey</type>
        <exonym:KeyUID>urn:rulebook:unique:29a655983776d9cd7b4be696ed4cd773e63e6d640241e05c3a40b5d81f5d1f1c:ni</exonym:KeyUID>
        <exonym:Signature>svi8Z5GpqmObT2SN3huADzMhVF2qllPGE4GDdvPy/Uz7KC8Or0GkKXr+z2PCrJXbrt9Jm208yQbJJukSjvFK3Lu6tySoy0R0DWf7U84Fdwm/+C+2DCCjqwgT+v2FalSS30Pe09pXGuccSGZdl2feP7aiCAuz0LqGMLGwdKDG2/woT0WJ1Te2YTLrnveN9tGlAjginy0UqU5TBZnOQ/cuwwcXTAJ2l0T1kRPNULnA32iWtsxtbm9/Hh9FAJRXej7VwqoNG4Sh+CpITyKMXTdx5DtYjOJpNTI/XPYMMi0p0jDgQybhuzyCVDqc3RqvEMPzOQcRe+fY7IZjs/ynqXJoQw==</exonym:Signature>
    </exonym:KeyPairs>
    <exonym:KeyPairs>
        <type>xkey</type>
        <exonym:KeyUID>urn:rulebook:29a655983776d9cd7b4be696ed4cd773e63e6d640241e05c3a40b5d81f5d1f1c:c</exonym:KeyUID>
        <exonym:Signature>e2ez6O43ay0qtSiinAwKhb2Gmbe0S+gF70WHWMnvLAo5XmwxpCCXKLBxNjNA8cZXesd3+FR1mccEFZUUImKmTMRo9p2ZEdVO3dKk3i4Vq3ffCEV3Ty78az7RAcbi6+fHk0FEAQeTBodQ5nbn9sTwgplp2sXwhccl5iYItyttz0u5fxT4/+cAhVB8Tr7lr7iTbTT8yApxio5EpfuYCT2fzSAtqwyAgsmpUaH7ti33tzvoi6hGg4m0Yfeu41UrgZajoH/9YXU2MzLQ++A4c/44AW1JRbeckHXAdnr8RgIQ8bWsMiRRdw1VyguYceu7f/wgir66CJL8+wzwiEKI56AmbA==</exonym:Signature>
    </exonym:KeyPairs>
    <exonym:KeyPairs>
        <type>xkey</type>
        <exonym:KeyUID>urn:sig-checksum</exonym:KeyUID>
        <exonym:Signature>cOW1pV/LurwQ1WjP9gS5ETqremqjEuFn/VniIr7zOJqRQRu65NoE9lNVwXqS4VoQpkexPEuCH37v0HnjKVq0HXpQtFmalwVfpwOh2hBgHv/ylG2vjU8A2VZFF58mNp/mDo0MAFLlmFP0/6DTawAS/HDGzo/tidv14oeK9LEymwMQom2fcpG9SwxiJlCUHLGz+KElo/AtJe18T4O+jsbuxHZsWHKCA+gVq3XrNA1mqdpGSkUSTJfHiYuYuiqHWyPo3TEHFMm82k2nPSZy6n6Y9tTCu17EduSb+T3ZJ6KRuAd4h80GbNTjVnC1BzV5/zJSXe3O2ki3P2fSob9BX0ou4Q==</exonym:Signature>
    </exonym:KeyPairs>
    <lastUpdateTime>2023-05-15T09:26:39Z</lastUpdateTime>
</exonym:KeyContainer>
```

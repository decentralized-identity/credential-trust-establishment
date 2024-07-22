# Credential Trust Establishment 1.0

**Specification Status:** Working Group Draft

**Latest Draft:**
[identity.foundation/credential-trust-establishment](https://identity.foundation/credential-trust-establishment)

Editors:
~ [Mike Ebert](https://www.linkedin.com/in/michaelebert/)
~ [Sam Curren](https://github.com/TelegramSam/)

Contributors:
~ [Simon Nazarenko](https://github.com/ElderSonar/)

Participate:
~ [GitHub repo](https://github.com/decentralized-identity/credential-trust-establishment)
~ [File a bug](https://github.com/decentralized-identity/credential-trust-establishment/issues)
~ [Commit history](https://github.com/decentralized-identity/credential-trust-establishment/commits/master)

---

## Introduction

Every use of Verifiable Credentials must evaluate the trustability of presented credentials. In addition to verifying the cryptographic signatures and proofs, it is vital to check the authority of a credential's issuer.

Solving this problem is the focus of this specification - establishing the roles and authority of participants with an ecosystem. It answers the fundamental question: Should I trust the issuer of this credential?

This document is created, signed, and published by an ecosystem authority. This specification does not indicate qualification to be an authority. Authority is obtained through the recognition of such by ecosystem participants either by agreement, contract, law, or some other method.

Using the information within a document published according to this specification allows for the identification and evaluation of credential issuers. The format is lightweight, supports ecosystems both large and small, supports offline verification capabilities, and is very low cost.

This specification incorporates the [Trust Establishment](https://identity.foundation/trust-establishment/) specification for the enumeration of ecosytem participants.

## Requirements of Ecosystem Participants

This data model is the expression about trust from the ecosystem authority, who creates, signs, and publishes the document. Any information in the document is assumed to be the carefully selected choices of the authority, including any schemas, participants, and linked governance files. Publishing the file itself places no requirement on any other participant to read or respect the opinions of the file.

The published file is read by ecosystem participants to understand the opinion of the publisher. Compliance with the format places no requirement for participants to respect or follow the same opinions. Participants may choose to respect only a portion of the published governance. Ecosystem factors outside the scope of this format may influence participant behavior.

## Format Overview

The following sections cover what is required to enable Credential Trust Establishment:

- Metadata
- Schemas
- Roles
- Participants

The appendix contains several full examples, complete with explanation and sample file.

## Governance File Metadata

As with Trust Establishment, it makes sense to add some meta data to the governance file to assist those who are using the file. See (Linked Governance)[#]

The full University Diploma example used in this document is included as the first sample listed in the Appendix.

**id**: REQUIRED. String value that uniquely identifies the governance document with the scope of the `author`. This string MUST remain consistent across versions. Those processing the document MUST consider the value to an opaque value, and no information may be inferred by inspection of the string.

**name**: REQUIRED. User oriented title of this document.

**description**: OPTIONAL. User oriented description of this document. Usually a more informative description than the name alone.

**version**: REQUIRED. Version string of this document. Must follow SemVer OR be lexographicly increasing version strings.

**format**: REQUIRED. Version of this data type. Current version is "1.0"

**last_updated**: REQUIRED. ISO 8601 string when this document was published.

**author**: REQUIRED. DID of the party publishing this document.

**docs_uri**: OPTIONAL. URI for human-oriented documentation for this governance.

**ttl**: OPTIONAL. Expected length of time this version of the document is expected to be valid. This suggests to consumers of this document the interval at which it should be checked for updates.

**trusted_governance** (optional): Contains a list of publisher DIDs and document URIs for goverance files trusted by the author of THIS governance file. Upon retrieval, the document must be signed by the stated publisher to be considered valid. The provided URI may link to the general goverance URI, or to a specific version of that governance. See the [Versioning](#versioning) section for more details on URIs of specific versions.

```json
{
  "@context": [
    "https://identity.foundation/credential-trust-establishment/context.jsonld"
  ],
  "id": "c64846d1-cf60-4ac5-835e-cbd25569f2fa",
  "name": "University Degree Governance",
  "description": "This document describes the governance for issuing accredited university degrees in a machine readable way",
  "version": "1.0",
  "format": "1.0",
  "last_updated": "2022-04-20T04:20:00Z",
  "author": "did:example:usdepartmentofeducation",
  "docs_uri": "https://url-for-docs...",
  "ttl": 86400,
  "trusted_governance": [
    {
      "publisher": "did:example:publisher",
      "uri": "https://example.com/other/trusted/goveranance"
    }
  ]
}
```

### Trusted Governance

Trusted governance links provide a flexible mechanism for governing larger ecosystems. Higher authorities can bundle the governance of smaller authorities together. Lower authorities can reference higher authorities. The result is a straightforward mechanism for practical ecosystem governance.

Trusted governance links allow for governance chains to exist - the inclusion of an external governance file allows that governance to itself reference other governance files. While in theory this allows for infinitely long chains, in practice the requirement for the publisher to trust linked files places a reasonable limit. Also see [Requirements of Ecosystem Participants](#requirements-of-ecosystem-participants) for further commentary on the responsibilities of the publisher and reader.

### Versioning

Versioning is a way to allow users of decentralized ecosystem governance to track/use the current version of published governance or track/use older version of the file.

The version number of the current file MUST be an integer. The version number MUST increase for each published version.
Example:

```json
{
  "version": 1
}
```

The "current_version" field contains a URL pointing to the most recently published file. The specific file name at this location is determined based on the current implementation. Example:

```json
{
  "current_version": "https://example.com/path/to/cte/1.json"
}
```

The base uri can point to a file with any file name of your choice as long as this is consistent within the ecosystem you are working with. Example:

```json
{
  "uri": "https://example.com/path/to/cte/file.json"
}
```

Previous versions are an array of objects represented by the version and uri pointing to the unique location of previously published file. Example:

```json
{
  "current_version": "https://example.com/path/to/cte/2.json",
  "previous_versions": [
    {
      "version": 1,
      "uri": "https://example.com/path/to/cte/1.json"
    }
  ]
}
```

Full example of versioning information:

```json
{
  "version": 2,
  "uri": "https://example.com/path/to/cte/file.json",
  "current_version": "https://example.com/path/to/cte/2.json",
  "previous_versions": [
    {
      "version": 1,
      "uri": "https://example.com/path/to/cte/1.json"
    }
  ]
}
```

Publishing a new version of the document requires creating, signing, and hosting two different files: the file specific to the version, and an updated file for the general URI. The file is identical, but is hosted at two different URIs.

## Schemas

The schemas section of the format specifies the schemas used in an ecosystem. Each list item describes a single schema.

Each schema is described with the following attributes:

- **id**: the identifier of the schema. This identifier must be URI that links to the schema definition. This identifier is used to refer to this schema elsewhere in the data model. This identifier is not intended to be visible during normal use.
- **name**: The human readable name for this credential. This name should be used to refer to this credential in any user interfaces.

Here is an example for a U.S.-based higher education ecosystem:

```json
...
	"schemas": [
		{
			"id": "uri:example:PdiVKGAjdiVKGAjLqtTroc:2:Accrediting_Body:1.0",
			"name": "Accrediting Body",
		},
		{
			"id": "uri:example:BXtzYPyPdiVKGAjLqtPexs:2:University_Degree_Issuer:1.0",
			"name": "University Degree Issuer",
		},
		{
			"id": "uri:example:QHqtjywxfZ3yYsFrRHFLQm:2:Bachelors_Degree:1.0",
			"name": "Bachelors Degree",
		},
		{
			"id": "uri:example:JAjLqtPexs3yYsFrRHFLQm:2:Student_ID:1.0",
			"name": "Student ID",
		}
	],
...
```

## Roles

Roles define the different types of activities that are present in the ecosystem.

Each role is represented in a struct, where the key is the name of the role, and the value is a struct with values defining the role. The name of the role is not intended to be displayed, but used as a reference elsewhere in the data structure. Any names may be chosen for the convenience of governance maintanance. The role name must consist of alphanumeric characters, dashes, and underscores.

Roles are described with the following attributes:

- **issue**: This is the list of the Schema IDs that this role is authorized to issue. The Schemas MUST be present in the schema section of the document.
- **verify**: This is the list of the Schema IDs that this role is authorized to verify. The Schemas MUST be present in the schema section of the document.
- **granted_by**: This, if present, specifies the Schema ID that, upon presentation, qualifies the subject to have this role. Any Schema specified here MUST be defined in the Schemas section of the document and approved verifiers specified via a role defined in the Roles section.

Roles may omit any attributes that do not apply.

This example shows potential roles used in a university diploma ecosystem application.

```json
...
	"roles": {
		"accrediting_authorizer": {
			"issue": [
				"uri:example:PdiVKGAjdiVKGAjLqtTroc:2:Accrediting_Body:1.0"
			]
		},
		"university_accreditor": {
			"issue": [
				"uri:example:BXtzYPyPdiVKGAjLqtPexs:2:University_Degree_Issuer:1.0"
			],
			"granted_by": "uri:example:PdiVKGAjdiVKGAjLqtTroc:2:Accrediting_Body:1.0"
		},
		"verify_student_id": {
			"verify": [
				"uri:example:JAjLqtPexs3yYsFrRHFLQm:2:Student_ID:1.0"
			],
			"granted_by": "uri:example:BXtzYPyPdiVKGAjLqtPexs:2:University_Degree_Issuer:1.0"
		},
		"issue_bachelors_degree": {
			"issue": [
				"uri:example:Lor8tASDc74EA268Jvc8J2:2:Bachelors_Degree:1.0"
			],
			"granted_by": "uri:example:BXtzYPyPdiVKGAjLqtPexs:2:University_Degree_Issuer:1.0"
		},
	}
...
```

## Participants

The participants section of the document lists explicitly identified participants. There MUST be at least one participant. Most larger ecosystems will have more.
Participants are described using the Trust Establishment specification, which allows statements to be made about a DID according to a schema. The following Schemas are officially recognized for use within this spec. Additional schemas may be specified by a profile of this specification. Unknown schemas MUST be ignored.

- Roles
  - Required
- Description
  - Recommended
- Aliases
  - Optional

### Linking Participants to Roles

Once schemas are listed and roles are described, they need to be linked to the trusted participants. This is done by adding a new section to the Trust Establishment section of the document.

The new section contains the enumerated sections of roles for a participant, along with the time range the role applies.

**start**: REQUIRED. The ISO 8601 encoded date and time of when the role was assigned to the participant.
**end**: OPTIONAL. The ISO 8601 encoded date and time of when the role was removed from the participant. This should not be present if the end date is not known.
**role**: REQUIRED. The name of the role, used within the roles section of this document.

```json
...
			"https://identity.foundation/credential-trust-establishment/schemas/roles.json": {
				"did:example:usdepartmentofeducation": {
					"roles": [
						{
							"start": "2023-02-25 00:00:00Z",
							"role": "accrediting_authorizer"
						}
					]
				},
				"did:example:nwccu": {
					"roles": [
						{
							"start": "2023-02-25 00:00:00Z",
							"role": "university_accreditor"
						}
					]
				},
				"did:example:faberuniversity": {
					"roles": [
						{
							"start": "2023-02-25 00:00:00Z",
							"role": "verify_student_id"
						},{
							"start": "2020-01-01 00:00:00Z",
							"end": "2021-01-01 00:00:00Z",
							"role": "issue_bachelors_degree"
						},{
							"start": "2022-01-01 00:00:00Z",
							"role": "issue_bachelors_degree"
						}
					]
				}
			}
...
```

### Description

This schema provides additional information for each identifier in the ecosystem. This is helpful for bootstrapping ecosystems without a reliable external system of participant identity. The following example shows names, email addresses, and the associated website for each participant.

```json
...
      "https://identity.foundation/credential-trust-establishment/schemas/description.json": {
        "did:example:usdepartmentofeducation": {
          "name": "U.S. Department of Education",
          "website": "https://www.ed.gov/accreditation",
          "email": "accreditation@www.ed.gov"
        },
        "did:example:nwccu": {
          "name": "Northwest Commission on Colleges and Universities",
          "website": "http://www.nwccu.org/",
          "email": "accrediting@nwccu.org"
        },
        "did:example:faberuniversity": {
          "name": "Faber University",
          "website": "https://faber.example.com/",
          "email": "graduation@faber.example.com"
        }
      },
...
```

### Aliases

This schema allows including alternate identifiers for ecosystem participants. This helps facilitate adjustments to underlying infrastructure for participant DIDs. Each DID listed should be considered to be a fully equivalent DID to the main identifier used in the document.

```json
...
      "https://identity.foundation/credential-trust-establishment/schemas/alias.json": {
        "did:example:usdepartmentofeducation": {
          "alias_dids": [
            "did:example:otherdid_a",
            "did:example:otherdid_b"
          ]
        }
      }

...
```

## Signing and Publishing

The details of signing and publishing of the governance file are not contained within this specification. Use of this specification must be accompanied by a profile of containing the specifics of both signing and publishing.

Profiles must indicate the following things:

### Method of signing

The document MUST be signed with a key associated with the `author` of the document. The profile specifies what signing method and format is to be use within ecosystems that use the profile.

### Hosting

The profile specifies any restrictions on where the document is hosted. Documents MUST be hosted someplace with a URI. Access to the URI may be public, or may require authentication or access control of some kind. The profile must indicate any included methods of access control and how to gain access.

# Example Interop Profile

This section describes an example interoperability profile for this specification. It contains specific examples of choices made for hosting and key types, but these are just included by example. Different choices may be made for another interop profile.

---

This Interop Profile specifies the standards and protocols for seamless interoperability in decentralized identity systems. It defines the technical requirements for digital identity verification, ensuring consistency, security, and compatibility across different platforms and systems. The profile focuses on key aspects such as cryptographic key types, identifier formats, and secure hosting protocols.

## Signing Details

- **Key Types**: `EdDSA`
- **Key Identifiers**: `DID URLs` (e.g., `did:example:123#keys-1`)
- **Signature Envelope**: `JWT`

## Hosting

- **Protocol**: `HTTPS`

---

## Example of the plaintext DEGov file:

```json
{
  "@context": [
    "https://identity.foundation/credential-trust-establishment/context.jsonld"
  ],
  "author": "did:example:Hp6LQzU774ZahkJ27dhqd9",
  "description": "Minimal governance file example",
  "docs_uri": "https://github.com/decentralized-identity/credential-trust-establishment/blob/main/credential-trust-establishment/spec.md",
  "format": "1.0",
  "id": "4cb546f8-4eae-4235-a971-28e08aad5621",
  "last_updated": 1703187439,
  "name": "CTE Governance",
  "version": "1",
  "uri": "",
  "current_version": "",
  "previous_versions": [],
  "schemas": [
    {
      "id": "Q7CyqfHss9RPK4hjwyZT32:2:User:1.0",
      "name": "User Credential"
    }
  ],
  "participants": {
    "id": "default_participants_uuid",
    "author": "did:example:Hp6LQzU774ZahkJ27dhqd9",
    "created": 1703187439,
    "version": "1",
    "topic": "No topic provided",
    "entries": {
      "https://example.com/roles.schema.json": {},
      "https://example.com/description.schema.json": {}
    }
  },
  "roles": {}
}
```

## Example of the signed DEGov file:

```
"eyJhbGciOiAiRWREU0EiLCAidHlwIjogIkpXVCIsICJraWQiOiAiZGlkOnNvdjpIcDZMUXpVNzc0WmFoa0oyN2RocWQ5I2tleS0xIn0.eyJhdXRob3IiOiAiZGlkOmV4YW1wbGU6SHA2TFF6VTc3NFphaGtKMjdkaHFkOSIsICJkZXNjcmlwdGlvbiI6ICJNaW5pbWFsIGdvdmVybmFuY2UgZmlsZSBleGFtcGxlIiwgImRvY3NfdXJpIjogImh0dHBzOi8vZ2l0aHViLmNvbS9kZWNlbnRyYWxpemVkLWlkZW50aXR5L2NyZWRlbnRpYWwtdHJ1c3QtZXN0YWJsaXNobWVudC9ibG9iL21haW4vY3JlZGVudGlhbC10cnVzdC1lc3RhYmxpc2htZW50L3NwZWMubWQiLCAiZm9ybWF0IjogIjEuMCIsICJpZCI6ICI0Y2I1NDZmOC00ZWFlLTQyMzUtYTk3MS0yOGUwOGFhZDU2MjEiLCAibGFzdF91cGRhdGVkIjogMTcwMzE4NzQzOSwgIm5hbWUiOiAiQ1RFIEdvdmVybmFuY2UiLCAidmVyc2lvbiI6ICIxIiwgInVyaSI6ICIiLCAiY3VycmVudF92ZXJzaW9uIjogIiIsICJwcmV2aW91c192ZXJzaW9ucyI6IFtdLCAic2NoZW1hcyI6IFt7ImlkIjogIlE3Q3lxZkhzczlSUEs0aGp3eVpUMzI6MjpVc2VyOjEuMCIsICJuYW1lIjogIlVzZXIgQ3JlZGVudGlhbCJ9XSwgInBhcnRpY2lwYW50cyI6IHsiaWQiOiAiZGVmYXVsdF9wYXJ0aWNpcGFudHNfdXVpZCIsICJhdXRob3IiOiAiZGlkOmV4YW1wbGU6SHA2TFF6VTc3NFphaGtKMjdkaHFkOSIsICJjcmVhdGVkIjogMTcwMzE4NzQzOSwgInZlcnNpb24iOiAiMSIsICJ0b3BpYyI6ICJObyB0b3BpYyBwcm92aWRlZCIsICJlbnRyaWVzIjogeyJodHRwczovL2V4YW1wbGUuY29tL3JvbGVzLnNjaGVtYS5qc29uIjoge30sICJodHRwczovL2V4YW1wbGUuY29tL2Rlc2NyaXB0aW9uLnNjaGVtYS5qc29uIjoge319fSwgInJvbGVzIjoge319.XimPz7gJ01GYJEjZxXg3RE0TxmvyVgCfEyT8GTA2dPljCI2yAkTKHo-93bYIA47TAETGoD4g4PnLAZX2kao0Aw"
```

Below are three examples of governance files which include sections for schemas, trust establishment lists, roles, and governance file meta data.

### University Diploma Example

The granting and recognition of university diplomas involve a large number of organizations at various tiers.

Fundamentally, the U.S. Department of Education authorizes organizations to be accrediting bodies.
These accrediting bodies review universities and their programs and state whether they are accredited or not.
In turn, accredited universities grant diplomas to their students that are recognized by various companies, organizations, etc.

This governance file includes schemas for accrediting organization, university, and diploma credentials. The role required to issue these credentials is included in the schema metadata.

A sample participant of each type is included in the "participants" section.

The governance below includes roles for the U.S. Department of Education, the accrediting bodies, and universities.
The U.S. Department of Education is granted the role of "accrediting_authorizer" directly in the governance file in the "participants" section.
Accrediting organizations can be granted the role of "university_accreditor" in the file or via the credential indicated by schema ID in the roles section.
Universities can also be granted the role of "verify_student_id" and "issue_bachelors_degree" in the file or via the credential indicated by schema ID in the roles section.
Finally, universities verify student IDs and issue degrees (such as a bachelors degree) to students (holders).

```json
{
  "@context": [
    "https://identity.foundation/credential-trust-establishment/context.jsonld"
  ],
  "id": "c64846d1-cf60-4ac5-835e-cbd25569f2fa",
  "name": "University Degree Governance",
  "version": "1.0",
  "format": "1.0",
  "description": "This document describes the governance for issuing accredited university degrees in a machine readable way",
  "last_updated": "2022-04-20T04:20:00Z",
  "author": "did:example:usdepartmentofeducation",
  "docs_uri": "https://url-for-docs...",
  "ttl": 86400,
  "schemas": [
    {
      "id": "uri:example:PdiVKGAjdiVKGAjLqtTroc:2:Accrediting_Body:1.0",
      "name": "Accrediting Body"
    },
    {
      "id": "uri:example:BXtzYPyPdiVKGAjLqtPexs:2:University_Degree_Issuer:1.0",
      "name": "University Degree Issuer"
    },
    {
      "id": "uri:example:QHqtjywxfZ3yYsFrRHFLQm:2:Bachelors_Degree:1.0",
      "name": "Bachelors Degree"
    },
    {
      "id": "uri:example:JAjLqtPexs3yYsFrRHFLQm:2:Student_ID:1.0",
      "name": "Student ID"
    }
  ],
  "participants": {
    "id": "1e762324-6a45-4f6a-b124-ecb21190fe09",
    "author": "did:example:usdepartmentofeducation",
    "created": "2022-04-20T04:20:00Z",
    "version": 2,
    "entries": {
      "https://identity.foundation/credential-trust-establishment/schemas/description.json": {
        "did:example:usdepartmentofeducation": {
          "name": "U.S. Department of Education",
          "website": "https://www.ed.gov/accreditation",
          "email": "accreditation@www.ed.gov"
        },
        "did:example:nwccu": {
          "name": "Northwest Commission on Colleges and Universities",
          "website": "http://www.nwccu.org/",
          "email": "accrediting@nwccu.org"
        },
        "did:example:faberuniversity": {
          "name": "Faber University",
          "website": "https://faber.example.com/",
          "email": "graduation@faber.example.com"
        }
      },
      "https://identity.foundation/credential-trust-establishment/schemas/roles.json": {
        "did:example:usdepartmentofeducation": {
          "roles": [
            {
              "start": "2023-02-25 00:00:00Z",
              "role": "accrediting_authorizer"
            }
          ]
        },
        "did:example:nwccu": {
          "roles": [
            {
              "start": "2023-02-25 00:00:00Z",
              "role": "university_accreditor"
            }
          ]
        },
        "did:example:faberuniversity": {
          "roles": [
            {
              "start": "2023-02-25 00:00:00Z",
              "role": "verify_student_id"
            },
            {
              "start": "2020-01-01 00:00:00Z",
              "end": "2021-01-01 00:00:00Z",
              "role": "issue_bachelors_degree"
            },
            {
              "start": "2022-01-01 00:00:00Z",
              "role": "issue_bachelors_degree"
            }
          ]
        }
      }
    }
  },
  "roles": {
    "accrediting_authorizer": {
      "issue": ["uri:example:PdiVKGAjdiVKGAjLqtTroc:2:Accrediting_Body:1.0"]
    },
    "university_accreditor": {
      "issue": [
        "uri:example:BXtzYPyPdiVKGAjLqtPexs:2:University_Degree_Issuer:1.0"
      ],
      "granted_by": "uri:example:PdiVKGAjdiVKGAjLqtTroc:2:Accrediting_Body:1.0"
    },
    "verify_student_id": {
      "verify": ["uri:example:JAjLqtPexs3yYsFrRHFLQm:2:Student_ID:1.0"],
      "granted_by": "uri:example:BXtzYPyPdiVKGAjLqtPexs:2:University_Degree_Issuer:1.0"
    },
    "issue_bachelors_degree": {
      "issue": ["uri:example:Lor8tASDc74EA268Jvc8J2:2:Bachelors_Degree:1.0"],
      "granted_by": "uri:example:BXtzYPyPdiVKGAjLqtPexs:2:University_Degree_Issuer:1.0"
    }
  }
}
```

### DIF Membership Example

The DIF regulates which organizations and individuals can be members and participate in DIF activities. Their governance is relatively
simple but could unlock a lot of powerful interactions via named and/or credentialed participants.

There are two schemas involved--one for authorizing individuals and one for authorizing issuers of the individual credentials.

There are three tiers of participants:

1. The DIF which runs the ecosystem and authorizes other parties
2. Member organizations which authorize individuals to represent them
3. Individuals who receive a membership credential from either the organization they represent or from the DIF directly.

The DIF is a single participant and is only included in the governance file. The organization tier can be authorized via explicitly
listed participantes or via a DIF Member Organization credential. Individuals could be listed but are likely to be authorized via
credentials only.

The governance file lists three roles: one for the DIF itself, one for organizations, and one for individuals.

```json
{
	"@context": [
		"https://identity.foundation/credential-trust-establishment/context.jsonld"
	],
	"name": "DIF Membership Governance",
	"version": "1.0",
	"format": "1.1",
	"id": "<uuid>",
	"description": "This document describes the governance of DIF membership in a machine readable way.",
	"last_updated": "2022-10-09",
	"docs_uri": "https://url-for-docs...",
	"schemas": [
		{
			"id": "uri:example:RuuJwd3JMffNwZ43DcJKN1:2:DIF_Member_Organization:1.4",
			"name": "DIF Member Organization"
		},
		{
			"id": "uri:example:4CLG5pU5v294VdkMWxSByu:2:DIF_Member_Individual:1.0",
			"name": "DIF Individual Member"
		}
	],
	"participants": {
		"id": "32f54163-7166-48f1-93d8-ff217bdb0653",
		"author": "did:example:dif",
		"created": "2020-01-01T19:23:24Z",
		"version": 2,
		"entries": {
			"https://identity.foundation/credential-trust-establishment/schemas/description.json": {
				"did:example:dif": {
					"name": "Decentralized Identity Foundation",
					"website": "https://identity.foundation/",
					"email": "membership@identity.foundation"
				},
				"did:example:acme":{
					"name": "ACME",
					"website": "https://example.com/",
					"email": "contact@example.com"
				}
			},
			"https://identity.foundation/credential-trust-establishment/schemas/roles.json":{
				"did:example:dif": [
					{
            "start": "2020-01-01 00:00:00Z",
            "role": "dif"
					}
				],
				"did:example:acme":{
					{
            "start": "2020-01-01 00:00:00Z",
            "role": "dif_member_organization"
					}
				}
			}
		}
	},
	"roles": {
		"dif": {
			"issue": [
				"uri:example:RuuJwd3JMffNwZ43DcJKN1:2:DIF_Member_Organization:1.4",
				"uri:example:4CLG5pU5v294VdkMWxSByu:2:DIF_Member_Individual:1.0"
			]
		},
		"dif_membership_organization": {
			"issue": ["uri:example:4CLG5pU5v294VdkMWxSByu:2:DIF_Member_Individual:1.0"],
			"granted_by": "uri:example:RuuJwd3JMffNwZ43DcJKN1:2:DIF_Member_Organization:1.4"
		}
	}
}
```

### Verified Email Ecosystem Example

A community could create a simple demo that shows how agents, credential issuance and verification, and basic governance work in a simple
email-based ecosystem. The Authority listed is generic, but could be a working group, a company, an industry association, or any other party that wished to use verified email addresses in their ecosystem.

There are two schemas used--one for an individual's email credential and one for authorizing issuers of the email credentials.

There are three tiers of participants:

1. The authority which runs the ecosystem and authorizes other parties
2. Issuers (organizations or individuals) which wish to support the demo ecosystem by issuing and/or verifying email credentials
3. Individuals who receive an email credential from an authorized email issuer and verify with whomever they choose to present the credential to

The authority is a single participant and is only included in the governance file. The email issuer tier can be authorized via explicitly
listed participantes or via an Email Issuer credential. Individuals could be listed but are likely to participate via credentials only.

The governance file lists three roles: one for the authority itself, one for issuers, and one for individuals.

```json
{
  "@context": [
    "https://identity.foundation/credential-trust-establishment/context.jsonld"
  ],
  "name": "Email Governance",
  "version": "1.0",
  "format": "1.1",
  "id": "<uuid>",
  "description": "This document describes the governance of the Aries Working Group's basic email ecosystem in a machine readable way.",
  "last_updated": "2022-10-09",
  "docs_uri": "https://url-for-docs...",
  "schemas": [
    {
      "id": "uri:example:RuuJwd3JMffNwZ43DcJKN1:2:Email_Issuer:1.4",
      "name": "Email Issuer"
    },
    {
      "id": "uri:example:BXtzYPyPdiVKGAjkqtPexs:2:Email:1.0",
      "name": "Email"
    }
  ],
  "participants": {
    "id": "32f54163-7166-48f1-93d8-ff217bdb0653",
    "author": "did:example:awg",
    "created": "2022-01-01T19:23:24Z",
    "version": 2,
    "entries": {
      "https://identity.foundation/credential-trust-establishment/schemas/description.json": {
        "did:example:authority": {
          "name": "Authority",
          "website": "https://example.com/",
          "email": "authority@example.org"
        },
        "did:example:acme": {
          "name": "ACME",
          "website": "https://example.com/",
          "email": "contact@example.com"
        }
      },
        "did:example:authority": [
      "https://identity.foundation/credential-trust-establishment/schemas/roles.json": {
          {
            "start": "2020-01-01 00:00:00Z",
            "role": "authority"
          }
        ],
        "did:example:acme": [
          {
            "start": "2020-01-01 00:00:00Z",
            "role": "email_issuer"
          }
        ]
      }
    }
  },
  "roles": {
    "authority": {
      "issue": ["uri:example:RuuJwd3JMffNwZ43DcJKN1:2:Email_Issuer:1.4"]
    },
    "email_issuer": {
      "issue": ["uri:example:BXtzYPyPdiVKGAjkqtPexs:2:Email:1.0"],
      "granted_by": ["uri:example:RuuJwd3JMffNwZ43DcJKN1:2:Email_Issuer:1.4"]
    }
  }
}
```

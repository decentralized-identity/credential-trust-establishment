Credential Trust Establishment
==================

**Specification Status:** Strawman

**Latest Draft:**
[identity.foundation/credential-trust-establishment](identity.foundation/credential-trust-establishment)

Editors:
~ [Mike Ebert](https://www.linkedin.com/in/michaelebert/)
~ [Gabe Cohen](https://www.linkedin.com/in/cohengabe/)

Participate:
~ [GitHub repo](https://github.com/decentralized-identity/credential-trust-establishment)
~ [File a bug](https://github.com/decentralized-identity/credential-trust-establishment/issues)
~ [Commit history](https://github.com/decentralized-identity/credential-trust-establishment/commits/master)

------------------------------------

## Introduction
Once you can create a trust list using [Trust Establishment](https://identity.foundation/trust-establishment/) , it is useful to extend Trust Establishment by defining roles and linking them to credentials for situations where:
1. Enumerating every trusted ecosystem participant in a governance file is not practical (or, in some cases, not possible).
2. It may be desirable to verify a participant's trusted status via a trust list entry AND/OR a credential.

Some of the most important roles are those tied to the issuing and/or verifying of credentials, so it is also necessary to tie roles to credential schemas.

Collecting all of this information in one place for ecosystem participants to access is powerful and convenient. A governance file is one relatively simple method for delivering trust establishment information.

The following sections cover what is required to enable Credential Trust Establishment:
- Schemas
- Roles
- Linking Participants to Roles
- Governance File Metadata

## Schemas
It is very useful to provide a list of schemas which are used in an ecosystem. This list can be quite simple, basically listing schema IDs and a human-friendly name. Here is an example for a U.S.-based higher education ecosystem:

```json
...
	"schemas": [
		{
			"id": "PdiVKGAjdiVKGAjLqtTroc:2:Accrediting_Body:1.0",
			"name": "Accrediting Body",
		},
		{
			"id": "BXtzYPyPdiVKGAjLqtPexs:2:University_Degree_Issuer:1.0",
			"name": "University Degree Issuer",
		},
		{
			"id": "QHqtjywxfZ3yYsFrRHFLQm:2:Bachelors_Degree:1.0",
			"name": "Bachelors Degree",
		},
		{
			"id": "JAjLqtPexs3yYsFrRHFLQm:2:Student_ID:1.0",
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
- **granted_by**: This, if present, specifies the Schema ID that, upon presentation, qualifies the subject to have this role. Any Schema specified here MUST be defined in the Schemas section of the document and aproved verifiers specified via a role defined in the Roles section.

Roles may omit any attributes that do not apply.

This example shows potential roles used in a univeristy diploma ecosystem application.

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

## Linking Participants to Roles
Once schemas are listed and roles are described, they need to be linked to the trusted participants. This is done by adding a new section to the Trust Establishment section of the document.
```json
...
			"https://example.com/roles.schema.json": {
				"did:example:usdepartmentofeducation": {
					"roles": [
						"accrediting_authorizer"
					]
				},
				"did:example:nwccu": {
					"roles": [
						"university_accreditor"
					]
				},
				"did:example:brighamyounguniversity": {
					"roles": [
						"verify_student_id"
						"issue_bachelors_degree",
					]
				}
			}
...
```

## Governance File Metadata
As with Trust Establishment, it makes sense to add some meta data to the governance file to assist those who are using the file.

The full University Diploma example used in this document is included as the first sample listed in the Appendix.

```json
{
	"@context": [
		"https://github.com/hyperledger/aries-rfcs/blob/main/concepts/0430-machine-readable-governance-frameworks/context.jsonld"
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
...
```

## Appendix
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
		"https://github.com/hyperledger/aries-rfcs/blob/main/concepts/0430-machine-readable-governance-frameworks/context.jsonld"
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
			"id": "PdiVKGAjdiVKGAjLqtTroc:2:Accrediting_Body:1.0",
			"name": "Accrediting Body",
		},
		{
			"id": "BXtzYPyPdiVKGAjLqtPexs:2:University_Degree_Issuer:1.0",
			"name": "University Degree Issuer",
		},
		{
			"id": "QHqtjywxfZ3yYsFrRHFLQm:2:Bachelors_Degree:1.0",
			"name": "Bachelors Degree",
		},
		{
			"id": "JAjLqtPexs3yYsFrRHFLQm:2:Student_ID:1.0",
			"name": "Student ID",
		}
	],
	"participants": {
		"id": "1e762324-6a45-4f6a-b124-ecb21190fe09",
		"author": "did:example:usdepartmentofeducation",
		"created": "2022-04-20T04:20:00Z",
		"version": 2,
		"entries": {
			"https://example.com/description.schema.json": {
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
				"did:example:brighamyounguniversity": {
					"name": "Brigham Young University",
					"website": "https://www.byu.edu/",
					"email": "graduation@byu.edu"
				}
			},
			"https://example.com/roles.schema.json": {
				"did:example:usdepartmentofeducation": {
					"roles": [
						"accrediting_authorizer"
					]
				},
				"did:example:nwccu": {
					"roles": [
						"university_accreditor"
					]
				},
				"did:example:brighamyounguniversity": {
					"roles": [
						"verify_student_id"
						"issue_bachelors_degree",
					]
				}
			}
		}
	},
	"roles": {
		"accrediting_authorizer": {
			"issue": [
				"PdiVKGAjdiVKGAjLqtTroc:2:Accrediting_Body:1.0"
			]
		},
		"university_accreditor": {
			"issue": [
				"BXtzYPyPdiVKGAjLqtPexs:2:University_Degree_Issuer:1.0"
			],
			"granted_by": "PdiVKGAjdiVKGAjLqtTroc:2:Accrediting_Body:1.0"
		},
		"verify_student_id": {
			"verify": [
				"JAjLqtPexs3yYsFrRHFLQm:2:Student_ID:1.0"
			],
			"granted_by": "BXtzYPyPdiVKGAjLqtPexs:2:University_Degree_Issuer:1.0"
		},
		"issue_bachelors_degree": {
			"issue": [
				"Lor8tASDc74EA268Jvc8J2:2:Bachelors_Degree:1.0"
			],
			"granted_by": "BXtzYPyPdiVKGAjLqtPexs:2:University_Degree_Issuer:1.0"
		},
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
		"https://github.com/hyperledger/aries-rfcs/blob/main/concepts/0430-machine-readable-governance-frameworks/context.jsonld"
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
			"id": "RuuJwd3JMffNwZ43DcJKN1:2:DIF_Member_Organization:1.4",
			"name": "DIF Member Organization",
			"issuer_roles": ["dif"]
		},
		{
			"id": "4CLG5pU5v294VdkMWxSByu:2:DIF_Member_Individual:1.0",
			"name": "DIF Individual Member",
			"issuer_roles": ["dif", "dif_member_organization"],
		}
	],
	"participants": {
		"id": "32f54163-7166-48f1-93d8-ff217bdb0653",
		"author": "did:example:dif",
		"created": "2020-01-01T19:23:24Z",
		"version": 2,
		"topic": "uri:to-multi-topic-schema",
		"entries": {
			"did:example:dif": {
				"uri:to-role_schema": {
					"roles": ["dif"],
				},
				"uri:to-describe_schema": {
					"name": "Decentralized Identity Foundation",
					"website": "https://identity.foundation/",
					"email": "membership@identity.foundation"
				}
			},
			"did:example:indicio": {
				"uri:to-role_schema": {
					"roles": ["dif_member_organization"],
				},
				"uri:to-describe_schema": {
					"name": "Indicio",
					"website": "https://indicio.tech/",
					"email": "contact@indicio.tech"
				}
			},
		}
	},
	"roles": {
		"dif": {},
		"dif_membership_organization": {
			"credentials": ["RuuJwd3JMffNwZ43DcJKN1:2:DIF_Member_Organization:1.4"]
		},
		"holder": {}
	}
}
```

### Aries Email Ecosystem Example
The Aries community shares code, demos, and examples to promote interoperability and community education.
The Aries community could create a simple demo that shows how agents, credential issuance and verification, and basic governance work in a simple
email-based ecosystem.

There are two schemas used--one for an individual's email credential and one for authorizing issuers of the email credentials.

There are three tiers of participants:
1. The Aries Working Group which runs the ecosystem and authorizes other parties
2. Issuers (organizations or individuals) which wish to support the demo ecosystem by issuing and/or verifying email credentials
3. Individuals who receive an email credential from an authorized email issuer and verify with whomever they choose to present the credential to

The Aries Working Group (AWG) is a single participant and is only included in the governance file. The email issuer tier can be authorized via explicitly
listed participantes or via an Email Issuer credential. Individuals could be listed but are likely to participate via credentials only.

The governance file lists three roles: one for the AWG itself, one for issuers, and one for individuals.

```json
{
	"@context": [
		"https://github.com/hyperledger/aries-rfcs/blob/main/concepts/0430-machine-readable-governance-frameworks/context.jsonld"
	],
	"name": "Aries Working Group Email Governance",
	"version": "1.0",
	"format": "1.1",
	"id": "<uuid>",
	"description": "This document describes the governance of the Aries Working Group's basic email ecosystem in a machine readable way.",
	"last_updated": "2022-10-09",
	"docs_uri": "https://url-for-docs...",
	"schemas": [
		{
			"id": "RuuJwd3JMffNwZ43DcJKN1:2:Email_Issuer:1.4",
			"name": "Email Issuer",
			"issuer_roles": ["awg"]
		},
		{
			"id": "BXtzYPyPdiVKGAjkqtPexs:2:Email:1.0",
			"name": "Email",
			"issuer_roles": ["email_issuer"],
		}
	],
	"participants": {
		"id": "32f54163-7166-48f1-93d8-ff217bdb0653",
		"author": "did:example:awg",
		"created": "2022-01-01T19:23:24Z",
		"version": 2,
		"topic": "uri:to-multi-topic-schema",
		"entries": {
			"did:example:dif": {
				"uri:to-role_schema": {
					"roles": ["awg"],
				},
				"uri:to-describe_schema": {
					"name": "Aries Working Group",
					"website": "https://wiki.hyperledger.org/display/ARIES/Aries+Working+Group",
					"email": "awg@hyperledger.org"
				}
			},
			"did:example:indicio": {
				"uri:to-role_schema": {
					"roles": ["email_issuer"],
				},
				"uri:to-describe_schema": {
					"name": "Indicio",
					"website": "https://indicio.tech/",
					"email": "contact@indicio.tech"
				}
			},
		}
	},
	"roles": {
		"awg": {},
		"email_issuer": {
			"credentials": ["RuuJwd3JMffNwZ43DcJKN1:2:Email_Issuer:1.4"]
		},
		"holder": {}
	}
}
```
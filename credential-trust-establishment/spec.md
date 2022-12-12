Credential Trust Establishment
==================

**Specification Status:** Strawman

**Latest Draft:**
  [identity.foundation/crendtial-trust-establishment](https://identity.foundation/trust-establishment)

Editors:
~ [Mike Ebert](https://www.linkedin.com/in/michaelebert/)
~ [Gabe Cohen](https://www.linkedin.com/in/cohengabe/)

Participate:
~ [GitHub repo](https://github.com/decentralized-identity/credential-trust-establishment)
~ [File a bug](https://github.com/decentralized-identity/credential-trust-establishment/issues)
~ [Commit history](https://github.com/decentralized-identity/credential-trust-establishment/commits/master)

------------------------------------

## Introduction

Once trust lists are established, it is natural to extend trust establishment by defining roles and linking them to credentials for situations where
1. Enumerating every trusted ecosystem participant in a governance file is not practical (or, in some cases, not possible).
2. It may be desirable to verify a participant's trusted status via both the trust list entry AND a credential.

Some of the most important roles are those tied to the issuing of credentials, so it is also necessary to tie roles to credential schemas.

Collecting all of this information in one place for ecosystem participants to access is powerful and convenient. A governance file is one relatively simple method for delivering trust establishment information.

Below are three examples of governance files which include sections for trust lists, schemas, roles, and general meta data.

## University Diploma Example
The granting and recognition of university diplomas involve a large number of organizations at various tiers.

Fundamentally, the U.S. Department of Education authorizes organizations to be accrediting bodies.
These accrediting bodies review universities and their programs and state whether they are accredited or not.
In turn, accredited universities grant diplomas to their students that are recognized by various companies, organizations, etc.

This governance file includes schemas for accrediting organization, university, and diploma credentials. The role required to issue these credentials is included
in the schema metadata.

A sample participant of each type is included in the "participants" section.

The governance below includes roles for the U.S. Department of Education, the accrediting bodies, and universities.
The U.S. Department of Education is granted the role of "accrediting_authorizer" directly in the governance file in the "participants" section.
Accrediting organizations can be granted the role of "university_accreditor" in the file or via the credential indicated by schema ID in the roles section.
Universities can also be granted the role of "university_diploma_issuer" in the file or via the credential indiciated by schema ID in the roles section.
Finally, universities issue university diplomas to students (holders).

```json
{
	"@context": [
		"https://github.com/hyperledger/aries-rfcs/blob/main/concepts/0430-machine-readable-governance-frameworks/context.jsonld"
	],
	"name": "University Diploma Governance",
	"version": "1.0",
	"format": "1.1",
	"id": "<uuid>",
	"description": "This document describes the governance and issuance of university diplomas in a machine readable way.",
	"last_updated": "2022-10-09",
	"docs_uri": "https://url-for-docs...",
	"schemas": [
		{
			"id": "RuuJwd3JMffNwZ43DcJKN1:2:University_Accreditation_Issuer:1.0",
			"name": "University Accreditation Issuer",
			"issuer_roles": ["accrediting_authorizer"]
		},
		{
			"id": "RuuJwd3JMffNwZ43DcJKN1:2:University_Diploma_Issuer:1.2",
			"name": "University Diploma Issuer",
			"issuer_roles": ["university_accreditor"]
		},
		{
			"id": "4CLG5pU5v294VdkMWxSByu:2:Bachelors_of_Science:1.5",
			"name": "Bachelors_of_Science",
			"issuer_roles": ["university_diploma_issuer"],
		}
	],
	"participants": {
		"id": "32f54163-7166-48f1-93d8-ff217bdb0653",
		"author": "did:example:usdepartmentofeducation",
		"created": "2020-01-01T19:23:24Z",
		"version": 2,
		"topic": "uri:to-multi-topic-schema",
		"entries": {
			"did:example:usdepartmentofeducation": {
				"uri:to-role_schema": {
					"roles": ["accrediting_authorizer"],
				},
				"uri:to-describe_schema": {
					"name": "U.S. Department of Education",
					"website": "https://www.ed.gov/accreditation",
					"email": "accreditation@www.ed.gov"
				}
			},
			"did:example:nwccu": {
				"uri:to-role_schema": {
					"roles": ["university_accreditor"],
				},
				"uri:to-describe_schema": {
					"name": "Northwest Commission on Colleges and Universities",
					"website": "http://www.nwccu.org/",
					"email": "accrediting@nwccu.org"
				}
			},
			"did:example:brighamyounguniversity": {
				"uri:to-role_schema": {
					"roles": ["university_diploma_issuer"]
				},
				"uri:to-describe_schema": {
					"name": "Brigham Young University",
					"website": "https://www.byu.edu/",
					"email": "graduation@byu.edu"
				}
			}
		}
	},
	"roles": {
		"accrediting_authorizer": {},
		"university_accreditor": {
			"credentials": ["RuuJwd3JMffNwZ43DcJKN1:2:University_Accreditation_Issuer:1.4"]
		},
		"university_diploma_issuer": {
			"credentials": ["RuuJwd3JMffNwZ43DcJKN1:2:University_Diploma_Issuer:1.4"]
		},
		"holder": {}
	}
}
```


## DIF Membership Example
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

## Aries Email Ecosystem Example
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
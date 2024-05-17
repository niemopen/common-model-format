<img src="https://github.com/niemopen/oasis-open-project/blob/main/artwork/NIEM-NO-Logo-v5.png" width="200">

# Common Model Format Specification

This repository is part of the NIEM Open Project.  It contains the NIEMOpen Common Model Format Specification (CMF).  

CMF is a NIEM message specification for data models,  In all versions of NIEM through version 5, data models are expressed in XML Schema (XSD).  CMF offers an equivalent expression, one that is more suitable for developers not working in XML and is easier to support with developer tools.  Using CMF, a data model can be expressed as XML or JSON or any other data serialization that NIEM supports, and the data described by the CMF data model can also be expressed in any supported serialization.  

More documentation on CMF is available [here](doc/README.md).

For more information on NIEMOpen, see the project's website at [www.niemopen.org](www.niemopen.org).

General questions about OASIS Open Projects may be directed to OASIS staff at project-admin@lists.oasis-open-projects.org

## What's new in version 1.0-alpha.2

* A message schema is provided in *message.xsd*

## What's new in version 1.0 alpha 1

* cmf:DefinitionText replaced with cmf:DocumentationText.  
  (Namespaces are documented, not defined.)
* Removed NIEM 5 schema documents

## What's new in version 0.8

* CMF is now a NIEM 6 message specification
* Namespace URIs start with `http://docs.open-oasis.org`
* `NCName` becomes `NCNameType` for NDR conformance
* *model5.xsd* is the NIEM 5 XSD for CMF version 0.7
* *model6.xsd* is the NIEM 6 reference XSD for CMF version 0.8
  (don't yet have the message XSD for CMF 0.8)

## What's new in version 0.7

* New `AugmentRecord` child in `NamespaceType`
* New `CodeListBinding` child in `ComponentType`
* Augmentation properties in `HasPropertyType were revised
* `NamespacePrefixText` put back into `SchemaDocumentType`

## What's new in version 0.6

* DefinitionText in CMF is now repeatable
* DefinitionText now has nc:TextType with xml:lang attribute
* HasProperty element in ClassType now has orderedPropertyIndicator="true"
* Added SchemaLanguageName property (xml:lang) to SchemaDocumentType in CMF
* Removed cmf:HasValue property
* All schema documents canonicalized
* Added CMF and XSD documentation

## Where are the older versions of CMF?

The pre-OASIS history of CMF is at [NIEM/CMF-MessageSpec (github.com)](https://github.com/NIEM/CMF-MessageSpec) 

## Other assets

In addition to this GitHub repository, this project also makes use of other assets. 

- The NIEMOpen website is at www.niemopen.org. The website contains news, announcements, and other information of interest about the project. 

- The [General purpose mailing list](https://lists.oasis-open-projects.org/g/niemopen). To subscribe, send an empty email message to niemopen+subscribe@lists.oasis-open-projects.org. Anyone interested is welcome to subscribe and send email to the list. The list maintains an [archive](https://lists.oasis-open-projects.org/g/niemopen/messages).

- The [Project Governing Board mailing list](https://lists.oasis-open-projects.org/g/niemopen-pgb). This is the discussion list for use by the members of the PGB. To subscribe, send an empty email message to niemopen-pgb+subscribe@lists.oasis-open-projects.org. Anyone interested is welcome to subscribe read-only. Only PGB members can post. The list maintains an [archive](https://lists.oasis-open-projects.org/g/niemopen-pgb/messages).

- [NBAC Technical Steering Committee mailing list](https://lists.oasis-open-projects.org/g/niemopen-nbactsc). This is the discussion list for use by the members of the NIEM Business Architecture Committee TSC. To subscribe, send an empty email message to niemopen-nbactsc+subscribe@lists.oasis-open-projects.org. Anyone interested is welcome to subscribe read-only. The list maintains an [archive](https://lists.oasis-open-projects.org/g/niemopen-nbactsc/messages).

- [NTAC Technical Steering Committee mailing list](https://lists.oasis-open-projects.org/g/niemopen-ntactsc). This is the discussion list for use by the members of the NIEM Technical Architecture Committee TSC. To subscribe, send an empty email message to niemopen-ntactsc+subscribe@lists.oasis-open-projects.org. Anyone interested is welcome to subscribe read-only. The list maintains an [archive](https://lists.oasis-open-projects.org/g/niemopen-ntactsc/messages).

# Contributing

Please read [CONTRIBUTING.md](CONTRIBUTING.md) for details how to join the project, contribute changes to our repositories and communicate with the rest of the project contributors. Please become familiar with and follow the [code of conduct](CODE-OF-CONDUCT.md).

# Governance

NIEM Open operates under the terms of the [Open Project Rules](https://www.oasis-open.org/policies-guidelines/open-projects-process) and the applicable license(s) specified in [LICENSE.md](LICENSE.md). Further details can be found in [GOVERNANCE.md](https://github.com/niemopen/oasis-open-project/blob/main/GOVERNANCE.md), [GOVERNANCE-NBAC.md](https://github.com/niemopen/nbac-admin/blob/main/documents/(APPROVED)%20%20NIEMOpen%20NBAC%20TSC%20Governance%20Doc%20(Rev%202)%20%20v7%20%207-27-2023.pdf), and [GOVERNANCE-NTAC.md](https://github.com/niemopen/ntac-admin/blob/main/GOVERNANCE-NTAC.md).

# CLA & Non-assert signatures required

All technical contributions must be covered by a Contributor's License Agreement. This requirement allows our work to advance through OASIS standards development stages and potentially be submitted to de jure organizations such as ISO. You will get a prompt to sign this document when you submit your first pull request to a project repository, or you can sign [here](https://cla-assistant.io/niemopen/oasis-open-project). If you are contributing on behalf of your employer, you must also sign the ECLA [here](https://www.oasis-open.org/open-projects/cla/entity-cla-20210630/).



## What's new in version 1.0-beta.2

* ComponentType and NamespaceType marked as referenceable (ReferenceCode = ANY)

## What's new in version 1.0-beta.1

* OrderedPropertyIndicator moves from ChildPropertyAssociationType to PropertyType
* AnyProperty becomes AnyPropertyAssociation
* NIEMVersion becomes ArchitectureVersion

## What's new in version 1.0-alpha.9

* Namespace objects store the documentation for each import (if any)
* NamespaceCategoryCode and NIEMVersionName have been restored
* Outdated documentation removed
* CrashDriver example 

## What's new in version 1.0-alpha.8

* DocumentationText is unbounded in FacetType
* ImportDocumentationText is unbounded in NamespaceType
* Class is optional in PropertyType

## What's new in version 1.0-alpha.7

* LocalTerm has at most one DocumentText child
* Update appinfo.xsd to include appinfo:Augmentation

## What's new in version 1.0-alpha.6

* Component and Namespace elements with content must be top-level
* Model object has xml:lang
* DocumentationText is repeatable
* GlobalClassCode is repeatable
* Removed NIEMVersionText and NamespaceCategoryCode

## What's new in version 1.0-alpha.5

* Wildcards supported: ClassType has AnyAttributeIndicator and AnyElementIndicator
* AugmentedGlobalClassID becomes GlobalClassCode
* PropertyAssociation becomes ChildPropertyAssociation
* RelationshpPropertyIndicator becomes RelationshipIndicator
* AugmentableIndicator removed
* AugmentingNamespace removed
* Unnecessary types and elements removed
* Examples not updated
* CMF version of model not included

## What's new in version 1.0-alpha.4

Several changes based on NTAC review of draft NDR 6:

* Now have a single `Facet` and `FacetType` with a new `FacetCategoryCode`. There is no longer any schema validation of facet values. 
* ExtensionOfClass becomes SubClassOf
* HasProperty becomes PropertyAssociation
* ListOf becomes ListItemDatatype
* NamespaceKindCode becomes NamespaceCategoryCode
* UnionOf becomes UnionMemberDatatype
* RestrictionDatatype becomes Restriction
* ListDatatype becomes List
* UnionDatatype becomes Union

## What's new in version 1.0-alpha.3

Several changes to component names and definitions that align CMF with the metamodel:

* AugmentRecord becomes AugmentationRecord
* AugmentationNamespace becomes AugmentingNamespace
* ClassType child elements are reordered
* Component (abstract element) added
* ComponentType now has subclasses instead of properties
* ComponentType doesn't have AbstractIndicator (datatypes can't be abstract)
* ConformanceTargetURIList becomes (repeatable) ConformanceTargetURI
* FacetAbstract becomes Facet
* GlobalAugmentationCode becomes GlobalAugmented
* HasProperty becomes @minOccurs="1" in ClassType
* ListType added (subclass of DatatypeType)
* ModelItemAbstract replaced with Namespace, Component
* PropertyAbstract becomes Property
* RestrictionOfType becomes RestrictionType (with a RestrictionOf property)
* SchemaLangugeName becomes NamespaceLanguageName
* SchemaVersionText becomes NamespaceVersionText
* SourceURIList becomes (repeatable) SourceURI
* UnionOfType becomes UnionType (with a repeatable UnionOf property)
* New documentation for

  * AttributeIndicator
  * AugmentationIndex
  * ComponentType
  * DeprecatedIndicator
  * DocumentFilePathText
  * DocumentationText
  * ExtensionOfClass
  * ImportDocumentationText
  * ListOf
  * MaxOccursQuantity
  * MinOccursQuantity
  * ModelType
  * NIEMVersionText
  * NamespaceLanguageName
  * OrderedPropertyIndicator
  * RelationshipPropertyIndicator


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

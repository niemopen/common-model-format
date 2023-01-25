# NIEM XSD and CMF equivalents

There are now two ways to represent a data model in NIEM:  as an XML Schema, or as a Common Model Format  (CMF) file.  We intend for these to be equivalent:  anything you can express in NIEM XSD can also be expressed in CMF, and vice versa.

In general:

* XML type definitions are equivalent to CMF Class or Datatype objects
* XML elements and attribute declarations are equivalent to CMF properties

Specific aspects of the XSD-CMF equivalence are described below:

* [Namespace prefix normalization](#Namespace prefix normalization)
* [Appinfo](#Appinfo)

* [Components from the structures namespace](#Components from the structures namespace)
* [Complex content](#Complex content)
* [Simple content with attributes](#Simple content with attributes)
* [Simple content without attributes](#Simple content without attributes)
* [Augmentations](#Augmentations)
* [Adapter types / External content](#Adapter types / External content)

## Namespace prefix normalization

An XML schema is constructed from any number of schema documents.  Those documents may assign many different namespace prefixes to the same namespace identifier.  They may assign a single namespace prefix to many different namespace identifiers.  As a result, you can't tell what a QName like `ns:Foo` means without the whole schema document for context.

In a CMF model, each namespace has exactly one namespace prefix, and each prefix is mapped to exactly one namespace, and so each QName identifies exactly one model component.  Supporting that CMF guarantee requires a fair amount of complexity in the XSD-to-CMF transformation.  We have to look at all the namespace assignments and decide which ones to keep, which ones to change.

The assignment of each namespace prefix is made in the following order:

1. Some namespace prefixes are predefined.  They can't be used for anything else in a CMF model:

   | Prefix | Namespace                                        |
   | ------ | ------------------------------------------------ |
   | cmf    | http://reference.niem.gov/specification/cmf/0.4/ |
   | rdf    | http://www.w3.org/1999/02/22-rdf-syntax-ns       |
   | rdfs   | http://www.w3.org/2000/01/rdf-schema             |
   | owl    | http://www.w3.org/2002/07/owl                    |
   | xs     | http://www.w3.org/2001/XMLSchema                 |
   | xsd    | http://www.w3.org/2001/XMLSchema                 |

   (It turns out that the "xsd" prefix is hard-wired into OWL2; that's why I reserve both prefixes.  I'm not sure whether the rdf, rdfs, and owl namespace URIs actually end with "#" -- but I do the right thing when binding a namespace prefix in JSON-LD and RDF.)

2. Namespace declarations in the extension schema documents have the next highest priority.  We assume the guy writing the extension schema knows what he wants.

3. Next are the namespaces in the NIEM model.  We prefer the prefix assigned to the target namespace.

4. Everything else is an external namespace.  Those guys choose last.

5. Ties within categories #2-4 go to the namespace declaration with the lowest depth. If that's a tie, the first declaration in document order wins.

6. The losing namespace prefix is munged; for example, `nc_1:`, then `nc_2:`, instead of `nc:`.

6. Prefixes for the built-in namespaces (appinfo, cli, clsi, ct, xs-proxy, and structures) are also guaranteed to be unique with each other and with namespaces in the model.  This becomes interesting when a model includes components from different NIEM versions.  (Later versions are preferred over older; e.g. 5.0 beats 4.0.)

## Components from the structures namespace

The attributes in this namespace -- `@id, @ref, @uri, @metadata, @relationshipMetadata, @sequenceID`-- are not part of the CMF model representation.  These attributes are part of the XML plumbing; they don't express model semantics.  The object types  -- `ObjectType, AssociationType, MetadataType` -- are likewise omitted from the model in CMF.  (There is [special handling](#Augmentations) for `AugmentationType`.)

## Appinfo

The attributes and elements in the appinfo namespace have equivalent elements in CMF:

| XSD                          | CMF                               |
| :--------------------------- | --------------------------------- |
| appliesToElements            | *no equivalent*                   |
| appliesToTypes               | *no equivalent*                   |
| deprecated                   | DeprecatedIndicator               |
| externalAdapterTypeIndicator | ExternalAdapterTypeIndicator      |
| externalImportIndicator      | TBD                               |
| metadataIndicator            | Metadata                          |
| orderedPropertyIndicator     | OrderedPropertyIndicator          |
| LocalTerm                    | LocalTerm *(not implemented yet)* |

## Complex content

A type definition with complex content always maps to a Class object in CMF.  Note that an attribute is just another property in CMF. 

**NIEM XSD:**

```xml
  <xs:complexType name="PersonNameType">
    <xs:annotation>
      <xs:documentation>A data type for a combination of names and/or titles by which a person is known.</xs:documentation>
    </xs:annotation>
    <xs:complexContent>
      <xs:extension base="structures:ObjectType">
        <xs:sequence>
          <xs:element ref="nc:PersonGivenName" minOccurs="0" maxOccurs="unbounded"/>
          <xs:element ref="nc:PersonMiddleName" minOccurs="0" maxOccurs="unbounded"/>
          <xs:element ref="nc:PersonSurName" minOccurs="0" maxOccurs="unbounded"/>
        </xs:sequence>
        <xs:attribute ref="nc:personNameCommentText" use="optional"/>
      </xs:extension>
    </xs:complexContent>
  </xs:complexType>
```

**NIEM CMF:**

```xml
 <Class structures:uri="nc:PersonNameType">
    <Name>PersonNameType</Name>
    <Namespace structures:uri="nc" xsi:nil="true"/>
    <DefinitionText>A data type for a combination of names and/or titles by which a person is known.</DefinitionText>
    <HasProperty structures:sequenceID="1">
      <Property structures:uri="nc:PersonGivenName" xsi:nil="true"/>
      <MinOccursQuantity>0</MinOccursQuantity>
      <MaxOccursQuantity>unbounded</MaxOccursQuantity>
    </HasProperty>
    <HasProperty structures:sequenceID="2">
      <Property structures:uri="nc:PersonMiddleName" xsi:nil="true"/>
      <MinOccursQuantity>0</MinOccursQuantity>
      <MaxOccursQuantity>unbounded</MaxOccursQuantity>
    </HasProperty>
    <HasProperty structures:sequenceID="3">
      <Property structures:uri="nc:PersonSurName" xsi:nil="true"/>
      <MinOccursQuantity>0</MinOccursQuantity>
      <MaxOccursQuantity>unbounded</MaxOccursQuantity>
    </HasProperty>
    <HasProperty>
      <Property structures:uri="nc:personNameCommentText" xsi:nil="true"/>
      <MinOccursQuantity>0</MinOccursQuantity>
      <MaxOccursQuantity>1</MaxOccursQuantity>
    </HasProperty>
  </Class>
```

## Simple content with attributes

A type definition with simple content usually maps to a Datatype object in CMF.  But if the type definition includes attributes, then it maps to a Class object with a HasValue object.

The HasValue object must have a Datatype -- in this case, a Datatype named`Degree90SimpleType`.  (This is one more reason why I don't like attributes.)  In general, `FooSimpleType`definitions don't appear in the CMF.

We'll have to think about what happens when a subset removes all the attributes from a simple content type.  We probably don't want subsetting to turn a Class into a Datatype.  But that's what will happen if we aren't careful.

**NIEM XSD:**

```xml
  <xs:simpleType name="Degree90SimpleType">
    <xs:annotation>
      <xs:documentation>A data type for a value between 0 (inclusive) and 90 (exclusive).</xs:documentation>
    </xs:annotation>
    <xs:restriction base="xs:decimal">
      <xs:minInclusive value="0">
        <xs:annotation>
          <xs:documentation>The minimum value for a degree.</xs:documentation>
        </xs:annotation>
      </xs:minInclusive>
      <xs:maxExclusive value="90">
        <xs:annotation>
          <xs:documentation>The maximum value for a degree.</xs:documentation>
        </xs:annotation>
      </xs:maxExclusive>
    </xs:restriction>
  </xs:simpleType>
  <xs:complexType name="Degree90Type">
    <xs:annotation>
      <xs:documentation>A data type for a value between 0 (inclusive) and 90 (exclusive).</xs:documentation>
    </xs:annotation>
    <xs:simpleContent>
      <xs:extension base="nc:Degree90SimpleType">
        <xs:attributeGroup ref="structures:SimpleObjectAttributeGroup"/>
        <xs:attribute ref="nc:errorValue"/>
      </xs:extension>
    </xs:simpleContent>
  </xs:complexType>
```

**NIEM CMF:**

```xml
  <Class structures:uri="nc:Degree90Type">
    <Name>Degree90Type</Name>
    <Namespace structures:uri="nc" xsi:nil="true"/>
    <DefinitionText>A data type for a value between 0 (inclusive) and 90 (exclusive).</DefinitionText>
    <HasValue structures:uri="nc:Degree90SimpleType" xsi:nil="true"/>
    <HasProperty>
      <Property structures:uri="nc:errorValue" xsi:nil="true"/>
      <MinOccursQuantity>0</MinOccursQuantity>
      <MaxOccursQuantity>1</MaxOccursQuantity>
    </HasProperty>
  </Class>
  <Datatype structures:uri="nc:Degree90SimpleType">
    <Name>Degree90SimpleType</Name>
    <Namespace structures:uri="nc" xsi:nil="true"/>
    <DefinitionText>A data type for a value between 0 (inclusive) and 90 (exclusive).</DefinitionText>
    <RestrictionOf>
      <Datatype structures:uri="xs:decimal" xsi:nil="true"/>
      <MaxExclusive>
        <StringValue>90.0</StringValue>
        <DefinitionText>The maximum value for a degree.</DefinitionText>
      </MaxExclusive>
      <MinInclusive>
        <StringValue>0.0</StringValue>
        <DefinitionText>The minimum value for a degree.</DefinitionText>
      </MinInclusive>
    </RestrictionOf>
  </Datatype>
```

## Simple content without attributes

A type definition with simple content and no semantic attributes maps to a Datatype object in CMF.  There are a few specific cases: 

* [Proxy types](#Proxy types)
* [Restriction](#Restriction)
* [Code list types](#Code list types) (also a restriction)
* [List types](#List types)
* [Union types](#Union types)

### Proxy types

The NIEM proxy types are a convenience mechanism for adding the `structures` attributes to the built-in XSD datatypes.  They are not included in the CMF representation of the model.

**NIEM XSD:**

```xml
  <xs:complexType name="PercentType">
    <xs:annotation>
      <xs:documentation>A data type for a ratio, proper fraction, or percentage, with 100% represented as the value 100.</xs:documentation>
    </xs:annotation>
    <xs:simpleContent>
      <xs:extension base="niem-xs:decimal"/>
    </xs:simpleContent>
  </xs:complexType>
```

**NIEM CMF:**

```xml
  <Datatype structures:uri="nc:PercentType">
    <Name>PercentType</Name>
    <Namespace structures:uri="nc" xsi:nil="true"/>
    <DefinitionText>A data type for a ratio, proper fraction, or percentage, with 100% represented as the value 100.</DefinitionText>
    <RestrictionOf>
      <Datatype structures:uri="xs:decimal" xsi:nil="true"/>
    </RestrictionOf>
  </Datatype>
```

**NIEM XSD:**

```xml
  <xs:element name="Date" type="niem-xs:date" substitutionGroup="nc:DateRepresentation" nillable="true">
    <xs:annotation>
      <xs:documentation>A full date.</xs:documentation>
    </xs:annotation>
  </xs:element>
```

**NIEM CMF:**

```xml
  <Property structures:uri="nc:Date">
    <Name>Date</Name>
    <Namespace structures:uri="nc" xsi:nil="true"/>
    <DefinitionText>A full date.</DefinitionText>
    <SubPropertyOf structures:uri="nc:DateRepresentation" xsi:nil="true"/>
    <Datatype structures:uri="xs:date" xsi:nil="true"/>
  </Property>
```

### Restriction

Here is an example of restricting the range of a decimal value.  Observe that `FooSimpleType` definitions do not appear in the CMF model representation.

**NIEM XSD:**

```xml
  <xs:simpleType name="AngularMinuteSimpleType">
    <xs:annotation>
      <xs:documentation>A data type for a minute of a degree, with a restricted range of 0 (inclusive) to 60 (exclusive).</xs:documentation>
    </xs:annotation>
    <xs:restriction base="xs:decimal">
      <xs:minInclusive value="0">
        <xs:annotation>
          <xs:documentation>The lowest value allowed (inclusive).</xs:documentation>
        </xs:annotation>
      </xs:minInclusive>
      <xs:maxExclusive value="60">
        <xs:annotation>
          <xs:documentation>The highest value allowed (exclusive).</xs:documentation>
        </xs:annotation>
      </xs:maxExclusive>
    </xs:restriction>
  </xs:simpleType>
  <xs:complexType name="AngularMinuteType">
    <xs:annotation>
      <xs:documentation>A data type for a minute of a degree, with a restricted range of 0 (inclusive) to 60 (exclusive).</xs:documentation>
    </xs:annotation>
    <xs:simpleContent>
      <xs:extension base="nc:AngularMinuteSimpleType">
        <xs:attributeGroup ref="structures:SimpleObjectAttributeGroup"/>
      </xs:extension>
    </xs:simpleContent>
  </xs:complexType>
```

**NIEM CMF:**

```xml
  <Datatype structures:uri="nc:AngularMinuteType">
    <Name>AngularMinuteType</Name>
    <Namespace structures:uri="nc" xsi:nil="true"/>
    <DefinitionText>A data type for a minute of a degree, with a restricted range of 0 (inclusive) to 60 (exclusive).</DefinitionText>
    <RestrictionOf>
      <Datatype structures:uri="xs:decimal" xsi:nil="true"/>
      <MaxExclusive>
        <StringValue>60.0</StringValue>
        <DefinitionText>The highest value allowed (exclusive).</DefinitionText>
      </MaxExclusive>
      <MinInclusive>
        <StringValue>0.0</StringValue>
        <DefinitionText>The lowest value allowed (inclusive).</DefinitionText>
      </MinInclusive>
    </RestrictionOf>
  </Datatype>
```

### Code list types

Another form of simple content restriction.  Observe that again the `FooSimpleType` does not appear in the CMF.

**NIEM XSD:**

```xml
  <xs:simpleType name="EmploymentPositionBasisCodeSimpleType">
    <xs:annotation>
      <xs:documentation>A data type for a nature or duration of the employment position.</xs:documentation>
    </xs:annotation>
    <xs:restriction base="xs:token">
      <xs:enumeration value="contractor"/>
      <xs:enumeration value="non-permanent"/>
      <xs:enumeration value="permanent"/>
    </xs:restriction>
  </xs:simpleType>
  <xs:complexType name="EmploymentPositionBasisCodeType">
    <xs:annotation>
      <xs:documentation>A data type for a nature or duration of the employment position.</xs:documentation>
    </xs:annotation>
    <xs:simpleContent>
      <xs:extension base="nc:EmploymentPositionBasisCodeSimpleType">
        <xs:attributeGroup ref="structures:SimpleObjectAttributeGroup"/>
      </xs:extension>
    </xs:simpleContent>
  </xs:complexType>
```

**NIEM CMF:**

```xml
  <Datatype structures:uri="nc:EmploymentPositionBasisCodeType">
    <Name>EmploymentPositionBasisCodeType</Name>
    <Namespace structures:uri="nc" xsi:nil="true"/>
    <DefinitionText>A data type for a nature or duration of the employment position.</DefinitionText>
    <RestrictionOf>
      <Datatype structures:uri="xs:token" xsi:nil="true"/>
      <Enumeration>
        <StringValue>contractor</StringValue>
      </Enumeration>
      <Enumeration>
        <StringValue>non-permanent</StringValue>
      </Enumeration>
      <Enumeration>
        <StringValue>permanent</StringValue>
      </Enumeration>
    </RestrictionOf>
  </Datatype>
```

### List types

Once again, the `FooSimpleType` does not appear in the CMF.

**NIEM XSD:**

```xml
  <xs:simpleType name="TokenListSimpleType">
    <xs:list itemType="xs:token"/>
  </xs:simpleType>
  <xs:complexType name="TokenListType">
    <xs:simpleContent>
      <xs:extension base="nc:TokenListSimpleType">
        <xs:attributeGroup 
          ref="structures:SimpleObjectAttributeGroup"/>
      </xs:extension>
    </xs:simpleContent>
  </xs:complexType>
```

**NIEM CMF:**

```xml
  <Datatype structures:uri="nc:TokenListType">
    <Name>TokenListType</Name>
    <Namespace structures:uri="nc" xsi:nil="true"/>
    <ListOf structures:uri="xs:token" xsi:nil="true"/>
  </Datatype>
```

### Union types

Still no `FooSimpleType` in the CMF.

**NIEM XSD:**

```xml
<xs:simpleType name="UnionSimpleType">
  <xs:union memberTypes="xs:decimal xs:float"/>
</xs:simpleType>  
<xs:complexType name="UnionType">
  <xs:simpleContent>
    <xs:extension base="ns:UnionSimpleType">
      <xs:attributeGroup 
        ref="structures:SimpleObjectAttributeGroup"/>
    </xs:extension>
  </xs:simpleContent>
</xs:complexType>
```

**NIEM CMF:**

```xml
  <Datatype structures:uri="ns:UnionType">
    <Name>UnionType</Name>
    <Namespace structures:uri="ns" xsi:nil="true"/>
    <UnionOf>
      <Datatype structures:uri="xs:decimal" xsi:nil="true"/>
      <Datatype structures:uri="xs:float" xsi:nil="true"/>
    </UnionOf>
  </Datatype>
```

## Augmentations

An augmentation is the XML Schema mechanism that allows the authors of one namespace to add a property to a type defined in another namespace.  For example, `j:AddressAugmentationType` is the way the NIEM Justice domain is able to add `j:AddressVerifiedDate` to `nc:AddressType` in NIEM Core.  

An element with an augmentation type (like `j:AddressAugmentation`) has no semantics of its own -- it is just a bag of properties.  For this reason, the augmentation type and augmentation element declaration do not appear in the CMF model.

The added property is just like the other properties of the augmented type, except that the CMF model needs to keep track of the namespace that made the addition.  For example:

**NIEM XSD:**

```xml
  <xs:complexType name="AddressAugmentationType">
    <xs:annotation>
      <xs:documentation>A data type for additional information about a Address.</xs:documentation>
    </xs:annotation>
    <xs:complexContent>
      <xs:extension base="structures:AugmentationType">
        <xs:sequence>
          <xs:element ref="j:AddressCommentText" minOccurs="0" maxOccurs="unbounded"/>
          <xs:element ref="j:AddressVerifiedDate" minOccurs="0" maxOccurs="unbounded"/>
        </xs:sequence>
      </xs:extension>
    </xs:complexContent>
  <xs:element name="AddressAugmentation" type="j:AddressAugmentationType" 
              substitutionGroup="nc:AddressAugmentationPoint" nillable="true">
    <xs:annotation>
      <xs:documentation>Additional information about a Address.</xs:documentation>
    </xs:annotation>
  </xs:element>      
```

**NIEM CMF:**

```xml
  <Class structures:uri="nc:AddressType">
    <Name>AddressType</Name>
    <Namespace structures:uri="nc" xsi:nil="true"/>
    <DefinitionText>A data type for a geophysical location described by postal information.</DefinitionText>
    <HasProperty structures:sequenceID="1">
      <Property structures:uri="nc:AddressFullText" xsi:nil="true"/>
      <MinOccursQuantity>0</MinOccursQuantity>
      <MaxOccursQuantity>unbounded</MaxOccursQuantity>
    </HasProperty>
    <HasProperty structures:sequenceID="2">
      <Property structures:uri="nc:AddressAugmentationPoint" xsi:nil="true"/>
      <MinOccursQuantity>0</MinOccursQuantity>
      <MaxOccursQuantity>unbounded</MaxOccursQuantity>
    </HasProperty>
    <HasProperty structures:sequenceID="3">
      <Property structures:uri="j:AddressCommentText" xsi:nil="true"/>
      <MinOccursQuantity>0</MinOccursQuantity>
      <MaxOccursQuantity>unbounded</MaxOccursQuantity>
      <AugmentingNamespace structures:uri="j" xsi:nil="true"/>
    </HasProperty>
    <HasProperty structures:sequenceID="4">
      <Property structures:uri="j:AddressVerifiedDate" xsi:nil="true"/>
      <MinOccursQuantity>0</MinOccursQuantity>
      <MaxOccursQuantity>unbounded</MaxOccursQuantity>
      <AugmentingNamespace structures:uri="j" xsi:nil="true"/>
    </HasProperty>
  </Class>
```

A single property could be added to a Class by augmentations in different namespaces.  In that case, the `AugmentingNamespace` element is repeated.

The element substituted for the augmentation point does not have to have an augmentation type.  For example:

```xml
  <xs:element name="OperatingSchedule" type="cbrn:OperatingScheduleType"
            substitutionGroup="nc:ScheduleAugmentationPoint"/>
```

In this case the CMF model must indicate that this property was not added through an augmentation element.

```xml
  <HasProperty>
    <Property structures:uri="cbrn:OperatingSchedule" xsi:nil="true"/>
    <MinOccursQuantity>0</MinOccursQuantity>
    <MaxOccursQuantity>unbounded</MaxOccursQuantity>
    <AugmentingNamespace structures:uri="cbrn" xsi:nil="true"/>
    <DirectAugmentationIndicator>true</DirectAugmentationIndicator>
  </HasProperty>
```

There are some other aspects of augmentation in XSD that do not contribute to the model semantics and do not appear in the CMF.  

* The name of the augmentation type (an augmentation type for `BarType` might not be named `BarAugmentationType`)
* The name of the augmentation element (might not be `BarAugmentation`) 
* More than one augmentation element in a namespace that is substituted for `BarAugmentationPoint`.  For example, someone could create`ns:AddressAugmentation` and `ns:OtherAddressAugmentation`, both substitutable for `nc:AddressAugmentationPoint`.  

My preference is to forbid all three practices in the NDR.  If that is not acceptable, we'll have to put some extra information in the CMF-XSD extension.

I don't think cardinality constraints on augmentation points make any sense, so I'm not supporting them at present.  (BTW, I also don't believe in metadata on an augmentation element -- but that's not a CMF issue.)

Types with complex content are augmentable if and only if they have an `AugmentationPoint` element.  This is a property in the CMF model.

## Adapter types / External content

A namespace defined by a schema document that is not NIEM-conformant is an *external namespace;* its components are *external components.*  External attributes may be directly incorporated by types defined in an extension schema document.  External elements require an *adapter type.*

The components of an external namespace do not appear in a CMF file.  A CMF-to-XSD transformation will not produce external schema documents.  The CMF file does have a Namespace object for the external namespace, marked as external.  

**NIEM XSD**

```xml
<xs:import namespace="http://www.opengis.net/gml/3.2" 
  schemaLocation="../external/ogc/gml/3.2.1/gml.xsd" 
  appinfo:externalImportIndicator="true">
```

**NIEM CMF**

```xml
  <Namespace structures:uri="gml">
    <NamespaceURI>http://www.opengis.net/gml/3.2</NamespaceURI>
    <NamespacePrefixName>gml</NamespacePrefixName>
    <DefinitionText>GML Subset schema, written by gmlSubset.xslt</DefinitionText>
    <ExternalIndicator>true</ExternalIndicator>
  </Namespace>
```

An adapter type appears in CMF as a Class object, also marked as external.

**NIEM XSD**

```xml
  <xs:complexType name="PointType" appinfo:externalAdapterTypeIndicator="true">
    <xs:annotation>
      <xs:documentation>A data type for a 2D or 3D geometric point.  A gml:Point is defined by a single coordinate tuple. The direct position of a point is specified by the gml:pos element which is of type gml:DirectPositionType.</xs:documentation>
    </xs:annotation>
    <xs:complexContent>
      <xs:extension base="structures:ObjectType">
        <xs:sequence>
          <xs:element ref="gml:Point" minOccurs="1" maxOccurs="1">
            <xs:annotation>
              <xs:documentation>A gml:Point is defined by a single coordinate tuple. The direct position of a point is specified by the gml:pos element which is of type gml:DirectPositionType.</xs:documentation>
            </xs:annotation>
          </xs:element>
        </xs:sequence>
      </xs:extension>
    </xs:complexContent>
  </xs:complexType>
```

**NIEM CMF**

```xml
  <Class structures:uri="geo:PointType">
    <Name>PointType</Name>
    <Namespace structures:uri="geo" xsi:nil="true"/>
    <DefinitionText>A gml:Point is defined by a single coordinate tuple. The direct position of a point is specified by the gml:pos element which is of type gml:DirectPositionType.</DefinitionText>
    <HasProperty structures:sequenceID="1">
      <Property structures:uri="gml:Point" xsi:nil="true"/>
      <MinOccursQuantity>1</MinOccursQuantity>
      <MaxOccursQuantity>1</MaxOccursQuantity>
	  <ExternalIndicator>true</ExternalIndicator>
    </HasProperty>
  </Class>
```

## CMF-XSD Extension

Information in the XSD schema pile that does not belong in the CMF model is recorded in the CMF-XSD extension file.  This file is used in the CMF-to-XSD transformation.  It can be edited to control things like schema file names and the schema directory structure.  It can be omitted, in which case default values are used.  Here is a sample:

```xml
<ModelExtension xmlns="http://reference.niem.gov/specification/cmf/XMLExtension/1.0/">
  <SchemaDocument>
    <NamespaceURI>http://release.niem.gov/niem/domains/humanServices/5.0/</NamespaceURI>
    <ConformanceTargetURIList>http://reference.niem.gov/niem/specification/naming-and-design-rules/5.0/#ReferenceSchemaDocument</ConformanceTargetURIList>
    <DocumentFilePathText>niem/domains/hs.xsd</DocumentFilePathText>
    <NIEMVersionText>5.0</NIEMVersionText>
    <SchemaVersionText>1</SchemaVersionText>
    <NamespacePrefixText>hs</NamespacePrefixText>
  </SchemaDocument>
  <AttributeQName>nc:personNameCommentText</AttributeQName>
  <NillableElementQName>hs:Child</NillableElementQName>
  <NillableElementQName>hs:Parent</NillableElementQName>
</ModelExtension>
```

The difference between an attribute and and an element is an XML thing, so I record it here.  LIkewise the difference between nillable and non-nillable elements.  But I could move these into the CMF model, with `AttributeIndicator` and `ReferenceableIndicator` properties.  Thoughts?

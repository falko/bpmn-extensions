> An ItemAwareElement MAY be underspecified, meaning that the structure attribute of its ItemDefinition is optional if the modeler does not wish to define the structure of the associated data.

> [...] the source of the association is copied into the target.

> The core concepts of a DataAssociation are that they have sources, a target, and an optional transformation.
>
> When a data association is “executed,” data is copied to the target. What is copied depends if there is a transformation defined or not.
>
> If there is no transformation defined or referenced, then only one source MUST be defined, and the contents of this source will be copied into the target.
>
> If there is a transformation defined or referenced, then this transformation Expression will be evaluated and the result of the evaluation is copied into the target. There can be zero (0) to many sources defined in this case, but there is no requirement that these sources are used inside the Expression.

> when an Activity starts executing, the scope of valid targets include the Activity data inputs, while at the end of the Activity execution, the scope of valid sources include Activity data outputs.

```xml
	<xsd:element name="dataAssociation" type="tDataAssociation" />
	<xsd:complexType name="tDataAssociation">
		<xsd:complexContent>
			<xsd:extension base="tBaseElement">
				<xsd:sequence>
					<xsd:element name="sourceRef" type="xsd:IDREF" minOccurs="0" maxOccurs="unbounded"/>
					<xsd:element name="targetRef" type="xsd:IDREF" minOccurs="1" maxOccurs="1"/>
					<xsd:element name="transformation" type="tFormalExpression" minOccurs="0" maxOccurs="1"/>
					<xsd:element ref="assignment" minOccurs="0" maxOccurs="unbounded"/>
				</xsd:sequence>
			</xsd:extension>
		</xsd:complexContent>
	</xsd:complexType>
```

- [x] ~~Bug: assignment@expressionLanguage missing in MM & XSD but implied in text.~~ Okay it's done by using sub-classes of Expression for `from` and `to`.

  > The default Expression language for all Expressions is specified in the Definitions element, using the expressionLanguage attribute. It can also be overridden on each individual Assignment using the **same attribute**.

> By using an Assignment, single data structure elements can be assigned from the source structure to the target structure.

#### Execution Semantics for DataAssociation

> The execution of any Data Associations MUST follow these semantics:
> • If the Data Association specifies a “transformation” Expression, this expression is evaluated and the result is copied
> to the targetRef. This operation replaces completely the previous value of the targetRef element.
> • For each “assignment” element specified:
> 	• Evaluate the Assignment’s “from” expression and obtain the *source value*.
> 	• Evaluate the Assignment’s “to” expression and obtain the *target element*. The *target element* can be any element in the context or a sub-element of it (e.g., a DataObject or a sub-element of it).
> 	• Copy the *source value* to the *target element*.
> • If no “transformation” Expression nor any “assignment” elements are defined in the Data Association:
> 	• Copy the Data Association “sourceRef” value into the “targetRef.” Only one sourceRef element is allowed in this case.

#### Proposal: Shortcut for Data Inputs & Outputs

If `dataInputAssociation@targetRef` or `dataOutputAssociation@sourceRef` point to non-existing IDs, their values shall be treated as names of Data Inputs respectively Data Outputs.

For example:

```xml
    <bpmn:callActivity id="CallActivity_1" name="Sub-Process" calledElement="MySubProcess">
      <bpmn:dataInputAssociation>
        <bpmn:sourceRef>a</bpmn:sourceRef>
        <bpmn:targetRef>x</bpmn:targetRef>
      </bpmn:dataInputAssociation>
      <bpmn:dataInputAssociation>
        <bpmn:sourceRef>b</bpmn:sourceRef>
        <bpmn:targetRef>y</bpmn:targetRef>
      </bpmn:dataInputAssociation>
      <bpmn:dataOutputAssociation>
        <bpmn:sourceRef>z</bpmn:sourceRef>
        <bpmn:targetRef>c</bpmn:targetRef>
      </bpmn:dataOutputAssociation>
    </bpmn:callActivity>
```

is equivalent to:

```xml
    <bpmn:callActivity id="CallActivity_1" name="Sub-Process" calledElement="MySubProcess">
      <bpmn:ioSpecification>
        <bpmn:dataInput  id="x" name="x" />
        <bpmn:dataInput  id="y" name="y" />
        <bpmn:dataOutput id="z" name="z" />
        <bpmn:inputSet>
          <bpmn:dataInputRefs>x</bpmn:dataInputRefs>
          <bpmn:dataInputRefs>y</bpmn:dataInputRefs>
        </bpmn:inputSet>
        <bpmn:outputSet>
          <bpmn:dataOutputRefs>z</bpmn:dataOutputRefs>
        </bpmn:outputSet>
      </bpmn:ioSpecification>
      <bpmn:dataInputAssociation>
        <bpmn:sourceRef>a</bpmn:sourceRef>
        <bpmn:targetRef>x</bpmn:targetRef>
      </bpmn:dataInputAssociation>
      <bpmn:dataInputAssociation>
        <bpmn:sourceRef>b</bpmn:sourceRef>
        <bpmn:targetRef>y</bpmn:targetRef>
      </bpmn:dataInputAssociation>
      <bpmn:dataOutputAssociation>
        <bpmn:sourceRef>z</bpmn:sourceRef>
        <bpmn:targetRef>c</bpmn:targetRef>
      </bpmn:dataOutputAssociation>
    </bpmn:callActivity>

```



### Expressions

> BPMN extensibility mechanism enables the usage of various languages for Expressions and queries.

- [ ] Bug: Expression does not have any facility to store a natural language expression (In XML it does because in can contain any child elements. But no body in MM)

  > BPMN supports underspecified Expressions, where the logic is captured as natural-language descriptive text. It also supports formal Expressions, where the logic is captured in an executable form using a specified Expression language.

- [ ] Zeebe Modeler: Set defaultExpressionLanguage to FEEL 1.3
- [ ] Camunda Engine: Support defaultExpressionLanguage FEEL 1.3 to get Camunda BPM users ready for Zeebe

#### Ideas for further exploration

- [ ] Collect a list of all
  - [ ] BPMN elements and attributes
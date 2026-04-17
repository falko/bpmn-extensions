# AGENTS

## Project Snapshot
This repository contains BPMN 2.0 extension proposals (MIWG), with extension areas such as CMMN, DMN, dynamic binding, envelope decorator bounds, and i18n.

## BPMN/XML Conventions Observed
- BPMN model elements are written with the `bpmn:` prefix.
- The BPMN model namespace URI is `http://www.omg.org/spec/BPMN/20100524/MODEL` and is bound to `xmlns:bpmn`.
- Default namespace is bound to `targetNamespace` which allows model-scoped names to be referenced with unprefixed QName values for `*Ref` attributes.

## Validation Notes
- Validate BPMN XML with:
  - `xmllint --noout --schema bpmn/BPMN20.xsd "data-association/Incident Management(Process Engine Executable).2026.bpmn"`
- If default namespace is set to `targetNamespace`, unprefixed `xsi:type` values can fail schema resolution.
- Use prefixed type names for BPMN types in that case (for example `xsi:type="bpmn:tFormalExpression"`).

## Practical Guidance For Edits
- After namespace or QName changes, re-run `xmllint` schema validation.
- Prefer explicit prefixes for type QNames (`xsi:type`) to avoid namespace-resolution ambiguity.

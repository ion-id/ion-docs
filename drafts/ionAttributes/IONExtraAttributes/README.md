# IONExtraAttributes

`IONExtraAttributes` is a JSON-LD aware container for NP-specific class attributes.

The container requires an NP-provided `@context` URI and an `@type` value that is either compact or a full IRI. All other properties are allowed and are interpreted according to the supplied JSON-LD context.

## Scope

- Protocol version: `2.0`
- Semantic model: `generalised`
- Default attachment metadata: `extraAttributes`
- Namespace: `https://schema.beckn.io/IONExtraAttributes#`

## Non-goals

This schema does not define NP-specific terms directly. Network participants should publish those terms in their JSON-LD context and vocabulary.

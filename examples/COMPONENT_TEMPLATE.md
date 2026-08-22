<!--
  COMPONENT_TEMPLATE.md — blank template
  
  Copy this file to the directory of your component, rename to
  COMPONENT_<component_name>.md (e.g., COMPONENT_authentication_service.md),
  and fill in the authored fields. The auto-generated fields should be filled
  by a generator tool, not by hand.
-->

# COMPONENT_TEMPLATE: 

## location
<!-- [auto] -->
- `<path/to/component.py>`

## verified_against
<!-- [authored] — git short hash at which the authored fields were last reviewed -->
`<commit_hash>` (<YYYY-MM-DD>)

## depends_on
<!-- [auto] -->
- `<DependencyName>` — `<path/to/dependency.py>`

## used_by
<!-- [auto] -->
- `<ConsumerName>` — `<path/to/consumer.py>`

## change_impact
<!-- [authored] -->

- <Describe what kinds of changes affect which downstream components, and how.>

## targeted_verification
<!-- [authored] -->

- `<test_module>::<test_name>`

## public_interface
<!-- [authored, optional] -->

Stable, do not change without coordination:
- `<signature>`

## stability
<!-- [authored, optional] -->
<!-- one of: experimental | stable | frozen | deprecated -->
`<stability>`

## notes
<!-- [authored, optional] -->

- <Free-form context that does not fit elsewhere. Use sparingly.>

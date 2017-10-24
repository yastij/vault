---
layout: "docs"
page_title: "Sentinel Properties"
sidebar_current: "docs-vault-enterprise-sentinel-properties"
description: |-
  An overview of how Sentinel interacts with Vault Enterprise.

---

Vault injects a rich set of data into the running Sentinel environment,
allowing for very fine-grained controls. The set of available properties are
enumerated on this page.

# Properties

The following properties are available for use in Sentinel policies.

## Request Properties

The following properties are available in the `request` namespace.

| Name | Type | Description |
| :------- | :--------------------------- | :--------------------- |
| `connection.remote_addr` | `string` | TCP/IP source address/port of the client |
| `data` | `map (string -> any)` | Raw request data |
| `operation` | `string` | Operation type, e.g. "read" or "update" |
| `path` | `string` | Path, with any leading `/` trimmed |
| `policy_override` | `bool` | `true` if a `soft-mandatory` policy override was requested |
| `unauthenticated` | `bool` | `true` if the requested path is an unauthenticated path |
| `wrapping.ttl` | `duration` | The requested response-wrapping TTL in nanoseconds, suitable for use with the `time` import|
| `wrapping.ttl_seconds` | `int` | The requested response-wrapping TTL in seconds |

## Token Properties

The following properties, if available, are in the `token` namespace. The
namespace will not exist if there is no token information attached to a
request, e.g. when logging in.

| Name | Type | Description |
| :------- | :--------------------------- | :--------------------- |
| `creation_time` | `string` | The timestamp of the token's creation, in RFC3339 format |
| `creation_time_unix` | `int` | The timestamp of the token's creation, in seconds since Unix epoch UTC |
| `creation_ttl` | `duration` | The TTL the token was first created with in nanoseconds, suitable for use with the `time` import |
| `creation_ttl_seconds` | `int` | The TTL the token was first created with in seconds |
| `display_name` | `string` | The display name set on the token, if any |
| `entity_id` | `string` | The Identity entity ID attached to the token, if any |
| `explicit_max_ttl` | `duration` | If the token has an explicit max TTL, the duration of the explicit max TTL in nanoseconds, suitable for use with the `time` import |
| `explicit_max_ttl_seconds` | `int` | If the token has an explicit max TTL, the duration of the explicit max TTL in seconds |
| `metadata` | `map (string -> string)` | Metadata set on the token |
| `num_uses` | `int` | The number of uses remaining on a use-count-limited token; 0 if the token has no use-count limit |
| `path` | `string` | The request path that resulted in creation of this token |
| `period` | `duration` | If the token has a period, the duration of the period in nanoseconds, suitable for use with the `time` import |
| `period_seconds` | `int` | If the token has a period, the duration of the period in seconds |
| `policies` | `list (string)` | Policies directly attached to the token |
| `role` | `string` | If created via a token role, the role that created the token |

## Identity Properties

The following properties, if available, are in the `identity` namespace. The
namespace may not exist if there is no token information attached to the
request; however, at login time the user's request data will be used to attempt
to find any existing Identity information, or create some information to pass
to MFA functions.

### Entity Properties

These exist at the `identity.entity` namespace.

| Name | Type | Description |
| :------- | :--------------------------- | :--------------------- |
| `creation_time` | `string` | The entity's creation time in RFC3339 format |
| `id` | `string` | The entity's ID |
| `last_update_time` | `string` | The entity's last update (modify) time in RFC3339 format |
| `metadata` | `map (string -> string)` | Metadata associated with the entity |
| `name` | `string` | The entity's name |
| `merged_entity_ids` | `list (string)` | A list of IDs of entities that have been merged into this one |
| `personas` | `list (persona)` | List of personas associated with this entity |
| `policies` | `list (string)` | List of the policies set on this entity |

### Persona Properties

These can be retrieved from `identity.entity.personas`.

| Name | Type | Description |
| :------- | :--------------------------- | :--------------------- |
| `creation_time` | `string` | The persona's creation time in RFC3339 format |
| `id` | `string` | The persona's ID |
| `last_update_time` | `string` | The persona's last update (modify) time in RFC3339 format |
| `metadata` | `map (string -> string)` | Metadata associated with the persona|
| `merged_from_entity_ids` | `list (string)` | If this persona was attached to the current entity via one or more merges, the original entity/entities will be in this list |
| `mount_accessor` | `string` | The immutable accessor of the mount that created this persona |
| `mount_path` | `string` | The path of the mount that created this persona; unlike the accessor, there is no guarantee that the current path represents the original mount |
| `mount_type` | `string` | The type of the mount that created this persona |
| `name` | `string` | The persona's name |

### Groups Properties

These exist at the `identity.groups` namespace.

| Name | Type | Description |
| :------- | :--------------------------- | :--------------------- |
| `by_id` | `map (string -> group)` | A map of group ID to group information |
| `by_name` | `map (string -> group)` | A map of group name to group information; unlike the group ID, there is no guarantee that the current name will always represent the same group |

### Group Properties

These can be retrieved from the `identity.groups` maps.

| Name | Type | Description |
| :------- | :--------------------------- | :--------------------- |
| `creation_time` | `string` | The group's creation time in RFC3339 format |
| `id` | `string` | The group's ID |
| `last_update_time` | `string` | The group's last update (modify) time in RFC3339 format |
| `metadata` | `map (string -> string)` | Metadata associated with the group |
| `name` | `string` | The group's name |
| `member_entity_ids` | `list (string)` | A list of IDs of entities that are directly assigned to this group |
| `parent_group_ids` | `list (string)` | A list of IDs of groups that are parents of this group |
| `policies` | `list (string)` | List of the policies set on this group |

## MFA Properties

These properties exist at the `mfa` namespace.

| Name | Type | Description |
| :------- | :--------------------------- | :--------------------- |
| `methods` | `map (string -> method)` | A map of method name to method properties |

### MFA Method Properties

These properties can be accessed via the `mfa.methods` selector.

| Name | Type | Description |
| :------- | :--------------------------- | :--------------------- |
| `valid` | `bool` | Whether the method has successfully been validated; if validation has not been attempted, this will trigger the validation attempt. The result of the validation attempt will be used for this method for all policies for the given request. |
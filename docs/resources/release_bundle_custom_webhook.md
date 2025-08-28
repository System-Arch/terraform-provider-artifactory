---
subcategory: "Deprecated"
---
# Artifactory Release Bundle Custom Webhook Resource

Provides an Artifactory custom webhook resource. This can be used to register and manage Artifactory webhook subscription which enables you to be notified or notify other users when such events take place in Artifactory.

!>This resource is being deprecated and replaced by `artifactory_destination_custom_webhook` resource.

## Example Usage

```hcl
resource "artifactory_release_bundle_custom_webhook" "release-bundle-custom-webhook" {
  key           = "release-bundle-custom-webhook"
  event_types   = ["created", "signed", "deleted"]
  criteria {
    any_release_bundle              = false
    registered_release_bundle_names = ["bundle-name"]
    include_patterns                = ["foo/**"]
    exclude_patterns                = ["bar/**"]
  }

  handler {
    url       = "https://tempurl.org"
    method    = "POST"
    secrets   = {
      secretName1 = "value1"
      secretName2 = "value2"
    }
    http_headers  = {
      headerName1    = "value1"
      headerName2    = "value2"
    }
    payload = "{ \"ref\": \"main\" , \"inputs\": { \"artifact_path\": \"test-repo/repo-path\" } }"
  }
}
```

## Argument Reference

Arguments have a one to one mapping with the [JFrog Webhook API](https://www.jfrog.com/confluence/display/JFROG/Artifactory+REST+API). The following arguments are supported:

The following arguments are supported:

* `key` - (Required) The identity key of the webhook. Must be between 2 and 200 characters. Cannot contain spaces.
* `description` - (Optional) Webhook description. Max length 1000 characters.
* `enabled` - (Optional) Status of webhook. Default to `true`.
* `event_types` - (Required) List of Events in Artifactory, Distribution, Release Bundle that function as the event trigger for the Webhook. Allow values: `created`, `signed`, `deleted`.
* `criteria` - (Required) Specifies where the webhook will be applied on which repositories.
  * `any_release_bundle` - (Required) Trigger on any release bundle.
  * `registered_release_bundle_names` - (Optional) Trigger on this list of release bundle names.
  * `include_patterns` - (Optional) Simple comma separated wildcard patterns for repository artifact paths (with no leading slash). Ant-style path expressions are supported (*, *\*, ?). For example: "org/apache/**".
  * `exclude_patterns` - (Optional) Simple comma separated wildcard patterns for repository artifact paths (with no leading slash). Ant-style path expressions are supported (*, *\*, ?). For example: "org/apache/**".
* `handler` - (Required) At least one is required.
  * `url` - (Required) Specifies the URL that the Webhook invokes. This will be the URL that Artifactory will send an HTTP POST request to.
  * `method` - (Optional) Specifies the HTTP method for the URL that the Webhook invokes. Allowed values are: `GET`, `POST`, `PUT`, `PATCH`, `DELETE`.
  * `secrets` - (Optional) Defines a set of sensitive values (such as, tokens and passwords) that can be injected in the headers and/or payload.Secrets’ values are encrypted. In the header/payload, the value can be invoked using the `{{.secrets.token}}` format, where token is the name provided for the secret value. Comprise key/value pair. **Note:** if multiple handlers are used, same secret name and different secret value for the same url won't work. Example:

```hcl
      handler {
        url       = "https://tempurl.org" # same url in both handlers
        secrets   = {
          secretName1 = "value1"
          secretName2 = "value2"
        }
        http_headers  = {
          headerName1    = "value1"
          headerName2    = "value2"
        }
        payload = "{ \"ref\": \"main\" , \"inputs\": { \"artifact_path\": \"test-repo/repo-path\" } }"
      }
      handler {
        url       = "https://tempurl.org" # same url in both handlers
        secrets   = {
          secretName1 = "newValue1" # same secret name, but different value
          secretName2 = "newValue2" # same secret name, but different value
        }
        http_headers  = {
          headerName1    = "value1"
          headerName2    = "value2"
        }
        payload = "{ \"ref\": \"main\" , \"inputs\": { \"artifact_path\": \"test-repo/repo-path\" } }"
      }
```

* `proxy` - (Optional) Proxy key from Artifactory UI (Administration -> Proxies -> Configuration).
* `http_headers` - (Optional) HTTP headers you wish to use to invoke the Webhook, comprise key/value pair.

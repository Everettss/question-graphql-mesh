# Example for question how to achieve nested encapsulation

Currently, there is one level of encapsulation,\
`wikimedia.org/1.0.0/swagger.yaml` is wrapped in `wikimedia`\
`healthcare.gov/1.0.0/openapi.yaml` is wrapped in `healthcare`:

`.meshrc.yaml` contains this definitions:
```yaml
sources:
  - name: Wikimedia
    handler:
      openapi:
        source: https://api.apis.guru/v2/specs/wikimedia.org/1.0.0/swagger.yaml
    transforms:
      - prefix:
          value: Wikimedia_
      - encapsulate:
          name: wikimedia
          applyTo:
            query: true
            mutation: false
            subscription: false
  - name: Healthcare
    handler:
      openapi:
        source: https://api.apis.guru/v2/specs/healthcare.gov/1.0.0/openapi.yaml
    transforms:
      - prefix:
          value: Healthcare_
      - encapsulate:
          name: healthcare
          applyTo:
            query: true
            mutation: false
            subscription: false
```

This `.meshrc.yaml` generates this root level Query type:

```graphql
type Query {
    healthcare: healthcareQuery
    wikimedia: wikimediaQuery
}
```

To execute this query I run

```graphql
query currentQuery {
    healthcare {
        getApiArticlesMediaTypeExtension(mediaTypeExtension: _JSON) {
            articles {
                date
            }
        }
    }
    wikimedia {
        getFeedAvailability {
            inTheNews
        }
    }
}
```

**My question is how to modify this example to wrap fields `wikimedia` and `healthcare`
in `NestedType` like this:**

```graphql
type NestedType {
    healthcare: healthcareQuery
    wikimedia: wikimediaQuery
}

type Query {
    nestedType: NestedType
}
```

So I could query this schema by:

```graphql
query desiredQuery {
    nestedType {
        healthcare {
            getApiArticlesMediaTypeExtension(mediaTypeExtension: _JSON) {
                articles {
                    date
                }
            }
        }
        wikimedia {
            getFeedAvailability {
                inTheNews
            }
        }
    }
}
```

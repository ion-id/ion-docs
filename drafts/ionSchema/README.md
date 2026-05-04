## Introduction
This folder contains files required to test the IONAttributes design proposal. This document describes the issue, the proposed solution as well as extracted json messages with annotation. 

## Requirement
The version 2 of Beckn is designed to be extensible through context.jsonld and corresponding attribute.yaml file. In v2, beckn.yaml file only contains the very basic structures and everything else is extensible through the Attributes class which allows plugging in of a context and type. So beckn.yaml contains very few json-schema validation rules. All the others have to be part of the attributes.yaml file found along with the context.jsonld of the particular extension object (e.g. resourceAttributes etc)

One problem with this approach is that a NP can actually point the context.jsonld to a custom location that can potentially skip all validation. It also skips validation at the other counter party NP. This is a fundamental problem that cannot be solved if the validation comes along with json packet. The only way validations will be guaranteed to work is when they are configured at the ONIX of the NP with rules either stored locally or fetched from network location.

Having said that, this exercise wanted to do the following.
1. Atleast ensure that the base extension objects (resourceAttributes, offerAttributes, participantAttributes) etc is not easily replaced by a dummy. 
2. Restrict NP from adding additional attributes to these core extension objects.
3. Provide the NPs ability to add additional attributes at one level below the core object. They should have the ability to extend it the way they want.

## Design
1. Limit the context of the Attributes object in Beckn.yaml to only refer to ION schema. That way the core object cannot be anything else. In this example we have defined a core object called TradeResource that goes into the resourceAttributes field of the Resource object. This cannot come from any other location. The pattern clause is added to the @context below to achieve it.

```
    Attributes:
      description: JSON-LD aware container for domain-specific attributes of an Item. MUST include @context (URI) and @type (compact or full IRI). Any additional properties are allowed and interpreted per the provided JSON-LD context.
      title: Attributes
      type: object
      properties:
        '@context':
          description: Use case specific JSON-LD context URI
          type: string
          pattern: "^(https://raw.githubusercontent.com/ion-id/ion-docs/refs/heads/ionAttributes/drafts/ionSchema/ionAttributes|https://raw.githubusercontent.com/beckn/local-retail)"
        '@type':
          description: JSON-LD type defined within the context
          type: string
      required:
      - '@context'
      - '@type'
      additionalProperties: true
```

2. Define a new [IONExtraAttributes Schema](https://github.com/ion-id/ion-docs/tree/ionAttributes/drafts/ionSchema/IONExtraAttributes) which is identical to the Attributes Schema in Beckn. This allows for NP extensions. 
3. Make the core object [TradeResource](https://github.com/ion-id/ion-docs/tree/ionAttributes/drafts/ionSchema/ionAttributes/resourceAttributes/TradeResource) by default closed to additionalProperties.
4. Create a field in TradeResource called extraAttributes that is of type IONExtraAttributes and allows NP specific extensions.



## Postman collections

The postman collection is present in this folder. The json message content is listed below for easy reference.

### Baseline Beckn Local Retail sample message
- This is the baseline request on which other modifications were made
- It should work without errors
```
{
  "context": {
    "version": "2.0.0",
    "action": "catalog/publish",
    "timestamp": "2026-05-04T10:17:45.585Z",
    "messageId": "4da3c319-16e5-4441-b074-3abe93740f7e",
    "transactionId": "3fc6ae79-3933-4c75-97db-5863be0aa932",
    "bppId": "sandbox.open-kitchen.com",
    "bppUri": "http://onix-bpp:8082/bpp/receiver",
    "ttl": "PT30S",
    "networkId": "beckn.one/testnet-retail"
  },
"message": {
    "catalogs": [
      {
        "id": "catalog-hbo-001",
        "bppId": "bpp.example.com",
        "bppUri": "http://onix-bpp:8082/bpp/receiver",
        "descriptor": {
          "name": "HBO",
          "shortDesc": "Electronics and home goods catalog"
        },
        "provider": {
          "id": "provider-venky-bazaar",
          "descriptor": {
            "name": "Venky.Mahadevan@Bazaar"
          }
        },
        "validity": {
          "startDate": "2023-01-01T00:00:00Z",
          "endDate": "2023-12-31T23:59:59Z"
        },
        "resources": [
          {
            "id": "item-flask-mh500-yellow",
            "descriptor": {
              "name": "Isothermal Stainless Steel Hiking Flask MH500 Yellow",
              "shortDesc": "Double walled vacuum insulated stainless steel thermos flask, 500ml",
              "mediaFile": [
                {
                  "label": "Product Image",
                  "mimeType": "image/jpeg",
                  "uri": "https://tourism-bpp-infra2.becknprotocol.io/attachments/view/253.jpg"
                }
              ]
            },
            "resourceAttributes": {
              "@context": "https://raw.githubusercontent.com/beckn/local-retail/refs/heads/main/schema/RetailResource/v2.1/context.jsonld",
              "@type": "RetailResource",
              "identity": {
                "brand": "InstaCuppa",
                "originCountry": "IN"
              },
              "physical": {
                "weight": {
                  "unitCode": "G",
                  "unitQuantity": 350
                },
                "volume": {
                  "unitCode": "ML",
                  "unitQuantity": 500
                },
                "dimensions": {
                  "unit": "CM",
                  "length": 25,
                  "breadth": 7,
                  "height": 7
                },
                "appearance": {
                  "color": "Yellow",
                  "material": "Stainless Steel",
                  "finish": "Matte"
                }
              },
              "packagedGoodsDeclaration": {
                "manufacturerOrPacker": {
                  "type": "MANUFACTURER",
                  "name": "InstaCuppa India Pvt Ltd",
                  "address": "Bangalore, Karnataka, IN"
                },
                "commonOrGenericName": "Stainless Steel Vacuum Flask",
                "netQuantity": {
                  "unitCode": "ML",
                  "unitQuantity": 500
                }
              }
            }
          }
        ],
        "offers": [
          {
            "id": "offer-flask-mh500-yellow",
            "descriptor": {
              "name": "Isothermal Stainless Steel Hiking Flask MH500 Yellow"
            },
            "resourceIds": [
              "item-flask-mh500-yellow"
            ],
            "provider": {
              "id": "provider-venky-bazaar",
              "descriptor": {
                "name": "Venky.Mahadevan@Bazaar"
              }
            },
            "validity": {
              "startDate": "2023-01-01T00:00:00Z",
              "endDate": "2023-12-31T23:59:59Z"
            },
            "offerAttributes": {
              "@context": "https://raw.githubusercontent.com/beckn/local-retail/refs/heads/main/schema/RetailOffer/v2.1/context.jsonld",
              "@type": "RetailOffer",
              "policies": {
                "returns": {
                  "allowed": true,
                  "window": "P7D",
                  "method": "SELLER_PICKUP"
                },
                "cancellation": {
                  "allowed": true,
                  "window": "PT2H",
                  "cutoffEvent": "BEFORE_PACKING"
                },
                "replacement": {
                  "allowed": true,
                  "window": "P7D",
                  "method": "SELLER_PICKUP",
                  "subjectToAvailability": true
                }
              },
              "paymentConstraints": {
                "codAvailable": true
              },
              "serviceability": {
                "distanceConstraint": {
                  "maxDistance": 15,
                  "unit": "KM"
                },
                "timing": [
                  {
                    "daysOfWeek": [
                      "MON",
                      "TUE",
                      "WED",
                      "THU",
                      "FRI",
                      "SAT",
                      "SUN"
                    ],
                    "timeRange": {
                      "start": "09:00",
                      "end": "21:00"
                    }
                  }
                ]
              }
            }
          }
        ]
      }
    ]
  }
}
```

### IONAttributes example with no extra NP attributes
- This uses the TradeResource jsonld, but does not have NP defined extra attributes.
- It should work

```
{
  "context": {
    "version": "2.0.0",
    "action": "catalog/publish",
    "timestamp": "2026-05-04T10:21:17.831Z",
    "messageId": "4ef0bfcf-b920-4ff9-8f1c-c7c838d11f2e",
    "transactionId": "4112ac53-6be4-4b29-ab45-380847814cd5",
    "bppId": "sandbox.open-kitchen.com",
    "bppUri": "http://onix-bpp:8082/bpp/receiver",
    "ttl": "PT30S",
    "networkId": "beckn.one/testnet-retail"
  },
"message": {
    "catalogs": [
      {
        "id": "catalog-hbo-001",
        "bppId": "bpp.example.com",
        "bppUri": "http://onix-bpp:8082/bpp/receiver",
        "descriptor": {
          "name": "HBO",
          "shortDesc": "Electronics and home goods catalog"
        },
        "provider": {
          "id": "provider-venky-bazaar",
          "descriptor": {
            "name": "Venky.Mahadevan@Bazaar"
          }
        },
        "validity": {
          "startDate": "2023-01-01T00:00:00Z",
          "endDate": "2023-12-31T23:59:59Z"
        },
        "resources": [
          {
            "id": "item-flask-mh500-yellow",
            "descriptor": {
              "name": "Isothermal Stainless Steel Hiking Flask MH500 Yellow",
              "shortDesc": "Double walled vacuum insulated stainless steel thermos flask, 500ml",
              "mediaFile": [
                {
                  "label": "Product Image",
                  "mimeType": "image/jpeg",
                  "uri": "https://tourism-bpp-infra2.becknprotocol.io/attachments/view/253.jpg"
                }
              ]
            },
            "resourceAttributes": {
              "@context": "https://raw.githubusercontent.com/ion-id/ion-docs/refs/heads/ionAttributes/drafts/ionSchema/ionAttributes/resourceAttributes/TradeResource/v2.1/context.jsonld", 
              "@type": "TradeResource",
              "identity": {
                "brand": "InstaCuppa",
                "originCountry": "IN"
              }
            }
          }
        ],
        "offers": [
          {
            "id": "offer-flask-mh500-yellow",
            "descriptor": {
              "name": "Isothermal Stainless Steel Hiking Flask MH500 Yellow"
            },
            "resourceIds": [
              "item-flask-mh500-yellow"
            ],
            "provider": {
              "id": "provider-venky-bazaar",
              "descriptor": {
                "name": "Venky.Mahadevan@Bazaar"
              }
            },
            "validity": {
              "startDate": "2023-01-01T00:00:00Z",
              "endDate": "2023-12-31T23:59:59Z"
            },
            "offerAttributes": {
              "@context": "https://raw.githubusercontent.com/beckn/local-retail/refs/heads/main/schema/RetailOffer/v2.1/context.jsonld",
              "@type": "RetailOffer",
              "policies": {
                "returns": {
                  "allowed": true,
                  "window": "P7D",
                  "method": "SELLER_PICKUP"
                },
                "cancellation": {
                  "allowed": true,
                  "window": "PT2H",
                  "cutoffEvent": "BEFORE_PACKING"
                },
                "replacement": {
                  "allowed": true,
                  "window": "P7D",
                  "method": "SELLER_PICKUP",
                  "subjectToAvailability": true
                }
              },
              "paymentConstraints": {
                "codAvailable": true
              },
              "serviceability": {
                "distanceConstraint": {
                  "maxDistance": 15,
                  "unit": "KM"
                },
                "timing": [
                  {
                    "daysOfWeek": [
                      "MON",
                      "TUE",
                      "WED",
                      "THU",
                      "FRI",
                      "SAT",
                      "SUN"
                    ],
                    "timeRange": {
                      "start": "09:00",
                      "end": "21:00"
                    }
                  }
                ]
              }
            }
          }
        ]
      }
    ]
  }
}
```

### IONAttribute Example with extra attributes at the TradeResource level
- Uses the TradeResource Schema
- Defines a field called physical at the TradeResource level
- Should fail with error 
```
{
    "code": "Bad Request",
    "paths": "message.catalogs[0].resources[0].resourceAttributes.properties",
    "message": "property \"physical\" is unsupported"
}
```

```
{
    "context": {
        "version": "2.0.0",
        "action": "catalog/publish",
        "timestamp": "2026-05-04T10:26:14.003Z",
        "messageId": "bee69dc8-6d8a-4716-b9ce-5e1bdbbecf30",
        "transactionId": "a3ad2e1e-5d3a-4e3b-a557-0b2e501604e1",
        "bppId": "sandbox.open-kitchen.com",
        "bppUri": "http://onix-bpp:8082/bpp/receiver",
        "ttl": "PT30S",
        "networkId": "beckn.one/testnet-retail"
    },
    "message": {
        "catalogs": [
            {
                "id": "catalog-hbo-001",
                "bppId": "bpp.example.com",
                "bppUri": "http://onix-bpp:8082/bpp/receiver",
                "descriptor": {
                    "name": "HBO",
                    "shortDesc": "Electronics and home goods catalog"
                },
                "provider": {
                    "id": "provider-venky-bazaar",
                    "descriptor": {
                        "name": "Venky.Mahadevan@Bazaar"
                    }
                },
                "validity": {
                    "startDate": "2023-01-01T00:00:00Z",
                    "endDate": "2023-12-31T23:59:59Z"
                },
                "resources": [
                    {
                        "id": "item-flask-mh500-yellow",
                        "descriptor": {
                            "name": "Isothermal Stainless Steel Hiking Flask MH500 Yellow",
                            "shortDesc": "Double walled vacuum insulated stainless steel thermos flask, 500ml",
                            "mediaFile": [
                                {
                                    "label": "Product Image",
                                    "mimeType": "image/jpeg",
                                    "uri": "https://tourism-bpp-infra2.becknprotocol.io/attachments/view/253.jpg"
                                }
                            ]
                        },
                        "resourceAttributes": {
                            "@context": "https://raw.githubusercontent.com/ion-id/ion-docs/refs/heads/ionAttributes/drafts/ionSchema/ionAttributes/resourceAttributes/TradeResource/v2.1/context.jsonld",
                            "@type": "TradeResource",
                            "identity": {
                                "brand": "InstaCuppa",
                                "originCountry": "IN"
                            },
                            "physical": {
                                "weight": {
                                    "unitCode": "G",
                                    "unitQuantity": 350
                                },
                                "volume": {
                                    "unitCode": "ML",
                                    "unitQuantity": 500
                                },
                                "dimensions": {
                                    "unit": "CM",
                                    "length": 25,
                                    "breadth": 7,
                                    "height": 7
                                },
                                "appearance": {
                                    "color": "Yellow",
                                    "material": "Stainless Steel",
                                    "finish": "Matte"
                                }
                            }
                        }
                    }
                ],
                "offers": [
                    {
                        "id": "offer-flask-mh500-yellow",
                        "descriptor": {
                            "name": "Isothermal Stainless Steel Hiking Flask MH500 Yellow"
                        },
                        "resourceIds": [
                            "item-flask-mh500-yellow"
                        ],
                        "provider": {
                            "id": "provider-venky-bazaar",
                            "descriptor": {
                                "name": "Venky.Mahadevan@Bazaar"
                            }
                        },
                        "validity": {
                            "startDate": "2023-01-01T00:00:00Z",
                            "endDate": "2023-12-31T23:59:59Z"
                        },
                        "offerAttributes": {
                            "@context": "https://raw.githubusercontent.com/beckn/local-retail/refs/heads/main/schema/RetailOffer/v2.1/context.jsonld",
                            "@type": "RetailOffer",
                            "policies": {
                                "returns": {
                                    "allowed": true,
                                    "window": "P7D",
                                    "method": "SELLER_PICKUP"
                                },
                                "cancellation": {
                                    "allowed": true,
                                    "window": "PT2H",
                                    "cutoffEvent": "BEFORE_PACKING"
                                },
                                "replacement": {
                                    "allowed": true,
                                    "window": "P7D",
                                    "method": "SELLER_PICKUP",
                                    "subjectToAvailability": true
                                }
                            },
                            "paymentConstraints": {
                                "codAvailable": true
                            },
                            "serviceability": {
                                "distanceConstraint": {
                                    "maxDistance": 15,
                                    "unit": "KM"
                                },
                                "timing": [
                                    {
                                        "daysOfWeek": [
                                            "MON",
                                            "TUE",
                                            "WED",
                                            "THU",
                                            "FRI",
                                            "SAT",
                                            "SUN"
                                        ],
                                        "timeRange": {
                                            "start": "09:00",
                                            "end": "21:00"
                                        }
                                    }
                                ]
                            }
                        }
                    }
                ]
            }
        ]
    }
}
```

### IONAttribute example with extra attributes at the NP Extra Attributes level
- Uses TradeResource schema
- Adds attribute `physical` under extraAttributes
- Should work without error


```
{
  "context": {
    "version": "2.0.0",
    "action": "catalog/publish",
    "timestamp": "2026-05-04T10:27:50.320Z",
    "messageId": "1f3232a3-50c4-45e4-95c4-4783f2e43da2",
    "transactionId": "45883038-6d44-4b66-90bb-502f875caf63",
    "bppId": "sandbox.open-kitchen.com",
    "bppUri": "http://onix-bpp:8082/bpp/receiver",
    "ttl": "PT30S",
    "networkId": "beckn.one/testnet-retail"
  },
"message": {
    "catalogs": [
      {
        "id": "catalog-hbo-001",
        "bppId": "bpp.example.com",
        "bppUri": "http://onix-bpp:8082/bpp/receiver",
        "descriptor": {
          "name": "HBO",
          "shortDesc": "Electronics and home goods catalog"
        },
        "provider": {
          "id": "provider-venky-bazaar",
          "descriptor": {
            "name": "Venky.Mahadevan@Bazaar"
          }
        },
        "validity": {
          "startDate": "2023-01-01T00:00:00Z",
          "endDate": "2023-12-31T23:59:59Z"
        },
        "resources": [
          {
            "id": "item-flask-mh500-yellow",
            "descriptor": {
              "name": "Isothermal Stainless Steel Hiking Flask MH500 Yellow",
              "shortDesc": "Double walled vacuum insulated stainless steel thermos flask, 500ml",
              "mediaFile": [
                {
                  "label": "Product Image",
                  "mimeType": "image/jpeg",
                  "uri": "https://tourism-bpp-infra2.becknprotocol.io/attachments/view/253.jpg"
                }
              ]
            },
            "resourceAttributes": {
              "@context": "https://raw.githubusercontent.com/ion-id/ion-docs/refs/heads/ionAttributes/drafts/ionSchema/ionAttributes/resourceAttributes/TradeResource/v2.1/context.jsonld", 
              "@type": "TradeResource",
              "identity": {
                "brand": "InstaCuppa",
                "originCountry": "IN"
              },
              "extraAttributes":{
                "@context": "https://raw.githubusercontent.com/beckn/local-retail/refs/heads/main/schema/RetailResource/v2.1/context.jsonld",
                "@type": "RetailResource",                
                "physical": {
                    "weight": {
                    "unitCode": "G",
                    "unitQuantity": 350
                    },
                    "volume": {
                    "unitCode": "ML",
                    "unitQuantity": 500
                    },
                    "dimensions": {
                    "unit": "CM",
                    "length": 25,
                    "breadth": 7,
                    "height": 7
                    },
                    "appearance": {
                    "color": "Yellow",
                    "material": "Stainless Steel",
                    "finish": "Matte"
                    }
                }
              }
            }
          }
        ],
        "offers": [
          {
            "id": "offer-flask-mh500-yellow",
            "descriptor": {
              "name": "Isothermal Stainless Steel Hiking Flask MH500 Yellow"
            },
            "resourceIds": [
              "item-flask-mh500-yellow"
            ],
            "provider": {
              "id": "provider-venky-bazaar",
              "descriptor": {
                "name": "Venky.Mahadevan@Bazaar"
              }
            },
            "validity": {
              "startDate": "2023-01-01T00:00:00Z",
              "endDate": "2023-12-31T23:59:59Z"
            },
            "offerAttributes": {
              "@context": "https://raw.githubusercontent.com/beckn/local-retail/refs/heads/main/schema/RetailOffer/v2.1/context.jsonld",
              "@type": "RetailOffer",
              "policies": {
                "returns": {
                  "allowed": true,
                  "window": "P7D",
                  "method": "SELLER_PICKUP"
                },
                "cancellation": {
                  "allowed": true,
                  "window": "PT2H",
                  "cutoffEvent": "BEFORE_PACKING"
                },
                "replacement": {
                  "allowed": true,
                  "window": "P7D",
                  "method": "SELLER_PICKUP",
                  "subjectToAvailability": true
                }
              },
              "paymentConstraints": {
                "codAvailable": true
              },
              "serviceability": {
                "distanceConstraint": {
                  "maxDistance": 15,
                  "unit": "KM"
                },
                "timing": [
                  {
                    "daysOfWeek": [
                      "MON",
                      "TUE",
                      "WED",
                      "THU",
                      "FRI",
                      "SAT",
                      "SUN"
                    ],
                    "timeRange": {
                      "start": "09:00",
                      "end": "21:00"
                    }
                  }
                ]
              }
            }
          }
        ]
      }
    ]
  }
}
```

### IONAttribute example where it tries to override the TradeResource context with something else.
- Tries to override TradeResource schema with custom schema called `GroceryResource`
- Fails with error 
```
{
  "code": "Bad Request",
  "paths": "message/catalogs/0/resources/0/resourceAttributes/@context",
  "message": "string doesn't match the regular expression \"^(https://raw.githubusercontent.com/ion-id/ion-docs/refs/heads/ionAttributes/drafts/ionSchema/ionAttributes|https://raw.githubusercontent.com/beckn/local-retail)\""
}
```

```
{
  "context": {
    "version": "2.0.0",
    "action": "catalog/publish",
    "timestamp": "2026-05-04T10:29:17.791Z",
    "messageId": "613771c9-e4b4-4e08-bb9d-7ab787602fa4",
    "transactionId": "9512c0bb-c99d-4dd2-b073-1a46905b5749",
    "bppId": "sandbox.open-kitchen.com",
    "bppUri": "http://onix-bpp:8082/bpp/receiver",
    "ttl": "PT30S",
    "networkId": "beckn.one/testnet-retail"
  },
"message": {
    "catalogs": [
      {
        "id": "catalog-hbo-001",
        "bppId": "bpp.example.com",
        "bppUri": "http://onix-bpp:8082/bpp/receiver",
        "descriptor": {
          "name": "HBO",
          "shortDesc": "Electronics and home goods catalog"
        },
        "provider": {
          "id": "provider-venky-bazaar",
          "descriptor": {
            "name": "Venky.Mahadevan@Bazaar"
          }
        },
        "validity": {
          "startDate": "2023-01-01T00:00:00Z",
          "endDate": "2023-12-31T23:59:59Z"
        },
        "resources": [
          {
            "id": "item-flask-mh500-yellow",
            "descriptor": {
              "name": "Isothermal Stainless Steel Hiking Flask MH500 Yellow",
              "shortDesc": "Double walled vacuum insulated stainless steel thermos flask, 500ml",
              "mediaFile": [
                {
                  "label": "Product Image",
                  "mimeType": "image/jpeg",
                  "uri": "https://tourism-bpp-infra2.becknprotocol.io/attachments/view/253.jpg"
                }
              ]
            },
            "resourceAttributes": {
              "@context": "https://schema.beckn.io/GroceryResource/v2.1/context.jsonld", 
              "@type": "GroceryResource",
              "identity": {
                "brand": "InstaCuppa",
                "originCountry": "IN"
              }
            }
          }
        ],
        "offers": [
          {
            "id": "offer-flask-mh500-yellow",
            "descriptor": {
              "name": "Isothermal Stainless Steel Hiking Flask MH500 Yellow"
            },
            "resourceIds": [
              "item-flask-mh500-yellow"
            ],
            "provider": {
              "id": "provider-venky-bazaar",
              "descriptor": {
                "name": "Venky.Mahadevan@Bazaar"
              }
            },
            "validity": {
              "startDate": "2023-01-01T00:00:00Z",
              "endDate": "2023-12-31T23:59:59Z"
            },
            "offerAttributes": {
              "@context": "https://raw.githubusercontent.com/beckn/local-retail/refs/heads/main/schema/RetailOffer/v2.1/context.jsonld",
              "@type": "RetailOffer",
              "policies": {
                "returns": {
                  "allowed": true,
                  "window": "P7D",
                  "method": "SELLER_PICKUP"
                },
                "cancellation": {
                  "allowed": true,
                  "window": "PT2H",
                  "cutoffEvent": "BEFORE_PACKING"
                },
                "replacement": {
                  "allowed": true,
                  "window": "P7D",
                  "method": "SELLER_PICKUP",
                  "subjectToAvailability": true
                }
              },
              "paymentConstraints": {
                "codAvailable": true
              },
              "serviceability": {
                "distanceConstraint": {
                  "maxDistance": 15,
                  "unit": "KM"
                },
                "timing": [
                  {
                    "daysOfWeek": [
                      "MON",
                      "TUE",
                      "WED",
                      "THU",
                      "FRI",
                      "SAT",
                      "SUN"
                    ],
                    "timeRange": {
                      "start": "09:00",
                      "end": "21:00"
                    }
                  }
                ]
              }
            }
          }
        ]
      }
    ]
  }
}
```

### IONAttribute example with NP specified schema for ExtraAttributes
- Uses the GroceryResource schema for ExtraAttributes
- Works fine

```
{
  "context": {
    "version": "2.0.0",
    "action": "catalog/publish",
    "timestamp": "2026-05-04T10:31:37.942Z",
    "messageId": "dd7e3c02-a215-4f25-b14f-b1d7d1d7b9de",
    "transactionId": "776264b0-c8f8-4ea6-ad79-8f1ca7d57ae7",
    "bppId": "sandbox.open-kitchen.com",
    "bppUri": "http://onix-bpp:8082/bpp/receiver",
    "ttl": "PT30S",
    "networkId": "beckn.one/testnet-retail"
  },
"message": {
    "catalogs": [
      {
        "id": "catalog-hbo-001",
        "bppId": "bpp.example.com",
        "bppUri": "http://onix-bpp:8082/bpp/receiver",
        "descriptor": {
          "name": "HBO",
          "shortDesc": "Electronics and home goods catalog"
        },
        "provider": {
          "id": "provider-venky-bazaar",
          "descriptor": {
            "name": "Venky.Mahadevan@Bazaar"
          }
        },
        "validity": {
          "startDate": "2023-01-01T00:00:00Z",
          "endDate": "2023-12-31T23:59:59Z"
        },
        "resources": [
          {
            "id": "item-flask-mh500-yellow",
            "descriptor": {
              "name": "Isothermal Stainless Steel Hiking Flask MH500 Yellow",
              "shortDesc": "Double walled vacuum insulated stainless steel thermos flask, 500ml",
              "mediaFile": [
                {
                  "label": "Product Image",
                  "mimeType": "image/jpeg",
                  "uri": "https://tourism-bpp-infra2.becknprotocol.io/attachments/view/253.jpg"
                }
              ]
            },
            "resourceAttributes": {
              "@context": "https://raw.githubusercontent.com/ion-id/ion-docs/refs/heads/ionAttributes/drafts/ionSchema/ionAttributes/resourceAttributes/TradeResource/v2.1/context.jsonld", 
              "@type": "TradeResource",
              "identity": {
                "brand": "InstaCuppa",
                "originCountry": "IN"
              },
              "extraAttributes":{
                "@context": "https://schema.beckn.io/GroceryResource/v2.1/context.jsonld", 
                "@type": "GroceryResource"
              }
            }
          }
        ],
        "offers": [
          {
            "id": "offer-flask-mh500-yellow",
            "descriptor": {
              "name": "Isothermal Stainless Steel Hiking Flask MH500 Yellow"
            },
            "resourceIds": [
              "item-flask-mh500-yellow"
            ],
            "provider": {
              "id": "provider-venky-bazaar",
              "descriptor": {
                "name": "Venky.Mahadevan@Bazaar"
              }
            },
            "validity": {
              "startDate": "2023-01-01T00:00:00Z",
              "endDate": "2023-12-31T23:59:59Z"
            },
            "offerAttributes": {
              "@context": "https://raw.githubusercontent.com/beckn/local-retail/refs/heads/main/schema/RetailOffer/v2.1/context.jsonld",
              "@type": "RetailOffer",
              "policies": {
                "returns": {
                  "allowed": true,
                  "window": "P7D",
                  "method": "SELLER_PICKUP"
                },
                "cancellation": {
                  "allowed": true,
                  "window": "PT2H",
                  "cutoffEvent": "BEFORE_PACKING"
                },
                "replacement": {
                  "allowed": true,
                  "window": "P7D",
                  "method": "SELLER_PICKUP",
                  "subjectToAvailability": true
                }
              },
              "paymentConstraints": {
                "codAvailable": true
              },
              "serviceability": {
                "distanceConstraint": {
                  "maxDistance": 15,
                  "unit": "KM"
                },
                "timing": [
                  {
                    "daysOfWeek": [
                      "MON",
                      "TUE",
                      "WED",
                      "THU",
                      "FRI",
                      "SAT",
                      "SUN"
                    ],
                    "timeRange": {
                      "start": "09:00",
                      "end": "21:00"
                    }
                  }
                ]
              }
            }
          }
        ]
      }
    ]
  }
}
```

## Introduction
This folder contains files required to test the IONAttributes design of Schema. 

<TODO> Explain about the requirement and design

## Postman collections

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
- It should 

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
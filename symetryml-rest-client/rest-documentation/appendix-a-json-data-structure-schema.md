# Appendix A JSON Data Structure Schema

This appendix describes the JSON schemas used in various SymetryML REST API HTTP request and response body. This is not a pure JSON schema definition because JSON schemas do not have a primitive type for hash map, while the SymetryML API uses hash maps extensively in request/response messages. The JSON type ‘object’ is the closest data structure to a hash map, as it can be seen as an infinite set of key=string and values=objects. When part of a JSON structure should be a hash map, the object type is used in the schema definition. When an entity contains an object - that is a hash map - additional comments are added that define the keys and values allowed. These comments are highlighted in red, so you can find them easily. The formats depicted in the following sections are important because this is how the REST server deserializes the HTTP request body and serializes the HTTP response body.

## Request Body JSON

### DataFrame JSON

```javascript
DataFrame : {
      "attributeTypes": {
          "type": "array" {
                "items": "string"
          }
      },
      "attributeNames": {
            "type": "array" {
                  "items": "string"

            }
      },
      "data" : {
            "type" : "array" {
                  "items" : "array" {
                        "items" : "string"
                  }
            }
      }
}
```

### MLContext JSON

```javascript
MLContext : {
      "targets" : "array" {
            "items" : "string"
      },
      "inputAttributes" : "array" {
            "items" : "string"
      },,
      "inputAttributeNames" : "array" {
            "items" : "string"
      },
      "targetAttributeNames" : "array" {
            "items" : "string"
      }
      "extraParameters" : object // key values hash map

}
```

### DSInfo JSON

```javascript
DSInfo : {
      "type" : "string",
      "name" : "string",
      "info" : "object",
      "extra" : "object"
}
```

### TaskInfo JSON

```javascript
TaskInfo : {
      "type" : "string",
      "name" : "string",
      "params" : "object",
}
```

### DSListingRequest JSON

```javascript
DSListingRequest : {
      "type" : "string",
      "path" : "string",
      "info" : "object"
}
```

### ExploreContext JSON

The ExploreContext data structure is a list of [MLContext](appendix-a-json-data-structure-schema.md#mlcontext-json). It allows to request for a given exploration metrics with multiple attributes or attributes pairs.

```javascript
ExploreContext : {
      "values" : "arrays" {
            "items" : "MLContext"
      }
}
```

## Request Response JSON

This is the main JSON response that all REST SymetryML calls return. Depending on the call, the values, if present, will be different.

```javascript
JSONResponseClient : {
    "statusCode ": {
        "type" : "number",
        "description" : "HTTP/HTTPS response code",
        "required" : true,
        "default" : "200"
    },
    "statusString" : {
        "type" : "String",
        "description" : "Contextual error information if needed",
        "required" : true,
        "default" : "No Error"
    },
    "values" : {
        "type" : "object" {
        "properties" : { // the different type of entities
            "KSKVMap" : {
                "type" : "KSVSMap"
            },
            "modelInfoResponse" : {
                "type" : "ModelInfo"
                },
            "status" : {
                "type" : "string"
            },
            "stringToSign" :{
                "type" : "string"
            },
            "KSVSMap" : {
                "type" : "KSVDMap"
            },
            "smlInfo" : {
                "type" : "ProjectInfo"
            },
            "smlInfoList" : {
                "type" : "ProjectInfoList"
            },
            "stringList" : {
                "type" : "StringList"
            },
            "jobinfo" : {
                "type" : "JobInfo"
            },
            "smlOptions" : {
                "type" : "SymetryProjectOptions"
            },
            "dataframe" : {
                "type" : "DataFrame"
            },
            "dsinfo" : {
                "type" : "DSInfo"
            },
            "dsdirectoryListing" : {
                "type" : "DSListingResponse"
            },
            "pcaValues" : {
                "type" : "DataFrame"
            },
            "pcaVectors" : {
                "type" : "DataFrame"
            },
            "pcaSumValues" : "number"
            },
            "federationInfo" : {
                "type" : "FederationInfo"
            },
            "cellsStatus" : {
                "type" : "cellsStatusEncrypted"
            },
            "fedSyncStats" : {
                "type" : "FedSyncStats"
            }
        }
    }
}
```

### Response Entities

Response entities are JSON data structures that are returned given a specific REST request. This secondary response is added to the main response values field.

#### DSListingResponse JSON

```javascript
DSListingResponse : {
    "ok" : "Boolean",
    "files" : {
        "type" : "array" {
            "items" : "string"
        }
    },
    "dirs" : {
        "type" : "array" {
            "items" : "string"
        }
    },
    "path" : "string",
    "marker" : "string",
    "delimiter" : "string"
}
```

#### JobInfo

```javascript
JobInfo : {
    "jobId" : "number",
    "jobType" : "string",
    "owner" : "string",
    "startingDate" : "number",
    "current" : "number",
    "maximum" : "number",
    "isOnBoundedExecutor" : "Boolean",
    "isStarted" : "Boolean",
    "isDone" : "Boolean",
    "details" : "object" // <string, string> map
}
```

#### KSVDMap

```javascript
KSVDMap : {
    "values" : {
    "type" : "array" {
        "items" : "object" {
            // key : string
            // value : number
        }
    }
}
```

#### FedSyncStats

```javascript
FedSyncStats : {
    "sndCount" : "number",           // # of time this peer tried to sync
    "sndPSRContratError" : "number", // # of time this peer tried to sync with error
    "rcvCount" : "number",   // # of time we received sync from other peers
    "rcvError" : "number",   // # of time we received sync from other peers with error
    "rcvPSRContractError" : "object", // keep count of # error by peer id
    "errorLog" : "array" {
        "items" : "String" // list of sync error
    }
    "syncLog" : "array" {
        "items" : "String"  // list of sucessful sync message
    }
}
```

#### SymetryProjectOptions

```javascript
SymetryProjectOptions : {
    "gpuEnabled" : "number"
}
```

#### KSVSMap

Prediction services use KSVSMap, which consists of a list of map\<string, string>.

```javascript
KSVSMap : {
    "values" : {
        "type" : "array" {
            "items" : "object" {
                // key : string
                // value : string
        }
    }
}
```

#### ModelInfo

```javascript
ModelInfo : {
    "modelId" : "string",
    "modelType" : "string",
    "modelBuilt" : "number",
    "buildTime" : "number",
    "attributeNames" : {
        "type" : "array" {
            "items" : "string"
        }
    },
    "attributeIndexes" : {
        "type" : "array" {
            "items" : "number"
        }
    },
    "attributeTypes" : {
        "type" : "array" {
            "items" : "string"
        }
    },
    "predictInfo" : { // value returned by the predictions.
        "type" : "array" {
            "items" : "string"
        }
    },
    "scoreInfo" : { // value containing score in predictions.
        "type" : "array" {
            "items" : "string"
        }
    },
    "targetNames" : {
        "type" : "array" {
            "items" : "string"
        }
    },
    "targetindexes" : {
        "type" : "array" {
            "items" : "number"
        }
    },
    "info" : { // additional information, model type dependent.
        "type" : "KSVSMap"
    },
    "creationDate" : "number"
    "extra": "Object" // For MLR and Kaplan Meier models contains additional info. The 'object' is effectively a map where keys are strings and value are Dataframe see "DataFrame JSON" section earlier.
}
```

#### FusionCellStatus

Fusion Status information

```javascript
FusionCellStatus : {
    "host" : "string",      // host
    "cid" : "string",       // customer id
    "status" : "string",        // status
    "pid" : "string",       // project id
}
```

#### FusionCellStatusList

List of Fusion Status information

```javascript
FusionCellStatusList : {
    "values" : {
        "type" : "array" {
            "items" : "FusionCellStatus"
        }
    }
}
```

#### FusionCellInfo

Fusion Cell information

```javascript
FusionCellInfo : {
    "host" : "string",      // host
    "cid" : "string",       // customer id
    "sk" : "string",        // secret key
    "pid" : "string",       // project id
}
```

#### FusionCellInfoList

Fusion Cell information

```javascript
FusionCellInfoList : {
    "values" : {
        "type" : "array" {
            "items" : "FusionCellInfo"
        }
    }
}
```

#### ProjectInfo

The SymetryML project info request uses ProjectInfo.

```javascript
ProjectInfo : {
    "pid" : "string",
    "encoderName" : "string",
    "modelsList" : {
    "type" : "array" {
        "items" : "string"
    },
    "modelTypeList" : {
        "type" : "array" {
            "items" : "string"
        }
    },
    "dsList" : {
        "type" : "array" {
            "items" : "string"
        }
    },
    "attributeNames" : {
        "type" : "array" {
            "items" : "string"
        }
    },
    "attributeIndexes" : {
        "type" : "array" {
         "items" : "number"
        }
    },
    "attributeTypes" : { // [c,b,x,s,l,-]
        "type" : "array" {
            "items" : "string"
        }
    },
    "modelAssessments" : {
        "type" : "array" {
            "items" : "string"
        }
    },
    "modelPredictions" : {
        "type" : "array" {
            "items" : "string"
        }
    },
    "partitionColumn" : "string",
    "params" : "object", // key values hash map <string, string>
    "hash" : "number", // internal use
    "categorySeparator" : "string",
    "type" : "string", //
    "creationDate" : "number",
    "lastModificationDate" : "number",
    "loaded" : "true|false",
    "persisted" : "true|false",
    "histogramEnabled" : "true|false",
    "autoSave" : "true|false",
    "fusionCellInfo" : "string" // encrypted json of fusioncellinfolist
}
```

#### StringList

StringList can be returned by various REST calls.

```javascript
StringList : {
    "type" : "array" {
        "items" : "string"
    }
}
```

#### ProjectInfoList

ProjectInfoList is returned after restoring a specific SymetryML project or restoring all SymetryML projects for a specific customer.

```javascript
ProjectInfoList : {
    "values" : {
        "type" : "array" {
            "items" : "ProjectInfo"
        }
    }
}
```

#### FederationInfo

The SymetryML Federation info request uses FederationInfo.

```javascript
FederationInfo : {
    "aws_id" : "string",
    "aws_access_key" : "string",
    "aws_queue_url" : "string",
    "aws_secret_key" : "string",
    "aws_topic_arn" : "string",
    "fed_local_id" : "string",
    "fed_name" : "string",
    "fed_secret_key" : "string",
    "sync_sched" : "string",
    "fed_type" : "string",
    "local_uuid" : "string",
    "s3_bucket" : "string",
    "is_pulsing" : "boolean",
    "peersInfo" : "object", // key values hash map<string, string>
    "psr_contract" : "string", // the psr contract for this federation, if it exists
    "rcv_error_action" : "string", // psr contract receive error action
    "snd_error_action" : "string", // psr contract send error action
    "fed_use_smpc" : "string", // whether this federation use SMPC or not
    "allow_peers_explore" : boolean // whether this peer allows peer exploration or not
}
```

#### AwsInfo

Amazon Web Service - AWS - information. This information will be returned as an encrypted JSON string.

```java
public class AwsInfo {

    /**
     * map<AWS_REGION, List<Map<topic_arn, List<Map<topic_arn_key, topic_arn_value>>>>>
     */
    public Map<String, List<Map<String, List<Map<String, String>>>>> topics;
    /**
     * Map<AWS_REGION, List<sqs_queue_name>>
     */
    public Map<String, List<String>> queues;

    // Example:
    /*
    {
      "topics":{"US_WEST_2":
        [{"arn:aws:sns:us-west-2:123456789012:fed1":
          [{"owner":"123456789012",
          "protocol":"sqs",
          "endpoint":"arn:aws:sqs:us-west-2:123456789012:fed1_proj1",
          "subscription_arn":"arn:aws:sns:us-west-2:123456789012:fed1:abcdefg1-8616-4c57-873f-abcdfef12345"}
          ]}
        ],
        "US_EAST_1":
        [{"arn:aws:sns:us-east-1:123456789012:FEEED":
          [{"owner":"123456789012",
          "protocol":"sqs",
          "endpoint":"arn:aws:sqs:us-east-1:123456789012:FEEED_toto",
          "subscription_arn":"arn:aws:sns:us-east-1:123456789012:FEEED:abcdefg1-8eaf-4c22-8062-abcdfef12345"}]
        },
        {"arn:aws:sns:us-east-1:123456789012:f200":
          [{"owner":"123456789012",
          "protocol":"sqs",
          "endpoint":"arn:aws:sqs:us-east-1:123456789012:f200_p1",
          "subscription_arn":"arn:aws:sns:us-east-1:123456789012:f200:abcdefg1-a665-4e82-961b-abcdfef12345"}]
        }
      },
      "queues":{
        "US_WEST_2":[
          "https://sqs.us-west-2.amazonaws.com/123456789012/fed1_proj1"],
        "US_EAST_1":[
        "https://sqs.us-east-1.amazonaws.com/123456789012/FEEED_toto",
        "https://sqs.us-east-1.amazonaws.com/123456789012/f200_p1",
        ]
      }
    }
    */
}
```

#### Density

DensityList contains a list of Density

```javascript
Density : {
    "attrName" : "string",
    "min" : "number",
    "max" : "number",
    "width" : "number",
    "histogram" : "array" {
            "items" : "number"
        },
    "error" : "string"
}
```

#### Density List

DensityList contains a list of [Density](appendix-a-json-data-structure-schema.md#density) objects.

```javascript
DensityList : {
    "values" : {
        "type" : "array" {
            "items" : "Density"
        }
    }
}
```

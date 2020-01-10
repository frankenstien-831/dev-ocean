# Compute Service Endpoints

## Status
  
  
### GET /api/v1/operator/status
   
   
Get all workflows and corresponding stats

Parameters
```
        owner: String object containg user ETH address (optional)
        agreementID: String object containing agreementID (optional)
        workflowID: String object containing workflowID (optional)
        
        At least one parameter is required (can be any of them)
```

Returns

An Array of objects, each object describing a workflow. If the array is empty, then the search yeld no results

Each object will contain:
```
        owner:The owner of this compute job
        agreementID:
        workflowID:
        createdat:Date/Time of job creation
        finishedat:Date/Time when job finished
        status:  Int, see below for list
        configlogURL: URL to get the configuration log (for admins only)
        publishlogURL: URL to get the publish log (for admins only)
        algologURL: URL to get the algo log (for user)
        outputsURL: Array of URLs for algo outputs
```

Status description:

| status   | Description        |
|----------|--------------------|
|  1       | Job started        |
|  2       | Configuring volumes|
|  3       | Running algorith   |
|  4       | Uploading results  |
|  5       | Job finished       |


Example:
```
GET /api/v1/operator/status?owner=0x1111
```

Output:
```
[
      {
        "owner":"0x1111",
        "agreementID":"0x2222",
        "workflowID":"3333",
        "createdat":"2020-10-01T01:00:00Z",
        "finishedat":"2020-10-01T01:00:00Z",
        "status":5,
        "configlogURL":"http://example.net/logs/config.log",
        "publishlogURL":"http://example.net/logs/publish.log",
        "algologURL":"http://example.net/logs/algo.log",
        "outputsURL":[
            {
            "http://example.net/logs/output/0",
            "http://example.net/logs/output/1"
            }
         ]
       },
       {
        "owner":"0x1111",
        "agreementID":"0x2222",
        "workflowID":"3333",
        "createdat":"2020-10-01T01:00:00Z",
        "finishedat":"2020-10-01T01:00:00Z",
        "status":5,
        "configlogURL":"http://example.net/logs2/config.log",
        "publishlogURL":"http://example.net/logs2/cpublish.log",
        "algologURL":"http://example.net/logs2/algo.log",
        "outputsURL":[
            {
            "http://example.net/logs2/output/0",
            "http://example.net/logs2/output/1"
            }
         ]
       }
 ]
 ```
       
        
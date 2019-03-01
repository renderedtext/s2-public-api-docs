Documentation for Sempahore 2.0 publlic HTTP API


# Resource: Schedule

## Basic information

API version: v1aplha

Base API url:
```
https://{ORGANIZATION_NAME}.semaphoreci.com/api/{API_VERSION}/schedules
```

Authorization: Auth token in request header

Backend service: [periodic-scheduler](https://github.com/renderedtext/periodic-scheduler)

API service: [plumber-public](https://github.com/renderedtext/plumber-public)  

## Actions

### Create Schedule

Creates new Schedule resource based on given yaml definition.

```
POST /schedules
```

Parameters:

| Name           |  Type  | Description                                     |
|----------------|:------:|-------------------------------------------------|
| yml_definition | string | Content of yaml file with schedule's definition |

Example of yml_definition content:
```
apiVersion: v1.0
kind: Schedule
metadata:
  name: New daily schedule
spec:
  project: My project
  branch: master
  at: "* * * * *"
  pipeline_file: .semaphore/cron.yml
```


Response:
```
Status: 200 OK
--------------
<ID of newly created Schedule resource>
```

### Update Schedule

Updates existing Schedule resource with new data from given yaml definition.
Uses same endpoint as for create action, and exact action is inferred on backend service.

```
POST /schedules
```

Parameters:

| Name           |  Type  | Description                                     |
|----------------|:------:|-------------------------------------------------|
| yml_definition | string | Content of yaml file with schedule's definition |

Example of yml_definition content:
```
apiVersion: v1.0
kind: Schedule
metadata:
  name: Updated schedule - every noon
  id: 841fcdbc-ffc1-4250-b555-b990152550bd
spec:
  project: My project
  branch: master
  at: "* * * 12 00"
  pipeline_file: .semaphore/cron_2.yml
```


Response:
```
Status: 200 OK
--------------
<ID of preexisting Schedule resource which was updated>
```

### List  Schedules

Returns paginated list of existing Schedule resources which match give search parameters.

```
GET /schedules
```

Parameters:

| Name       |  Type  | Description                                           |
|------------|:------:|-------------------------------------------------------|
| identifier | string | Either name or id of schedule which should be deleted |


Response:
```
Status: 200 OK
--------------
[
  {
    "updated_at":"2019-02-27 12:40:53.108836Z",
    "requester_id":"a72bbf12-7688-47f3-bfef-a86b95309ba3",
    "project_id":"e1d819a6-8f15-4386-ab82-2bb30ed1ef8a",
    "pipeline_file":".semaphore/semaphore.yml",
    "name":"Third periodic",
    "id":"3f809e1e-5469-46bc-938f-d06982316f76",
    "branch":"master",
    "at":"*/2 * * * *"
  },
  {
    "updated_at":"2019-02-27 12:25:26.572949Z",
    "requester_id":"a72bbf12-7688-47f3-bfef-a86b95309ba3",
    "project_id":"e1d819a6-8f15-4386-ab82-2bb30ed1ef8a",
    "pipeline_file":".semaphore/semaphore.yml",
    "name":"Second periodic",
    "id":"599de734-1554-4904-a7d5-19f17dbe391a",
    "branch":"master",
    "at":"*/12 * * * *"
  }
]
```

### Describe Schedule

Describes existing Schedule resource with given id or name.
Returned description contains schedule's description and also it's latest 10 triggers.

```
GET /schedules/<identifier>
```

Parameters:

| Name       |  Type  | Description                                               |
|------------|:------:|-----------------------------------------------------------|
| identifier | string | Either name or id of schedule which description is wanted |


Response if schedule is found:
```
Status: 200 OK
--------------
{
  "schedule":
    {
      "updated_at":"2019-02-27 12:25:26.572949Z",
      "requester_id":"a72bbf12-7688-47f3-bfef-a86b95309ba3",
      "project_id":"e1d819a6-8f15-4386-ab82-2bb30ed1ef8a",
      "pipeline_file":".semaphore/semaphore.yml",
      "name":"Second periodic",
      "id":"599de734-1554-4904-a7d5-19f17dbe391a",
      "branch":"master",
      "at":"*/12 * * * *"
    },
  "triggers":
  [
    {
      "triggered_at":"2019-02-27 12:42:05.004770Z",
      "scheduling_status":"failed",
      "scheduled_workflow_id":"",
      "scheduled_at":"2019-02-27 12:42:10.047220Z",
      "project_id":"e1d819a6-8f15-4386-ab82-2bb30ed1ef8a",
      "pipeline_file":".semaphore/semaphore.yml",
      "error_description":"",
      "branch":"master"
    }
  ]
}
```

Response if schedule is not found:
```
Status: 400 BAD_REQUEST
--------------
"Periodic with name 'Non-existent name' not found."
OR
"Periodic with id: 'e1d819a6-8f15-4386-ab82-2bb30ed1ef8a' not found."
```

### Delete Schedule

Deletes existing Schedule resource with given id or name.

```
DELETE /schedules/<identifier>
```

Parameters:

| Name       |  Type  | Description                                           |
|------------|:------:|-------------------------------------------------------|
| identifier | string | Either name or id of schedule which should be deleted |


Response if schedule is found and deleted:
```
Status: 200 OK
--------------
"Schedule successfully deleted."
```

Response if schedule is not found:
```
Status: 400 BAD_REQUEST
--------------
"Periodic with name 'Non-existent name' not found."
OR
"Periodic with id: 'e1d819a6-8f15-4386-ab82-2bb30ed1ef8a' not found."
```

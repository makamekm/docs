---
editable: false
sourcePath: en/_api-ref-grpc/serverless/workflows/v1/workflows/api-ref/grpc/Execution/stop.md
---

# Workflows Service, gRPC: ExecutionService.Stop

Stops specified Workflow execution.

## gRPC request

**rpc Stop ([StopExecutionRequest](#yandex.cloud.serverless.workflows.v1.StopExecutionRequest)) returns ([StopExecutionResponse](#yandex.cloud.serverless.workflows.v1.StopExecutionResponse))**

## StopExecutionRequest {#yandex.cloud.serverless.workflows.v1.StopExecutionRequest}

```json
{
  "execution_id": "string"
}
```

#|
||Field | Description ||
|| execution_id | **string**

Required field. ID of the Workflow execution. ||
|#

## StopExecutionResponse {#yandex.cloud.serverless.workflows.v1.StopExecutionResponse}

```json
{
  "execution_id": "string"
}
```

#|
||Field | Description ||
|| execution_id | **string**

ID of the Workflow execution. ||
|#
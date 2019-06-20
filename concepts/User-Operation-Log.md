# Overview of logged actions
This site provides a detailed overview on all user operation logs that are created by the engine. 

It also links the logs to the Java API methods and their implementing Command classes.

The log operations are displayed by category.

## TaskWorker Log

|  **command class** | **operation type** | **entity type** | **property changes** | **API methods** |
| --- | --- | --- | --- | --- |
|   |  |  |  |  |
|  AddGroupIdentityLinkCmd | AddGroupLink | IdentityLink | "candidate", null, groupId | TaskService#addCandidateGroup |
|  AddUserIdentityLinkCmd | AddUserLink | IdentityLink | "candidate", null, userId | TaskService#addCandidateUser |
|  AssignTaskCmd | Assign | Task | "assignee", previousAssignee, assignee | TaskService#setAssignee |
|  ClaimTaskCmd | Claim | Task | "assignee", previousAssignee, assignee | TaskService#claimTask |
|  CompleteTaskCmd | Complete | Task | "delete", null, true | TaskService#completeTask<br/>TaskService#completeWithVariablesInReturn |
|  CreateAttachmentCmd | AddAttachment | Attachment | "name", null, attachmentName | TaskService#createAttachment |
|  DelegateTaskCmd | Delegate | Task | "delegation", null, "PENDING"<br/>"owner", null, assignee (if no previous owner)<br/>"assignee", previousAssignee, assignee | TaskServiceImpl#delegateTask |
|  DeleteAttachmentCmd | DeleteAttachment | Attachment | "name", null, attachmentName | TaskService#deleteAttachment |
|  DeleteFilterCmd | Delete | Filter | "filterId", null, filterId | FilterService#deleteFilter |
|  DeleteGroupIdentityLinkCmd | DeleteGroupLink | IdentityLink | "candidate", null, groupId | TaskService#deleteCandidateGroup |
|  DeleteTaskCmd | Delete | Task | "delete", null, true | TaskService#deleteTask<br/>TaskService#deleteTasks (one entry for every task) |
|  DeleteUserIdentityLinkCmd | DeleteUserLink | IdentityLink | "candidate", null, userId | TaskService#deleteCandidateUser |
|  PatchTaskVariablesCmd | ModifyVariable | Variable | - | TaskServiceImpl#updateVariables<br/>TaskServiceImpl#updateVariablesLocal |
|  RemoveTaskVariablesCmd | RemoveVariable | Variable |  | TaskService#removeVariable<br/>TaskService#removeVariableLocal<br/>TaskService#removeVariables<br/>TaskService#removeVariablesLocal |
|  ResolveTaskCmd | Resolve | Task | "delegation", null, "RESOLVED" | TaskService#resolveTask |
|  SaveFilterCmd | Create | Filter | "filterId", null, filter.getId() | FilterService#saveFilter |
|  SaveTaskCmd | Create | Task | - | TaskService#saveTask |
|  SaveTaskCmd | Update | Task | all updated properties | TaskService#saveTask |
|  SetTaskOwnerCmd | SetOwner | Task | "owner", previousOwner, owner | TaskService#setOwner |
|  SetTaskPriorityCmd | SetPriority | Task | "priority", previousPriority, priority | TaskService#setPriority |
|  SetTaskVariablesCmd | SetVariable | Variable | - | TaskService#setVariable<br/>TaskService#setVariableLocal<br/>TaskService#setVariables<br/>TaskService#setVariablesLocal |

## Operator Log

|  **command class** | **operation type** | **entity type** | **property changes** | **API methods** |
| --- | --- | --- | --- | --- |
|   |  |  |  |  |
|  ActivateBatchCmd | ActivateBatch | Batch | "suspensionState", null, "active" | ManagementService#activateBatchById |
|  ActivateJobCmd | ActivateJob | Job | "suspensionState", null, "active" | ManagementService#activateJobById<br/>ManagementService#activateJobByJobDefinitionId<br/>ManagementService#activateJobByProcessInstanceId<br/>ManagementService#activateJobByProcessDefinitionId<br/>ManagementService#activateJobByProcessDefinitionKey |
|  ActivateJobDefinitionCmd | ActivateJobDefinition | JobDefinition | "suspensionState", null, "active" | ManagementService#activateJobDefinitionById<br/>ManagementService#activateJobDefinitionByProcessDefinitionId<br/>ManagementService#activateJobDefinitionByProcessDefinitionKey |
|  ActivateProcessDefinitionCmd | ActivateProcessDefinition | ProcessDefinition | "suspensionState", null, "active" | RepositoryService#activateProcessDefinitionById<br/>RepositoryService#activateProcessDefinitionByKey |
|  ActivateProcessInstanceCmd | Activate | ProcessInstance | "suspensionState", null, "active" | RuntimeService#activateProcessInstanceById<br/>RuntimeService#activateProcessInstanceByProcessDefinitionId<br/>RuntimeService#activateProcessInstanceByProcessDefinitionKey |
|  CreateIncidentCmd | CreateIncident | ProcessInstance | "incidentType", null, incidentType<br/>"configuration", null, configuration | RuntimeService#createIncident |
|  DeleteBatchCmd | Delete | Batch | "cascadeToHistory", null, cascadeToHistory | ManagementService#deleteBatch |
|  DeleteDeploymentCmd | Delete | Deployment | "cascade", null, cascade | RepositoryService#deleteDeployment |
|  DeleteHistoricBatchCmd | DeleteHistory | Batch | - | HistoryService#deleteHistoricBatch |
|  DeleteHistoricCaseInstanceCmd | DeleteHistory | CaseInstance |  | HistoryService#deleteHistoricCaseInstance |
|  DeleteHistoricCaseInstancesBulkCmd | DeleteHistory | CaseInstance | "nrOfInstances", null, caseInstanceIds.size() | HistoryService#deleteHistoricCaseInstancesBulk |
|  DeleteHistoricDecisionInstanceByDefinitionIdCmd | DeleteHistory | DecisionInstance | "nrOfInstances", null, numInstances<br/>"async", null, false | HistoryService#deleteHistoricDecisionInstance<br/>HistoryService#deleteHistoricDecisionInstanceByDefinitionId |
|  DeleteHistoricDecisionInstanceByInstanceIdCmd | DeleteHistory | DecisionInstance | "decisionInstanceId", null, historicDecisionInstanceId | HistoryService#deleteHistoricDecisionInstanceByInstanceId |
|  DeleteHistoricDecisionInstancesBatchCmd | DeleteHistory | DecisionInstance | "nrOfInstances", null, numInstances<br/>"async", null, true<br/>"deleteReason", null, deleteReason | HistoryService#deleteHistoricDecisionInstancesAsync |
|  DeleteHistoricDecisionInstancesBulkCmd | DeleteHistory | DecisionInstance | "nrOfInstances", null, numInstances<br/>"async", null, false | HistoryService#deleteHistoricDecisionInstancesBulk |
|  DeleteHistoricProcessInstancesBatchCmd | DeleteHistory | ProcessInstance | "nrOfInstances", null, numInstances<br/>"async", null, true<br/>"deleteReason", null, deleteReason | HistoryService#deleteHistoricProcessInstancesAsync<br/>HistoryService#deleteHistoricProcessInstancesAsyncIfExists |
|  DeleteHistoricProcessInstancesCmd | DeleteHistory | ProcessInstance | "nrOfInstances", null, numInstances<br/>"async", null, false | HistoryService#deleteHistoricProcessInstance<br/>HistoryService#deleteHistoricProcessInstances<br/>HistoryService#deleteHistoricProcessInstancesBulk<br/>HistoryService#deleteHistoricProcessInstanceIfExists<br/>HistoryService#deleteHistoricProcessInstancesIfExists |
|  DeleteHistoricTaskInstanceCmd | DeleteHistory | Task | "nrOfInstances", null, 1<br/>"async", null, false | HistoryService#deleteHistoricTaskInstance |
|  DeleteHistoricVariableInstanceCmd | DeleteHistory | Variable | "name", null, variableName | HistoryService#deleteHistoricVariableInstance |
|  DeleteHistoricVariableInstancesByProcessInstanceIdCmd | DeleteHistory | Variable | - | HistoryService#deleteHistoricVariableInstancesByProcessInstanceId |
|  DeleteJobCmd | Delete | Job |  | ManagementService#deleteJob |
|  DeleteMetricsCmd | Delete | Metrics | "timestamp", null, timestamp<br/>"reporter", null, reporter | ManagementService#deleteMetrics |
|  DeleteProcessDefinitionsByIdsCmd | Delete | ProcessDefinition | "cascade", null, cascade | RepositoryService#deleteProcessDefinition |
|  DeleteProcessDefinitionsByKeyCmd | Delete | ProcessDefinition | "cascade", null, cascade | RepositoryService#deleteProcessDefinitions (with key) |
|  DeleteProcessInstanceBatchCmd | Delete | ProcessInstance | "nrOfInstances", null, numInstances<br/><br/>"async", null, true<br/><br/>"deleteReason", null, deleteReason | RuntimeService#deleteProcessInstancesAsync |
|  DeleteProcessInstanceCmd | Delete | ProcessInstance | - | RuntimeService#deleteProcessInstance |
|  DeleteProcessInstancesCmd | Delete | ProcessInstance | - | RuntimeService#deleteProcessInstances (one entry for every instance) |
|  DeployCmd | Create | Deployment | "duplicateFilterEnabled", null, isDuplicateFilterEnabled<br/>"deployChangedOnly", null, isDeployChangedOnly | DeploymentBuilder#deploy |
|  EvaluateDecisionCmd | Evaluate | DecisionDefinition | "decisionDefinitionId", null, decisionDefinition.getId()<br/>"decisionDefinitionKey", null, decisionDefinition.getKey() | DecisionService#evaluateDecisionById<br/>DecisionService#evaluateDecisionByKey |
|  EvaluateDecisionTableCmd | Evaluate | DecisionDefinition | "decisionDefinitionId", null, decisionDefinition.getId()<br/>"decisionDefinitionKey", null, decisionDefinition.getKey() | DecisionService#evaluateDecisionTableById<br/>DecisionService#evaluateDecisionTableByKey<br/>DecisionService#evaluateDecisionTableByKeyAndVersion |
|  ExecuteJobsCmd | Execute | Job |  | ManagementService#executeJob |
|  HistoryCleanupCmd | CreateHistoryCleanupJobs | Job | "immediatelyDue", null, isImmediatelyDue | HistoryService#cleanUpHistoryAsync |
|  MigrateProcessInstanceBatchCmd | Migrate | ProcessInstance | "processDefinitionId", sourceDefinitionId, targetDefinitionId<br/>"nrOfInstances", null, numInstances<br/>"async", null, true | RuntimeService#newMigration (via executeAsync) |
|  MigrateProcessInstanceCmd | Migrate | ProcessInstance | "processDefinitionId", sourceDefinitionId, targetDefinitionId<br/>"nrOfInstances", null, numInstances<br/>"async", null, false | RuntimeService#newMigration (via execute) |
|  ModifyProcessInstanceAsyncCmd | ModifyProcessInstance | ProcessInstance | - | RuntimeService#createModification |
|  ModifyProcessInstanceCmd | ModifyProcessInstance | ProcessInstance | - | RuntimeService#createModification |
|  PatchExecutionVariablesCmd | ModifyVariable | Variable | - | RuntimeService#updateVariables<br/>RuntimeService#updateVariablesLocal |
|  RecalculateJobDuedateCmd | RecalculateDueDate | Job | "creationDateBased", null, isCreationDateBased<br/>"duedate", oldDuedate, newDuedate | ManagementService#recalculateJobDuedate |
|  RemoveExecutionVariablesCmd | RemoveVariable | Variable | - | RuntimeService#removeVariable<br/>RuntimeService#removeVariableLocal<br/>RuntimeService#removeVariables<br/>RuntimeService#removeVariablesLocal |
|  ResolveIncidentCmd | Resolve | ProcessInstance | "incidentId", null, incidentId | RuntimeService#resolveIncident |
|  RestartProcessInstancesBatchCmd | RestartProcessInstance | ProcessInstance | "nrOfInstances", null, numInstances<br/>"async", null, true | RuntimeService#restartProcessInstances |
|  RestartProcessInstancesCmd | RestartProcessInstance | ProcessInstance | "nrOfInstances", null, numInstances<br/>"async", null, false | RuntimeService#restartProcessInstances |
|  SetExecutionVariablesCmd | SetVariable | Variable | - | RuntimeService#updateVariables<br/>RuntimeService#updateVariablesLocal |
|  SetExternalTaskPriorityCmd | SetPriority | ExternalTask | "priority", task.getPriority(), priority | ExternalTaskService#setPriority |
|  SetExternalTaskRetriesCmd | SetExternalTaskRetries | ExternalTask | "retries", oldRetries, newRetries | ExternalTaskService#setRetries(String) |
|  SetExternalTasksRetriesBatchCmd | SetExternalTaskRetries | ExternalTask | "nrOfInstances", null, numInstances<br/>"async", null, true<br/>"retries", null, retries | ExternalTaskService#setRetriesAsync |
|  SetExternalTasksRetriesCmd | SetExternalTaskRetries | ExternalTask | "nrOfInstances", null, numInstances<br/>"async", null, false<br/>"retries", null, retries | ExternalTaskService#setRetries(List<String>) |
|  SetJobDefinitionPriorityCmd | SetPriority | JobDefinition | "overridingPriority", previousPriority, null | ManagementService#clearOverridingJobPriorityForJobDefinition |
|  SetJobDefinitionPriorityCmd<br/>  (if cascade == true) | SetPriority<br/>SetPriority | JobDefinition<br/>Job | "overridingPriority", previousPriority, newPriority<br/>"priority", null, newPriority | ManagementService#setOverridingJobPriorityForJobDefinition |
|  SetJobDuedateCmd | SetDuedate | Job | "duedate", oldDuedate, newDuedate | ManagementService#setJobDuedate |
|  SetJobPriorityCmd | SetPriority | Job | "priority", previousPriority, newPriority | ManagementService#setJobPriority |
|  SetJobRetriesCmd | SetJobRetries | Job | "retries", oldRetries, retries | ManagementService#setJobRetries(String) |
|  SetJobRetriesCmd | SetJobRetries | Job | "retries", null, retries | ManagementService#setJobRetriesByJobDefinitionId |
|  SetJobsRetriesBatchCmd | SetJobRetries | Job | "nrOfInstances", null, numInstances<br/>"async", null, true<br/>"retries", null, retries | ManagementService#setJobRetriesAsync |
|  SetJobsRetriesCmd | SetJobRetries | Job | "retries", oldRetries, retries | ManagementService#setJobRetries(List<String>) (one entry for every job) |
|  SetRemovalTimeToHistoricBatchesCmd | SetRemovalTime | Batch | "async", true<br/>"nrOfInstances", numberOfInstances<br/>"removalTime", null, removalTime<br/>"mode", null, mode | HistoryService#setRemovalTimeToHistoricBatches |
|  SetRemovalTimeToHistoricDecisionInstancesCmd | SetRemovalTime | DecisionInstance | "async", true<br/>"nrOfInstances", numberOfInstances<br/>"removalTime", null, removalTime<br/>"mode", null, mode <br/>"hierarchical", null, isHierarchical | HistoryService#setRemovalTimeToHistoricDecisionInstances |
|  SetRemovalTimeToHistoricProcessInstancesCmd | SetRemovalTime | ProcessInstance | "async", true<br/>"nrOfInstances", numberOfInstances<br/>"removalTime", null, removalTime<br/>"mode", null, mode <br/>"hierarchical", null, isHierarchical | HistoryService#setRemovalTimeToHistoricProcessInstances |
|  StartProcessInstanceAtActivitiesCmd | Create | ProcessInstance | - | RuntimeService#startProcessInstanceById (if modifications present)<br/>RuntimeService#startProcessInstanceByKey (if modifications present)<br/>RuntimeService#restartProcessInstances |
|  StartProcessInstanceCmd | Create | ProcessInstance | - | RuntimeService#startProcessInstanceById<br/>RuntimeService#startProcessInstanceByKey |
|  SuspendBatchCmd | SuspendBatch | Batch | "suspensionState", null, "suspended" | ManagementService#suspendBatchById |
|  SuspendJobCmd | SuspendJob | Job | "suspensionState", null, "suspended" | ManagementService#suspendJobById<br/>ManagementService#suspendJobByJobDefinitionId<br/>ManagementService#suspendJobByProcessInstanceId<br/>ManagementService#suspendJobByProcessDefinitionId<br/>ManagementService#suspendJobByProcessDefinitionKey |
|  SuspendJobDefinitionCmd | SuspendJobDefinition | JobDefinition | "suspensionState", null, "suspended" | ManagementService#suspendJobDefinitionById<br/>ManagementService#suspendJobDefinitionByProcessDefinitionId<br/>ManagementService#suspendJobDefinitionByProcessDefinitionKey |
|  SuspendProcessDefinitionCmd | SuspendProcessDefinition | ProcessDefinition | "suspensionState", null, "suspended" | RepositoryService#suspendProcessDefinitionById<br/>RepositoryService#suspendProcessDefinitionByKey |
|  SuspendProcessInstanceCmd | Suspend | ProcessInstance | "suspensionState", null, "suspended" | RuntimeService#suspendProcessInstanceById<br/>RuntimeService#suspendProcessInstanceByProcessDefinitionId<br/>RuntimeService#suspendProcessInstanceByProcessDefinitionKey |
|  UnlockExternalTaskCmd | Unlock | ExternalTask |  | ExternalTaskService#unlock |
|  UpdateCaseDefinitionHistoryTimeToLiveCmd | UpdateHistoryTimeToLive | CaseDefinition | "historyTimeToLive", caseDefinition.getHistoryTimeToLive(), newHistoryTimeToLive<br/>"caseDefinitionKey", null, caseDefinitionKey | RepositoryService#updateCaseDefinitionHistoryTimeToLive |
|  UpdateDecisionDefinitionHistoryTimeToLiveCmd | UpdateHistoryTimeToLive | DecisionDefinition | "historyTimeToLive", decisionDefinition.getHistoryTimeToLive(), newHistoryTimeToLive<br/>"decisionDefinitionId", null, decisionDefinitionId<br/>"decisionDefinitionKey", null, decisionDefinitionKey | RepositoryService#updateDecisionDefinitionHistoryTimeToLive |
|  UpdateProcessDefinitionHistoryTimeToLiveCmd | UpdateHistoryTimeToLive | ProcessDefinition | "historyTimeToLive", processDefinition.getHistoryTimeToLive(), newHistoryTimeToLive | RepositoryService#updateProcessDefinitionHistoryTimeToLive |

## Admin Log

|  **command class** | **operation type** | **entity type** | **property changes** | **API methods** |
| --- | --- | --- | --- | --- |
|   |  |  |  |  |
|  CreateMembershipCmd | Create | Group membership | id (user)<br/>id (group) | IdentityService#createMembership |
|  CreateTenantGroupMembershipCmd | Create | TenantMembership | id (tenant)<br/>id (group) | IdentityService#createTenantGroupMembership |
|  CreateTenantUserMembershipCmd | Create | TenantMembership | id (user)<br/>id (tenant) | IdentityService#createTenantUserMembership |
|  DeleteAuthorizationCmd | Delete | Authorization | "pemissions", null, stringListOfPermissions(authorization)<br/>"permissionBits", null, authorization.getPermissions()<br/>"type", null, authorization.getAuthorizationType()<br/>"resource", null, resourceName(authorization.getResourceType())<br/>"resourceId", null, authorization.getResourceId()<br/>"groupId", null, authorization.getGroupId()<br/>"userId", null, authorization.getUserId() | AuthorizationService#deleteAuthorization |
|  DeleteGroupCmd | Delete | Group | id (group) | IdentityService#deleteGroup |
|  DeleteMembershipCmd | Delete | Group membership | id (user)<br/>id (group) | IdentityService#deleteMembership |
|  DeletePropertyCmd | Delete | Property | "name", null, name | ManagementService#deleteProperty |
|  DeleteTenantCmd | Delete | Tenant | id (tenant) | IdentityService#deleteTenant |
|  DeleteTenantGroupMembershipCmd | Delete | TenantMembership | id (tenant)<br/>id (group) | IdentityService#deleteTenantGroupMembership |
|  DeleteTenantUserMembershipCmd | Delete | TenantMembership | id (user)<br/>id (tenant) | IdentityService#deleteTenantUserMembership |
|  DeleteUserCmd | Delete | User | id (user) | IdentityService#deleteUser |
|  SaveAuthorizationCmd | Create / Update | Authorization | "pemissions", null, stringListOfPermissions(authorization)<br/>"permissionBits", null, authorization.getPermissions()<br/>"type", null, authorization.getAuthorizationType()<br/>"resource", null, resourceName(authorization.getResourceType())<br/>"resourceId", null, authorization.getResourceId()<br/>"groupId", null, authorization.getGroupId()<br/>"userId", null, authorization.getUserId() | AuthorizationService#saveAuthorization |
|  SaveGroupCmd | Create / Update | Group | id (group) | IdentityService#saveGroup |
|  SaveTenantCmd | Create / Update | Tenant | id (tenant) | IdentityService#saveTenant |
|  SaveUserCmd | Create / Update | User | id (user) | IdentityService#saveUser |
|  SetPropertyCmd | Create / Update | Property | "name", null, name | ManagementService#setProperty |
|  UnlockUserCmd | Unlock | User | id (user) | IdentityService#unlockUser |
Scenario:

* Jobs are executed slowly
* A job is only executed a relatively long time after it becomes due

## How does the Job Executor work?

For a general introduction, see https://docs.camunda.org/manual/7.10/user-guide/process-engine/the-job-executor/.

Lifecycle of a job:

1. Job is created and inserted to `ACT_RU_JOB`
1. Job acquisition polls and locks job in `ACT_RU_JOB`
1. Job acquisition submits job to execution thread pool's queue
1. Execution thread picks up job and executes it
1. Job Execution finishes

Delays can occur between any of these events.

## How to understand where time is spent?

If the situation is reproducible, this question can be answered with logging. In particular, set the following loggers to `DEBUG/FINE`:

* `org.camunda.bpm.engine.impl.persistence.entity.JobEntity`
* `org.camunda.bpm.engine.jobexecutor`
* `org.camunda.bpm.engine.cmd`

Pick a single job for which you want to diagnose the timing of the lifecycle. `org.camunda.bpm.engine.impl.persistence.entity.JobEntity` logs all SQL statements for the `ACT_RU_JOB` table, so it should should provide the time for lifecycle steps 1, 3 and 5. `org.camunda.bpm.engine.jobexecutor` logs when the job executor makes attempts to acquire jobs, when and for how long the job acquisition pauses, and when jobs are submitted to the thread pool's queue. It helps finding the steps 2, 3 and 4. `org.camunda.bpm.engine.cmd` logs the start and end of every command. It adds context to all the steps and in addition can help with understanding transaction boundaries.

Example log snippet:

```
13-Feb-2019 18:10:28.927 FEIN [http-nio-8080-exec-9] org.camunda.commons.logging.BaseLogger.logDebug ENGINE-13005 Starting command -------------------- SubmitStartFormCmd ----------------------
13-Feb-2019 18:10:28.929 FEIN [http-nio-8080-exec-9] org.camunda.commons.logging.BaseLogger.logDebug ENGINE-13009 opening new command context
13-Feb-2019 18:10:28.944 FEIN [http-nio-8080-exec-9] org.camunda.commons.logging.BaseLogger.logDebug ENGINE-13011 closing existing command context
13-Feb-2019 18:10:28.946 FEIN [http-nio-8080-exec-9] org.apache.ibatis.logging.jdbc.BaseJdbcLogger.debug ==>  Preparing: insert into ACT_RU_JOB ( ID_, TYPE_, LOCK_OWNER_, LOCK_EXP_TIME_, EXCLUSIVE_, EXECUTION_ID_, PROCESS_INSTANCE_ID_, PROCESS_DEF_ID_, PROCESS_DEF_KEY_, RETRIES_, EXCEPTION_STACK_ID_, EXCEPTION_MSG_, DUEDATE_, HANDLER_TYPE_, HANDLER_CFG_, DEPLOYMENT_ID_, SUSPENSION_STATE_, JOB_DEF_ID_, PRIORITY_, SEQUENCE_COUNTER_, TENANT_ID_, CREATE_TIME_, REV_ ) values (?, 'message', ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, 1 )
13-Feb-2019 18:10:28.948 FEIN [http-nio-8080-exec-9] org.apache.ibatis.logging.jdbc.BaseJdbcLogger.debug ==> Parameters: 43303e02-2fb2-11e9-9427-28d24461aeb0(String), null, null, true(Boolean), 432f7ab0-2fb2-11e9-9427-28d24461aeb0(String), 432f7ab0-2fb2-11e9-9427-28d24461aeb0(String), Process_1:2:3f5d640e-2fb2-11e9-9427-28d24461aeb0(String), Process_1(String), 3(Integer), null, null, null, async-continuation(String), transition-create-scope(String), 3f5b8f4c-2fb2-11e9-9427-28d24461aeb0(String), 1(Integer), 3f5d8b1f-2fb2-11e9-9427-28d24461aeb0(String), 0(Long), 1(Long), null, 2019-02-13 18:10:28.943(Timestamp)
13-Feb-2019 18:10:28.955 FEIN [http-nio-8080-exec-9] org.camunda.commons.logging.BaseLogger.logDebug ENGINE-14017 Notifying Job Executor of new job notifying job executor of new job
13-Feb-2019 18:10:28.957 FEIN [Thread-6] org.camunda.commons.logging.BaseLogger.logDebug ENGINE-14012 Job acquisition thread woke up
13-Feb-2019 18:10:28.957 FEIN [http-nio-8080-exec-9] org.camunda.commons.logging.BaseLogger.logDebug ENGINE-13006 Finishing command -------------------- SubmitStartFormCmd ----------------------
13-Feb-2019 18:10:28.958 FEIN [Thread-6] org.camunda.commons.logging.BaseLogger.logDebug ENGINE-13005 Starting command -------------------- AcquireJobsCmd ----------------------
13-Feb-2019 18:10:28.959 FEIN [Thread-6] org.camunda.commons.logging.BaseLogger.logDebug ENGINE-13009 opening new command context
13-Feb-2019 18:10:28.961 FEIN [Thread-6] org.apache.ibatis.logging.jdbc.BaseJdbcLogger.debug ==>  Preparing: select RES.* from ACT_RU_JOB RES where (RES.RETRIES_ > 0) and ( RES.DUEDATE_ is null or RES.DUEDATE_ <= ? ) and (RES.LOCK_OWNER_ is null or RES.LOCK_EXP_TIME_ < ?) and RES.SUSPENSION_STATE_ = 1 and (RES.DEPLOYMENT_ID_ is null or ( RES.DEPLOYMENT_ID_ IN ( ? , ? , ? , ? ) ) ) and ( ( RES.EXCLUSIVE_ = 1 and not exists( select J2.* from ACT_RU_JOB J2 where J2.PROCESS_INSTANCE_ID_ = RES.PROCESS_INSTANCE_ID_ -- from the same proc. inst. and (J2.EXCLUSIVE_ = 1) -- also exclusive and (J2.LOCK_OWNER_ is not null and J2.LOCK_EXP_TIME_ >= ?) -- in progress ) ) or RES.EXCLUSIVE_ = 0 ) LIMIT ? OFFSET ?
13-Feb-2019 18:10:28.974 FEIN [Thread-6] org.apache.ibatis.logging.jdbc.BaseJdbcLogger.debug ==> Parameters: 2019-02-13 18:10:28.961(Timestamp), 2019-02-13 18:10:28.961(Timestamp), ce221dde-2fb1-11e9-9427-28d24461aeb0(String), ce654168-2fb1-11e9-9427-28d24461aeb0(String), 283a8a05-2fb2-11e9-9427-28d24461aeb0(String), 3f5b8f4c-2fb2-11e9-9427-28d24461aeb0(String), 2019-02-13 18:10:28.961(Timestamp), 3(Integer), 0(Integer)
13-Feb-2019 18:10:28.979 FEIN [Thread-6] org.apache.ibatis.logging.jdbc.BaseJdbcLogger.debug <==      Total: 1
13-Feb-2019 18:10:28.979 FEIN [Thread-6] org.camunda.commons.logging.BaseLogger.logDebug ENGINE-13011 closing existing command context
13-Feb-2019 18:10:28.980 FEIN [Thread-6] org.apache.ibatis.logging.jdbc.BaseJdbcLogger.debug ==>  Preparing: update ACT_RU_JOB SET REV_ = ?, EXECUTION_ID_ = ?, LOCK_EXP_TIME_ = ?, LOCK_OWNER_ = ?, RETRIES_ = ?, EXCEPTION_STACK_ID_ = ?, EXCEPTION_MSG_ = ?, DUEDATE_ = ?, SUSPENSION_STATE_ = ?, PROCESS_DEF_ID_ = ?, PROCESS_DEF_KEY_ = ?, JOB_DEF_ID_ = ?, DEPLOYMENT_ID_ = ?, HANDLER_CFG_ = ?, PRIORITY_ = ?, SEQUENCE_COUNTER_ = ? where ID_= ? and REV_ = ?
13-Feb-2019 18:10:28.982 FEIN [Thread-6] org.apache.ibatis.logging.jdbc.BaseJdbcLogger.debug ==> Parameters: 2(Integer), 432f7ab0-2fb2-11e9-9427-28d24461aeb0(String), 2019-02-13 18:15:28.979(Timestamp), 30780143-3c62-44f3-a47f-21ba1e049a25(String), 3(Integer), null, null, null, 1(Integer), Process_1:2:3f5d640e-2fb2-11e9-9427-28d24461aeb0(String), Process_1(String), 3f5d8b1f-2fb2-11e9-9427-28d24461aeb0(String), 3f5b8f4c-2fb2-11e9-9427-28d24461aeb0(String), transition-create-scope(String), 0(Long), 1(Long), 43303e02-2fb2-11e9-9427-28d24461aeb0(String), 1(Integer)
13-Feb-2019 18:10:28.983 FEIN [Thread-6] org.camunda.commons.logging.BaseLogger.logDebug ENGINE-13006 Finishing command -------------------- AcquireJobsCmd ----------------------
13-Feb-2019 18:10:28.985 FEIN [Thread-6] org.camunda.commons.logging.BaseLogger.logDebug ENGINE-14022 Acquired 1 jobs for process engine 'default': [[43303e02-2fb2-11e9-9427-28d24461aeb0]]
13-Feb-2019 18:10:28.986 FEIN [Thread-6] org.camunda.commons.logging.BaseLogger.logDebug ENGINE-14023 Execute jobs for process engine 'default': [43303e02-2fb2-11e9-9427-28d24461aeb0]
13-Feb-2019 18:10:28.986 FEIN [pool-2-thread-2] org.camunda.commons.logging.BaseLogger.logDebug ENGINE-13005 Starting command -------------------- ExecuteJobsCmd ----------------------
13-Feb-2019 18:10:28.987 FEIN [pool-2-thread-2] org.camunda.commons.logging.BaseLogger.logDebug ENGINE-13009 opening new command context
13-Feb-2019 18:10:28.988 FEIN [pool-2-thread-2] org.apache.ibatis.logging.jdbc.BaseJdbcLogger.debug ==>  Preparing: select * from ACT_RU_JOB where ID_ = ?
13-Feb-2019 18:10:28.992 FEIN [pool-2-thread-2] org.apache.ibatis.logging.jdbc.BaseJdbcLogger.debug ==> Parameters: 43303e02-2fb2-11e9-9427-28d24461aeb0(String)
13-Feb-2019 18:10:28.993 FEIN [pool-2-thread-2] org.apache.ibatis.logging.jdbc.BaseJdbcLogger.debug <==      Total: 1
13-Feb-2019 18:10:28.998 FEIN [pool-2-thread-2] org.apache.ibatis.logging.jdbc.BaseJdbcLogger.debug ==>  Preparing: select * from ACT_RU_JOB J where J.EXECUTION_ID_ = ?
13-Feb-2019 18:10:28.999 FEIN [pool-2-thread-2] org.apache.ibatis.logging.jdbc.BaseJdbcLogger.debug ==> Parameters: 432f7ab0-2fb2-11e9-9427-28d24461aeb0(String)
13-Feb-2019 18:10:29.001 FEIN [pool-2-thread-2] org.apache.ibatis.logging.jdbc.BaseJdbcLogger.debug <==      Total: 1
13-Feb-2019 18:10:29.004 FEIN [pool-2-thread-2] org.camunda.commons.logging.BaseLogger.logDebug ENGINE-13011 closing existing command context
13-Feb-2019 18:10:29.005 FEIN [pool-2-thread-2] org.apache.ibatis.logging.jdbc.BaseJdbcLogger.debug ==>  Preparing: delete from ACT_RU_JOB where ID_ = ? and REV_ = ?
13-Feb-2019 18:10:29.006 FEIN [pool-2-thread-2] org.apache.ibatis.logging.jdbc.BaseJdbcLogger.debug ==> Parameters: 43303e02-2fb2-11e9-9427-28d24461aeb0(String), 2(Integer)
13-Feb-2019 18:10:29.010 FEIN [pool-2-thread-2] org.camunda.commons.logging.BaseLogger.logDebug ENGINE-13006 Finishing command -------------------- ExecuteJobsCmd ----------------------
```

A real-world log file contains much more entries. Strip down the log file to the entries relevant to a single job. You can identify entries that belong to the same command execution via the thread id.

In case you examine a cluster, collect logs from all cluster nodes. Apply the same diagnosing process to all logs.

## I have understood where the time is spent. How to continue?

### The job spends a lot of time in the thread pool's queue.

* The thread pool is busy doing other things (e.g. executing other jobs). Analyze why this is the case (can be caused by Camunda code (e.g. query performance) or user code (e.g. long-running computations)).
* Check the queue size. A smaller queue can be better than a bigger queue. The jobs are already locked when they enter the queue. If the queue is saturated, job acquisition will back off instead of locking jobs that then simply time out while sitting in the queue.

### The job acquisition polls the job only with delay, because the acquisition is idle

* Explain that the problem does not occur under load. Recommend to tweak job acquisition wait timeout to reduce delay.

### Job acquisition and execution are busy processing other jobs

* This may be acceptable if it is only a temporary burst
* If this is a permanent situation, then the processing resources should be increased
* Prioritized job execution can help to improve fairness

### Time is spent in the Camunda SQL queries

* Diagnose SQL performance.

### Time is spent in job execution

* Diagnose if this is cause by Camunda code (e.g. poorly-performing queries) or user code (e.g. complex computations). Continue accordingly.

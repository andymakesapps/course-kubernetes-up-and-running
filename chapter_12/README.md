# Jobs

- Jobs are meant to run short-lived and one-off tasks
- Jobs create Pods that run until successful (exit code 0), whereas Pods continously restart regardless of exit code

## Job Object

- The Job object is responsible for creating and managing Pods defined in the template specification

### Job Patterns

- Jobs are designed to manage batch like workloads where items are processed by one or more Pods
- there are 2 main parameters, number of successes and number of pods to run in parallel => completions and parallelism 

| Type | Desc | completions | parallelism |
| ---- | ---- | ----------- | ----------  |
| One Shot | A single pod running once until successful | 1 | 1 |
| Parallel fixed completions | One or more pods running until reaching a fixed completion | 1+ | 1+ |
| Work queue | One or more pods running until successful termination | 1 | 2+ |

#### One Shot

- There are multiple ways of running a one-shot job, easiest is done via kubectl:

> kubectl run -i oneshot --image=gcr.io/kuar-demo/kuard-amd64:blue --restart=OnFailure --command /kuard -- --keygen-enable --keygen-exit-on-complete --keygen-num-to-gen 10

    - *-i* tells kubectl that this is an interactive command, hence it will wait until it is done
    - *restart=OnFailure* tells Kubernetes to create a job object
    - *--* sends commands to the image once the Pod is running

- To view the job details we can use kubectl, we need the *-a* flag if the job has completed:

> kubectl get jobs

- Another option is to create the job via yaml

```
apiVersion: batch/v1
kind: Job
metadata:
  name: oneshot
spec:
  template:
    spec:
      containers:
      - name: kuard
        image: gcr.io/kuar-demo/kuard-amd64:blue
        imagePullPolicy: Always
        command:
        - "/kuard"
        args:
        - "--keygen-enable"
        - "--keygen-exit-on-complete"
        - "--keygen-num-to-gen=10"
      restartPolicy: OnFailure
```

- Note we are not defining labels as these are one-off tasks, Kubernetes will apply randomlly generated labels on its own to know which pods to use

#### Parallelism

- If we want to use multiple pods for a task we can employ parallelism, all that's needed is for us to set the number of parallelism and completions

```
apiVersion: batch/v1
kind: Job
metadata:
  name: parallel
  labels:
    chapter: jobs
spec:
  parallelism: 5
  completions: 10
  template:
    metadata:
      labels:
        chapter: jobs
    spec:
      containers:
      - name: kuard
        image: gcr.io/kuar-demo/kuard-amd64:blue
        imagePullPolicy: Always
        command:
        - "/kuard"
        args:
        - "--keygen-enable"
        - "--keygen-exit-on-complete"
        - "--keygen-num-to-gen=10"
      restartPolicy: OnFailure
```


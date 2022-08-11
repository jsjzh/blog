# kubernetes

## install

### Tekton Pipelines

```shell
kubectl apply --filename https://storage.googleapis.com/tekton-releases/pipeline/latest/release.yaml
```

查看安装状态

```shell
kubectl get pods --namespace tekton-pipelines --watch
```

### Tekton Pipelines cli

```shell
brew tap tektoncd/tools
brew install tektoncd/tools/tektoncd-cli
```

- clustertask
  - Parent command of the ClusterTask command group.
- clustertriggerbinding
  - Parent command of the ClusterTriggerBinding command group.
- condition
  - Parent command of the Condition command group.
- eventlistener
  - Parent command of the Eventlistener command group.
- pipeline
  - Parent command of the Pipeline command group.
- pipelinerun
  - Parent command of the Pipelinerun command group.
- resource
  - Parent command of the Resource command group.
- task
  - Parent command of the Task command group.
- taskrun
  - Parent command of the Taskrun command group.
- triggerbinding
  - Parent command of the Triggerbinding command group.
- triggertemplate
  - Parent command of the Triggertemplate command group.

## start

### Task

基础配置

hello.yaml

```yaml
apiVersion: tekton.dev/v1beta1
kind: Task
metadata:
  name: echo-hello-world
spec:
  steps:
    - name: echo
      image: ubuntu
      command:
        - echo
      args:
        - "Hello World"
```

应用配置

```shell
kubectl apply -f hello.yaml
```

查看 Task

```shell
tkn task describe echo-hello-world
```

### TaskRun

要运行上面的任务，先要创建一个 TaskRun

hello-run.yaml

```yaml
apiVersion: tekton.dev/v1beta1
kind: TaskRun
metadata:
  name: echo-hello-world-task-run
spec:
  taskRef:
    name: echo-hello-world
```

应用配置

```shell
kubectl apply -f hello-run.yaml
```

查看配置

```shell
tkn taskrun describe echo-hello-world-task-run
```

查看日志

```shell
tkn taskrun logs echo-hello-world-task-run
```

此时会看到输出 `hello world`

## 其他

```shell
kubectl get pods -n wireless-ci
kubectl logs el-f2e-ci-listener-59fbfb9d7-56bp8 -n wireless-ci
kubectl delete pod wireless-runner-51373-frontend-task-r2t5j-pod-8654d2 -n wireless-ci
kubectl get pipelinerun $pipelinerun -n wireless-ci -o yaml  | kubectl  -n wireless-ci replace --force -f -
```

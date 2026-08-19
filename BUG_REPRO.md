# Bug Reproduction

## 包的性质

当前 test_model_fix 保存的是被测模型修复后的结果源码，不是初始含 Bug 源码。要复现原始缺陷，必须检出下面固定的 parent SHA；不要在当前修复结果源码上期待重新出现修复前失败。生成系统使用的可信验证补丁和完整验证日志仅在本地留存，不提交到结果分支。

## 问题现象

存在未决质量漂移的推理运行完成后，原本隔离的数据快照被自动改成已物化，质量审核入口随之失去隔离状态。请先不要修改代码，定位完成流程为何覆盖质量状态，并提供调用链和持久化证据。 调查全程不要修改目标仓库中的生产代码、测试代码或配置。

## 含 Bug 版本

- 仓库：zhanglei10281852-gif/ai-featuremesh-task-06
- 仓库地址：https://github.com/zhanglei10281852-gif/ai-featuremesh-task-06.git
- parent SHA：1be8e702b05d9719bd820675478017ed81d885cc

## 复现步骤

```bash
git clone -- https://github.com/zhanglei10281852-gif/ai-featuremesh-task-06.git bug-repro
cd bug-repro
git checkout --detach 1be8e702b05d9719bd820675478017ed81d885cc
go test ./internal/service -run "^TestCompletionKeepsQuarantinedSnapshots$" -count=1
```

## 双架构完整错误信息

### linux/amd64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/service -run "^TestCompletionKeepsQuarantinedSnapshots$" -count=1
--- FAIL: TestCompletionKeepsQuarantinedSnapshots (0.62s)
    annotation_behavior_test.go:77: arrived quarantined batch = {ID:snapshot_3987d081bba5e56b69b56937 WorkspaceID:workspace_72a9006f9984756cc3f93ee0 SourceZoneID:data_zone_300f6f0a96e54128e9000b07 SourceRevision:EXT-1 SchemaFamily:plasma PartitionCount:2 EstimatedRows:100 State:materialized ExpiresAt:2026-08-20 08:00:00 +0000 UTC InferenceRunID:run_ab6fb6dee477eed6672b7e37 QuarantineNote: CreatedAt:2026-08-18 08:00:00 +0000 UTC UpdatedAt:2026-08-18 08:00:00 +0000 UTC Version:6}
FAIL
FAIL	github.com/zhanglei10281852-gif/ai-featuremesh-base/internal/service	0.621s
FAIL

```

stderr：

```text
warning: internal/service/annotation_behavior_test.go has type 100755, expected 100644
warning: internal/service/service_test.go has type 100755, expected 100644
warning: internal/service/annotation_behavior_test.go has type 100755, expected 100644
warning: internal/service/service_test.go has type 100755, expected 100644

```

### linux/arm64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/service -run "^TestCompletionKeepsQuarantinedSnapshots$" -count=1
--- FAIL: TestCompletionKeepsQuarantinedSnapshots (1.34s)
    annotation_behavior_test.go:77: arrived quarantined batch = {ID:snapshot_81647f578664687e9fb4ef9f WorkspaceID:workspace_7a8186d453662b228e4e211a SourceZoneID:data_zone_5d5879c4c0588218035c3093 SourceRevision:EXT-1 SchemaFamily:plasma PartitionCount:2 EstimatedRows:100 State:materialized ExpiresAt:2026-08-20 08:00:00 +0000 UTC InferenceRunID:run_86fe0d08a605bd64b6775507 QuarantineNote: CreatedAt:2026-08-18 08:00:00 +0000 UTC UpdatedAt:2026-08-18 08:00:00 +0000 UTC Version:6}
FAIL
FAIL	github.com/zhanglei10281852-gif/ai-featuremesh-base/internal/service	1.518s
FAIL

```

stderr：

```text
warning: internal/service/annotation_behavior_test.go has type 100755, expected 100644
warning: internal/service/service_test.go has type 100755, expected 100644
warning: internal/service/annotation_behavior_test.go has type 100755, expected 100644
warning: internal/service/service_test.go has type 100755, expected 100644

```

## 通过条件

准确定位根因，指出具体 Go 文件和符号，解释错误行为如何导致题面症状，并给出实际复现、调用链或持久化证据；完成时目标仓库代码、测试和配置零改动。

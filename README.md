# aws-stepfunctions

## 

```
aws --version
aws-cli/2.24.5 Python/3.12.6 Linux/5.15.167.4-microsoft-standard-WSL2 exe/x86_64.ubuntu.24
```

## Step Functions 基礎

* ワークフローの定義（ステートマシンの定義とも呼ばれる）


### ステートマシンの定義 

json形式

```json
{
  "StartAt": "FirstState",
  "States": {
    "FirstState": {
      "Type": "Pass",
      "Next": "SecondState"
    },
    "SecondState": {
      "Type": "Succeed"
    }
  }
}
```

yaml形式

```yaml
StartAt: FirstState
States:
  FirstState:
    Type: Pass
    Next: SecondState
  SecondState:
    Type: Succeed
```

### IAMロールの作成 (aws cli)

ロールの作成とポリシーのアタッチ

```
aws iam create-role --role-name StepFunctionsExecutionRole \
  --assume-role-policy-document file://<(cat <<EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "states.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
EOF
)
```

```
aws iam attach-role-policy --role-name StepFunctionsExecutionRole \
  --policy-arn arn:aws:iam::aws:policy/service-role/AWSStepFunctionsFullAccess
```

### ステートマシンの作成 (aws cli)

```
aws stepfunctions create-state-machine \
  --name "SimpleStateMachine" \
  --definition file://state-machine-definition.json \
  --role-arn arn:aws:iam::<your-account-id>:role/StepFunctionsExecutionRole
```

### ステートマシンの実行 (aws cli)

実行
```
aws stepfunctions start-execution \
  --state-machine-arn arn:aws:states:<region>:<account-id>:stateMachine:SimpleStateMachine \
  --input '{}'
```

取得
```
aws stepfunctions describe-execution \
  --execution-arn arn:aws:states:<region>:<account-id>:execution:SimpleStateMachine:<execution-id>
```
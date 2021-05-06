**Problems**
flogo build fails because shim needs additional modules which are not listed in `go.mod`

```
Building a new handler file
go: main: package github.com/aws/aws-lambda-go/lambda imported from implicitly required module; to add missing requirements, run:
        go get github.com/aws/aws-lambda-go/lambda@v1.10.0
exit status 1
```

**FIX:**

run `go get` in same folder to update `go.mod` bevore `go build`

**Create ERROR:**

```sh
$ flogo build -f src/flogo.json -e --shim aws_lambda_trigger --verbose
flogo build -f src/flogo.json -e --shim aws_lambda_trigger --verbose
Creating Flogo App: etl_lambda_2
Setting up app directory: /tmp/flogo073965684/etl_lambda_2
go: creating new go.mod: module main
go: to add module requirements and sums:
        go mod tidy
Installing: github.com/project-flogo/core@latest
all modules verified
Importing Dependencies...
all modules verified
all modules verified
all modules verified
all modules verified
Installed trigger: github.com/project-flogo/aws-contrib/trigger/lambda
Installed activity: github.com/project-flogo/contrib/activity/actreturn
Installed function: github.com/project-flogo/contrib/function/string
Installed action: github.com/project-flogo/flow
Created App: etl_lambda_2
Embedding configuration in application...
Backing up main.go
Creating shim support files...
Preparing shim...
Using build.go to build shim......
Running build script for the Lambda trigger
Cleaning up previous executables
Building a new handler file
go: main: package github.com/aws/aws-lambda-go/lambda imported from implicitly required module; to add missing requirements, run:
        go get github.com/aws/aws-lambda-go/lambda@v1.10.0
exit status 1Zipping the new handler file
Failed to compress file: open /tmp/flogo073965684/etl_lambda_2/src/handler: no such file or directoryCleaning up shim support files...
Copying the binary from  /tmp/flogo073965684/etl_lambda_2/bin to /workspaces/flogo-aws-trigger-bug 
Error renaming executable: rename /tmp/flogo073965684/etl_lambda_2/bin/etl_lambda_2 /workspaces/flogo-aws-trigger-bug/etl_lambda_2: invalid cross-device link
```


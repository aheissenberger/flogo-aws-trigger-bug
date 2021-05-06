**Problems**
flogo build fails because shim needs additional modules which are not listed in `go.mod`

```
Building a new handler file
go: main: package github.com/aws/aws-lambda-go/lambda imported from implicitly required module; to add missing requirements, run:
        go get github.com/aws/aws-lambda-go/lambda@v1.10.0
exit status 1
```

**Produce the ERROR use case:**

alle resource to reproduce this error including setup can be found in this repository
https://github.com/aheissenberger/flogo-aws-trigger-bug


```sh

rm -fr src/app
flogo create -f flogo.json src/app
cd src/app
flogo build -e --shim aws_lambda_trigger --verbose
```
Output:
```
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
Failed to compress file: open /workspaces/flogo-aws-trigger-bug/src/app/src/handler: no such file or directoryCleaning up shim support files...
```


**FIX:**

change the shim [build](./aws-contrib-fixed/trigger/lambda/shim/build.go) of the trigger `github.com/project-flogo/aws-contrib/trigger/lambda` to run `go get` in same folder to update `go.mod` bevore `go build`

```sh
rm -fr src-fix/app
flogo create -f flogo.json src-fix/app
cd src-fix/app/src
go mod edit -replace github.com/project-flogo/aws-contrib/trigger/lambda="$(realpath $PWD/../../../aws-contrib-fixed/trigger/lambda)"
cd ..
flogo build -e --shim aws_lambda_trigger --verbose
```
Output
```
Embedding configuration in application...
Backing up main.go
Creating shim support files...
Preparing shim...
Using build.go to build shim......
FIXED: Running build script for the Lambda trigger
Cleaning up previous executables
Install modules
Building a new handler file
Zipping the new handler file
Cleaning up shim support files...
```

**Note:** Would it be better to run `go get` in the global cli code [/api/shim.go](https://github.com/project-flogo/cli/blob/master/api/shim.go) which prepares the shim?
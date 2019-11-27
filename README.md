# Kaniko demo

This repo is to demonstrate how Kaniko makes use of build cache.


## Build images with Docker CLI

1. Initial build:

   ```
   % docker build  -t todoapi:1  src 
   ```

2. Build again with cache:

   ```
   % docker build  -t todoapi:2  src 
   ```

3. Modify src and re-build with cache:

   ```
   % touch src/empty-3

   % docker build  -t todoapi:3  src 
   ```

Result:

| Step  | Time      |
|-------|-----------|
|   1   | 3m19s     | 
|   2   | 0m5.81s   |
|   3   | 0m15.85s  |




## Build images with Kaniko

1. Set environment variable `PROJECT_ID` and `REPO` for convenience:

   ```
   % export PROJECT_ID=project_id_of_your_gcp_project

   % export REPO=repo_name_of_your_image
   ```

2. Initial build with empty cache:

   ```
   % time \
     docker run --rm  -v $(pwd)/src:/kaniko-workspace  \
     gcr.io/kaniko-project/executor:latest             \
         --cache=true  --context=/kaniko-workspace     \
         --destination=gcr.io/$PROJECT_ID/$REPO
   ```

3. Build again with cache:

   ```
   % time \
     docker run --rm  -v $(pwd)/src:/kaniko-workspace  \
     gcr.io/kaniko-project/executor:latest             \
         --cache=true  --context=/kaniko-workspace     \
         --destination=gcr.io/$PROJECT_ID/$REPO
   ```

4. Modify src and re-build with cache:

   ```
   % touch src/empty-4

   % time \
     docker run --rm  -v $(pwd)/src:/kaniko-workspace  \
     gcr.io/kaniko-project/executor:latest             \
         --cache=true  --context=/kaniko-workspace     \
         --destination=gcr.io/$PROJECT_ID/$REPO
   ```

Result:

| Step  | Time   |
|-------|--------|
|   2   | 4m23s  | 
|   3   | 1m20s  |
|   4   | 1m55s  |


### Caveat with Kaniko + .NET Core SDK

If you use `dotnet/core/sdk` as base image, you may encounter error messages near the end of Kaniko build steps, similar to the following:

```
error building image: error building stage: Unable to add file /tmp/clr-debug-pipe-149-16587078-out to layered map: Error creating hash for /tmp/clr-debug-pipe-149-16587078-out: lstat /tmp/clr-debug-pipe-149-16587078-out: no such file or directory
```

A workaround is to set environment variable `COMPlus_EnableDiagnostics` before `dotnet publish` in `Dockerfile`: 

```
ENV COMPlus_EnableDiagnostics=0
...
dotnet publish
```

For more info, see https://github.com/GoogleContainerTools/kaniko/issues/391#issuecomment-477472649


## About the TodoApi source code

The sample code in `src` directory was extracted from the TodoApi demo in the Microsoft Docs site, retrieved on Nov 27, 2019:

 - Document - [Tutorial: Create a web API with ASP.NET Core MVC](https://docs.microsoft.com/en-us/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.0&tabs=visual-studio)

 - Source code - https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples/3.0/TodoApi


The original source code to be used in this repo is packed in the `TodoApi-original.zip` file for your reference.


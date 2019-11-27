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


## Build images with Kaniko

TODO


## About the source code

The sample code in `src` directory was extracted from the TodoApi demo in the Microsoft Docs site, retrieved on Nov 27, 2019:

 - Document - [Tutorial: Create a web API with ASP.NET Core MVC](https://docs.microsoft.com/en-us/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.0&tabs=visual-studio)

 - Source code - https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples/3.0/TodoApi


The original source code to be used in this repo is packed in the `TodoApi-original.zip` file for your reference.


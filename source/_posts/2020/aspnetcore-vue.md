---
title: 在 .NET CORE 项目中使用 VUE
date: 2020-02-22 13:08:35
tags: ["C#", "dotnet"]

keywords: .NET CORE VUE 前后分离 单页面
comments: true
---

最近在改造一个项目，想要在 .net core 项目中使用 vue 单页面应用。

<!-- more -->

## 创建项目

正常创建一个 MVC 项目，用 Web 应用程序模板或者 API 模板都可以。我就使用 API 模板作为示例了。

## 添加 Vue 支持

> 以下内容可能已经过期，请以项目文档为标准
> - [VueCliMiddleware](https://github.com/EEParker/aspnetcore-vueclimiddleware)
> - [Vue Cli](https://cli.vuejs.org/zh/guide/)


全局安装 `vue-cli`

```
npm install -g @vue/cli
```

在项目目录下创建一个 vue 项目

```
vue create myapp
```

修个 `package.json`

```diff package.json
  "scripts": {
    "serve": "vue-cli-service serve",
-   "build": "vue-cli-service build",
+   "build": "vue-cli-service build --dest ../wwwroot/",
    "lint": "vue-cli-service lint"
  },
```

项目安装 nuget 包 `VueCliMiddleware`，然后配置 `Startup.cs`，添加以下内容


```cs Startup.cs
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
#if DEBUG //重点
using VueCliMiddleware;
#endif

namespace WebApplication1
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        public void ConfigureServices(IServiceCollection services)
        {
            services.AddControllers();
        }


        public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.UseDefaultFiles(); //重点
            app.UseStaticFiles();  //重点

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints => { endpoints.MapControllers(); });

#if DEBUG //重点
            app.UseSpa(spa =>
            {
                spa.Options.SourcePath = "myapp";
                spa.UseVueCli();
            });
#endif
        }
    }
}
```

Debug环境下条件 `#DEBUG`预编译指令

![](/assets/img/2020/Snipaste_2020-02-22_10-15-44.png)

启动项目，访问 `http://localhost:5000`

![](/assets/img/2020/Snipaste_2020-02-22_10-25-21.png)

我目前是使用 visual studio 写api项目，使用visual studio code写vue项目。

## 构建项目

发布项目之前需要先在`myapp`目录执行

```
npm run build
```

也可以配置成自动执行，但是我一直在用ci，所以就写在了ci里面，关键语句如下

```yaml azure-pipelines.yml

steps:
  - task: UseNode@1
    inputs:
      version: "12.x"
  - task: CmdLine@2
    inputs:
      script: |
        cd $(Build.SourcesDirectory)/WebApplication1
        dotnet remove package VueCliMiddleware
        cd clientapp
        npm install
        npm run build
```

如果需要配置成在发布时自动执行，需要修改 `WebApplication1.csproj`

```xml WebApplication1.csproj`
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.1</TargetFramework>
  </PropertyGroup>

  <PropertyGroup Condition="'$(Configuration)|$(Platform)'=='Debug|AnyCPU'">
    <DefineConstants>DEBUG;TRACE</DefineConstants>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="3.1.1" />
    <PackageReference Include="VueCliMiddleware" Version="3.1.1" />
  </ItemGroup>

  <!-- 下面是重点 -->
  <Target Name="CheckNodeEnv" BeforeTargets="Build">
    <Exec Command="npm --version" ContinueOnError="true">
      <Output TaskParameter="ExitCode" PropertyName="ErrorCode" />
    </Exec>
  </Target>

  <Target Name="NpmInstall" AfterTargets="CheckNodeEnv" Inputs="package.json" Outputs="packages-lock.json">
    <Message Importance="high" Text="正在安装npm包" />
    <Exec WorkingDirectory="$(ProjectDir)myapp" Command="npm install" />
  </Target>
  <Target Name="NpmRunBuild" AfterTargets="NpmInstall">
    <Message Importance="high" Text="正在构建静态页面" />
    <Exec WorkingDirectory="$(ProjectDir)myapp" Command="npm run build" />
  </Target>
</Project>
```

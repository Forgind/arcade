# Codeflow PRs

Codeflow PRs are automated pull requests created by [Maestro](https://maestro.dot.net), and are generated by source-enabled subscriptions.  
These subscriptions can only be created between the [VMR (Virtual Monolithic Repository)](https://github.com/dotnet/dotnet/) and product repositories that have their [sources in the VMR](https://github.com/dotnet/dotnet/tree/main/src).  

As the name suggests, source-enabled subscriptions not only update dependencies but also synchronize source code changes **to and from** the VMR.
They ensure that repository sources remain in sync with the corresponding `src/` directory in the VMR.
Source code changes can originate in either the repository or the VMR and are synchronized to the other side via source-enabled subscriptions.

> [!IMPORTANT]
> Opening PRs against the VMR is **not yet permitted** and will be allowed in the near future. For now, source code changes should only be made in the repositories, barring exceptional cases.

For more details on code flow and the VMR, see [VMR Code and Build Workflow](https://github.com/dotnet/arcade/blob/main/Documentation/UnifiedBuild/VMR-Code-And-Build-Workflow.md).  

## Terminology

- **Product repository** / **VMR repository** - A repository that is required to build the .NET SDK, and which is synchronized [into the VMR](https://github.com/dotnet/dotnet/tree/main/src).
- **Dependency flow** / **Binary flow** - The old type of Maestro subscriptions that only update dependencies, e.g. [this one](https://github.com/dotnet/sdk/pull/47085).
- **Code flow** / **Source-enabled dependency flow** - New type of Maestro subscriptions that are, together with dependency updates, also flowing sources to/from the VMR.
- **Backflow** - A PR that flows changes from the VMR back to the product repository, carrying source updates as well as dependency updates (packages built in the VMR).
- **Forward flow** - A PR that flows changes from the product repository to the VMR, carrying repository source updates.
- **Flat flow** - A new structure of subscriptions between product repositories and the VMR.


## Codeflow PR Metadata

The code flow mechanism relies on metadata files similarly to the existing Maestro dependency flow.
- **`eng/Version.Details.xml`**
  - A file that was already used in the old dependency flow. It contains version information for dependencies between repositories. A new addition is the `<Source>` tag, used for tracking the latest codeflow from the VMR.
- **`src/source-manifest.json`**
  - A file [contained in the VMR](https://github.com/dotnet/dotnet/blob/main/src/source-manifest.json) that contains the last version of every repository synchronized into the VMR.

## FAQ

- **Where do packages get built? Where can I find the official build of the VMR?**
  - Packages are built in [Unified Build pipelines](https://dev.azure.com/dnceng/internal/_build?definitionId=1330).

- **How do I find the new codeflow subscriptions?**
  - A full list of subscriptions can be found at on the [maestro.dot.net](https://maestro.dot.net/subscriptions) webpage. Subscriptions can be managed with DARC commands in the same way they always were.

- **What should I do in case of conflicts?**
  - If you have an ongoing dependency PR in your repository with additional changes/work: Finish the PR as you would normally. If there are conflicts with the newly merged backflow PRs, you may tag **@dotnet/product-construction** in your PR and we will help you resolve those.
  
- **How can I see dependency subscriptions for my repository**?
  - Either use the [Maestro website](https://maestro.dot.net/subscriptions) or use the [`darc get-subscriptions`](../Darc.md) command.

- **Where can I find the new dependency PRs**?
  - The dependency PRs look almost the same as the old ones and are still [authored by the `dotnet-maestro` bot](https://github.com/pulls?q=sort%3Aupdated-desc+is%3Apr+author%3Aapp%2Fdotnet-maestro+archived%3Afalse+).  
The forward flow PRs will be opened [against the VMR](https://github.com/dotnet/dotnet/pulls/app%2Fdotnet-maestro) while the backflow PRs will be opened in your repository and named something like `[branch] Source code changes from dotnet/dotnet`.

- **My repo X depends on repo Y's packages. How will I get the new packages**?
  - If repo Y is part of the VMR, you will depend on the VMR instead of repo Y.
    The packages will be produced by the official VMR build and published to the `.NET 10 UB` channel.  
    The target feed for some packages might change from `dotnet-eng` to [`dotnet10-transport`](https://pkgs.dev.azure.com/dnceng/internal/_packaging/dotnet10-transport/nuget/v3/index.json).

    Furthermore, if you depend on multiple VMR repositories, you will get all the packages in a single backflow subscription (PR).

## Contacts & Support

If you need help or have questions around the new flow, please either:
- tag the **@dotnet/product-construction** team on your PR/issue,
- use the [First Responder channel](https://teams.microsoft.com/l/channel/19%3Aafba3d1545dd45d7b79f34c1821f6055%40thread.skype/First%20Responders?groupId=4d73664c-9f2f-450d-82a5-c2f02756606d),
- open an issue in [dotnet/arcade-services](https://github.com/dotnet/arcade-services/issues/new?template=BLANK_ISSUE),
- or contact the [.NET Product Construction Services team](mailto:dotnetprodconsvcs@microsoft.com) via e-mail.


## Additional Resources

- [VMR Code and Build Workflow](https://github.com/dotnet/arcade/blob/main/Documentation/UnifiedBuild/VMR-Code-And-Build-Workflow.md)
- [VMR Full Code Flow](https://github.com/dotnet/arcade/blob/main/Documentation/UnifiedBuild/VMR-Full-Code-Flow.md)
- [Flat Flow Migration Guide](./Flat-Flow-Migration-Guide.md)
- [Darc Documentation](https://github.com/dotnet/arcade/tree/main/Documentation/Darc)
- [Branches, channels, and subscriptions](https://github.com/dotnet/arcade/blob/main/Documentation/BranchesChannelsAndSubscriptions.md)

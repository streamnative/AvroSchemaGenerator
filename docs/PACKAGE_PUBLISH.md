# Package Publishing

This document describes how to publish `StreamNative.AvroSchemaGenerator` to NuGet.

## Package identity

- Package ID: `StreamNative.AvroSchemaGenerator`
- NuGet source: `https://api.nuget.org/v3/index.json`
- Repository: `https://github.com/streamnative/AvroSchemaGenerator`
- License: Apache-2.0
- Upstream project: `https://github.com/Sharp-Pulsar/AvroSchemaGenerator`
- Target frameworks: `netstandard2.0`, `net8.0`
- First stable version: `1.0.0`

The NuGet package name is `StreamNative.AvroSchemaGenerator`, but the public C# namespace remains `AvroSchemaGenerator` for compatibility.

This package is a StreamNative-maintained distribution based on `Sharp-Pulsar/AvroSchemaGenerator`. Keep the Apache-2.0 license, README attribution, and `NOTICE` file intact when publishing.

NuGet prerelease versions are supported. Use versions such as `1.0.0-rc.1`, `1.0.0-rc.2`, and then `1.0.0` for the stable release.

## Prepare a release

1. Update `CHANGELOG.md`.
2. Update `GitVersion.yml` if the next package version changes.
3. Make sure the package metadata in `SchemaGenerator/AvroSchemaGenerator.csproj` is correct.
4. Run the release build locally:

```bash
dotnet restore SchemaGenerator.sln
dotnet test SchemaGenerator.sln -c Release
dotnet pack SchemaGenerator/AvroSchemaGenerator.csproj -c Release -o output/nuget
```

For a local RC package, pass the package version explicitly:

```bash
dotnet pack SchemaGenerator/AvroSchemaGenerator.csproj \
  -c Release \
  -o output/nuget \
  /p:Version=1.0.0-rc.1
```

5. Inspect the package contents:

```bash
unzip -l output/nuget/StreamNative.AvroSchemaGenerator.*.nupkg
```

The package should contain:

- `lib/netstandard2.0/AvroSchemaGenerator.dll`
- `lib/net8.0/AvroSchemaGenerator.dll`
- `README.md`
- `NOTICE`
- `avro-schema.png`

When symbol package generation is enabled, the output should also include a `.snupkg` file.

## Optional manual publish

Manual publishing is useful for the first release because NuGet shows a verification screen before submission.

1. Open https://www.nuget.org/packages/manage/upload.
2. Upload `output/nuget/StreamNative.AvroSchemaGenerator.<version>.nupkg`.
3. Review the package ID, version, README attribution, repository URL, icon, license, NOTICE file, and dependencies.
4. Submit the package only after the verification page is correct.

Manual CLI publishing is also supported:

```bash
dotnet nuget push output/nuget/StreamNative.AvroSchemaGenerator.<version>.nupkg \
  --api-key "$NUGET_API_KEY" \
  --source https://api.nuget.org/v3/index.json
```

If a `.snupkg` file was generated, publish it with the same command:

```bash
dotnet nuget push output/nuget/StreamNative.AvroSchemaGenerator.<version>.snupkg \
  --api-key "$NUGET_API_KEY" \
  --source https://api.nuget.org/v3/index.json
```

## CI publish

The GitHub Actions release workflow publishes packages when a tag is pushed.
The build reads the GitHub tag name from `GITHUB_REF_NAME`, so the tag is the NuGet package version.

1. Confirm that GitHub repository secret `NUGET_API_KEY` exists.
2. To publish an RC, create and push a prerelease SemVer tag:

```bash
git tag 1.0.0-rc.1
git push origin 1.0.0-rc.1
```

3. To publish the first stable release, create and push the stable SemVer tag:

```bash
git tag 1.0.0
git push origin 1.0.0
```

4. Watch the `Release` GitHub Actions workflow.
5. Confirm that both `.nupkg` and `.snupkg` files were uploaded.
6. Wait for NuGet indexing to complete.

The build script uses the full NuGet package version for the package itself, so tags such as `1.0.0-rc.1` produce `StreamNative.AvroSchemaGenerator.1.0.0-rc.1.nupkg`. Assembly and file versions use the stable numeric portion, so `1.0.0-rc.1` produces assembly/file version `1.0.0`.

## Post-publish verification

Create a temporary consumer project and install the published package:

```bash
mkdir /tmp/sn-avro-schema-generator-check
cd /tmp/sn-avro-schema-generator-check
dotnet new console
dotnet add package StreamNative.AvroSchemaGenerator --version 1.0.0
dotnet build
```

For an RC verification, install the prerelease version explicitly:

```bash
dotnet add package StreamNative.AvroSchemaGenerator --version 1.0.0-rc.1
```

Then verify a minimal usage example:

```csharp
using AvroSchemaGenerator;

public class Course
{
    public string Level { get; set; }
    public int Year { get; set; }
}

var schema = typeof(Course).GetSchema();
Console.WriteLine(schema);
```

## If the release has a problem

NuGet package versions are immutable after publishing. If a bad package is published:

1. Unlist the bad version from nuget.org.
2. Fix the issue in the repository.
3. Publish a new patch version.

Do not try to reuse the same version number.

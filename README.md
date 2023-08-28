# Template Project for REST Contracts

This repo contains an example of a REST contract repository, to be used as a template repository in GitHub.

# Tools

See [ronin-contract-rest-tooling](https://github.com/projectronin/ronin-gradle/blob/main/gradle-plugins/ronin-contract-openapi-plugin) for more information about the tooling and how it works.

# Usage

## Project setup

The project looks like this:

```
├── .github                                    GitHub workflows
├── .gitignore                                 Generally applicable ignores
├── build.gradle.kts                           Gradle build script
├── settings.gradle.kts                        Gradle build settings script
└── src/main/openapi
    └── <project-name>.json
```

Change the `rootProject.name` value in [settings.gradle.kts](settings.gradle.kts) to reflect what you want your deployed artifact ID to be.

Replace [ronin-contract-rest-template.json](src/main/openapi/ronin-contract-rest-template.json) with your OpenAPI spec.  You may break up using references to other local files so long as they are
contained inside the `src/main/openapi` directory, but you should put them in sub-folders (e.g. `src/main/openapi/schemas/<schema-name>.json`).  Ignore the `info/version` value of your spec, as it
will be replaced during the build process.

The plugin outputs five artifacts:
- a tar.gz file that contains the compiled schemas
- a .json copy of the schema
- a .yaml copy of the schema
- a .jar file with the compiled schema and compiled kotlin classes
- a -sources.jar file with the source files

### Dependencies

If your contract depends on _other_ contracts, you can reference them as project dependencies.  Make entries in your build.gradle.kts file like this:

```kotlin
dependencies {
    openapi("<dependency group id>:<dependency artifact id>:<dependency version>")
}
```

When you run the commands described below, these dependencies will be downloaded and placed in the `src/main/openapi/.dependencies` directory .  If they
are archives, they'll be unzipped, and you can reference the dependent contracts, schemas, etc. directly by path from inside your OpenAPI schema.

## Running

In general, run:

`gradle <COMMAND> <ARGUMENTS>`

Available commands are mostly general gradle commands.  Important ones are:

`check`: Verifies the contract using spectral tooling, making sure it's a valid contract.

`downloadApiDependencies`: downloads any API dependencies as specified in the `Dependencies` section above.

`build`: Runs `check`, generates schema documentation, and generates simple combined schema files.

`clean`: removes all generated files

`assemble`: Assembles the schema into deployable archives.

`publishToMavenLocal`: publishes all outputs to the local maven repo (e.g. `$HOME/.m2/repository`).  If you are using the docker image, it will try and
copy files in and out of the host's repository directory so they can be used for builds later on the host.

`publish`: publishes all outputs to the remote Ronin maven repository.

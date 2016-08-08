# Rome

Rome is a tool that allows developer on Apple platforms to use Amazon's S3 as a
cache for frameworks build with [Carthage](https://github.com/Carthage/Carthage).

## The problem

Suppose you're working on some frameworks for you iOS project and want to share
your frameworks with your team. A great way to do so is to use Carthage and have
team members point the `Cartfile` to the new framework version (or branch, tag, commit)
and run `carthage update`.

Unfortunately this will require them to build from scratch the new framework.
This is particularly annoying if the dependency tree for that framework is big
and / or takes a long time to build.

## The solution

Use a cache. The first team member can build the framework and share it while all
other developers can get it from the cache with no waiting time.

## Workflow

The Rome's workflow changes depending if you are the producer (i.e. the first
person in your team to build the framework) or the consumer.

### Producer workflow

```
$ vi Cartfile #point to the new version of the framework
$ carthage update && rome upload
```

### Consumer workflow

```
$ vi Cartfile
$ carthage update --no-build && rome download
```

or

```
$ vi Cartfile.resolved #point to the new version of the framework
$ rome download
```
## Set up and Usage

- First you need a `.aws-keys` file in your home folder. This is used to specify
your AWS Credentials
- Second you need a `Romefile` in the project where you want to use Rome. At the
same level where the `Cartfile` is.

### Setting up AWS credentials
In your home folder create a `.aws-keys` that contains the following line
```
default AWS_IDENTITY AWS_PRIVATE_KEY
```

this should look something like

```
default AGIAJQARMD67CE3DTKHA TedRV2/dFkBr1H3D7xuPsF9+CBHTjK0NKrJuoVs8
```

these will be the credentials that Rome will use to access S3 on your behalf

### Romefile

The Romefile has tow purposes:
1. Specifies what S3 bucket to use - [S3Bucket] section. This section is __required__.
1. Allows to use custom name mappings between repository names and framework names - [RepositoryMap] section. This section is __optional__ and can be omitted.

A Romefile looks like this

```
[S3Bucket]
  ios-dev-bucket

[RepositoryMap]
  awesome-framework-for-cat-names CatFramework
  better-dog-names  DogFramework
```  

#### S3Bucket section
This section contains the name of the S3 bucket you want Rome to use to upload/download.

#### RepositoryMap
This contains the mappings of git repository names with framework names.
This is particularly useful inn case you are not using github and the "Organization/FrameworkName" convention.

Example:

Suppose you have the following in your `Cartfile`

```
git "http://stash.myAimalStartup.com/scm/iossdk/awesome-framework-for-cat-names.git" ~> 3.3.1
git "http://stash.myAimalStartup.com/scm/iossdk/better-dog-name.git" ~> 0.4.4
```

but your framework names are actually `CatFramework` and `DogFramework` as opposed to `awesome-framework-for-cat-names` and `better-dog-names`.

simply add a `[RepositoryMap]` section to your `Romefile` and specify the following mapping:

```
[S3Bucket]
  ios-dev-bucket

[RepositoryMap]
  awesome-framework-for-cat-names CatFramework
  better-dog-names  DogFramework
```

## Get Rome
The Rome binary is attached as a zip to the releases here on GitHub.

# Scalate support for SBT 0.1x.x
 
Integration for SBT that lets you generate sources for your Scalate templates and precompile them as part of the normal compilation process. This plugin is published to scala-tools.org.
 
## Usage

### Getting the plugin

Include the plugin in `project/plugins.sbt`:

For sbt 0.12.0:

```scala
addSbtPlugin("com.mojolly.scalate" % "xsbt-scalate-generator" % "0.2.2")
```

For sbt 0.11.3:

```scala
resolvers += Resolver.url("sbt-plugin-releases",
  new URL("http://scalasbt.artifactoryonline.com/scalasbt/sbt-plugin-releases/"))(
    Resolver.ivyStylePatterns)

addSbtPlugin("com.mojolly.scalate" % "xsbt-scalate-generator" % "0.2.0")
```

for sbt 0.11.2: (maven central)

```scala
libraryDependencies <+= sbtVersion(v => "com.mojolly.scalate" %% "xsbt-scalate-generator" % (v + "-0.1.6"))
```

or as a git dependency in `project/project/build.scala`:

```scala
import sbt._
import Keys._

object PluginsBuild extends Build {
  lazy val root = Project("plugins", file(".")) dependsOn (scalateGenerate) settings (scalacOptions += "-deprecation")
  lazy val scalateGenerate = ProjectRef(uri("git://github.com/mojolly/xsbt-scalate-generate.git"), "xsbt-scalate-generator")
}
```

### Configuring the plugin

Configure the plugin in `build.sbt`:

```scala

import ScalateKeys._

seq(scalateSettings:_*)
      
scalateTemplateDirectory in Compile <<= (sourceDirectory in Compile) { _ / "webapp" / "WEB-INF" }

scalateImports ++= Seq(
  "import scalaz._",
  "import Scalaz._",
  "import org.scalatra.oauth2._",
  "import OAuth2Imports._",
  "import model._"
)

scalateBindings ++= Seq(
  Binding("flash", "scala.collection.Map[String, Any]", defaultValue = Some("Map.empty")),
  Binding("session", "org.scalatra.Session"),
  Binding("sessionOption", "Option[org.scalatra.Session]"),
  Binding("params", "scala.collection.Map[String, String]"),
  Binding("multiParams", "org.scalatra.MultiParams"),
  Binding("userOption", "Option[ResourceOwner]", defaultValue = Some("None")),
  Binding("user", "ResourceOwner", defaultValue = Some("null")),
  Binding("isAnonymous", "Boolean", defaultValue = Some("true")),
  Binding("isAuthenticated", "Boolean", defaultValue = Some("false")))

```

### Trigger recompilation on save

From version 0.2.2 onwards the plugin detects when sources are changed and will trigger a recompilation.
Older versions can add this to their build.sbt:

```scala
watchSources <++= (scalateTemplateDirectory in Compile) map (d => (d ** "*").get)
```

## Patches

Patches are gladly accepted from their original author. Along with any patches, please state that the patch is your original work and that you license the work to the *xsbt-scalate-generate* project under the MIT License.
 
## License
 
MIT licensed. Check the [LICENSE](https://raw.github.com/backchatio/xsbt-scalate-generate/master/LICENSE) file.

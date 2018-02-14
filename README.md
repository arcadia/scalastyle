# Arcadia unified Scalastyle configuration

We use [Scalastyle](http://www.scalastyle.org/) in conjunction with our build systems to establish and maintain unified and automated style conformation.


## Integration

Our project templates should already have integration in place, but in case not, here's a starting point.

### SBT via [scalastyle-sbt-plugin](http://www.scalastyle.org/sbt.html) 

```sbt
(scalastyleConfigUrl in Compile) := Some(
  url("https://raw.githubusercontent.com/arcadia/scalastyle/master/scalastyle-config.xml")
)

lazy val testScalastyle = taskKey[Unit]("testScalastyle")
(scalastyleConfigUrl in Test) := Some(
  url("https://raw.githubusercontent.com/arcadia/scalastyle/master/scalastyle-config.xml")
)
(scalastyleFailOnError in Test) := false
(scalastyleFailOnWarning in Test) := false
testScalastyle := scalastyle.in(Test).toTask("").value
(test in Test) := ((test in Test) dependsOn testScalastyle).value

lazy val symlinkScalaStyle = taskKey[Unit]("Creates a symbolic link that enables ScalaStyle within IntelliJ")
symlinkScalaStyle := {
    val symTarget = (target.value / scalastyleConfigUrlCacheFile.value).toString
    val symFile = (baseDirectory.value / s"project/${scalastyleConfig.value}").toString
    val command: Seq[String] = "ln" :: "-fs" :: symTarget :: symFile :: Nil
    Process(command) !
}
```
